# Chrome设置跨域访问

1. 新建一个文件夹用来保存用户数据

   例如：`E:\chromeDevData`

2. 在快捷方式的属性页面中的目标中加上：

   `--disable-web-security --user-data-dir=E:\chromeDevData`

   其中最后的路径是保存数据的文件夹

   <img src="http://img.inaction.fun/static/74594.png" alt="image-20200630161554941" style="zoom: 50%;" />

3. 用这个快捷方式打开html文件，会出现如下标记

   ![image-20200630161737023](http://img.inaction.fun/static/34116.png)