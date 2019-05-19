### 一、增加时间戳

![](https://s2.ax1x.com/2019/05/15/E7YRNq.png)

##### 1）增加修改时间的列选项
```
 private static final String[] PROJECTION =
        new String[] {
            NotePad.Notes._ID,
            NotePad.Notes.COLUMN_NAME_TITLE,
            NotePad.Notes.COLUMN_NAME_NOTE,
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,
            NotePad.Notes.COLUMN_NAME_BACK_COLOR
    };
```
##### 2）在editnote的updatenote方法设置时间格式
```
 //修改时间
        SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd  HH:mm:ss");
        values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, sf.format(new Date()));
        values.put(NotePad.Notes.COLUMN_NAME_CREATE_DATE,sf.format(new Date()));
```

##### 3）NoteList上修改dataColumns 
```
 final String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE} ;
```
##### 4）ListView的item布局
```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="80dp"
    android:id="@+id/color_item"
    >
    <ImageView
        android:id="@+id/imageView"
        android:layout_width="80dp"
        android:layout_height="80dp"
        android:background="@drawable/notepad_listitem"/>

    <TextView
        android:id="@android:id/text1"
        android:layout_width="match_parent"
        android:layout_height="?android:attr/listPreferredItemHeight"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:paddingLeft="5dip"
        android:singleLine="true"
        android:textSize="22dp"
        android:layout_toRightOf="@id/imageView"
        android:paddingTop="10dp"
    />

    <TextView
        android:id="@+id/txt2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="45dp"
        android:textSize="22dp"
        android:layout_toRightOf="@id/imageView"
        />
</RelativeLayout>
```
##### 5）NoteList上修改viewIDs 
```
final int[] viewIDs = { android.R.id.text1 ,R.id.txt2};
```
### 二、搜索功能

![](https://s2.ax1x.com/2019/05/15/E7VUkd.gif)

##### 1)在主界面增加一个EditText控件，用于搜索功能
```
 <EditText
        android:padding="10dp"
        android:id="@+id/edt"
        android:layout_gravity="center_vertical"
        android:layout_margin="6dp"
        android:drawableLeft="@drawable/search_icon"
        android:drawablePadding="5dp"
        android:layout_width="match_parent"
        android:layout_height="45dp"
        android:textColor="#000"
        android:background="@drawable/search_shape"
        android:textSize="16sp"
        android:hint="请输入关键字"/>
```

##### 2)设置搜索框样式
 ```
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <stroke android:width="2dp" android:color="@color/grey"/>
    <corners android:radius="25sp"/>
    <solid android:color="@color/white"/>
</shape>
```
##### 3)添加EditText的文本改变的监听器来模糊搜索note
```
  editText.addTextChangedListener(new TextWatcher() {
            private String search_word;
            @Override
            public void beforeTextChanged(CharSequence charSequence, int i, int i1, int i2) {

            }

            @Override
            public void onTextChanged(CharSequence charSequence, int i, int i1, int i2) {

            }

            @Override
            public void afterTextChanged(Editable editable) {
                search_word = editText.getText().toString();

                Cursor cursor1 = managedQuery(
                        getIntent().getData(),
                        PROJECTION,
                        "title like '%" + search_word + "%'",
                        null,
                        NotePad.Notes.DEFAULT_SORT_ORDER
                );

                SimpleCursorAdapter adapter1 = new SimpleCursorAdapter(
                        NotesList.this,
                        R.layout.noteslist_item,
                        cursor1,
                        dataColumns,
                        viewIDs
                );
                listView.setAdapter(adapter1);
            }
        });
```


### 三、语音识别

![](https://s2.ax1x.com/2019/05/15/E7tEPP.gif)

##### 1)调用讯飞语音接口，添加资源和lib库

##### 2）添加权限
```
 <!-- 连接网络权限 -->
    <uses-permission android:name="android.permission.INTERNET" />
    <!-- 获取手机录音机使用权限，听写、识别、语义理解需要用到此权限 -->
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <!-- 读取网络信息状态 -->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <!-- 获取当前wifi状态 -->
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <!-- 允许程序改变网络连接状态 -->
    <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
    <!-- 读取手机状态权限 -->
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <!-- 读取联系人权限 -->
    <uses-permission android:name="android.permission.READ_CONTACTS" />
    <!-- 外存储写权限 -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <!-- 外存储读权限， -->
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <!-- 配置权限，用来记录应用配置信息 -->
    <uses-permission android:name="android.permission.WRITE_SETTINGS"/>
    <!-- 在SD卡中创建与删除文件权限 -->
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"
        />
```
##### 3)布局
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <EditText
        class="com.example.android.notepad.NoteEditor$LinedEditText"
        android:id="@+id/note"
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:background="@android:color/transparent"
        android:padding="5dp"
        android:scrollbars="vertical"
        android:fadingEdge="vertical"
        android:gravity="top"
        android:textSize="22sp"
        android:capitalize="sentences"
    />
    <Button
        android:id="@+id/voice"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="@drawable/voice"
        android:layout_marginLeft="160dp"
        android:layout_marginTop="380dp"
        />
</LinearLayout>
```
##### 4)调用
```
 mText = (EditText) findViewById(R.id.note);
        voiceButton = (Button) findViewById(R.id.voice);
        initIatData(mText);
        voiceButton.setOnClickListener(this);
```

### 四、背景颜色更改

![](https://s2.ax1x.com/2019/05/15/E7tNMF.gif)

##### 1)添加颜色字段
```
public static final String COLUMN_NAME_BACK_COLOR = "color";


        public static final int DEFAULT_COLOR = 0; //白
        public static final int YELLOW_COLOR = 1; //黄
        public static final int BLUE_COLOR = 2; //蓝
        public static final int GREEN_COLOR = 3; //绿
        public static final int RED_COLOR = 4; //红
```
##### 2）将颜色填充到ListView
```
@Override
    public void bindView(View view, Context context, Cursor cursor){
        super.bindView(view, context, cursor);
        //从数据库中读取的cursor中获取笔记列表对应的颜色数据，并设置笔记颜色
        int x = cursor.getInt(cursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_BACK_COLOR));
        
        switch (x){
            case NotePad.Notes.DEFAULT_COLOR:
                view.setBackgroundColor(Color.rgb(255, 255, 255));
                break;
            case NotePad.Notes.YELLOW_COLOR:
                view.setBackgroundColor(Color.rgb(247, 216, 133));
                break;
            case NotePad.Notes.BLUE_COLOR:
                view.setBackgroundColor(Color.rgb(165, 202, 237));
                break;
            case NotePad.Notes.GREEN_COLOR:
                view.setBackgroundColor(Color.rgb(161, 214, 174));
                break;
            case NotePad.Notes.RED_COLOR:
                view.setBackgroundColor(Color.rgb(244, 149, 133));
                break;
            default:
                view.setBackgroundColor(Color.rgb(255, 255, 255));
                break;
        }
    }
```
##### 3)修改为可以填充颜色的自定义的adapter
```

adapter = new MyCursorAdapter(
                this,
                R.layout.noteslist_item,
                cursor,
                dataColumns,
                viewIDs
        );
```
##### 4)在菜单文件中添加一个更改背景的选项
```
<item android:id="@+id/menu_color"
        android:title="@string/menu_color"
        android:icon="@drawable/ic_menu_color"
        android:showAsAction="always"/>
```
##### 5)在NoteEditor中找到onOptionsItemSelected()
```
case R.id.menu_color:
        changeColor();
        break;
```
##### 6)在NoteEditor中添加函数changeColor()
```
private final void changeColor() {
        Intent intent = new Intent(null,mUri);
        intent.setClass(NoteEditor.this,NoteColor.class);
        NoteEditor.this.startActivity(intent);
    }
```

### 五、导出笔记

![](https://s2.ax1x.com/2019/05/15/E7tgMD.gif)
![](https://s2.ax1x.com/2019/05/15/E7tTRf.png)
![](https://s2.ax1x.com/2019/05/15/E7tqsg.png)

##### 1)在菜单文件中添加一个导出笔记的选项
```
<item android:id="@+id/menu_output"
    android:title="@string/menu_output" />
```
##### 2)导出
```
public class OutputText extends Activity {

    private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_NOTE, // 2
            NotePad.Notes.COLUMN_NAME_CREATE_DATE, // 3
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, // 4
            NotePad.Notes.COLUMN_NAME_BACK_COLOR, //5
    };

    private String TITLE;
    private String NOTE;
    private String CREATE_DATE;
    private String MODIFICATION_DATE;

    private Cursor mCursor;
    private EditText mName;
    private Uri mUri;

    private boolean flag = false;

    private static final int COLUMN_INDEX_TITLE = 1;

    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.output_text);

        mUri = getIntent().getData();
        mCursor = managedQuery(
                mUri,        // The URI for the note that is to be retrieved.
                PROJECTION,  // The columns to retrieve
                null,        // No selection criteria are used, so no where columns are needed.
                null,        // No where columns are used, so no where values are needed.
                null         // No sort order is needed.
        );

        mName = (EditText) findViewById(R.id.output_name);
    }


    @Override
    protected void onResume(){
        super.onResume();
        if (mCursor != null) {
            // The Cursor was just retrieved, so its index is set to one record *before* the first
            // record retrieved. This moves it to the first record.
            mCursor.moveToFirst();
            // Displays the current title text in the EditText object.
            mName.setText(mCursor.getString(COLUMN_INDEX_TITLE));
        }
    }

    @Override
    protected void onPause() {
        super.onPause();
        if (mCursor != null) {
            TITLE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_TITLE));
            NOTE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_NOTE));
            CREATE_DATE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_CREATE_DATE));
            MODIFICATION_DATE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE));
            if (flag == true) {
                write();
            }
            flag = false;
        }
    }


    public void OutputOk(View v){
        flag = true;
        finish();
    }


    private void write()
    {
        try
        {
            // 如果手机插入了SD卡，而且应用程序具有访问SD的权限
            if (Environment.getExternalStorageState().equals(
                    Environment.MEDIA_MOUNTED)) {
                // 获取SD卡的目录
                File sdCardDir = Environment.getExternalStorageDirectory();
                //创建文件目录
                File targetFile = new File(sdCardDir.getCanonicalPath() + "/" + mName.getText() + ".txt");
                //写文件
                PrintWriter ps = new PrintWriter(new OutputStreamWriter(new FileOutputStream(targetFile), "UTF-8"));
                ps.println(TITLE);
                ps.println(NOTE);
                ps.println("创建时间：" + CREATE_DATE);
                ps.println("最后一次修改时间：" + MODIFICATION_DATE);
                ps.close();
                Toast.makeText(this, "保存成功,保存位置：" + sdCardDir.getCanonicalPath() + "/" + mName.getText() + ".txt", Toast.LENGTH_LONG).show();
            }
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
    }
}
```

##### 3)在AndroidManifest.xml修改
```
<activity android:name="OutputText"
            android:label="@string/output_name"
            android:theme="@android:style/Theme.Holo.Dialog"
            android:windowSoftInputMode="stateVisible">
        </activity>
```
```
<!-- 在SD卡中创建与删除文件权限 -->
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"
        />
    <!-- 向SD卡写入数据权限 -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
