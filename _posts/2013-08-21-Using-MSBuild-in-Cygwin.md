---
layout: post
title:  "Using MSBuild in Cygwin"
date:   2013-08-21
categories: cygwin msbuild
---

Run Cygwin and run **echo $HOME** – it should return your Cygwin home folder location.

{% highlight bat %}
$ echo $HOME
/home/username
{% endhighlight %}

Go to that folder (**cd /home/username**) and run **vim .bash_profile** (use vi if vim is not installed), at the end of the file add:

{% highlight bat %}
alias vsYYYY="cd /cygdrive/X/Users/username/Documents/Visual\ Studio\ YYYY/Projects"
alias csc="/cygdrive/c/Windows/Microsoft.NET/Framework/v4.z.zzzzz/csc.exe"
alias msbuild="/cygdrive/c/Windows/Microsoft.NET/Framework/v4.z.zzzzz/msbuild.exe"
alias csc64="/cygdrive/c/Windows/Microsoft.NET/Framework64/v4.z.zzzzz/csc.exe"
alias msbuild64="/cygdrive/c/Windows/Microsoft.NET/Framework64/v4.z.zzzzz/msbuild.exe"
{% endhighlight %}

Where **X** is your drive with **Users** folder, **YYYY** is your Visual Studio version and **z.zzzzz** is your .NET4 version. If you want you can use different folder for C# projects, I decided to use the default one provided by Visual Studio.

Restart Cygwin for the changes to take effect.

Now you can quickly go to your C# projects folder by just typing **vsYYYY**. You can also use c# compiler by using command **csc** (or **csc64** for 64bit version) and Microsoft Build Engine by using command **msbuild** (or **msbuild64** for 64bit version).

Next – follow instructions from <a href="http://msdn.microsoft.com/en-us/library/dd576348.aspx" target="_blank">http://msdn.microsoft.com/en-us/library/dd576348.aspx</a> to learn basics of MSBuild – just remember to use unix-style commands instead of windows-style commands (ex. mkdir instead of md, etc).

Notice – when setting the PATH – I needed to manually change it in environment variables, as changing through Cygwin was not working.