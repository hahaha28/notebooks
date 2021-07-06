# Creating a content provider

1. 创建一个类继承`ContentProvider`，实现方法

   ```java
   public class MyContentProvider extends ContentProvider {
       public MyContentProvider() {
       }
   
       @Override
       public boolean onCreate() {
       }
   
       @Override
       public int delete(Uri uri, String selection, String[] selectionArgs) {
       }
   
       @Override
       public Uri insert(Uri uri, ContentValues values) {
       }
   
       @Override
       public Cursor query(Uri uri, String[] projection, String selection,
                           String[] selectionArgs, String sortOrder) {
       }
   
       @Override
       public int update(Uri uri, ContentValues values, String selection,
                         String[] selectionArgs) {
       }
   
       @Override
       public String getType(Uri uri) {
       }
   
   }
   ```

2. 在清单文件注册

   ```xml
   <provider
             android:name=".MyContentProvider"
             android:authorities="com.example.ssssss"
             android:enabled="true"
             android:exported="true"></provider>
   ```

   