title: WPF使用Canvas绘制可变矩形
date: 2014-06-01 22:30:03
categories:
	- c#
	- wpf
tags:
	- c#
	- wpf
	- canvas
---

####1、问题以及解决办法
最近因为项目需要，需要实现一个位置校对的功能，大致的需求如下：有一个图片，有一些位置信息，但是位置信息可能和实际有些偏差，需要做简单调整，后面会对这张图片进行切割等，做些处理。（位置信息连接起来是一个个小矩形。）

解决以上问题的大致思路如下：使用canvas进行绘制，把图片作为canvas的背景，在canvas上绘制矩形，类似于qq截图一样，矩形框可以使用鼠标拖动调整大小。然后在记下修改后的位置，提供给后面切割图片使用。目前的关键问题就是实现**类似qq截图那样可以拖动的矩形**。<!--more-->
####2、实现的效果预览
![可拖动的矩形](http://yywang.qiniudn.com/QQ%E6%88%AA%E5%9B%BE20140601225729.png)

以上是实现的demo的效果。主要由定位点和连线组成。
####3、可变矩形实现
1. 定位点
  定位点主要用于描述在矩形上的小方框，鼠标拖动的事件就是由它触发，它位置的移动会联动相关线的移动。

![定位点类图](http://yywang.qiniudn.com/AnchorPointClass.png)

定位点主要是定位点的一些基本属性和基本方法，主要包括绘制方法，移动方法。
其中：AnchorPointType为定位点的类型;

```C#
    public enum AnchorPointType
    {
        /// <summary>
        /// 上下
        /// </summary>
        NS,
        /// <summary>
        /// 左右
        /// </summary>
        WE,
        /// <summary>
        /// 右上
        /// </summary>
        NE,
        /// <summary>
        /// 左下
        /// </summary>
        SW,
        /// <summary>
        /// 右下
        /// </summary>
        NW,
        /// <summary>
        /// 左上
        /// </summary>
        SE
    }
```
draw()方法用于绘制矩形：

```c#
        public Rectangle Draw() 
        {
            double offset = this.Width / 2;
            Rectangle retc = new Rectangle()
            {
                Margin = new Thickness(this.X - offset, this.Y - offset, 0, 0),
                Width = this.Width,
                Height = this.Height,
                Fill = Brushes.LightGoldenrodYellow,
                Stroke = Brushes.Black,
                StrokeThickness = 1,
                DataContext = this.Key
            };
            this.retc = retc;
            return retc;
        }
```
move()方法用户改变定位点位置
```C#
        public void Move(double x,double y)
        {
            double offset = this.Width / 2;
            this.retc.Margin = new Thickness(x-offset,y-offset,0,0);
            this.X = x;
            this.Y = y;
        }
```

2. 可变矩形
 这部分主要实现绘制矩形功能，主要代码如下：
```c#
       public void Init()
        {
            //按x轴分类
            IEnumerable<IGrouping<double, Point>> pointXs = points.GroupBy(o => o.X);
            //按y周分类
            IEnumerable<IGrouping<double, Point>> pointYs = points.GroupBy(o => o.Y);
            //绘制竖线
            DrawXLine(pointXs);
            //绘制横线
            DrawYLine(pointYs);
            //设置定位点
            AddAnchorPoints();
            //绘制定位点并且添加事件
            foreach (AnchorPoint anchorPoint in anchorPoints)
            {
                Rectangle rec=anchorPoint.Draw();
                rec.MouseLeftButtonDown += new MouseButtonEventHandler(rec_MouseLeftButtonDown);
                rec.MouseMove += new MouseEventHandler(rec_MouseMove);
                canvas.Children.Add(rec);
            }
            //canvas添加事件
            canvas.MouseLeftButtonUp += new MouseButtonEventHandler(canvas_MouseLeftButtonUp);
            canvas.MouseMove += new MouseEventHandler(canvas_MouseMove);
            canvas.MouseLeave += new MouseEventHandler(canvas_MouseLeave);
        }
```
如上代码：
    如上代码：
	1、按x轴，y轴分类
	2、绘制竖线，横线
	3、设置定位点
	4、绘制定位点并且添加事件监听
	5、给canvas添加事件
给每个定位点添加鼠标MouseMove和MouseLeftButtonDown事件，给canvas添加MouseMove,MouseLeave,MouseLeftButtonUp事件。
具体代码不在粘贴了，如果需要代码，可以去下载源代码
3. 矩形线联动
矩形线联动，主要是以点带线，通过判断线是否和动点相关联，联动相关的线。主要代码如下：
```c#
        private void MoveLines(double x, double y)
        {
            List<Line> moveLines = new List<Line>();
            moveLines = lines.Where(o => o.Y1 == curAnchorPoint.Y
                || o.Y2 == curAnchorPoint.Y
                || o.X1 == curAnchorPoint.X
                || o.X2 == curAnchorPoint.X).ToList();
            foreach (Line line in moveLines)
            {
                if (line.Y1 == curAnchorPoint.Y)
                {
                    line.Y1 = y;
                }
                if (line.Y2 == curAnchorPoint.Y)
                {
                    line.Y2 = y;
                }
                if (line.X1 == curAnchorPoint.X)
                {
                    line.X1 = x;
                }
                if (line.X2 == curAnchorPoint.X)
                {
                    line.X2 = x;
                }
            }
        }
```
4. 定位点联动

点的联动，和线的联动方法类似，但是点的联动要比线的联动复杂，主要在以下三个方面：1、点的移动需要联动中点的联动。2、在矩形的四个顶点变动时，需要联动其他两个相邻的顶点，和相邻两条线的中点的联动。3、不同的方向点的联动，不一样，会有很多if else。
由于代码较长，就不粘贴了，需要源代码可以去获取源代码。
线中点联动：线的中点联动需要考虑，移动的点是否关联到计算这个中点的两个点或者一个点，关联到中点的这两个点如果有改变，则这个中点就需要改变。主要代码如下：

```c#
        private void MoveRefAnchorPoint(double x, double y,AnchorPoint movedAnchorPoint)
        {
            foreach (AnchorPoint anchorPoint in anchorPoints)
            {
                if (anchorPoint.RefPoint.Length == 2)
                {
                    if (anchorPoint.RefPoint[0].X == x && anchorPoint.RefPoint[0].Y == y)
                    {
                        anchorPoint.RefPoint[0].X = movedAnchorPoint.X;
                        anchorPoint.RefPoint[0].Y = movedAnchorPoint.Y;
                    }
                    else if (anchorPoint.RefPoint[1].X == x && anchorPoint.RefPoint[1].Y == y)
                    {
                        anchorPoint.RefPoint[1].X = movedAnchorPoint.X;
                        anchorPoint.RefPoint[1].Y = movedAnchorPoint.Y;
                    }
                    anchorPoint.X = (anchorPoint.RefPoint[0].X + anchorPoint.RefPoint[1].X) / 2;
                    anchorPoint.Y = (anchorPoint.RefPoint[0].Y + anchorPoint.RefPoint[1].Y) / 2;
                    anchorPoint.Move();
                }
            }
        }
```
以上为单个关联点改变的情况的代码，两个关联点都变动的代码和这个类似。
####4、获取源代码
由于文字表达能力有限，另外说清点和线的关系，特别是点和线的联动，确实有点困难，如果感兴趣的话，强烈建议下载代码进行调试阅读。
https://github.com/wangyan9110/ProjectDemos/tree/master/WPFCanvasDemo
