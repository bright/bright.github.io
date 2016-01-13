---
layout: post
excerpt: "Since version 1.1 of Android Gradle Plugin we can run unit test on a local JVM on our development machine. In this article I'll demonstrate how to make local resources available in unit test case."
title: "Using the file resources in Android POJO unit test"
modified: 2015-04-13
tags: [android, unit-testing]
comments: true
author: mateuszklimek
---
Since version 1.1 of Android Gradle Plugin we can run unit test on a local JVM on our development machine. It's still [experimental](http://tools.android.com/tech-docs/unit-testing-support) feature but I've found it's fully usable.<br/>
Finally Android developers have lighweight build-in tool for unit testing :)<br />
The times when we need [third party](http://robolectric.org/configuring/) libraries to test unit classes *quickly* seems to be gone :)  
<br/>
I had to implement a parser which operates on JSON data returned from REST API. <br/>
The first thing I thought was to get some sort of responses from the server and put them in local files.
This approach was the simplest way to make my parser work with proper data without any mocking stuff.
After a while I prepared simple test which was ready to read json from the file:

{% highlight java %}
 @Test
public void parserShouldHasInvalidStateIfResponseHasMissingField() throws Exception {
	Parser parser = new Parser();
	File file = Utils.getFileFromPath("res/response_with_missing_field.json");
	String json = Utils.readFileAsString(file);
	parser.parse(json);
	assertThat(parser.getState(), equalTo(State.INVALID));
}
{% endhighlight %}


Where method `getFileFromPath` is:
{% highlight java %}
public static File getFileFromPath(Object obj, String fileName) {
	ClassLoader classLoader = obj.getClass().getClassLoader();
	URL resource = classLoader.getResource(fileName);
	return new File(resource.getPath());
}
{% endhighlight %}


Unfortunately if you put *response_with_missing_field.json* directly in the sources directory or any other, `getResources()` returns `null`. <br/>
After quick research I realized the reason was the apk doesn't contain any resource file at all. 
So it's going to be problem with build tool which ignores the resource. 

Let's play with *gradle* a bit to make it work.

 1. Ensure you're using at least [Android Gradle Plugin version 1.1](http://tools.android.com/tech-docs/unit-testing-support). Follow the link to set up Android Studio correctly. 
 2. Create test directory. Put unit test classes in java directory and put your resources file in res directory. Android Studio should mark them like follow: 

 	![test-directory-structure]({{site.url}}/images/test-directory-structure.png)

 3. Create `gradle` task to copy resources into classes directory to make them visible for classloader `getResources()` method:
{% highlight groovy %}

android{
  ...
}

task copyResDirectoryToClasses(type: Copy){
    from "${projectDir}/src/test/res"
    into "${buildDir}/intermediates/classes/test/debug/res"
}

assembleDebug.dependsOn(copyResDirectoryToClasses)
{% endhighlight %}
 Run unit test from Android Studio by <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>F10</kbd> on whole class or specific test method.<br/>
 Now you should be able to get the `File` reference to the resource.


See this post on my [personal blog](http://mklimek.github.io/using-file-resources-in-android-unit-test/).

