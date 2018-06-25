---
layout: post
title:  "How to restore item from trash using python"
date:   2014-09-20
categories: python desktop
---

It is easy to find a python library that trashes files and folders on both Windows and OSX. Link to [Send2Trash](https://pypi.org/project/Send2Trash/) can be found on 1st page of google search.

What is not so easy to find, is how to restore something from trash.

On OSX its quite simple to do. Trashed items are just moved to special folder `~/.Trash`. Untrashing item is as simple, as moving the item back from `~/.Trash/original_item_name` to it's original path.

Windows is more tricky. Sure, when opening trash in UI (explorer.exe) we see items under their original names, but actual paths of items are hidden from user.

Moreover, even if we list path like e.g. `C:\$Recycle.Bin` the items there are just some uuids.

So how to do it on Windows? Use another python library called [winshell](http://winshell.readthedocs.io/en/latest/recycle-bin.html) and the method [`winshell.undelete(original_item_path)`](http://winshell.readthedocs.io/en/latest/recycle-bin.html#winshell.undelete). That's it, it's so simple. Enjoy.
