---
layout: post
title:  "The joy of Android programming with Groovy"
date:   2015-08-22
categories: android groovy
author: "Liew Jun Tung"
---
### tl;dr ###
If you are having difficulties grasping Android programming because you are struggling with Java at the same time. Use `Groovy` programming language to learn the Android part first while you slowly understand how Java works in Android.

-------------------------------

We all want to do those awesome cool apps that is in your Android smartphones. But there's a problem, perhaps you are new to the programming scene, or perhaps you are a web developer that do all your stuffs in dynamic language like PHP, Ruby or Python. You Google'd up instructions and finally set up everything. Now you have to juggle between learning the Android framework and the verbosity of Java programming. 

It's a pain in the butt, I've been there. In the first few months in my journey of learning how to create an Android app, I was dumbfounded with how "static" it was with Java, I now know and appreciate the reason it is written this way now, but I sure wasn't  wasn't back then. Until the release of a Gradle plugin that allows developers to code Android using Groovy. I was able to temporarily forget about wrestling with Java and focus on learning the Android frameworks. In just a few weeks I am 'clicked' and comfortable to do my Android app with minimal effort.Besides that, I get to understand Java better too! Right now I'm doing my apps in Java because it's still the official way to do Android app with minimal surprises. I do owe Groovy programming alot for that.

For the uninitiated, [Groovy][groovy]{:target="_blank"} is practically a superset of Java. Copy paste in Java code in Groovy it will run, and it can do alot more. There are [lots][example1]{:target="_blank"} of other [examples][example2]{:target="_blank"} and [tutorials][example3]{:target="_blank"} on how to get started on doing Android apps in Groovy. 

So I'm not going to do that and instead focusing on what I love about it.

# 1. Semicolons are not enforced! #
You still can have semicolon if you want, but it's optional in Groovy now! 

# 2. Creating classes #

I don't have to deal with getters, setters, constructors, and the toString methods in writing classes. (Yes, I'm that lazy) Instead of doing like so.
{% highlight java %}
public class Weather{
	public int temperature;
	public int windSpeed;
	public String cityName;

	public Weather(int temperature, int windSpeed, String cityName){
		this.temperature = temperature;
		this.windSpeed = windSpeed;
		this.cityName = cityName;
	}

	public int getTemperature(){...}
	public void setTemperature(){...}
	...
}
{% endhighlight %}

You can do a class in a far simpler manner like this...
{% highlight groovy %}
@ToString
class Weather{
	int temperature
	int windSpeed
	String cityName

}
{% endhighlight %}
That's it, you can use the class to create an object like s. Getters and setters are automatically created for you.

{% highlight groovy %}
Weather weather = new Weather(temperature: 25, windSpeed:10, cityName: "Kuala Lumpur")
println(weather.cityName) //Groovy sugar, it's the same as System.out.println(weather.getCityName());
{% endhighlight %}

There are a few ways to overcome this in Java with the help of the Android Studio IDE, but still who would have figure it out 
when you first started? All I want is to learn how to create and app and put it on my phone!

# 3. Condition checking #
`null` or an empty list is automatically `false` in Groovy
{% highlight groovy %}
if(weatherList){
	//do stuffs
}
{% endhighlight %}
instead of 
{% highlight java %}
if(weatherList != null && weatherList.size() > 0){
	//do stuffs
}
{% endhighlight %}

# 4. Arrays, List and Maps #
Coming from a Python background, creating list in Java is really weird if you don't know what's going on. In Groovy, you would do like this
{% highlight groovy %}
Map priceOfFruit = ["apple": 10, "durian": 20, "lemon": 44] //just use it as it is! example priceOfFruit[apple]
List listOfFruit = ["apple", "orange", "durian", "lemon"] 
{% endhighlight %}
In Java, you do like this
{% highlight java %}
HashMap<String, Int> priceOfFruit = new HashMap<>();
priceOfFruit.put("apple", 10);
priceOfFruit.put("durian", 20);
priceOfFruit.put("lemon", 44);
List<String> listOfFruit = new ArrayList<>();
listOfFruit.put("apple");
...
{% endhighlight %}

