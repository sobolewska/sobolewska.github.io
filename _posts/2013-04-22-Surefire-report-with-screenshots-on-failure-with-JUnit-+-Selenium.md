---
layout: post
title:  "Surefire report with screenshots on failure with JUnit + Selenium"
date:   2013-04-22
categories: junit maven selenium java
---

Open **pom.xml** of your project. Change JUnit version to 4.11 (or newer), make sure other dependencies (like Selenium or Sikuli) are added. In **&lt;project&gt;** nod, after **&lt;dependencies&gt;** nod, add:

{% highlight xml %}
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.1</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-surefire-plugin</artifactId>
      <version>2.14.1</version>
      <configuration>
        <testFailureIgnore>true</testFailureIgnore>
      </configuration>
    </plugin>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-antrun-plugin</artifactId>
      <version>1.7</version>
      <executions>
        <execution>
          <id>test-reports</id>
          <phase>test</phase>
          <configuration>
            <tasks>
              <mkdir dir="target/surefire-reports"/>
              <junitreport todir="target/surefire-reports">
                <fileset dir="target/surefire-reports">
                  <include name="**/*.xml"/>
                </fileset>
                <report styledir="resources/reportstyle" format="frames" todir="target/surefire-reports"/>
              </junitreport>
            </tasks>
          </configuration>
          <goals>
            <goal>run</goal>
          </goals>
        </execution>
      </executions>
      <dependencies>
        <dependency>
          <groupId>org.apache.ant</groupId>
          <artifactId>ant-junit</artifactId>
          <version>1.9.0</version>
        </dependency>
      </dependencies>
    </plugin>
  </plugins>
</build>
{% endhighlight %}

Create folder **resources/reportstyle**. You can keep your report style file in a different folder if you want, just remember to change **styledir** in above code. Download <a href="http://svn.apache.org/repos/asf/synapse/tags/M2/etc/junit-frames.xsl" target="_blank">junit-frames.xsl</a> and save it in the **reportstyle** folder. Open **junit-frames.xsl** and modify:

Change:

{% highlight xslt %}
xmlns:redirect="org.apache.xalan.lib.Redirect"
{% endhighlight %}

To:

{% highlight xslt %}
xmlns:redirect="http://xml.apache.org/xalan/redirect"
{% endhighlight %}

After:

{% highlight xslt %}
<th>Name</th>
<th>Status</th>
<th width="80%">Type</th>
<th nowrap="nowrap">Time(s)</th>
{% endhighlight %}

Add:

{% highlight xslt %}
<!-- ADDED -->
<th>Screenshot</th>
{% endhighlight %}

After (the second occurrence):

{% highlight xslt %}
<td>
 <xsl:call-template name="display-time">
  <xsl:with-param name="value" select="@time"/>
 </xsl:call-template>
</td>
{% endhighlight %}

Add:

{% highlight xslt %}
<!-- ADDED -->
<xsl:choose>
 <xsl:when test="failure">
  <td><a href="../../../../../surefire-reports/screenshots/{../@name}_{@name}.png" target="_blank">screenshot</a>
  </td>
 </xsl:when>
 <xsl:when test="error">
  <td><a href="../../../../../surefire-reports/screenshots/{../@name}_{@name}.png" target="_blank">screenshot</a>
  </td>
 </xsl:when>
 <xsl:otherwise>
  <td></td>
 </xsl:otherwise>
</xsl:choose>
{% endhighlight %}

Execute "**mvn clean install**" for your project.

Now configure your code to save failure screenshots in folder **target/surefire-reports/screenshots** (check how <a href="/blog/Screenshot-on-fail-in-JUnit-+-Selenium/" target="_blank">here</a>).

Run your tests by executing "**mvn test**". A simple html/frame based report with links to open screenshots will be saved in **target/surefire-reports**. Open **index.html** to see the report.

To always have a fresh copy of report, before running tests, manually remove whole **target** folder.

You can download whole example project from <a href="https://github.com/sobolewska/selenium-example" target="_blank">github</a>.
