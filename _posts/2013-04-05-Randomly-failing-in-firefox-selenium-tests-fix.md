---
layout: post
title:  "Randomly failing in firefox selenium tests - fix"
date:   2013-04-05
categories: selenium java
---

A while ago I had a problem with some selenium tests in firefox. All the tests where passing in chrome and IE, but there were some random fails when using firefox. In my company we couldn't figure it out for couple of days. The page that we were testing was based on AJAX, we were using Webdriver/Selenium 2.29.1 and JUnit 4.11 + firefox from version 16.x up to version 19.x. Finnaly J. from my company found a solution.

Turned out that there is a special "strategy" option for firefox webdriver, that when set to unstable, helps handle AJAX based pages.

{% highlight java %}
FirefoxProfile profile = new FirefoxProfile();
profile.setPreference("webdriver.load.strategy", "unstable");
WebDriver se = new FirefoxDriver(profile);
{% endhighlight %}

Quoting from <a href="http://code.google.com/p/selenium/wiki/FirefoxDriver" target="_blank">selenium wiki</a>:
<blockquote>
There is beta feature to make firefox not wait for the full page to load after calling .get or .click. This may cause immediate find's to break, so please be sure to use an implicit or explicit wait too. This is only available for Firefox and not other browsers.
</blockquote>

This solution requires to add a small wait after each page load / click / etc (depending on how the page is build), so I added simple 1-2 second wait after each button click, for example:

{% highlight java %}
/**
* Clicks submit button
*/
public void send() {
  se.findElement(By.id("submit")).click();
  waitNsec(1);
}
{% endhighlight %}

Where **waitNsec** is a simple wait:

{% highlight java %}
/**
* Waits n seconds
*
* @param n
*/
public static void waitNsec(int n) {
  long t0, t1;
  t0 = System.currentTimeMillis();
  do {
    t1 = System.currentTimeMillis();
  } while (t1 - t0 < (n * 1000));
}
{% endhighlight %}

This solved the problem.
