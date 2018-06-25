---
layout: post
title:  "WPF Testing Part 3 – Writing C# UIAutomatioin + NUnit tests"
date:   2013-08-23
categories: c# nunit uiautomatioin
---

In the beginning I recommend to go through <a href="http://msdn.microsoft.com/en-us/magazine/dd483216.aspx" target="_blank">this tutorial written by James McCaffrey</a>, it’s a good start for beginners. I used couple of solutions from there in my example.

But if you want you can go straight to my example test project – <a href="https://github.com/sobolewska/CalcWpfNunitTests/archive/master.zip" target="_blank">download it here</a> or git clone form here: https://github.com/sobolewska/CalcWpfNunitTests.git and open it in Visual Studio (it includes the test app).

I started with creating new **Visual C# Console Application** solution – **CalcWpfNunitTests**. I added second project to existing solution – **RunTests**. Then, as you can see, I added bunch of needed references to **CalcWpfNunitTests** project. I also set the **RunTests** project as the single startup project in solution properties.

<figure>
	<img src="{{ '/assets/img/LearningWPF_4.png' | prepend: site.baseurl }}" alt=""> 
	<figcaption>Fig1. - References of CalcWpfNunitTests.</figcaption>
</figure>

I won’t describe how to use C# UIAutomatioin, as there are a lot of tutorials online (for example the one that I mentioned in the beginning of this post), instead, I will just highlight couple of worth mentioning things here. I tried to left many comments in the code, for it to be self-explanatory, so you can go straight to the code if you prefer code-reading style of learning.

You can start up tests with build in Test Explorer or run them with Nunit GUI. To run with NUnit GUI try <a href="http://www.damirscorner.com/UnitTestingWithNUnitInVisualC2010Express.aspx" target="_blank">this trick</a>. Thanks to it you can run your test project straight to NUnit with just one f5 click, without setting anything in project properties. I placed that code in a separate project in the same solution (class **Run.cs**), and set that project to be the startup project.

{% highlight csharp %}
using System;
using System.IO;
using System.Reflection;

namespace RunTests
{
  /*
   * For this solution credits goes to: http://www.damirscorner.com/UnitTestingWithNUnitInVisualC2010Express.aspx
   * Thanks to this class, and the fact that it is set as single startup project, you can run your test project straight to NUnit with just one f5 click.
   * */
  class Run
  {
    // path to latest NUnit bin folder
    private static readonly string nunitFolder = @"C:\Program Files (x86)\NUnit 2.6.2\bin";
	
    // if you prefer you can set "NUNIT" environment variable with NUnit bin folder path and then use: Environment.GetEnvironmentVariable("NUNIT");
    // Without this attribute "DragDrop registration did not succeed" exception occurs while starting without debugging. For more info check: http://stackoverflow.com/questions/135803/dragdrop-registration-did-not-succeed
    [STAThread]
    public static void Main()
    {
      AppDomainSetup setup = new AppDomainSetup();
      setup.ApplicationBase = nunitFolder;
      setup.ConfigurationFile = Path.Combine(nunitFolder, "NUnit.exe.config");
      AppDomain nunitDomain = AppDomain.CreateDomain("NUnit", null, setup);
      nunitDomain.ExecuteAssembly(Path.Combine(nunitFolder, "NUnit.exe"), new string[] { Path.Combine(Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location), @"..\..\..\CalcWpfNunitTests\bin\Debug\CalcWpfNunitTests.dll") });
    }
  }
}
{% endhighlight %}

As I like simple txt properties file I decided to prepare **PropertiesReader.cs** class. As described in comment inside that class, it reads lines that start with A-Z, a-z, 1-9 and _ sign, and as a value it saves string after = sign.

{% highlight csharp %}
using System;
using System.Collections.Generic;
using System.IO;

namespace CalcWpfNunitTests.Tests
{
  public class PropertiesReader
  {
    public PropertiesReader(string fileName)
    {
      ReadDictionaryFile(fileName);
    }
    private Dictionary _dictionary = null;
    private void ReadDictionaryFile(string fileName)
    {
      _dictionary = new Dictionary();
      /*
       * property name should start with letter, number or _
       * property name can contain letters, numbers and _
       * values can be in format: ="value", ='value', or =value
       * spaces can be used only in value, do not use space before =
       * */
      string sPattern = "^[a-zA-Z1-9_]*=.*";
      foreach (string line in File.ReadAllLines(fileName))
      {
        if (System.Text.RegularExpressions.Regex.IsMatch(line, sPattern))
        {
          int index = line.IndexOf('=');
          string key = line.Substring(0, index).Trim();
          string value = line.Substring(index + 1).Trim();
          // take value, remove "" or ''
          if ((value.StartsWith("\"") && value.EndsWith("\"")) || (value.StartsWith("'") && value.EndsWith("'")))
          {
            value = value.Substring(1, value.Length - 2);
          }
          _dictionary.Add(key, value);
        }
      }
    }
    public string GetProperty(string key, string defaultValue)
    {
      // If key doesn't exist, return default value
      return String.IsNullOrEmpty(_dictionary[key]) ? defaultValue : _dictionary[key];
    }
  }
}
{% endhighlight %}

