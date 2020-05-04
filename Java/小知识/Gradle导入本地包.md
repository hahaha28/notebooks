# Gradle导入本地包

1. 建立一个 `lib` 文件夹，和 `src` 平级

    ![img](http://img.inaction.fun/static/36379.png)

2. 打开 `project structure` 

    ```note
    Moudles -> main -> Dependencies -> + -> jar or directories
    ```

    ![img](http://img.inaction.fun/static/84142.png)

3. 在 `build.gradle`

    ```groovy
    dependencies {
        ....
        complie file('lib/XXXX.jar')
        ....
    }
    ```

    