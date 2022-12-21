# 用 Pythonic 的方式祝圣诞快乐

> 原文：<https://dev.to/anuragrana/wishing-merry-christmas-in-pythonic-way-47mk>

大家圣诞快乐。

因为今天是圣诞节，所以我想用不同的方式祝福大家。我是 python 程序员，我喜欢写代码，所以我决定用 python 做点什么，1 个小时后，我准备好了下面的脚本，祝所有使用 python turtle 的人圣诞快乐。

代码也可以在 [Github](https://github.com/anuragrana/Python-Scripts/blob/master/merry_christmas_using_python.py) 上获得。

```
from turtle import *
from random import randint

def create_rectangle(turtle, color, x, y, width, height):
    turtle.penup()
    turtle.color(color)
    turtle.fillcolor(color)
    turtle.goto(x, y)
    turtle.pendown()
    turtle.begin_fill()
    turtle.forward(width)
    turtle.left(90)
    turtle.forward(height)
    turtle.left(90)
    turtle.forward(width)
    turtle.left(90)
    turtle.forward(height)
    turtle.left(90)
    # fill the above shape
    turtle.end_fill()
    # Reset the orientation of the turtle
    turtle.setheading(0)

def create_circle(turtle, x, y, radius, color):
    oogway.penup()
    oogway.color(color)
    oogway.fillcolor(color)
    oogway.goto(x, y)
    oogway.pendown()
    oogway.begin_fill()
    oogway.circle(radius)
    oogway.end_fill()

BG_COLOR = "#0080ff"
# "Yesterday is history, tomorrow is a mystery, but today is a gift. That is why it is called the present.”
#                                                       — Oogway to Po under the peach tree, Kung Fu Panda oogway = Turtle()
# set turtle speed oogway.speed(2)
screen = oogway.getscreen()
# set background color screen.bgcolor(BG_COLOR)
# set tile of screen screen.title("Merry Christmas")
# maximize the screen screen.setup(width=1.0, height=1.0)
y = -100
# create tree trunk create_rectangle(oogway, "red", -15, y-60, 30, 60)
# create tree width = 240
oogway.speed(10)
while width > 10:
    width = width - 10
    height = 10
    x = 0 - width/2
    create_rectangle(oogway, "green", x, y, width, height)
    y = y + height
# create a star a top of tree oogway.speed(1)
oogway.penup()
oogway.color('yellow')
oogway.goto(-20, y+10)
oogway.begin_fill()
oogway.pendown()
for i in range(5):
    oogway.forward(40)
    oogway.right(144)
oogway.end_fill()
tree_height = y + 40
# create moon in sky
# create a full circle create_circle(oogway, 230, 180, 60, "white")
# overlap with full circle of BG color to make a crescent shape create_circle(oogway, 220, 180, 60, BG_COLOR)
# now add few stars in sky oogway.speed(10)
number_of_stars = randint(20,30)
# print(number_of_stars) for _ in range(0,number_of_stars):
    x_star = randint(-(screen.window_width()//2),screen.window_width()//2)
    y_star = randint(tree_height, screen.window_height()//2)
    size = randint(5,20)
    oogway.penup()
    oogway.color('white')
    oogway.goto(x_star, y_star)
    oogway.begin_fill()
    oogway.pendown()
    for i in range(5):
        oogway.forward(size)
        oogway.right(144)
    oogway.end_fill()
# print greeting message oogway.speed(1)
oogway.penup()
msg = "Merry Christmas from ThePythonDjango.Com"
oogway.goto(0, -200)  # y is in minus because tree trunk was below x axis oogway.color("white")
oogway.pendown()
oogway.write(msg, move=False, align="center", font=("Arial", 15, "bold"))
oogway.hideturtle()
screen.mainloop() 
```

输出视频:

[https://youtu.be/mjhoQonjVDk](https://youtu.be/mjhoQonjVDk)

**更从[https://www.pythoncircle.com](https://www.pythoncircle.com)T3】**

[**-如何在 Pythonanywhere 上免费托管 Django 应用程序**](https://www.pythoncircle.com/post/18/how-to-host-django-app-on-pythonanywhere-for-free/)
[**-Python 中列表、集合和元组的区别以及更多此类比较**](https://www.pythoncircle.com/post/249/difference-between-list-set-and-tuples-in-python-and-more-such-comparisons/)
[**-如何使用 Python 创建完全自动化的电报通道**](https://www.pythoncircle.com/post/265/how-to-create-completely-automated-telegram-channel-with-python/)
[**-使用 Kibana 和 Django 进行弹性搜索**](https://www.pythoncircle.com/post/291/elastic-search-with-kibana-and-django/)