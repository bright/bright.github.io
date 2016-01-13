---
layout: post
excerpt: "VideoView is the most straightforward way to show video content in layout. <br />
It took a few lines of code to setup and show for example mp4 file. <br />
It's fine when you don't care about UX too much, but when you do, things are going to be annoying."
title: How to avoid flickering and black screen issues when using VideoView?
modified: 2015-01-11
tags: [android, videoview]
comments: true
author: mateuszklimek
---

VideoView is the most straightforward way to show video content in layout. <br />
It took a few lines of code to setup and show for example mp4 file. <br />
It's fine when you don't care about UX too much, but when you do, things are going to be annoying.
When you tested app for different cases like: change device orientation, swipe the view, lock screen or go to home screen and back to the app, you probably know what I'm talking about. <br />
If you don't, take a look at: <br /><br />
[VideoView Black Screen](http://stackoverflow.com/questions/9765629/android-videoview-black-screen) <br />
[VideoView Black Flash Before and After Playing](http://stackoverflow.com/questions/4343350/videoview-black-flash-before-and-after-playing?lq=1) <br />
[VideoView Inside Fragment Causes Black Screen Flickering](http://stackoverflow.com/questions/17717906/videoview-inside-fragment-causes-black-screen-flicking?lq=1) <br />
[VideoView Flickering Issue](http://stackoverflow.com/questions/17587476/videoview-flickering-issue?lq=1) <br />

# Workaround
To deal with these problems we can use 'placeholder solution' described in the above links. <br />
The idea is to show placeholder, which has the same color as video background, when `VideoView` is not ready to show the content yet. When `VideoView` is ready the placeholder will go away and already prepared video will be revealed without any issues. <br />
Thereby we achieve nice solid experience, because user doesn't see how system prepares video to render it, which causes `VideoView` displays black screen or the content flickering. If you want to observe these issues try to move the app to the background (by pressing home screen button) and bring back the app to the foreground. <br />
If it still seems to work well, try it a couple of times... it will happen ;)

# FrameVideoView
`FrameVideoView` is a class which implements 'placeholder workaround' dynamically by creating views in runtime. You don't have to create additional layout for placeholder and put `VideoView` in there nor have you to take care about showing and hiding placeholder in an appropriate time. `FrameVideoView` does it for you. <br />
If device is running API level 13 or below, [`VideoView`](https://developer.android.com/reference/android/widget/VideoView.html) will be used to show video content. This approach should fix most of the flickering and black screen issues. <br />
For devices running API level 14 or higher, [`TextureView`](https://developer.android.com/reference/android/view/TextureView.html) will be used to show video content. It provides a lot better performance than `VideoView` implementation because it was created to show 'stream content' like video or OpenGL scenes. <br /> <br />
I wrote [app](https://play.google.com/store/apps/details?id=com.everytap&hl=en) with `ViewPager` where each of three fragments has different video and are playing simultaneously. When `FrameVideoView` used `VideoView` implementation there were problems because sometimes black screen issues appeared again when swiping. There was also the problem when user swiped to the last page and `ViewPager` showed overscroll animation that can't overlay `VideoView`. When `TextureView` implementation was used all problems were gone and app worked as expected.

# How to use it?

Add `FrameVideoView` to layout:
{% highlight xml %}
  <mateuszklimek.framevideoview.FrameVideoView
    android:id="@+id/frame_video_view"
    android:layout_width="@dimen/video_width"
    android:layout_height="@dimen/video_height"
  />
{% endhighlight %}

find its instance in `Activity` and call corresponding methods in `onResume` and `onPause`:
{% highlight java %}
public class SampleActivity extends Activity {

  private FrameVideoView videoView;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.simple);

    String uriString = "android.resource://" + getPackageName() + "/" + R.raw.movie;
    videoView = (FrameVideoView) findViewById(R.id.frame_video_view);
    videoView.setup(Uri.parse(uriString), Color.GREEN);
  }

    @Override
    protected void onResume() {
      super.onResume();
      videoView.onResume();
    }

    @Override
    protected void onPause() {
      videoView.onPause();
      super.onPause();
    }
  }
  {% endhighlight %}

If you want to execute method for particular implementation eg. `seekTo()` from `VideoView` you can call it like that:
{% highlight java %}
  frameVideoView.asVideoView().seekTo(x);
{% endhighlight %}
but before, it's better to check what type of implementation is used:
{% highlight java %}
if(videoView.getImplType() == VIDEO_VIEW){
    frameVideoView.asVideoView().seekTo(x);
}
{% endhighlight %}


  <br />
  I uploaded project to [github](https://github.com/mklimek/FrameVideoView) so you can run example app and see full implementation of `FrameVideoView`. Feel free to use it as you like.
  <br />

  See this post on my [personal blog](http://mklimek.github.io/frame-video-view/).
