# 长连接 read() 阻塞问题

> [参考文章](https://blog.csdn.net/totxian/article/details/48718413?locationNum=10)

使用TCP进行长连接传输文件，我先发送了文件相关的信息，包括文件大小等。然后发送文件的字节数据。

在另一端接收时，我首先接收文件相关的信息，然后根据文件大小读取指定字节数（使用`read(bytes)`方法）。结果发现，`read()`竟然阻塞了，而如果我关闭`socket`连接，就不会有问题。

百度了很久，才发现是因为我使用了`BufferedInputStream`来读取文件相关信息，因为这个自带缓存，所以它把后面的文件字节也读取了，所以`read()`读取不到就阻塞了。

解决办法，不使用`BufferedInputStream`就行了。