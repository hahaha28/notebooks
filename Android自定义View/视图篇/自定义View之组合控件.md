# 自定义View之组合控件

1. 新建布局文件

   把想要封装的控件弄好

2. 新建一个类，继承`FrameLayout`

   > 不是必须用 FrameLayout ，就是用这个简单

3. 把布局文件填充到`FrameLayout`中

   ```java
   public class MyEditText extends FrameLayout {
   
       public MyEditText(@NonNull Context context) {
           super(context);
       }
   
       public MyEditText(@NonNull Context context, @Nullable AttributeSet attrs) {
           super(context, attrs);
   
           View view = LayoutInflater.from(context).inflate(R.layout.my_edit_text,this);
   
       }
   
       public MyEditText(@NonNull Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
           super(context, attrs, defStyleAttr);
       }
   
   
   }
   ```

4. 设置控件的一些属性

   ```java
   public class MyEditText extends FrameLayout {
   
       private EditText editText;
   
       public MyEditText(@NonNull Context context) {
           super(context);
       }
   
       public MyEditText(@NonNull Context context, @Nullable AttributeSet attrs) {
           super(context, attrs);
   
           View view = LayoutInflater.from(context).inflate(R.layout.my_edit_text,this);
   
           editText = view.findViewById(R.id.editText);
           ImageView cancelButton = view.findViewById(R.id.imageView2);
   
   
   
           // 输入文字，取消按钮出现，没有文字，取消按钮消失
           editText.addTextChangedListener(new TextWatcher() {
               @Override
               public void beforeTextChanged(CharSequence s, int start, int count, int after) {
   
               }
   
               @Override
               public void onTextChanged(CharSequence s, int start, int before, int count) {
                   if(s.length() == 0){
                       // 没有文字，x 消失
                       cancelButton.setVisibility(INVISIBLE);
                   }else{
                       // 有文字，x 出现
                       cancelButton.setVisibility(VISIBLE);
                   }
               }
   
               @Override
               public void afterTextChanged(Editable s) {
   
               }
           });
   
           // 点击取消按钮，清空文字
           cancelButton.setOnClickListener(new OnClickListener() {
               @Override
               public void onClick(View v) {
                   editText.setText("");
               }
           });
       }
   
       public MyEditText(@NonNull Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
           super(context, attrs, defStyleAttr);
       }
   
       // 获取EditText
       public EditText getEditTextView(){
           return editText;
       }
   
       // 获取EditText里面的字符串
       public String getText(){
           return editText.getText().toString();
       }
   
   }
   ```

   