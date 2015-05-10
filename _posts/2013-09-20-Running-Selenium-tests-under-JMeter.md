---
layout: post
title:  "Running Selenium tests under JMeter"
date:   2013-09-20
categories: jmeter selenium
---

I don’t think that doing performance tests with the usage of JUnit/Selenium tests run from JMeter is THE BEST idea... but nevertheless – it felt like a challenge to do it, when, at some point, I read about such possibility. So I decided to give it a try. It turn out to be an interesting challenge, that took me more than couple of hours to figure it out.

The main problem was not running the tests under JMeter, but the fact that for the sake of whole thing being anyhow useful, I needed to find gui-less browser working with selenium driver, in which I could control user-agent string. For some time I got stuck with **HtmlUnit**, eventually to find much better – IMHO – **PhantomJS**, which gives users far bigger control of key properties of how the browser is interpreted by servers.

Second problem was to implement "screenshot on fail" logic. In the beginning I tried to do it as usual, to start driver before class, close it after class and take screenshot in case of fail using @Rule, but it turn out to be problematic. First of all – to take a screenshot from gui-less driver the driver needs to be augmented. There is no reason for the driver to stay in enhanced state after a failed test, as the enhanced driver occupies more memory, so I decided to close/open it after/before each test, no matter whether passed or failed. Second of all – JMeter doesn’t support @Rule, so I needed to figure out something else. Simple boolean variable set to false at the beginning of each test, and to true at the end of 100% passed test, turn out to work just fine.

After that, using guidelines from <a href="http://www.anzaan.com/2012/07/integrating-selenium-with-jmeter-for-application-load-testing/" target="_blank">here</a> and <a href="http://rationaleemotions.wordpress.com/2012/01/23/setting-up-grid2-and-working-with-it/" target="_blank">here</a>, it was fairly easy to run those tests from JMeter.

You can git clone my example project from here: https://github.com/sobolewska/selenium\_jmeter-example.git or download it as zip from <a href="https://github.com/sobolewska/selenium_jmeter-example/archive/master.zip" target="_blank">here</a>. You can also prepare your own project, here are some guidelines.

Create a new Selenium/JUnit project with maven. Add dependencies to **selenium-java**, **selenium-server**, **selenium-remote-driver**, **phantomjsdriver** and **junit**. Add **maven-jar-plugin** plugin with **test-jar** goal.

<a href="http://phantomjs.org/" target="_blank">Download phantomjs</a> and unpack it. You either need to use absolute path in code, so JMeter would be able to get to phantomjs.exe file, or add it to resources and copy it automatically from there to temp file while testing.

{% highlight java %}
public static File getPhantomJs() {
  InputStream input = MethodsForTestsAbstract.class.getResourceAsStream("/phantomjs-windows/phantomjs.exe");
  OutputStream output;
  File phantomjs_exe = new File(System.getenv("TEMP") + "/phantomjs.exe");
  try {
    output = new FileOutputStream(phantomjs_exe);
    int read = 0;
    byte[] bytes = new byte[1024];
    while ((read = input.read(bytes)) != -1) {
    output.write(bytes, 0, read);
    }
    input.close();
    output.close();
  } catch (FileNotFoundException e) {
    e.printStackTrace();
  } catch (IOException e) {
    e.printStackTrace();
  }
  return phantomjs_exe;
}
{% endhighlight %}

We will be using **RemoteWebDriver** so we need to set capabilities for it:

{% highlight java %}
@Before
public void setUp() throws MalformedURLException {
  /*
   * Done based on: http://rationaleemotions.wordpress.com/2012/01/23/setting-up-grid2-and-working-with-it/
   * http://www.anzaan.com/2012/07/integrating-selenium-with-jmeter-for-application-load-testing/
   * http://code.google.com/p/selenium/wiki/Grid2
   */
  DesiredCapabilities caps = new DesiredCapabilities();
  caps.setJavascriptEnabled(true);
  caps.setBrowserName(DesiredCapabilities.phantomjs().getBrowserName());
  caps.setCapability("phantomjs.binary.path", phantomjs_exe_file.getAbsolutePath());
  caps.setCapability("phantomjs.page.settings.userAgent", user_agent);
  driver = new RemoteWebDriver(new URL(selenium_server), caps);
  // JMeter doesn't support @Rule, so taking test result and name needs to be done the "dirty" way, for taking
  // screenshot on fail.
  testResult = false;
  testName = "no_name";
}
{% endhighlight %}

As you can see, we can set user agent string here.

