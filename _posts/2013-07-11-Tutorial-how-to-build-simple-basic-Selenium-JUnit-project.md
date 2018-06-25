---
layout: post
title:  "Tutorial how to build simple basic Selenium / JUnit project"
date:   2013-07-11
categories: general junit maven selenium java
---

For those of you who just started adventure with automation testing and programming, this is a step by step tutorial how to build simple basic Selenium / JUnit project. It's made for Windows user, as I myself am using Windows.

**Install Java JDK**

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Download</a> and install Java JDK
* Add **JAVA_HOME** variable to environment variables, set value to: *X:\path_to_java_jdk* (example: *D:\Program Files\Java\jdk1.7.0_03*)
* Add **%JAVA_HOME%\bin** to **Path** environment variable value

**Install Maven**

* <a href="http://maven.apache.org/download.cgi" target="_blank">Download</a> Maven
* Unpack; although not required it's better to unpack Maven in folder without spaces (ex. *X:\apache-maven*, NOT *X:\Program Files\apache-maven*)
* Add **M2_HOME** variable to environment variables, set value to: *X:\path_to_maven* (example: *D:\apache-maven*)
* Add **M2** variable to environment variables, set value to: *%M2_HOME%\bin*
* Add **%M2%** to **Path** environment variable value

**Install Cygwin**

* <a href="https://cygwin.com/install.html" target="_blank">Download</a> and install cygwin; although not required it’s better to install Cygwin in folder without spaces (ex. *X:\cygwin*, NOT *X:\Program Files\cygwin*)
* To run Cygwin choose Cygwin Terminal from menu start

**Add maven plugin to eclipse**

* In eclipse open **Help > Install new software**, use *http://download.eclipse.org/technology/m2e/releases* link

**Build new project**

* Run cygwin, move to folder where you want to create folder with your project, execute:

{% highlight bat %}
mvn archetype:generate -DgroupId=com.company.selenium.example -DartifactId=selenium-example -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
{% endhighlight %}

* In newly created folder find and open <a href="https://github.com/sobolewska/selenium-example/blob/master/pom.xml" target="_blank">pom.xml</a> (in notepad, notepad++ etc), add Selenium dependency and change JUnit version to 4.11 (or newer) (follow the link to see the code)
* Follow instructions from <a href="/blog/Surefire-report-with-screenshots-on-failure-with-JUnit-+-Selenium/" target="_blank">here</a>, but DO NOT configure your code yet
* In cygwin move to folder with **selenium-example** and execute **mvn clean install**
* Create folder **resources/drivers**
* <a href="http://code.google.com/p/chromedriver/downloads/list" target="_blank">Download</a> chrome win driver and save in drivers folder
* <a href="http://code.google.com/p/selenium/downloads/list" target="_blank">Download</a> ie 64bit and ie32bit driver and save in drivers folder
* Unpack drivers, rename **ie 32bit** driver as *IEDriverServer_32.exe*, and **ie 64bit** driver as *IEDriverServer_64.exe*
* Create **config.properties** file
* Open eclipse, choose **import - existing maven projects** in **Root Directory** find **selenium-example** folder, choose **pom.xml**, click Finish
* In **src/test/java / com.company.selenium.example** add classes: <a href="https://github.com/sobolewska/selenium-example/blob/master/src/test/java/com/company/selenium/example/MethodsForTestsAbstract.java" target="_blank"> MethodsForTestsAbstract</a>, <a href="https://github.com/sobolewska/selenium-example/blob/master/src/test/java/com/company/selenium/example/RunAll.java" target="_blank">RunAll</a>, <a href="https://github.com/sobolewska/selenium-example/blob/master/src/test/java/com/company/selenium/example/MyFirstTest.java" target="_blank">MyFirstTest</a> (follow the links to see the code for each class), remove **AppTest**.

Your project is ready! You can change browser you want to use by editing config file. Valid strings are: *BROWSER_TYPE=firefox* or *chrome*, *ie32*, *ie64* - but you can change that by editing **getBrowser(String b)** method.

**Running test from Eclipse**

* To run all tests open **RunAll.java**, run (Ctrl + F11), if asked choose Junit Launcher
* Screenshots of errors will be kept in: **/target/surefire-reports/imgs**

**Running test from cygwin**

* Run cygwin, move to folder with project (cd */cygdrive/drive/path_to_project/*) and execute **mvn test**
* Screenshots of errors will be kept in: **/target/surefire-reports/imgs**
* Html report will be kept in: **/target/surefire-reports**, use **index.html** to open. To always have a fresh copy of report, before running tests, manually remove whole **target** folder.

**Adding new tests**

* Each test class name should end with **Test** (ex. *someNameTest.java*)
* Each test in test class should be **public**, **void**, **no arguments**, and preceded by **@Test** annotation
* Each new test class should be added to **RunAll.java** to **@Suite.SuiteClasses** annotation

You can git clone whole example project from here: <a href="https://github.com/sobolewska/selenium-example.git" target="_blank">https://github.com/sobolewska/selenium-example.git</a>.
Build it with maven, import to eclipse as existing maven project.
