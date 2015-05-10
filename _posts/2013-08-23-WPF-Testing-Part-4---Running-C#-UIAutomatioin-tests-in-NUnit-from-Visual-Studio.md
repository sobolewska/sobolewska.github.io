---
layout: post
title:  "WPF Testing Part 4 – Running C# UIAutomatioin tests in NUnit from Visual Studio"
date:   2013-08-23
categories: c# nunit visualstudio
---

**Running with Test Explorer**

To run with Test Explorer - select **Test -&gt; Windows -&gt; Test Explorer**.

Selecting categories is not possible in Test Explorer, but you can select tests tu run by fixtures or select couple of single tests with holding Ctrl.

Test output should be visible below tests list.

**Running with NUnit GUI**

If you did set up running with NUnit GUI – to run NUnit you just need to hit f5. If you only added the NUnit to toolbar, and you don’t have the Run.cs class, you can start NUnit by clicking the added toolbar button. In both cases – it should load your tests automatically. You can manually start NUnit (with nunit.exe) and then load test project by opening CalcWpfNunitTests\bin\Debug\CalcWpfNunitTests.dll file.

In NUnit GUI Categories tab you can choose what categories of test you want to start. In Tests tab you can choose what test fixtures you want to start. Click the Run button to start tests. 

In Errors and Failures tab you can check what tests failed and where the problem lies.

In Text Output tab you can check the log, which was logging by writing to the console prompt.

You can save the test results as xml file (Tools -&gt; Save Results as XML) – in the future maybe I will write some code to automatically create nice visual pdf or html report from it.

<figure>
	<img src="{{ '/assets/img/LearningWPF_5.png' | prepend: site.baseurl }}" alt=""> 
	<figcaption>Fig1. - Nunit GUI run results.</figcaption>
</figure>

<p class="intro">So that’s it for now, now you know as much as I do :)</p>

Go to parent post: <a href="/blog/Learning-WPF-testing-with-C-UIAutomatioin-and-NUnit/">Learning WPF testing with C# UIAutomatioin and NUnit</a>.