---
layout: post
title:  "Learning WPF testing with C# UIAutomatioin and NUnit"
date:   2013-08-24
categories: c# nunit uiautomatioin visualstudio 
---

For my new project I needed to learn how to test Windows Presentation Foundation applications. C# offers nice framework to test WPF apps, called <a href="http://msdn.microsoft.com/en-us/library/ms747327.aspx" target="_blank">UIAutomatioin</a>. And when it comes to "JUnit for C#" I decided to use free <a href="http://www.nunit.org/" target="_blank">NUnit</a>.

In the beginning I needed to Google **a lot** to find out how to use VisualStudio + C# UIAutomatioin + NUnit effectively. So I decided to prepare some tutorial for total beginners and noobs like me when it comes to WPF testing.

Notice I’m still considering myself as a total beginner in WPF testing – so be my guest and leave some comments, tips &amp; tricks, corrections and better solutions.

For this tutorial you will need to install:

* .Net 4+,
* Visual Studio (while writing this tutorial I was using VS2010 trial version)
* <a href="http://uiautomationverify.codeplex.com/" target="_blank">UIAVerify tool</a>
* <a href="http://www.nunit.org/" target="_blank">NUnit</a>

Now – download this <a href="https://github.com/sobolewska/CalcWpf/blob/master/CalcWpf/bin/Release/CalcWpf.exe" target="_blank">example application</a> that I did in C# WPF (you need only the exe file, but if you want, you can git clone whole project from here: https://github.com/sobolewska/CalcWpf.git). Please keep in mind that it wasn’t my goal to learn how to do WPF applications, so this calculator is not perfect, probably not optimal, etc, I wanted for my own "pleasure" write simple WPF app and then tests it.

For formatting / not-making-to-long-post purpose I divided this tutorial into 4 parts:

* <a href="/blog/WPF-Testing-Part-1---Integrating-NUnit-to-Visual-Studio/" target="_blank">WPF Testing Part 1 – Integrating NUnit to Visial Studio</a>
* <a href="/blog/WPF-Testing-Part-2---Using-UIAVerify-tool/" target="_blank">WPF Testing Part 2 – Using UIA Verify tool</a>
* <a href="/blog/WPF-Testing-Part-3---Writing-C%23-UIAutomatioin-+-NUnit-tests/" target="_blank">WPF Testing Part 3 – Writing C# UIAutomatioin + NUnit tests</a>
* <a href="/blog/WPF-Testing-Part-4---Running-C%23-UIAutomatioin-tests-in-NUnit-from-Visual-Studio/" target="_blank">WPF Testing Part 4 – Running C# UIAutomatioin tests in NUnit from Visual Studio</a>

Enjoy!