Then in **CalcWpfTestsAbstract.cs** I’m setting all the properties.

{% highlight csharp %}
#region Properties
public static PropertiesReader _propertiesReader = new PropertiesReader(@"..\..\..\properties.properties");
public static string _appPath = _propertiesReader.GetProperty("app_path", @"..\..\..\CalcWpf.exe");
public static string _appName = _propertiesReader.GetProperty("app_name", "CalcViewTitle");
public static bool _screenShotsFolderOveride = bool.Parse(_propertiesReader.GetProperty("screenshots_folder_overide", "false"));
public static string _screenshotsFolder = SetScreenshotFolder(_propertiesReader.GetProperty("screenshots_folder", @"..\..\..\Screenshots"));
#endregion
{% endhighlight %}

Now we will go through **CalcWpfTestsAbstract.cs** class. You can notice I’m using **Log()** method, but as I didn’t want to add to much unnecessary code, this "log" it just outputting to console prompt. **SetUp()** starts the test app before each test, and **TearDown()** takes screenshot in case of failure result of the test and closes the app after each test.

**GetTestAppMainWindow()** method is based on James McCaffrey tutorial – it checks that the test app exists, starts it and looks for the main window. It will throw an exception when something goes wrong, and mark the whole test as failed with **Assert.Fail()** method. As you can see I’m using **AutomationElement.NameProperty** to look for the main window, so I need to use the **Name** string from UIAVerify (which corresponds to **Title** attribute in xaml).

{% highlight csharp %}
public void GetTestAppMainWindow()
{
  try
  {
    (...)
    // Look for main window of the test app, look by the main window name (title in xaml) attribute
    count = 0;
    do
    {
      Log("Looking for main window...");
      _aMainWindow = aeDesktop.FindFirst(TreeScope.Children, new PropertyCondition(AutomationElement.NameProperty, _appName));
      count++;
      Thread.Sleep(1000);
    } while (_aMainWindow == null && count &lt; 60);
    if (_aMainWindow == null)
    {
      throw new Exception("Failed to find element with name " + _appName);
    }
    else
    {
      Log("Found element with name " + _appName);
    }
  }
  catch (Exception ex)
  {
    Log(ex.Message);
    Assert.Fail(ex.Message);
  }
}
{% endhighlight %}

There is one little trick that I decided to use that I will mention now. All NUnit assert methods are throwing exceptions when the conditions are not met – it doesn’t matter when starting without debugging, as the test is marked as failed and the whole suite continues, but (by default) starting without debugging requires pushing two keys Ctrl+f5 and, because I’m lazy, I wanted to be able to start my tests by single f5 push ;) I found out a solution for this <a href="http://social.msdn.microsoft.com/Forums/en-US/f7e89111-5cdc-440d-84c8-960c34034b85/how-to-make-a-coded-ui-test-be-set-to-failed-when-an-exception-is-caught-using-trycatch-blocks" target="_blank">here (post by Saurabh Arora)</a>. Basically you just need to add new **Common Language Runtime Exceptions** exception named **NUnit.Framework.AssertionException** in **Debug -&gt; Exceptions...**, and uncheck **Thrown** and **User-unhandled**.

To show that its possible, I created own **AsserEquals()** method to add some logging info. Here the logging is redundant to NUnit Assert method, but you could add a screen-shot method, more logging, machine state capture, etc.

{% highlight csharp %}
public void AsserEquals(string expected, string actual)
{
  Log("Expected = " + expected + ", actual = " + actual);
  Assert.AreEqual(expected, actual);
}
{% endhighlight %}

In **FindElementWithId()** method I again used **Assert.Fail()** trick to mark the test as failed when element is not found.

{% highlight csharp %}
public AutomationElement FindElementWithId(string id, AutomationElement parent)
{
  AutomationElement element = parent.FindFirst(TreeScope.Children, new PropertyCondition(AutomationElement.AutomationIdProperty, id));
  try
  {
    (...)
  }
  catch (Exception ex)
  {
    // Fail test in case of not finding the element
    Log(ex.Message);
    Assert.Fail(ex.Message);
  }
  return element;
}
{% endhighlight %}

