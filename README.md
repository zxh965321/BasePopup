BasePopup（v2）
-------------
**这是一个快速实现PopupWindow的基类，本基类易于扩展，并且几乎没有使用限制，便于您快速实现各种各样的PopupWindow。**


| **Release** | **Candy** | **License** | **Api** | **Author** |
| ---- | ---- | ---- | ---- | ---- |
| [![Download](https://api.bintray.com/packages/razerdp/maven/BasePopup/images/download.svg) ](https://bintray.com/razerdp/maven/BasePopup/_latestVersion) | [![Download](https://api.bintray.com/packages/razerdp/maven/BasePopup_Candy/images/download.svg) ](https://bintray.com/razerdp/maven/BasePopup_Candy/_latestVersion)| [![license](https://img.shields.io/badge/license-Apache--2.0-blue.svg )](https://github.com/razerdp/BasePopup/blob/master/LICENSE) | [![Api](https://img.shields.io/badge/Api-14%2B-green.svg)](https://img.shields.io/badge/Api-14%2B-green.svg) | [![Author](https://img.shields.io/badge/Author-razerdp-blue.svg)](https://github.com/razerdp) |


注意事项
----

Android P 未进行适配！！！因目前Native绕过API限制方法尚未稳定，且不知道官方是否会有进一步限制，因此暂时不进行P的适配。

如果您用到本库并且考虑到Android P用户，请慎重！！！

---

**请务必查看更新日志和例子预览，里面会详细解释每个版本增加或修复的功能**

**请注意引用版本的问题，Release版本是稳定版，可商用。**

**Candy不稳定（且更新很频繁），但包含着新功能或者新的优化，不建议商用。**

[**v1.x READ_ME**](https://github.com/razerdp/BasePopup/blob/master/README_OLD.md)

[**1.x迁移到2.x帮助文档**](https://github.com/razerdp/BasePopup/blob/master/1.x%E8%BF%81%E7%A7%BB2.x%E5%B8%AE%E5%8A%A9%E6%96%87%E6%A1%A3.md)

[**更新日志**](https://github.com/razerdp/BasePopup#%E6%9B%B4%E6%96%B0%E6%97%A5%E5%BF%97%E5%8E%86%E5%8F%B2%E6%9B%B4%E6%96%B0)

[**例子预览**](https://github.com/razerdp/BasePopup#%E4%B8%80%E4%BA%9B%E4%BE%8B%E5%AD%90)


依赖
---

| **Release** | **Candy** |
| ---- | ---- |
| [![Download](https://api.bintray.com/packages/razerdp/maven/BasePopup/images/download.svg) ](https://bintray.com/razerdp/maven/BasePopup/_latestVersion) | [![Download](https://api.bintray.com/packages/razerdp/maven/BasePopup_Candy/images/download.svg) ](https://bintray.com/razerdp/maven/BasePopup_Candy/_latestVersion) |


添加依赖（请把{latestVersion}替换成上面的Jcenter标签所示版本

**【candy版本不一定稳定，包含有新功能或者新的修复，完善后将会发布其release版】**

```xml
	dependencies {
	        implementation 'com.github.razerdp:BasePopup:{latestVersion}'
	        
	        //candy版本，不稳定，但会带有新功能
	        //implementation 'com.github.razerdp:BasePopup_Candy:{latestVersion}'
	}
```


使用方法
----

### 方法一
----

* **Step 1:**

像您平时定制activity布局文件一样定制您的popup布局

etc.
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >
    
    <RelativeLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@drawable/bg_dialog"
        android:layout_centerInParent="true"
        android:layout_margin="25dp">
        
        
        <... many views>
        
        

    </RelativeLayout>
</RelativeLayout>
```
![image](https://github.com/razerdp/BasePopup/blob/master/img/etc.png)


* **Step 2:**

新建一个类继承BasePopupWindow

* **Step 3:**

实现必要的几个方法：

`onCreateShowAnimation()`/`onCreateDismissAnimation()`:初始化一个显示/退出动画，该动画将会用到`onCreatePopupView()`所返回的view,可以为空。

`onCreatePopupView()`:初始化您的popupwindow界面，建议直接使用`createPopupById()`


例如

```java
public class DialogPopup extends BasePopupWindow implements View.OnClickListener{

    private TextView ok;
    private TextView cancel;

    public DialogPopup(Activity context) {
        super(context);

        ok= (TextView) findViewById(R.id.ok);
        cancel= (TextView) findViewById(R.id.cancel);

        setViewClickListener(this,ok,cancel);
    }

    @Override
    protected Animation onCreateShowAnimation() {
        AnimationSet set=new AnimationSet(false);
        Animation shakeAnima=new RotateAnimation(0,15,Animation.RELATIVE_TO_SELF,0.5f,Animation.RELATIVE_TO_SELF,0.5f);
        shakeAnima.setInterpolator(new CycleInterpolator(5));
        shakeAnima.setDuration(400);
        set.addAnimation(getDefaultAlphaAnimation());
        set.addAnimation(shakeAnima);
        return set;
    }

    @Override
    protected Animation onCreateDismissAnimation() {
        return null;
    }

    @Override
    public View onCreateContentView() {
        return createPopupById(R.layout.popup_dialog);
    }

    @Override
    public void onClick(View v) {
        //... click event
    }
}
```

* **Step 4:**

把您刚才实现的popup给new出来并调用show方法

例如

```java
    DialogPopup popup = new DialogPopup(context);
    popup.showPopupWindow();
```

----

### 方法二
----
如果您并不需要很详细的定义一个PopupWindow，您也可以选择`QuickPopupBuilder`采取链式写法快速编写出一个Popup以使用。

```java
    QuickPopupBuilder.with(getContext())
                     .contentView(R.layout.popup_menu_small)
                     .wrapContentMode()
                     .config(new QuickPopupConfig()
                                .withShowAnimation(enterAnimation)
                                .withDismissAnimation(dismissAnimation)
                                .offsetX(offsetX, offsetRatioOfPopupWidth)
                                .offsetY(offsetY, offsetRatioOfPopupHeight)
                                .blurBackground(true, new BasePopupWindow.OnBlurOptionInitListener() {
                                    @Override
                                    public void onCreateBlurOption(PopupBlurOption option) {
                                        option.setBlurRadius(6)
                                                .setBlurPreScaleRatio(0.9f);
                                    }
                                })
                                .withClick(R.id.tx_1, new View.OnClickListener() {
                                    @Override
                                    public void onClick(View v) {
                                        ToastUtils.ToastMessage(getContext(), "tx1");
                                    }
                                }))
                     .show(v);
````





**ps:从1.9.0-alpha开始支持背景模糊（只需要一个方法：`setBlurBackgroundEnable()`）**

**RenderScript最低支持api 17（更低的情况将会使用fastblur），您需要在gradle配置一下代码**

```xml
defaultConfig {
        renderscriptTargetApi 25
        renderscriptSupportModeEnabled true
    }
```

方法介绍：
---

请看wiki（陆续完善中）

**Link👉**[WIKI](https://github.com/razerdp/BasePopup/wiki)

交流群：590777418
---

因为目前还有朋友圈项目，建立了一个交流群，出于懒得管理那么多，所以如果有想法或者优化建议或者其他问题，欢迎加入“朋友圈交流群”

![](https://github.com/razerdp/FriendCircle/blob/master/qqgroup.png)

打赏（看在我那么努力维护的份上。。。给个零食呗~）
---

| 微信 |支付宝 | 
| ---- | ---- | 
| ![](https://github.com/razerdp/FriendCircle/blob/master/wechat.png)      | ![](https://github.com/razerdp/FriendCircle/blob/master/alipay.png) |




更新日志([历史更新](https://github.com/razerdp/BasePopup/blob/master/UpdateLog.md))
---------------------------------------------------------------------------

* 2018/09/19
  * 继续丰富QuickPopupBuilder
  * 增加Demo:根据某个View控制各个方向的Popup。[示例](https://github.com/razerdp/BasePopup/blob/HEAD/app/src/main/java/razerdp/demo/fragment/LocatePopupFrag.java)
  
* **2.0.1-alpha2**(2018/09/06)
  * 增加快速构建QuickPopupBuilder
  * 尝试修复[#59](https://github.com/razerdp/BasePopup/issues/59)
  
* **2.0.1-alpha1** (2018/08/22)
  * 修复无法在onCreate()里面显示的问题
  * 增加setBackground(Drawable/ResourceId)方法，fixed [#79](https://github.com/razerdp/BasePopup/issues/79)
  * 正式版即将发布，。

* **2.0.0-alpha1(candy)**
  * 发布预览v2版本


一些例子
---

| 对应popup | 预览 |
| :---- | ---- |
| [LocatePopupFrag.java](https://github.com/razerdp/BasePopup/blob/master/app/src/main/java/razerdp/demo/fragment/LocatePopupFrag.java)     | ![image](https://github.com/razerdp/BasePopup/blob/master/img/locate_popup.gif) |
| [BlurSlideFromBottomPopup.java](https://github.com/razerdp/BasePopup/blob/master/app/src/main/java/razerdp/demo/popup/BlurSlideFromBottomPopup.java)     | ![image](https://github.com/razerdp/BasePopup/blob/master/img/blur_popup.gif) |
| [CommentPopup.java](https://github.com/razerdp/BasePopup/blob/master/app/src/main/java/razerdp/demo/popup/CommentPopup.java)     | ![image](https://github.com/razerdp/BasePopup/blob/master/img/comment_popup_with_exitAnima.gif) |
| [ScalePopup.java](https://github.com/razerdp/BasePopup/blob/master/app/src/main/java/razerdp/demo/popup/ScalePopup.java)     | ![image](https://github.com/razerdp/BasePopup/blob/master/img/scale_popup.gif) |
| [SlideFromBottomPopup.java](https://github.com/razerdp/BasePopup/blob/master/app/src/main/java/razerdp/demo/popup/SlideFromBottomPopup.java)     | ![image](https://github.com/razerdp/BasePopup/blob/master/img/slide_from_bottom_popup.gif) |
| [InputPopup.java](https://github.com/razerdp/BasePopup/blob/master/app/src/main/java/razerdp/demo/popup/InputPopup.java)     | ![image](https://github.com/razerdp/BasePopup/blob/master/img/input_popup.gif) |
| [ListPopup.java](https://github.com/razerdp/BasePopup/blob/master/app/src/main/java/razerdp/demo/popup/ListPopup.java)     | ![image](https://github.com/razerdp/BasePopup/blob/master/img/list_popup.gif) |
| [MenuPopup.java](https://github.com/razerdp/BasePopup/blob/master/app/src/main/java/razerdp/demo/popup/MenuPopup.java)     | ![image](https://github.com/razerdp/BasePopup/blob/master/img/menu_popup.gif) |


License
---

Apache-2.0
