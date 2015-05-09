---
layout: post
title:  "Screenshot on fail in JUnit + Selenium"
date:   2013-04-10
categories: selenium junit java
---

This will do a screenshot on fail in Selenium + JUnit. You need to add below code in every test class (or a class that is extended by every test class). A screenshot will be saved for every failed selenium action.

{% highlight java %}
@Rule
public ScreenshotTestRule screenshotTestRule = new ScreenshotTestRule();

/**
* To make a screenshot on fail, will save it in
* target/surefire-reports/screenshots as ClassName_methodName.png.
*/
class ScreenshotTestRule implements TestRule {
  public Statement apply(final Statement statement, final Description description) {
    return new Statement() {
      @Override
      public void evaluate() throws Throwable {
        try {
          statement.evaluate();
        } catch (Throwable t) {
          takeScreenshot(getName(description.getClassName(), description.getMethodName()));
          throw t;
          // rethrow to allow the failure to be reported to JUnit
        }
      }
    };
  }
  
  private String getName(String className, String methodName) {
    return className.substring(className.lastIndexOf(".") + 1) + "_" + methodName;
    // ClassName_methodName
  }
  
  private void takeScreenshot(String name) {
    try {
      new File("target/surefire-reports/screenshots/").mkdirs();
      // check that folder exists
      FileOutputStream out = new FileOutputStream("target/surefire-reports/screenshots/"
 												  + name + ".png");
      out.write(((TakesScreenshot) se).getScreenshotAs(OutputType.BYTES));
      out.close();
      System.out.println("Screenshot saved (" + name + ".png).");
    } catch (Exception e) {
      System.err.println("Failed to save screenshot (" + name + ".png).");
      // No need to crash the tests if the screenshot fails
    }
  }
}
{% endhighlight %}

Where se is Selenium Webdriver, for example:

{% highlight java %}
File file = new File("resources/drivers/chromedriver.exe");
System.setProperty("webdriver.chrome.driver", file.getAbsolutePath());
WebDriver se = new ChromeDriver();
/*
* Wiki: http://code.google.com/p/selenium/wiki/ChromeDriver. The latest
* version can be downloaded from:
* http://code.google.com/p/chromedriver/downloads/list
*/
{% endhighlight %}