The **SetScreenshotFolder(string screenshotsFolder)** method sets the folder for screenshots. When **screenshots_folder_overide** property is set to **true**, it removes the default screenshots folder (set by **screenshots_folder** property) and its entire content. When **screenshots_folder_overide** property is set to **false** it creates new folder with timestamp in name.

{% highlight csharp %}
public static string SetScreenshotFolder(string screenshotsFolder)
{
  // If screen_shots_folder property ends with \, remove last character
  if (screenshotsFolder.EndsWith("\\"))
  {
    screenshotsFolder = screenshotsFolder.Substring(0, screenshotsFolder.Length - 1);
  }
  // If screen_shots_folder_overide is set to true, delate whole existing ScreenShots folder with everyting inside
  if (_screenShotsFolderOveride)
  {
    Directory.Delete(screenshotsFolder, true);
  }
  // If screen_shots_folder_overide is set to false, create new ScreenShots folder with timestamp in name
  else
  {
    screenshotsFolder = screenshotsFolder + DateTime.Now.ToString("_dd-MM-yyyy_HH-mm-ss");
  }
  Directory.CreateDirectory(screenshotsFolder);
  return screenshotsFolder;
}
{% endhighlight %}

The **TakeScreenShot()** method saves a jpg screenshot of the test app, the screenshot name patter is **NameOfTheClass_NameOfTheTest.jpg**.

{% highlight csharp %}
public void TakeScreenShot()
{
  // Gets main test window bounds in form: X;Y;Width;Height
  System.Windows.Rect aMainWindowBounds = _aMainWindow.Current.BoundingRectangle;
  // Creates new bounds based on aMainWindowBounds in form: {X=X,Y=Y,Width=Width,Height=Height}
  Rectangle bounds = new Rectangle(Convert.ToInt32(aMainWindowBounds.X), Convert.ToInt32(aMainWindowBounds.Y), Convert.ToInt32(aMainWindowBounds.Width), Convert.ToInt32(aMainWindowBounds.Height));
  using (Bitmap bitmap = new Bitmap(bounds.Width, bounds.Height))
  {
    using (Graphics g = Graphics.FromImage(bitmap))
    {
      g.CopyFromScreen(new Point(bounds.Left, bounds.Top), Point.Empty, bounds.Size);
    }
    // Save the screenshot as ClassName_MethodName.jpgl
    string[] tmp = TestContext.CurrentContext.Test.FullName.Split('.');
    bitmap.Save(_screenshotsFolder + "\\" + tmp[tmp.Length - 2] + "_" + tmp[tmp.Length - 1] + ".jpg", ImageFormat.Jpeg);
  }
}
{% endhighlight %}

I didn’t do the full set of test cases, I added just a couple of them under **Tests.TestCases** to show some basic things. All three test cases classes are extending the **CalcWpfTestsAbstract.cs** class. With the use of **[TestFixture]** and **[Category("name_of_category")]** attributes you can group your tests. You will find more about fixtures and categories in the next part of this tutorial (<a href="/blog/WPF-Testing-Part-4---Running-C%23-UIAutomatioin-tests-in-NUnit-from-Visual-Studio/" target="_blank">Running C# UIAutomatioin tests in NUnit from Visual Studio</a>).

The first thing I would like to highlight here is comparing expected and actual result. Test **TestOrderAddDivSub()** is computing equation (1 + 2 / 3 – 4) witch result with -2.33333333... As I don’t really know how many 3 there will be, I do not use a magic string here as expected value, instead I just put the whole equation to calculate the expected value by itself.

{% highlight csharp %}
AsserEquals((1.0 + 2.0 / 3.0 - 4.0).ToString(), GetResultValue());
{% endhighlight %}

In test **TestOrderAddMulSqrt()** my calculator will return a slightly different value than the equation computed in the expected value. This is because my calculator is operating on strings and its rounding the results. So – we can treat this as error or assume some rounding. The full result for this equation returned by system calculator is 4,4641016151377545870548926830117, my calculator returns 4,46410161513776 which is the full result rounded to 14 digits, so we can assume that it is correct, and compare values rounded to 13 digits.

{% highlight csharp %}
AsserEquals(Math.Round(1.0 + 2.0 * Math.Sqrt(3.0), 13).ToString(), Math.Round(Double.Parse(GetResultValue()), 13).ToString());
{% endhighlight %}

Second thing to mention is that test **TestOrderAddMulSubToFail()** expects wrong value, so it should fail.

Go to parent post: <a href="/blog/Learning-WPF-testing-with-C-UIAutomatioin-and-NUnit/">Learning WPF testing with C# UIAutomatioin and NUnit</a>.
