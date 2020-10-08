# Paint常用函数

> [参考文章](https://blog.csdn.net/harvic880925/article/details/51010839)

## 基本设置函数

| 函数名                                | 作用                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| reset ( )                             | 重置画笔                                                     |
| setColor (int color)                  | 设置画笔颜色                                                 |
| setARGB (int a, int r, int g ,int b)  | 设置画笔颜色，用ARGB                                         |
| setAlpha ( int a )                    | 设置画笔透明度                                               |
| setStyle ( Paint.Style   style)       | 设置画笔样式，有三种取值 <br>  Paint.Style.FILL              填充内部<br>  Paint.Style.STROKE           仅描边<br>  Paint.Style.FILL_AND_STROKE    填充内部和描边 |
| setStrokeWidth ( float   width )      | 设置画笔宽度                                                 |
| setAntiAlias ( boolean aa )           | 设置画笔是否抗锯齿                                           |
| setStrokeMiter ( float   miter )      | 设置画笔的倾斜度                                             |
| setPathEffect ( PathEffect   effect ) | 取值是PathEffect的子类，可以参考顶部的链接                   |
| setStrokeCap ( Paint.Cap   cap )      | 设置线帽样式，有三种取值<br>  Paint.Cap.ROUND     圆形线帽<br>  Paint.Cap.SQUARE     方形线帽<br>  Paint.Cap.BUTT       无线帽<br>  具体效果可以看顶部链接或者书220页 |
| setStrokeJoin ( Paint.Join   join )   | 设置路径的转角样式，有三种取值<br>  Paint.Join.MITER    结合处为锐角<br>  Paint.Join.ROUND   结合处为圆角<br>  Paint.Join.BEVEL     结合处为直线<br>  具体效果可以看顶部链接或者书220页 |
| setDither ( boolean dither  )         | 设置在绘制图像时的抗抖动效果                                 |

## 字体相关函数

| 函数名                                         | 作用                                                      |
| ---------------------------------------------- | --------------------------------------------------------- |
| setTextSize ( float   textSize )               | 设置文字的大小                                            |
| setFakeBoldText ( boolean   fakeBoldText)      | 设置是否为粗体文字                                        |
| setStrikeThruText ( boolean   strikeThruText ) | 设置带有删除线效果                                        |
| setUnderLineText ( boolean   underlineText )   | 设置下划线                                                |
| setTextAlign ( Paint.Align   align )           | 设置开始绘图位置                                          |
| setTextScaleX ( float   scaleX )               | 设置水平拉伸                                              |
| setTextSkewX ( float skewX  )                  | 设置字体水平倾斜度。<br>  普通斜体字设置为-0.25，往右倾斜 |
| setTypeface ( Typeface   typeface )            | 设置字体样式                                              |
| setLinearText ( boolean   linearText )         | 设置是否打开线性文本标识<br>（具体含义见书222页）         |
| setSubpixelText ( boolean   subpixelText )     | 是否打开亚像素设置来绘制文本<br>（具体含义见书222页）     |