**RemoteWebDriver** will be trying to connect with Selenium Server over address http://localhost:4444/wd/hub, so we need to establish that server.

In project folder create **src\test\resources\selenium-server** folder and open it.

Download newest **selenium-server-standalone-x.jar** and save it to **selenium-server** folder. Now open notepad and paste this:

{% highlight bat %}
set SELENIUM_VERSION=2.35.0
set HUB_URL=http://localhost:4444/wd/hub
set PORT=5555
start java -jar selenium-server-standalone-%SELENIUM_VERSION%.jar -role hub
start java -jar selenium-server-standalone-%SELENIUM_VERSION%.jar -role node -hub %HUB_URL% -port %PORT% -nodeConfig config.txt
{% endhighlight %}

Change selenium version to the newest one. Save the file as **start-selenium-server.bat** file. Note that you must use the same selenium version here, in project pom file and later in JMeter lib folder.

You can change the port if you want, if port 5555 is busy at your machine, just remember to change it also in **config.txt**.

Once again open notepad and paste this:

{% highlight bat %}
{
  "capabilities":
  [
    {
      "browserName": "phantomjs",
      "maxInstances": 30,
      "takesScreenshot":true,
      "acceptSslCerts":true,
    },
  ],
  "configuration":
  {
    "proxy": "org.openqa.grid.selenium.proxy.DefaultRemoteProxy",
    "maxSession": 5,
    "port": 5555,
    "host": ip,
    "register": true,
    "registerCycle": 5000,
    "hubPort": 4444,
    "hubHost": ip,
  }
}
{% endhighlight %}

Save the file as **config.txt**. In this file we once again set the port to 5555. We also allow 30 instances of phantomjs driver. You can check default config file for selenium grid <a href="https://code.google.com/p/selenium/source/browse/java/server/src/org/openqa/grid/common/defaults/DefaultNodeWebDriver.json" target="_blank">here</a>.

Now we can start selenium server by just prepared **start-selenium-server.bat** file.

<figure>
	<img src="{{ '/assets/img/selenium_jmeter_selenium-server_1.png' | prepend: site.baseurl }}" alt=""> 
	<figcaption>Fig1. - Result of running start-selenium-server.bat.</figcaption>
</figure>

We can check whether it works by entering page: <a href="http://localhost:4444/wd/" target="_blank">http://localhost:4444/wd/</a>.

<figure>
	<img src="{{ '/assets/img/selenium_jmeter_selenium-server_2.png' | prepend: site.baseurl }}" alt=""> 
	<figcaption>Fig2. - Selenium server.</figcaption>
</figure>

Now we can check, whether our tests are working, by running them form eclipse. In my example project – one test should pass and one should fail and save screenshot. I left code showing how to start selenium-server automatically from code, but as in JMeter it would be called for every thread and loop, we need to start in manually.

One important thing to notice here: Jmeter does not support @Rule annotations, so as I mentioned before, I decided to use some simple solution and "set" each test to passed "manually":

{% highlight java %}
@Test
public void testToPass() {
  testName = getMethodName();
  driver.get("http://www.useragentstring.com/");
  assertTrue(!driver.findElement(By.id("uas_textfeld")).getText().isEmpty());
  testResult = true;
}
{% endhighlight %}

The testName variable is for naming the screenshots purpose.

Next, as everything is working from eclipse, we can move on to JMeter. Build the project with mvn, but skip tests: **mvn clean install –DskipTests**.

Copy just created **\*-1.0-SNAPSHOT-tests.jar** file from target folder to JMeter **lib\junit** folder. Copy **selenium-server-standalone-x.jar** to JMeter **lib** folder and also download **selenium-java-x.jar** and place it in the same **lib** folder.

Create new JMeter project. Add **ThreadGroup** to the test plan and add **Sampler – Junit Request** to the **Thread Group**. Check **Search for Junit 4 annotations (instead of JUnit 3)**. You should be able to choose witch test you want to run, they should be visible under Test Method.

Add some listener (for example **View Results Tree**), make sure that selenium-server is running and run your tests.

You can run up to 30 threads, as set in **config.txt** file, but you can increase it if you want, just mind your machine and connection efficiency.

All should work, in my example: one test should pass, one test should fail and save screenshot.

<figure>
	<img src="{{ '/assets/img/selenium_jmeter_jmeter.png' | prepend: site.baseurl }}" alt=""> 
	<figcaption>Fig3. - Selenium under JMeter run - results.</figcaption>
</figure>