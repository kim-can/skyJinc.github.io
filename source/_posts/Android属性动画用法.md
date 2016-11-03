---
title: Android属性动画用法
date: 2016-09-14 17:53:13
tags: 属性动画
category: Android
---
Android系统在一开始的时候就给我们提供了两种实现动画效果的方式，**逐帧动画(frame-by-frame animation)** 和 **补间动画(tweened animation)**

 - 逐帧动画的工作原理很简单，其实就是将一个完整的动画拆分成一张张单独的图片，然后再将它们连贯起来进行播放，类似于动画片的工作原理。

 - 补间动画则是可以对View进行一系列的动画操作，包括淡入淡出、缩放、平移、旋转四种。
然而自Android 3.0版本开始，系统给我们提供了一种全新的动画模式，属性动画(property animation)，它的功能非常强大，弥补了之前补间动画的一些缺陷，几乎是可以完全替代掉补间动画了。对于逐帧动画和补间动画的用法，我不想再多讲，它们的技术已经比较老了，而且网上资料也非常多，那么今天我们这篇文章的主题就是对Android属性动画进行一次完全解析。

<!-- more -->

## 引入属性动画的原因
android.view.animation包下面有好多的类可以供我们操作，比如说对View进行移动、缩放、旋转和淡入淡出，并且我们还可以借助AnimationSet来将这些动画效果组合起来使用，除此之外还可以通过配置Interpolator来控制动画的播放速度等等等等。

 - 为什么还要引入属性动画呢？

其实上面所谓的健全都是相对的，如果你的需求中只需要对View进行移动、缩放、旋转和淡入淡出操作，那么补间动画确实已经足够健全了。但是很显然，这些功能是不足以覆盖所有的场景的，一旦我们的需求超出了移动、缩放、旋转和淡入淡出这四种对View的操作，那么补间动画就不能再帮我们忙了，

也就是说它在功能和可扩展方面都有相当大的局限性，那么下面我们就来看看补间动画所不能胜任的场景。

注意上面我在介绍补间动画的时候都有使用“对View进行操作”这样的描述，没错，补间动画是只能够作用在View上的。也就是说，我们可以对一个Button、TextView、甚至是LinearLayout、或者其它任何继承自View的组件进行动画操作，但是如果我们想要对一个非View的对象进行动画操作，补间动画就帮不上忙了。

 - 补间动画缺陷

举一个简单的例子，比如说我们有一个自定义的View，在这个View当中有一个Point对象用于管理坐标，然后在onDraw()方法当中就是根据这个Point对象的坐标值来进行绘制的。也就是说，如果我们可以对Point对象进行动画操作，那么整个自定义View的动画效果就有了。显然，补间动画是不具备这个功能的，这是它的第一个缺陷。

第二个缺陷，就是它只能够实现移动、缩放、旋转和淡入淡出这四种动画操作，那如果我们希望可以对View的背景色进行动态地改变呢？很遗憾，我们只能靠自己去实现了。说白了，之前的补间动画机制就是使用硬编码的方式来完成的，功能限定死就是这些，基本上没有任何扩展性可言。

第三个缺陷，就是它只是改变了View的显示效果而已，而不会真正去改变View的属性。 就是你动画移动了控件的位置，看起来是移动了，其实还在原先那里。。点击事件还是在原有的位置

 - 改变

Android开发团队决定在3.0版本当中引入属性动画这个功能，那么属性动画是不是就把上述的问题全部解决掉了？下面我们就来一起看一看。
新引入的属性动画机制已经不再是针对于View来设计的了，也不限定于只能实现移动、缩放、旋转和淡入淡出这几种动画操作，同时也不再只是一种视觉上的动画效果了。它实际上是一种不断地对值进行操作的机制，并将值赋值到指定对象的指定属性上，可以是任意对象的任意属性。所以我们仍然可以将一个View进行移动或者缩放，但同时也可以对自定义View中的Point对象进行动画操作了。我们只需要告诉系统动画的运行时长，需要执行哪种类型的动画，以及动画的初始值和结束值，剩下的工作就可以全部交给系统去完成了。
既然属性动画的实现机制是通过对目标对象进行赋值并修改其属性来实现的，那么之前所说的按钮显示的问题也就不复存在了，如果我们通过属性动画来移动一个按钮，那么这个按钮就是真正的移动了，而不再是仅仅在另外一个位置绘制了而已。


## ValueAnimator

