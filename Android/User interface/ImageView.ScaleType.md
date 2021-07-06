# ImageView.ScaleType

> ImageView 背景为蓝色
>
> 兔子图片大小大于 ImageView 大小
>
> 人像图片大小小于ImageView大小

### fitCenter（默认）

把图片居中，如果图片的宽或高超过或小于`ImageView`，则图片会按比例缩放宽高直到能放进ImageView为止

<img src="http://img.inaction.fun/static/28532.png" alt="image-20210331105630551" style="zoom: 25%;" />

<img src="http://img.inaction.fun/static/14680.png" alt="image-20210331105802384" style="zoom:25%;" />

<img src="http://img.inaction.fun/static/56469.png" alt="image-20210331130218593" style="zoom:25%;" />

### center

把图片居中，但不会对图片做任何缩放，如果图片大于ImageView大小，只能显示部分

<img src="http://img.inaction.fun/static/58371.png" alt="image-20210331110751964" style="zoom:25%;" />

<img src="http://img.inaction.fun/static/69285.png" alt="image-20210331110814562" style="zoom:25%;" />

### centerCrop

按图片原始比例缩放图片，直到`ImageView`完全被图片充满（图片大小将会 >= `ImageView`大小）

<img src="http://img.inaction.fun/static/61913.png" alt="image-20210331130703937" style="zoom:25%;" />

<img src="http://img.inaction.fun/static/83764.png" alt="image-20210331130729428" style="zoom:25%;" />

### centerInside

把图片居中

* 如果图片大小小于`ImageView`，则不缩放
* 如果图片大小大于`ImageView`，则按图片原始比例缩放到能放入`ImageView`为止

<img src="http://img.inaction.fun/static/38230.png" alt="image-20210331131624762" style="zoom:33%;" />

<img src="http://img.inaction.fun/static/99396.png" alt="image-20210331131643646" style="zoom:25%;" />

### fitXY

把图片大小缩放到和`ImageView`宽高相等，会改变图片的宽高比例

<img src="http://img.inaction.fun/static/21278.png" alt="image-20210331131933292" style="zoom:25%;" />![image-20210331131951532](http://img.inaction.fun/static/28437.png)

![image-20210331131951532](http://img.inaction.fun/static/28437.png)

### fitStart

缩放规则和`fitCenter`相同，但是图片不是居中，而是放在左侧（如果手机文字是从左到右）

### fitEnd

缩放规则和`fitCenter`相同，但是图片不是居中，而是放在右侧（如果手机文字是从左到右）