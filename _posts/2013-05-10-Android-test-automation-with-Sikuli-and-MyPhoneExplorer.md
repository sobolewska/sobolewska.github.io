---
layout: post
title:  "Android test automation with Sikuli and MyPhoneExplorer"
date:   2013-05-10
categories: android sikuli 
---

By using Sikuli and <a href="http://www.fjsoft.at/en/home.php" target="_blank">MyPhoneExplorer</a> you can automate testing for Android physical device! Here are instructions how to start using MyPhoneExplorer and run your phone screen on your PC.

1. Install Android ADB.
2. Install USB drivers for your test phone (drivers should be installed automatically after connecting the phone to the PC).
3. On your phone install <a href="https://play.google.com/store/apps/details?id=com.fjsoft.myphoneexplorer.client" target="_blank">MyPhoneExplorer Client</a>
4. On your PC install <a href="http://www.fjsoft.at/en/downloads.php" target="_blank">MyPhoneExplorer For PC</a>.
5. On your phone enable *USB debugging (Settings –> Applications –> Development)*
6. Connect phone to PC by USB (if the phone is not already connected).
7. On your phone start **MyPhoneExplorer Client**.
8. On your PC start **MyPhoneExplorer**.
9. In MyPhoneExplorer open *File –> Settings –> Connection* and set *Phone With Android OS* and *USB cable*.
10. In MyPhoneExplorer open *File -> Connect* and connect your phone.
11. In MyPhoneExplorer open *Extras –> Load Screenshot*.
12. In newly open window enable *Refresh automatically* option.

The screen of your phone will be now visible in the new window on your PC. You can perform actions from your PC and they will be done also on your phone – and vice versa – while performing actions on your phone, the PC view will be automatically updated.

Now you can do some Sikuli tests and automate tests for an Android physical device from PC level.
