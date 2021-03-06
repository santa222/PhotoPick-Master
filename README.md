# PhotoPick-Master
我把项目中做的图库抽了出来，本项目中包括了以下内容：1、图库选图，可多选，单选，可选完图片后进行裁剪，2、查看大图(包括sd卡，res，网络图)，并可以保存到手机图库,3、适配沉浸式状态栏 ,4 、使用fresco加载图片(图片是随便网上找的，性感吧- -!)

下面来看几张效果图：

![image](https://github.com/Awent/PhotoPick-Master/blob/master/pictrue/304079-052c8fd0c9d22efd.gif)

![image](https://github.com/Awent/PhotoPick-Master/blob/master/pictrue/304079-8d726553c6c0b6ba.gif)

![image](https://github.com/Awent/PhotoPick-Master/blob/master/pictrue/304079-e4c819f695ed83c0.gif)

你可以先[下载apk](https://github.com/Awent/PhotoPick-Master/blob/master/simaple/simaple-release.apk)运行看下


![tips.png](http://upload-images.jianshu.io/upload_images/304079-2344f7ec6b950a05.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

项目我已经挂载在[github](https://github.com/Awent/PhotoPick-Master)，如果有需要的朋友，欢迎fork，项目有什么issue可以在这里留言，也可以[点击这里](https://github.com/Awent/PhotoPick-Master/issues)进行commit。希望能帮到你。

##下面讲解如何引入到你的项目中

1、在你的root gradle(也就是项目最外层的gradle)添加如下代码

```

allprojects {
    repositories {
        jcenter()
        maven { url 'https://jitpack.io' }
    }
}

```

2、然后在module gradle dependencies 添加以下代码

```
compile 'com.github.Awent:PhotoPick-Master:v1.05'

```

3、然后把以下代码复制到你的AndroidManifest.xml文件的application节点里

```
        <activity
            android:theme="@style/PhoAppTheme.AppTheme"
            android:name="com.awen.photo.photopick.ui.ClipPictureActivity" />
        <activity
            android:theme="@style/PhoAppTheme.AppTheme"
            android:name="com.awen.photo.photopick.ui.PhotoPagerActivity" />
        <activity
            android:theme="@style/PhoAppTheme.AppTheme"
            android:name="com.awen.photo.photopick.ui.PhotoPickActivity" />
        <activity
            android:theme="@style/PhoAppTheme.AppTheme"
            android:name="com.awen.photo.photopick.ui.PhotoPreviewActivity" />

```

4、然后在你的Application的onCreate()方法里初始化即可使用

```
public class App extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        Awen.init(this);
        //下面是配置toolbar颜色和存储图片地址的
//        Awen.init(this,android.R.color.holo_blue_light);
//        Awen.init(this,android.R.color.holo_blue_light,"/storage/xxxx/xxx");
    }
}

```

##下面讲解如何使用
- Hao to use(可进行动态设置参数，不用的可以不设置)

- 图库

```
new PhotoPickConfig.Builder(this)
    .pickMode(PhotoPickConfig.MODE_MULTIP_PICK) //多选，这里有单选和多选
    .maxPickSize(15)                            //最多可选15张
    .showCamera(false)                          //是否展示拍照icon,默认展示
    .clipPhoto(true)                            //是否选完图片进行图片裁剪，默认是false,如果这里设置成true,就算设置了是多选也会被配置成单选
    .spanCount(4)                               //图库的列数，默认3列，这个数建议不要太大
    .build();

```

- 获取图库选择了(或裁剪好)的图片地址

```
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode != Activity.RESULT_OK || data == null) {
        return;
    }
    switch (requestCode) {
        case PhotoPickConfig.PICK_REQUEST_CODE:
            //返回的图片地址集合
            ArrayList<String> photoLists = data.getStringArrayListExtra(PhotoPickConfig.EXTRA_STRING_ARRAYLIST);
            if (photoLists != null && !photoLists.isEmpty()) {
                File file = new File(photoLists.get(0));//获取第一张图片
                if (file.exists()) {
                    //you can do something

                } else {
                    //toast error
                }
            }
            break;
    }
}

```

- 查看大图

```
new PhotoPagerConfig.Builder(this)
    .setImageUrls(ImageProvider.getImageUrls()) //图片url,可以是sd卡res，asset，网络图片.
    .setSavaImage(true)                         //开启保存图片，默认false
    .setPosition(2)                             //默认展示第2张图片
    .setSaveImageLocalPath("Android/SD/xxx/xxx")//这里是你想保存大图片到手机的地址，不传会有默认地址
    .build();

```

- ToolBar沉浸式

```
1、记得style.xml中设置:<style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
2、默认开启ToolBar,如果不需要开启的在setContentView(int)之前调用这个:setOpenToolBar(false)，
3、注意，如果不开启ToolBar,不要调用这里的toolBar对象

```

### v1.0.1
2016-9-04 PM-17：00
P:最近开发意识到之前的代码设计有点龊，改起来有点麻烦，所以现在换了一种方法，在javabean里进行参数的配置，这样修改更加方便清晰，注意如果项目打包的时候用到混淆，记得这里的javabean不要进行混淆，因为这里的javabean实现了Parcelable接口，详情可见代码中的['proguard-rules.pro'](https://github.com/Awent/PhotoPick-Master/blob/master/photoLibrary/proguard-rules.pro)文件。另外，还新增两点内容：1、android6.0权限适配，2、大图展示前先展示小图。详情可见项目代码，我已更新到github.

### v1.1
2017-01-10 PM-19:00
新增图片下载展示圆形进度条和下载百分比，新增仿微信图片预览，gif播放，并可保存gif到本地图库

### v1.2
2017-02-17 AM-00:06
把项目抽成Library形式，更加方便引入到你的项目中去

### v1.3
2017-02-19 PM-10：00
修复清单文件aplication节点中lable被替换的bug
请更新到最新library

### v1.31
2017-04-10 PM-17：00
1、gradle更新到3.4
2、修复查看大图，单张图片添加bug
方法：addSingleBigImageUrl(String bigImageUrl) 和 addSingleLowImageUrl(String lowImageUrl)
请更新到最新library




