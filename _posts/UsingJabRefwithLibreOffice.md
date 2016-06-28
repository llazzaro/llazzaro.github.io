---
title: Using JabRef with LibreOffice
date: 2016-06-27 23:37
lang: en
---

I Started to use JabRef as a database of BibTex reference for using it with LaTex and LibreOffce.
Googling around I found a [post](http://homepage.agrl.ethz.ch/~eugsterw/knowhow/jabref-libreoffice/) that were using a plugin to integrate JabRef with Libreoffice, however it was old and today is not necessary to instal a plugin.
Steps to integrate LibreOffice with JabRef

1. Configure LibreOffice

Go to **Preferences > LibreOffice > Advanced** and make sure that *"Use a Java runtime environment"* is checked and that a runtime is properly selected in the installed list.
Advanced settings should look like this:

![alt text](https://raw.githubusercontent.com/llazzaro/llazzaro.github.io/master/_posts/Screen%20Shot%202016-06-28%20at%2012.00.16%20AM.png "Logo Title Text 1")


2. Configure JabRef to use LibreOffice

* Go to **Tools -> OpenOffice/LibreOffice connection**. A new tab will appear on the left and select settings on the tab

![alt text](https://raw.githubusercontent.com/llazzaro/llazzaro.github.io/master/_posts/Screen%20Shot%202016-06-28%20at%2012.00.25%20AM.png "Logo Title Text 1")

Start using the "cite" button to add cites on  your articles, when you are done click on the icon for manual connect and use the path of LibreOffice, which usually is **/Applications/LibreOffice.app**.

