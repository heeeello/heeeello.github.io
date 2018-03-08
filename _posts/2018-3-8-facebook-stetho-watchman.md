---
layout: post
title:  facebook opensource - stetho, watchman
---

##  stetho

A debug bridge for Android applications

Stetho is a sophisticated debug bridge for Android applications. When enabled, developers have access to the Chrome Developer Tools feature natively part of the Chrome desktop browser. Developers can also choose to enable the optional dumpapp tool which offers a powerful command-line interface to application internals.

可以把android应用程序集成到chrome dev tools： <br />
The integration with the Chrome DevTools frontend is implemented using a client/server protocol which the Stetho software provides for your application. Once your application is integrated, simply navigate to chrome://inspect and click "Inspect" to get started!

可以检查http网络访问： <br />
Network inspection is possible with the full spectrum of Chrome Developer Tools features, including image preview, JSON response helpers, and even exporting traces to the HAR format.
   
sqlite数据库内容检查：<br />
SQLite databases can be visualized and interactively explored with full read/write capabilities.

APP 视图层次结构：<br />
View hierarchy support for ICS (API 15) and up! Lots of goodies such as instances virtually placed in the hierarchy, view highlighting, and the ability to tap on a view to jump to its position in the hierarchy.

dumpapp：<br />
Dumpapp extends beyond the DevTools UI features shown above to provide a much more extensible, command-line interface to application components. A default set of plugins is provided, but the real power of dumpapp is the ability to easily create your own!

Javascript Console: <br />
Javascript Console allows for execution of javascript code that can interact with the application or even the Android SDK.

##watchman

A file watching service

Watchman exists to watch files and record when they change. It can also trigger actions (such as rebuilding assets) when matching files change.

例子: <br />
These two lines establish a watch on a source directory and then set up a trigger named buildme that will run a tool named minify-css whenever a CSS file is changed. The tool will be passed a list of the changed filenames.
```
$ watchman watch ~/src
# the single quotes around '*.css' are important!
$ watchman -- trigger ~/src buildme '*.css' -- minify-css
```
