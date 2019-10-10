## 关于添加第二y轴
### 对于同一plot中存在的两个图层，存在两种情况：
#### 1.共用一个y轴（左侧）
这种操作比较常规，只要添加两个独立图层即可， 如：
、、、{r}
ggplot(data) +
  geom_line(aes(order_year_month, sum_sales, color = "sum_sales")) +
  geom_line(aes(order_year_month, sum_profit, color = "sum_profit"))
、、、
这其中需要注意的是color参数的位置应该在aes()内，并且建议颜色就写所添加y轴变量的列名（必须要用双引号，否则颜色默认成此变量的渐变色），
以此来区分两个图层各自表示什么（此操作会自动选择两种颜色并添加y1，y2的图例）。如果把color参数放在aes()外面，则只能区分颜色而不能添加图例。
  
#### 2.分别使用左右两个独立y轴
这种操作要复杂一点，主要有3个点需要注意：
##### 首先，分轴需要用到ggplot以外的第三方包"scales"，在library(scales)以后，可以在scale_y_continuous()中使用sec.axis参数和sec_axis()函数。
scale_y_continuous()本身控制的是左边的主y轴，所以关于主y轴的设定都在此函数内。右边的次y轴的设置则在sec.axis = sec_axis()这个参数的函数内，如
scale_y_continuous(labels = comma, sec.axis = sec_axis(~ .*0.1, name = "profit", labels = comma))
##### 其次，是次轴（右）的取值范围问题。左轴的范围会根据y1的范围自动判断，次轴则需要手动放缩。sec_axis(~ .)中的"~ ."表示跟主轴同范围，后可接运算符号调整，
如~ .*0.1就是把次轴的取值范围缩小十倍，但是千万注意此时的y2图像位置是不会自动根据次轴的范围调整变化的。
需要手动将y2放大十倍来确保数值与次轴的取值范围一致（sum_profit*10）：
ggplot(data) +
  geom_line(aes(order_year_month, sum_sales, color = "sum_sales")) +
  geom_line(aes(order_year_month, sum_profit*10, color = "sum_profit")) +
  scale_y_continuous(labels = comma, sec.axis = sec_axis(~ .*0.1,name = "profit", labels = comma))
##### 另外，还有个问题是数值过大的显示往往自动变成科学记数法，如1e+06，要避免此问题需要在scale_y_continuous()中添加"labels = comma"，
注意添加位置决定了调整的是哪一个y轴（sec.axis = sec_axis()的外面还是里面）
