+++
date = '2017-08-21T10:08:35+05:30'
draft = false
title = "Listening to Memory events using onTrimMemory() on Android"
author = "Gurpreet"
tags = ["android", "migrated-from-medium"]
+++

Anyone who has been developing android application knows how memory-scarce the platform is, with each application being provided with [upto 24/32mb of memory](https://stackoverflow.com/questions/18675557/what-is-the-maximum-amount-of-ram-an-app-can-use) (which can go to as low as 16mb for older devices). In such a scenario, it is the developer’s duty to look out for memory leaks and free any resources that are not been used by the application.

One way of managing memory in response to system events is the [onTrimMemory() method](https://developer.android.com/reference/android/content/ComponentCallbacks2.html#onTrimMemory(int)). From the docs:
```
onTrimMemory():
Called when the operating system has determined that it is a good time for a process to trim unneeded memory from its process.
This will happen for example when it goes in the background and there is not enough memory to keep as many background processes running as desired.
```

Found in the ComponentsCallback2 interface, this method should be implemented to incrementally release memory based on current system constraints. This helps provide a more responsive system overall, and benefits the user experience for the application by allowing the system to keep the process alive longer.

For a better read on how to optimise the memory usage, do read the official docs, or show some love and I’ll post another article on Using More Memory-Efficient Code Constructs to create android applications. All kinds of feedback is welcome.

**References**:

1. Memory limits per app are here depending on screen size and Android version: https://drive.google.com/file/d/0B7Vx1OvzrLa3Y0R0X1BZbUpicGc/view
2. https://stackoverflow.com/questions/19398827/understanding-ontrimmemory-int-level

*Originally posted on Medium [link](https://medium.com/@gurpreetsk/memory-management-on-android-using-ontrimmemory-f500d364bc1a)*
