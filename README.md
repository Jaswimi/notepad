(由于图片先上传到CSDN所以带有水印）
### 代码修改展示
在NotePadProvider里面可以看到创建数据库有一列COLUMN_NAME_MODIFICATION_DATE，存储笔记最近更新时间
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210607200240603.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80OTc2OTA2NQ==,size_16,color_FFFFFF,t_70)

找到这个字段之后就可以在适配器里添加时间戳，适配器会从数据库里面找到COLUMN_NAME_MODIFICATION_DATE的值并将它传递给ListView，再由ListView显示出来
notepad里面采用的是SimpleCursorAdapter和ListView的结合，SimpleCursorAdapter的构造参数为
```java
SimpleCursorAdapter adapter
            = new SimpleCursorAdapter(
                      this,                             // The Context for the ListView
                      R.layout.noteslist_item,          // list中一栏的布局
                      cursor,                           //cursor存放数据库的数据
                      dataColumns,	//数据库的列名
                      viewIDs		//对应数据库的列应该对应的展示View的id
              );
```
要更改显示内容就要先更改适配器
- 首先更改第二个参数所指向的布局文件notelist_item.xml
添加一个TextView用来显示时间戳
```html
 <TextView
        android:id="@android:id/text2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:gravity="center_vertical"
        android:paddingLeft="5dp"
        android:singleLine="true" />  `

```
- 接着更改第二个参数cursor,cursor的定义如下：
```java
 Cursor cursor = managedQuery(    
            getIntent().getData(),            // Use the default content URI for the provider.
            PROJECTION,                       // 用于标识有哪些columns需要包含在返回数据中
            null,                             // 作为查询符合条件的过滤参数，类似于SQL语句中Where之后的条件判断。
            null,                             //同上
            NotePad.Notes.DEFAULT_SORT_ORDER  // 用于对返回信息进行排序
        );
```
 找到PROJECTION,添加列COLUMN_NAME_MODIFICATION_DATE
```java
 private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,//在这里加入了修改时间的显示
    };
    
```

- 更改第三个参数指向的数组dataColumns,添加列名COLUMN_NAME_MODIFICATION_DATE
```java
String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE,
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE}; 
```
- 更改第四个参数指向的viewID数组,把刚才在布局文件中加入的text2添加进数组，这样就将text2和数据库取出来的时间戳绑定
```java
int[] viewIDs = { android.R.id.text1 ,android.R.id.text2};
```
最后修改NoteEditor里的updateNote方法，在ContentValue里面添加时间戳的键值对
```java
	ContentValues values = new ContentValues();
    // 转化时间格式
    Long now = Long.valueOf(System.currentTimeMillis());
    SimpleDateFormat sf = new SimpleDateFormat("yy/MM/dd HH:mm");
    Date d = new Date(now);
    String format = sf.format(d);
    values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, format);
```
### 基本笔记页面及时间戳

 #### 主页

这是刚进入笔记页面的样子，可以看到主页的每一栏的第一行展示了笔记的标题，第二行展示编辑的时间，右上角有新建笔记的图标

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210524094829615.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80OTc2OTA2NQ==,size_16,color_FFFFFF,t_70)

 #### 编辑页面
右上角有保存笔记和删除笔记的图标

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210524095048342.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80OTc2OTA2NQ==,size_16,color_FFFFFF,t_70)
### 基本操作

 #### 新建笔记

点击右上角的小图标可以新建笔记

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210524100020520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80OTc2OTA2NQ==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210524095048342.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80OTc2OTA2NQ==,size_16,color_FFFFFF,t_70)
 #### 保存笔记

点击编辑页面右上角的保存图标保存回到主页面，可以看到新增了一栏

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210524095127240.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80OTc2OTA2NQ==,size_16,color_FFFFFF,t_70)

 #### 修改笔记
点击每一栏可以进入这一篇笔记，编辑并保存

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210524095324138.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80OTc2OTA2NQ==,size_16,color_FFFFFF,t_70)

#### 删除笔记
也可以选择右上角的删除图标删除这篇笔记