# 5. Closures #
You will see this quite often whenever you do a listener in any of the Android's views such as buttons.
In Java you will do like so.
{% highlight java %}
button.setOnClickListener(new View.OnClickListener() {
             public void onClick(View v) {
                 // do stuffs.
             }
         });
{% endhighlight %}
Android Studio will do all the boilerplates for you. But it still pretty ugly. We can have the below instead.
{% highlight groovy %}
button.onClickListener = {
	//do stuffs
}
{% endhighlight %}

### However, however fun it is. There are some stuffs to look out for when you programming Android using Groovy. ###

# 1. It's not the "official" way #
Android programming still belongs to Java, while you can apply most of the solutions found in StackOverflow. You will most likely need to search harder if your problem is Groovy Android related. If you are slightly advance and you want to use the newest Dependency Injection library Dagger 2, You are out of luck. For now the `android-apt` is only for [Android in Java][bitbucket]{:target="_blank"}. Until Google say Groovy is THE REPLACEMENT for Java in programming Android, it will remain like this for some time to come. 

# 2. Some very weird inconsistencies #
Using some these Groovy syntax sugars are very cool and all, but there are some weird stuffs going on. For example,
{% highlight groovy %}
// SomeFragment.groovy
View onCreateView(LayoutInflater inflater, ViewGroup viewgroup, Bundle savedInstanceState){
	...
	activity // you can use this as the getActivity() in java
	...
}
{% endhighlight %}
you ought to go easy on doing that when you need it in an anonymous inner class, such as:
{% highlight groovy %}
// SomeFragment.groovy
View onCreateView(LayoutInflater inflater, ViewGroup viewgroup, Bundle savedInstanceState){
	...
	//just an example, there will be times you can't use closures.
	button.setOnClickListener(new View.OnClickListener() {
	    @Override           
	    public void onClick(View v) {
	        Toast.makeText(activity, "BLAH BLAH BLAH", Toast.LENGTH_SHORT).show  //The IDE will complain about it, it won't build 
	         Toast.makeText(getActivity(), "BLAH BLAH BLAH", Toast.LENGTH_SHORT).show  //this one is good
	    }
	})

	...
}
{% endhighlight %}

# 3. Android Studio integration is not so good
In Android Studio you can 'jump' directly to the layouts, string resources or styles directly from the with just a Ctrl key pressed down and a mouse click. As the time I ma writing now. You can't with Groovy. It'll boot you directly to the generated R.java file. Besides that, you can't create the ".groovy" file directly by right clicking on a folder. You should create a groovy template with "Edit File Template..." 
![image]({{site.url}}/img/studio.jpg){:width="200"}

# 4. Slow and cranky during compilation
It has a weird bug when you do a `./gradlew build` after you did a `./gradlew clean`. Gradle will error out and the Groovy files will contains lots of red letters, which means the classes aren't prepared for you to use yet. You have to run `./gradlew build` again in order for it to be compiled successfully. And it takes quite some time to get it up and running. 

# 5. Use `@CompileStatic` to annotate your classes to get performance as close to Java as much as possible. Do it in activities, fragments and domain classes like so.
{% highlight groovy %}
@CompileStatic
class Weather{
	...
}
{% endhighlight %}

So that's that, **have fun learning Android through Groovy!**


[bitbucket]: https://bitbucket.org/hvisser/android-apt/issues/43/support-groovy-android-plugin
[groovy]: http://www.groovy-lang.org/
[example1]: https://objectpartners.com/2014/09/04/developing-native-android-app-using-groovy/
[example2]: http://hosain.net/2015/02/07/getting-started-with-android-development-using-groovy-2.4-and-android-studio.html
[example3]: https://dzone.com/articles/creating-android-apps-groovy