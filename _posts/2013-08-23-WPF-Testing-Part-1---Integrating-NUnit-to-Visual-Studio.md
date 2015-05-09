---
layout: post
title:  "WPF Testing Part 1 – Integrating NUnit to Visual Studio"
date:   2013-08-23
categories: nunit visualstudio
---

You can use build in Test Explorer, just select **Test -&gt; Windows -&gt; Test Explorer**.

But if you prefer Nunit GUI, you can use following solution to set up starting tests with NUinit GUI.

Open Visual Studio and go to **Tools -&gt; External Tools** (if you don’t see this menu enable it in **Tools -&gt; Settings -&gt; Expert Settings**).

Add new external tool and name it NUinit. Set Command to path to **nunit.exe**, Arguments to **$(TargetName)$(TargetExt)**, Initail directory to **$(ProjectDir)\bin\Debug**. Notice that NUnit is 6th tool on the list on above screenshot, remember the position in which the added tool is in your case – you will need it in a moment.

Now add this external tool to a tool bar. To do so – go to **View -&gt; Toolbars -&gt; Customize**. Add new toolbar, name it for example NUnit Tools. Switch to **Commands tab**, mark **Toolbar** radio button, select **NUnit Tools** and click **Add Command...**, as category select **Tools** and as command select **External Command 6** (use the remembered position of NUint from External Tools list). Click OK and Close.

NUnit button should be now visible on your toolbar. Clicking the button should start NUnit GUI and load your test suite.

For described solution – credits goes to: <a href="http://www.marthijnvandenheuvel.com/2010/06/09/using-nunit-in-visual-studio-2010/" target="_blank">marthijnvandenheuvel.com</a>.

Go to parent post: <a href="/blog/Learning-WPF-testing-with-C-UIAutomatioin-and-NUnit/">Learning WPF testing with C# UIAutomatioin and NUnit</a>.
