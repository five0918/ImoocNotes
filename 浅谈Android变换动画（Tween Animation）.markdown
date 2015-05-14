# 浅谈Android变换动画（Tween Animation）

标签（空格分隔）： Android 动画 慕课网

[TOC]

---
##一.四种常用动画

- Alpha ：渐变透明度动画
- Scale ：渐变尺寸缩放动画
- Translate :位置移动动画
- Rotate :旋转动画

##二.Tween Animation共同属性

- Duration:动画持续时间(单位：毫秒)
- fillAfter:设置为true，动画转化在动画结束后被应用
- fillBefore:设置为true，动画转换在动画开始前被应用
- interpolator:动画插入其(加速、减速插入器)
- repateMode:顺序重复/倒序重复
- startOffset:动画之间的时间间隔

##三.Animation实现方式 
1. 配置文件(/res/anim)——alpha、scale、translate、rotate

示例代码：
```java
Animation scale = AnimationUtils.loadAnimation(TweenActivity.this,R.anim.scale_anim);
//开始动画
img.startAnimation(scale)
```
2. Java代码实现——AlphaAnimation、ScaleAnimation、TranslateAnimation、RotateAnimation

示例代码：
```java
    //创建Alpha动画
    Animation alpha=new AlphaAnimation(0.1f,1.0f);
    //设置动画时间为5秒
    alpha.setDuration(5000);
    //开始播放
    img.startAnimation(alpha);
```
##四.四种基础动画实现

###1.AlphaAnimation（透明度动画）
- fromAlpha：动画起始时透明度
- toAlpha ：动画终止时透明度

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <alpha
        android:duration="1000"
        android:fromAlpha="0.1"
        android:toAlpha="1.0">
    </alpha>
</set>
```

0.0 表示完全透明
1.0表示完全不透明
###2.ScaleAnimation(缩放动画)
- fromX,toX分别是起始和结束时x坐标上的伸缩尺寸
- fromY,toY分别是起始和结束时y坐标上的伸缩尺寸
- pivoX,piovY分别为伸缩动画相对于x,y坐标开始的位置

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <scale
        android:duration="2000"
        android:fillAfter="false"
        android:fromXScale="0.0"
        android:fromYScale="0.0"
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"
        android:pivotX="50%"
        android:pivotY="50%"
        android:toXScale="1.0"
        android:toYScale="1.0"/>
</set>
```
###3.TranslateAnimation(位移动画)
- fromXDeleta,fromYDelta分别是X、Y的坐标
- toXDelta,toYDelta分别是结束时X、Y的坐标

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <translate
        android:duration="3000"
        android:fromXDelta="10"
        android:fromYDelta="10"
        android:toXDelta="100"
        android:toYDelta="100"/>
</set>
```
###4.RotateAnimation(旋转动画)
- fromDegress起始的角度
- toDegress终止的角度
- pivotX、pivotY分别为旋转动画相对于x,y的坐标开始位置
```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <rotate
        android:duration="1000"
        android:fromDegrees="0"
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"
        android:pivotX="50%"
        android:pivotY="50%"
        android:toDegrees="+360"/>
</set>
```
##五、特效示例
###1.示例一（续播1）
>*分别两个动画A和B，然后先播放动画A，设置A的AnimationListener。当onAnimationEnd触发（即A播放完毕）时，开始播放B。*
```java
loadAnimation = AnimationUtils.loadAnimation(this, R.anim.translate);
                image.startAnimation(loadAnimation);
                final Animation loadAnimation2 = AnimationUtils.loadAnimation(this, R.anim.rotate);
                loadAnimation.setAnimationListener(new Animation.AnimationListener() {
                    @Override
                    public void onAnimationStart(Animation animation) {

                    }

                    @Override
                    public void onAnimationEnd(Animation animation) {
                        image.startAnimation(loadAnimation2);
                    }

                    @Override
                    public void onAnimationRepeat(Animation animation) {

                    }
                });
```

###2.示例二（续播2）
>*写一个动画集AnimationSet，在其中定义动画A和B,为动画B设置startOffset,其值就是前一个动画播放的所需时间。*
```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <alpha
        android:duration="3000"
        android:fromAlpha="0.2"
        android:toAlpha="1.0"/>
    <alpha
        android:duration="3000"
        android:fromAlpha="1.0"
        android:startOffset="3000"
        android:toAlpha="0.2"
        />
</set>
```
###3.示例三（循环闪烁）
>*利用Animation的setRepeatCount、setRepeatMode来实现动画循环。*
```java
AlphaAnimation alphaAnimation = new AlphaAnimation(0.1f, 1.0f);
                alphaAnimation.setDuration(100);
                alphaAnimation.setRepeatCount(10);
                //倒序重复REVERSE 正序重复RESTART
                alphaAnimation.setRepeatMode(Animation.REVERSE);
                image.startAnimation(alphaAnimation);
```

###4.示例四（Activity切换动画）
>*使用overridePendingTransition方法。*
参数一：第二个activity进入动画  
参数二：第一个activity退出动画
```java
Intent intent = new Intent(MainActivity.this, MainActivity2.class);
                startActivity(intent);
                overridePendingTransition(R.anim.zoom_in, R.anim.zoom_out);
```

###5.LayoutAnimation(布局动画)
为View Groups添加动画
使用LayoutAnimationController

ListActivity.java
```java
public class ListActivity extends Activity {
    private ListView listView;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.list_layout);
        listView = (ListView) findViewById(R.id.listView);
        List<String> list = new ArrayList<>();
        for (int i=0;i<20;i++) {
            list.add("慕课网" + i);
        }
        ArrayAdapter<String> adapter = new ArrayAdapter<>(this, android.R.layout.simple_list_item_1, list);
        listView.setAdapter(adapter);
        LayoutAnimationController lac = new LayoutAnimationController(AnimationUtils.loadAnimation(this, R.anim.zoom_in));
        lac.setOrder(LayoutAnimationController.ORDER_NORMAL);
        listView.setLayoutAnimation(lac);
        listView.startLayoutAnimation();
    }
}
```
###6.FrameAnimation（逐帧动画）
在drawable下新建一个XML文件
使用animation-list标签来分组一个item标签集合，
android:drawable 定义要显示的图片
android:duration 指定显示它的时间（以毫秒为单位）

anim_list.xml:
```xml
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android" >

    <item
        android:drawable="@drawable/one"
        android:duration="500"/>
    <item
        android:drawable="@drawable/two"
        android:duration="500"/>
    <item
        android:drawable="@drawable/three"
        android:duration="500"/>
    <item
        android:drawable="@drawable/four"
        android:duration="500"/>
    <item
        android:drawable="@drawable/five"
        android:duration="500"/>
    <item
        android:drawable="@drawable/six"
        android:duration="500"/>

</animation-list>
```
MainActivity.java
```java
image.setImageResource(R.drawable.anim_list);
```

在此输入正文




