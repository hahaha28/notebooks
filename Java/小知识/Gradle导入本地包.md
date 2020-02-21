# Gradle导入本地包

1. 建立一个 `lib` 文件夹，和 `src` 平级

    ![img](https://github.com/hahaha28/photos/raw/master/notebooks/Java/Gradle的lib目录位置.png)

2. 打开 `project structure` 

    ```note
    Moudles -> main -> Dependencies -> + -> jar or directories
    ```

    ![img](https://github.com/hahaha28/photos/raw/master/notebooks/Java/Gradle添加依赖包.png)

3. 在 `build.gradle`

    ```groovy
    dependencies {
        ....
        complie file('lib/XXXX.jar')
        ....
    }
    ```

    