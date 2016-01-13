---
layout: post
excerpt: "People around the world are waiting for Google to push Lollipop to theirs smartphones. Material Design completely changed the appearance of Android, and did it right. Material Design is really beautiful. But who says we have to wait to see Material.Theme in action? Most of features has been packed into android-support-library. Use it and build app with material for pre-lollipop devices."
title: Playing with Material Design, Toolbar and Palette
modified: 2014-11-11
tags: [android, materialdesign, android-support-library]
comments: true
author: mateuszklimek
---

Since Android L(ollipop) was presented in June at [Google I/O](https://www.youtube.com/watch?v=wtLJPvx7-ys), only two devices on market can run it officially. Nexus 6 and Nexus 9 were released a week ago, and these two guys are ready to go with most recent system from Google, but what about other devices? My Nexus 5 still tryin update everyday, but still no effects :)
Hopefully, we can use some part of Material Design with devices running **Android 2.1 and higher**, because most of implementation was placed in android-support-v7.

I wrote simple app which displays images in grid with possibility to pick them to see them in full size with ActionBar colors based on picked image. App uses new classes: [Palette](http://developer.android.com/reference/android/support/v7/graphics/Palette.html) and [Toolbar](http://developer.android.com/reference/android/support/v7/widget/Toolbar.html).


It's looks like this:<br /><br />
![first screenshot]({{site.url}}/images/palette-example1.png)
![second screenshot]({{site.url}}/images/palette-example2.png)
![third screenshot]({{site.url}}/images/palette-example3.png)
![fourth screenshot]({{site.url}}/images/palette-example4.png)

Let's code.<br />
All dependencies we need:
{% highlight groovy %}
dependencies {
    compile 'com.android.support:appcompat-v7:21.0.0'
    compile 'com.android.support:palette-v7:21.0.0'
    compile 'com.squareup.picasso:picasso:2.4.0'
}
{% endhighlight %}
as you can see Google split android.support and put Palette as separate lib (similar to [RecyclerView](http://developer.android.com/reference/android/support/v7/widget/RecyclerView.html) and [CardView](http://developer.android.com/reference/android/support/v7/widget/CardView.html)). I used [Picasso](http://square.github.io/picasso/) to simple load images from web.

Next, define AppTheme with parent as `Theme.AppCombat`. You can also specify `colorPrimary` and `accentColor` which are used by framework to color surface background of widgets such as text fields, checkboxes etc.
{% highlight xml %}
<style name="AppTheme" parent="Theme.AppCompat">
  <item name="colorPrimary">@color/black</item>
</style>
<color name="black">#000000</color>
{% endhighlight %}

Apply style to application:
{% highlight xml %}
<application android:theme="@style/AppTheme" />
{% endhighlight %}

<br />
App has two activities. First `MainActvity` extends `ActionBarActivity` so framework will show window with `ActionBar` (see first screenshot with GridView).  `ImageActivity` is just `Activity`, so by default window will be rendered without `ActionBar`. But look at other screenshots, they have an `ActionBar`, but wait... actually it's `Toolbar`!

`Toolbar` is more flexible (easier to customize) **standard** `View`, so you can put it everywhere in layout, multiple times... and it still can act as `ActionBar` (see [setActionBar](https://developer.android.com/reference/android/app/Activity.html#setActionBar(android.widget.Toolbar)))

Let's look at the layout file:
{% highlight xml %}
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:background="@color/black"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:background="@drawable/rectangle"
        android:layout_width="match_parent"
        android:layout_height="@dimen/abc_action_bar_default_height_material"
        android:title="@string/app_name"
        />

    <ImageView
        android:id="@+id/image"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:src="@drawable/rectangle"
        android:scaleType="center"
        />

</LinearLayout>
{% endhighlight %}
It's simple. Nothing special here, except using of `Toolbar`.

Finally we can use `Pallete` to extract prominent colors from image and change style of `Toolbar` at runtime.
{% highlight java %}
final Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
final ImageView image = (ImageView) findViewById(R.id.image);
target = new Target() {
    @Override
    public void onBitmapLoaded(final Bitmap bitmap, Picasso.LoadedFrom from) {
        Palette.generateAsync(bitmap, new Palette.PaletteAsyncListener() {
            public void onGenerated(Palette palette) {
                image.setImageBitmap(bitmap);
                int defaultColor = getResources().getColor(R.color.black);
                toolbar.setBackgroundColor(palette.getLightVibrantColor(defaultColor));
                toolbar.setTitleTextColor(palette.getDarkMutedColor(defaultColor));
                toolbar.setTitle(getString(R.string.app_name));
            }
        });
    }
};
Picasso.with(this)
        .load(getIntent().getStringExtra("url"))
        .into(target);
{% endhighlight %}
Look at the screenshots, I think they look pretty well and we didn't need a graphic designer to choose correct colors ;)

<br />
See [full source code at github](https://github.com/mklimek/palette-sample).<br />
See [Creating Apps with Material Design](https://developer.android.com/training/material/index.html).<br />
See [Material Design specification](http://www.google.com/design/spec/material-design/introduction.html#).

See this post on my [personal blog](http://mklimek.github.io/playing-with-material-design-toolbar-palette/).