ValueAnimator当中最常用的应该就是ofFloat()和ofInt()这两个方法了，另外还有一个ofObject()方法,那么除此之外，

 - setStartDelay()方法来设置动画延迟播放的时间
 - setRepeatCount()方法来设置动画循环播放的次数
 - setRepeatMode()方法来以及循环播放的模式，循环模式包括RESTART和REVERSE两种，分别表示重新播放和倒序播放的意思
 - addUpdateListener()方法来添加一个动画的监听器

![tu](https://skyJinc.github.io/images/animatioin/1.png)


ValueAnimator确实已经在正常工作了，值在300毫秒的时间内从0平滑过渡到了1，而这个计算工作就是由ValueAnimator帮助我们完成的。另外ofFloat()方法当中是可以传入任意多个参数的，因此我们还可以构建出更加复杂的动画逻辑，比如说将一个值在5秒内从0过渡到5，再过渡到3，再过渡到10，就可以这样写：

	ValueAnimator anim = ValueAnimator.ofFloat(0f, 5f, 3f, 10f);  
	anim.setDuration(5000);  
	anim.start();  

![tu](https://skyJinc.github.io/images/animatioin/2.png)

## ObjectAnimator

  - 区别 

ValueAnimator只不过是对值进行了一个平滑的动画过渡，而ObjectAnimator则就不同了，它是可以直接对任意对象的任意属性进行动画操作的，比如说View的alpha属性。

注意: ObjectAnimator 继承 ValueAnimator 所以 ValueAnimator有的ObjecAnimator都有

### 透明

	ObjectAnimator animator = ObjectAnimator.ofFloat(tvContent, "alpha", 1f, 0f,1f);
	animator.setDuration(500);
	animator.start();

![tu](https://skyJinc.github.io/images/animatioin/4.gif)

### 旋转

	ObjectAnimator animator = ObjectAnimator.ofFloat(tvContent, "rotation", 0f, 360f);
	animator.setDuration(5000);
	animator.start();


![tu](https://skyJinc.github.io/images/animatioin/3.gif)

### 移动 

	float translationX = tvContent.getTranslationX();
	ObjectAnimator animator = ObjectAnimator.ofFloat(tvContent, "translationX",translationX, -700f, translationX);
	animator.setDuration(5000);
	animator.start();

![tu](https://skyJinc.github.io/images/animatioin/5.gif)

### 缩放

	ObjectAnimator animator = ObjectAnimator.ofFloat(tvContent, "scaleY", 1f, 3f, 1f);
	animator.setDuration(5000);
	animator.start();
	
![tu](https://skyJinc.github.io/images/animatioin/6.gif)


### 说明

目前我们使用过了alpha、rotation、translationX和scaleY这几个值，分别可以完成淡入淡出、旋转、水平移动、垂直缩放这几种动画，那么还有哪些值是可以使用的呢？就是我们可以传入任意的值到ofFloat()方法的第二个参数当中。任意的值？相信这很出乎大家的意料吧，但事实就是如此。因为ObjectAnimator在设计的时候就没有针对于View来进行设计，而是针对于任意对象的，它所负责的工作就是不断地向某个对象中的某个属性进行赋值，然后对象根据属性值的改变再来决定如何展现出来

ObjectAnimator是如何进行操作的呢？其实ObjectAnimator内部的工作机制并不是直接对我们传入的属性名进行操作的，而是会去寻找这个属性名对应的get和set方法，因此alpha属性所对应的get和set方法..方法是由 View 提供


## 组合动画

独立的动画能够实现的视觉效果毕竟是相当有限的，因此将多个动画组合到一起播放就显得尤为重要

### AnimatorSet

 - after(Animator anim)   将现有动画插入到传入的动画之后执行
 - after(long delay)   将现有动画延迟指定毫秒后执行
 - before(Animator anim)   将现有动画插入到传入的动画之前执行
 - with(Animator anim)   将现有动画和传入的动画同时执行

		float tx = tvContent.getTranslationX();

		ObjectAnimator moveIn = ObjectAnimator.ofFloat(tvContent, "translationX",tx, -500f, tx);
		ObjectAnimator rotate = ObjectAnimator.ofFloat(tvContent, "rotation", 0f, 360f);
		ObjectAnimator fadeInOut = ObjectAnimator.ofFloat(tvContent, "alpha", 1f, 0f, 1f);
		AnimatorSet animSet = new AnimatorSet();
		animSet.play(rotate).with(fadeInOut).after(moveIn);
		animSet.setDuration(5000);
		animSet.start();
		
![tu](https://skyJinc.github.io/images/animatioin/7.gif)

## Animator监听

 - onAnimationStart()方法会在动画开始的时候调用
 - onAnimationRepeat()方法会在动画重复执行的时候调用
 - onAnimationEnd()方法会在动画结束的时候调用
 - onAnimationCancel()方法会在动画被取消的时候调用。

	animSet.addListener(new Animator.AnimatorListener() {

			@Override public void onAnimationStart(Animator animation) {
				L.i("开始动画");
			}

			@Override public void onAnimationEnd(Animator animation) {
				L.i("结束动画");
			}

			@Override public void onAnimationCancel(Animator animation) {
				L.i("取消动画");
			}

			@Override public void onAnimationRepeat(Animator animation) {
				L.i("重复动画");
			}
		});

如果觉得接口用不到这么多，可以使用 AnimatorListenerAdapter


	animSet.addListener(new AnimatorListenerAdapter(){});

	//类
	public abstract class AnimatorListenerAdapter implements Animator.AnimatorListener {

	    /**
	     * {@inheritDoc}
	     */
	    @Override
	    public void onAnimationCancel(Animator animation) {
	    }
	
	    /**
	     * {@inheritDoc}
	     */
	    @Override
	    public void onAnimationEnd(Animator animation) {
	    }
	
	    /**
	     * {@inheritDoc}
	     */
	    @Override
	    public void onAnimationRepeat(Animator animation) {
	    }
	
	    /**
	     * {@inheritDoc}
	     */
	    @Override
	    public void onAnimationStart(Animator animation) {
	    }
	}
	
	// 需要什么接口 加什么接口
	anim.addListener(new AnimatorListenerAdapter() {  
    	@Override  
    	public void onAnimationEnd(Animator animation) {  
    	}  
	}); 
	
## XML编写动画

 - <animator>  对应代码中的ValueAnimator
 - <objectAnimator>  对应代码中的ObjectAnimator
 - <set>  对应代码中的AnimatorSet

### animator XML
	//从0到100平滑过渡的动画
	<animator xmlns:android="http://schemas.android.com/apk/res/android"  
    	android:valueFrom="0"  
    	android:valueTo="100"  
    	android:valueType="intType"/>
    	
### objectAnimator XML

	//视图的alpha属性从1变成0
	<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"  
    	android:valueFrom="1"  
    	android:valueTo="0"  
    	android:valueType="floatType"  
    	android:propertyName="alpha"/>  
    	
### set XML

	//视图先从屏幕外移动进屏幕，然后开始旋转360度，旋转的同时进行淡入淡出操作
	
	<set xmlns:android="http://schemas.android.com/apk/res/android"  
    	android:ordering="sequentially" >  
  
	    <objectAnimator  
	        android:duration="2000"  
	        android:propertyName="translationX"  
	        android:valueFrom="-500"  
	        android:valueTo="0"  
	        android:valueType="floatType" >  
	    </objectAnimator>  
  
	    <set android:ordering="together" >  
	        <objectAnimator  
	            android:duration="3000"  
	            android:propertyName="rotation"  
	            android:valueFrom="0"  
	            android:valueTo="360"  
	            android:valueType="floatType" >  
	        </objectAnimator>  
	  
	        <set android:ordering="sequentially" >  
	            <objectAnimator  
	                android:duration="1500"  
	                android:propertyName="alpha"  
	                android:valueFrom="1"  
	                android:valueTo="0"  
	                android:valueType="floatType" >  
	            </objectAnimator>  
	            <objectAnimator  
	                android:duration="1500"  
	                android:propertyName="alpha"  
	                android:valueFrom="0"  
	                android:valueTo="1"  
	                android:valueType="floatType" >  
	            </objectAnimator>  
	        </set>  
	    </set>    
    </set>  
    
### 使用

	Animator animator = AnimatorInflater.loadAnimator(context, R.animator.anim_file);  
	animator.setTarget(view);  
	animator.start();  
## ValueAnimator高级用法

### TypeEvaluator 了解

多数情况下我们使用属性动画的时候都不会用到TypeEvaluator，不过还是要了解一下。。。

主要作用是动画系统如何从初始值过度到结束值

ValueAnimator.ofFloat()方法就是实现了初始值与结束值之间的平滑过度，内置了一个FloatEvaluator

	public class FloatEvaluator implements TypeEvaluator {  
	    public Object evaluate(float fraction, Object startValue, Object endValue) {  
	        float startFloat = ((Number) startValue).floatValue();  
	        return startFloat + fraction * (((Number) endValue).floatValue() - startFloat);  
	    }  
	}  
	
 - 第一个参数用于表示动画的完成度的，我们应该根据它来计算当前动画的值应该是多少
 - 第二个参数动画初始值
 - 第三个参数动画结束值

demo
	
	//位置model
	public class SkyPoint {
	
		public float	x;
	
		public float	y;
	
	    public SkyPoint(){}
	
		public SkyPoint(float x, float y) {
			this.x = x;
			this.y = y;
		}
	}
	//实现接口
	public class SkyPointEvaluator implements TypeEvaluator<SkyPoint> {

		@Override public SkyPoint evaluate(float fraction, SkyPoint startPoint, SkyPoint endPoint) {
        float x = startPoint.x + fraction * (endPoint.x - startPoint.x);
        float y = startPoint.y + fraction * (endPoint.y - startPoint.y);
        SkyPoint point = new SkyPoint();
        point.x = x;
        point.y = y;
        return point;
		}
	}
	//测试
	SkyPoint point1 = new SkyPoint(0, 0);
	SkyPoint point2 = new SkyPoint(300, 300);
	final ValueAnimator anim = ValueAnimator.ofObject(new SkyPointEvaluator(), point1, point2);
	anim.setDuration(5000);
	anim.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
		public void onAnimationUpdate(ValueAnimator animation) {
				SkyPoint value = (SkyPoint) animation.getAnimatedValue();
				L.i("值x" + value.x +", 值y" + value.y);
			}
		});
	anim.start();

有的时候通过，过程计算得到的结果 来做一些处理，是很有用处的。。

### 自定义View

	public class SkyAnimView extends View {
	
	    public static final float SIZE = 50;
	
		// 坐标
		SkyPoint	skyPoint;
	
		// 画笔
		Paint		paint;
		
		//图片
	   Bitmap bitmap;
	
		public SkyAnimView(Context context, AttributeSet attrs) {
			super(context, attrs);
			paint = new Paint(Paint.ANTI_ALIAS_FLAG);
			paint.setColor(ContextCompat.getColor(context,R.color.colorPrimary));
	        bitmap = BitmapFactory.decodeResource(getResources(),R.mipmap.ic_launcher);
		}
	
		@Override protected void onDraw(Canvas canvas) {
			super.onDraw(canvas);
	        if (skyPoint == null) {
	            skyPoint = new SkyPoint(0, 0);//第一次的位置
	            canvas.drawBitmap(bitmap,skyPoint.x, skyPoint.y, paint);//先画一个
	            startAnimation();//执行动画
	        } else {
	        	  //根据计算获得的坐标 移动	
	            canvas.drawBitmap(bitmap,skyPoint.x, skyPoint.y, paint);
	        }
	
	    }
	
	    private void startAnimation() {
	    	 //起始位置
	        SkyPoint startPoint = new SkyPoint(0, 0);
	        //结束位置 - 宽度减去图片的宽度 就不会图片飞出去了
	        SkyPoint endPoint = new SkyPoint(getWidth()-bitmap.getWidth(), getHeight()-bitmap.getHeight());
	        ValueAnimator anim = ValueAnimator.ofObject(new SkyPointEvaluator(), startPoint, endPoint);
	        anim.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
	            @Override
	            public void onAnimationUpdate(ValueAnimator animation) {
	                skyPoint = (SkyPoint) animation.getAnimatedValue();
	                L.i("值x" + skyPoint.x +", 值y" + skyPoint.y);
	                invalidate();
	            }
	        });
	        anim.setDuration(5000);
	        anim.start();
	    }
	}
	
XML
	
	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	              android:layout_width="match_parent"
	              android:layout_height="match_parent"
	              android:orientation="vertical">
	
	    <TextView
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:text="高级"/>
	
	    <sky.skyanimation.view.gaoji.custom.SkyAnimView
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"/>
	</LinearLayout>


![tu](https://skyJinc.github.io/images/animatioin/8.gif)

## ObjectAnimator高级用法

ObjectAnimator内部的工作机制是通过寻找特定属性的get和set方法，然后通过方法不断地对值进行改变，从而实现动画效果的。因此我们就需要在SkyAnimView中定义一个color属性，并提供它的get和set方法

![tu](https://skyJinc.github.io/images/animatioin/9.png)

	//定义一个TypeEvaluator 来 根据不同进度改变颜色值
	public class SkyColorEvaluator implements TypeEvaluator<String> {
	
		@Override public String evaluate(float v, String s, String t1) {
	
			if (v < 0.1f) {
				return s;
			} else if (v < 0.2f) {
				return "#99FFFF";
			} else if (v < 0.4f) {
				return "#99FFFF";
			} else if (v < 0.6f) {
				return "#9933FF";
			} else if (v < 0.8f) {
				return "#FF66FF";
			} else if (v < 0.9f) {
				return "#3366CC";
			}
	
			return t1;
		}
	}

修改 SkyAnimView 方法 

![tu](https://skyJinc.github.io/images/animatioin/10.png)

![tu](https://skyJinc.github.io/images/animatioin/11.gif)


这样组合起来出现了上面的效果...其实挺简单的～～

## Interpolator

这个怎么说呢？ 不太好翻译， 简单的说 就像一个插件一样，可以让动画加速或者减速，或者做一些有意思的事情。。属性动画 新增了一个类 TimeInterpolator接口，兼容之前 Interpolator。 看一下实现类

![tu](https://skyJinc.github.io/images/animatioin/12.png)

这些实现类 自己找找都是哪些功能吧

比如说AccelerateInterpolator就是一个加速运动的Interpolator，而DecelerateInterpolator就是一个减速运动的Interpolator

修改 SkyAnimView 的 startAnimation 方法

让它垂直的往下掉 BounceInterpolator  这个是Google给我们写好的

![tu](https://skyJinc.github.io/images/animatioin/13.png)

![tu](https://skyJinc.github.io/images/animatioin/14.gif)

我们来看看 源代码

	@HasNativeInterpolator  
	public class AccelerateDecelerateInterpolator implements Interpolator, NativeInterpolatorFactory {  
	    public AccelerateDecelerateInterpolator() {  
	    }  
	      
	    @SuppressWarnings({"UnusedDeclaration"})  
	    public AccelerateDecelerateInterpolator(Context context, AttributeSet attrs) {  
	    }  
	      
	    public float getInterpolation(float input) {  
	        return (float)(Math.cos((input + 1) * Math.PI) / 2.0f) + 0.5f;  
	    }  
	  
	    /** @hide */  
	    @Override  
	    public long createNativeInterpolator() {  
	        return NativeInterpolatorFactoryHelper.createAccelerateDecelerateInterpolator();  
	    }  
	}  

过度动画是很平滑的 ， 当然数学好的 一下就能看的出来

自定义一个

	//正弦函数的变化值也会逐渐变小 实现了减速的效果
	//先加速后减速
	public class SkyAccelerateInterpolator implements Interpolator {
	
		@Override public float getInterpolation(float v) {
			float result;
			if (v <= 0.5) {
				result = (float) (Math.sin(Math.PI * v)) / 2;
			} else {
				result = (float) (2 - Math.sin(Math.PI * v)) / 2;
			}
			return result;
		}
	} 
	
![tu](https://skyJinc.github.io/images/animatioin/15.png)

自己运行看效果吧


## ViewPropertyAnimator 
这个比较好理解了。。
像TextView自带的Animator 看代码吧

	ObjectAnimator animator = ObjectAnimator.ofFloat(textview, "alpha", 0f);  
	animator.start();  
	
	替换
	
	tvContent.animate().alpha(0f);  

你还可以这样...

	tvContent.animate().x(500).y(500); 
	tvContent.animate().x(500).y(500).setDuration(5000);
	tvContent.animate().x(500).y(500).setDuration(5000).setInterpolator(new BounceInterpolator());
        

 - 整个ViewPropertyAnimator的功能都是建立在View类新增的animate()方法之上的，这个方法会创建并返回一个ViewPropertyAnimator的实例，之后的调用的所有方法，设置的所有属性都是通过这个实例完成的。
 - 使用ViewPropertyAnimator时，我们自始至终没有调用过start()方法，这是因为新的接口中使用了隐式启动动画的功能，只要我们将动画定义完成之后，动画就会自动启动。并且这个机制对于组合动画也同样有效，只要我们不断地连缀新的方法，那么动画就不会立刻执行

 - ViewPropertyAnimator上设置的方法都执行完毕后，动画就会自动启动。当然如果不想使用这一默认机制的话，我们也可以显式地调用start()方法来启动动画。
 
 - ViewPropertyAnimator的所有接口都是使用连缀的语法来设计的，每个方法的返回值都是它自身的实例，因此调用完一个方法之后可以直接连缀调用它的另一个方法，这样把所有的功能都串接起来，我们甚至可以仅通过一行代码就完成任意复杂度的动画功能。
## 结尾

动画gif图使用了adb命令录制成mp4后用PicGIF来生成Gif图

录像命令
	
	//录制10秒停止
	adb shell screenrecord  --time-limit 10 /sdcard/demo.mp4
	
	//停止录像并保存
	Ctrl-C 

[DEMO地址](https://github.com/skyJinc/SkyAnimation)
