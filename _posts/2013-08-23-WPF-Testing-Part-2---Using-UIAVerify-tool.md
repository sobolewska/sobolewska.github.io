---
layout: post
title:  "WPF Testing Part 2 – Using UIAVerify tool"
date:   2013-08-23
categories: uiaverify
---

Download the <a href="http://uiautomationverify.codeplex.com/" target="_blank">UIAVerify</a> and unpack. To launch the tool use **VisualUIAVerify.exe**.

Now launch any WPF app (you can use <a href="https://github.com/sobolewska/CalcWpf/blob/master/CalcWpf/bin/Release/CalcWpf.exe" target="_blank">example app made by me</a>), but any windows app should work as well.

You can find visible elements by setting **Mode -&gt; Hover Mode (use Ctrl)** and then tracing elements with Ctrl button pressed.

If you can’t find your test app on the list of open apps, click the refresh button.

My example app should be visible as **"window" "CalcViewTitle" "CalcViewName"**.

<figure>
	<img src="{{ '/assets/img/LearningWPF_2.png' | prepend: site.baseurl }}" alt=""> 
	<figcaption>Fig1. - UIAVerify view.</figcaption>
</figure>

After expanding the tree you can see list of all child elements of the app. Clicking an element will highlight that element on the app itself and list element properties. As you can see, the naming patter is **"ClassName" "Name" "AutomationId"**. This corresponds to following xaml attributes: **&lt;ClassName Content/Title="Name" Name="AutomationId" &gt;**. This will be important later when coding the tests as you can call to **AutomationElement.NameProperty** or to **AutomationElement.AutomationIdProperty**.

I won’t explain how to build tests cases with the tool, as the whole point of this tutorial is to show how to test WPF apps with C# UIAutomatioin and NUinit. Besides, crating long and complicated test cases with UIAVerify is actually a little bit annoying.

<figure>
	<img src="{{ '/assets/img/LearningWPF_3.png' | prepend: site.baseurl }}" alt=""> 
	<figcaption>Fig2. - UIAVerify view with selected element.</figcaption>
</figure>

Go to parent post: <a href="/blog/Learning-WPF-testing-with-C-UIAutomatioin-and-NUnit/">Learning WPF testing with C# UIAutomatioin and NUnit</a>.
