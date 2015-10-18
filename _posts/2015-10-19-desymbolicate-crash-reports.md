---
layout: post
title: Desymbolicate crash reports from the command line
tags: iOS
comments: true
author: michal
---

I was struggling again with desymbolicating of iOS app crash logs and thought that it would be nice
to have a handy script that is a bit easier to use than the [multi step process](http://stackoverflow.com/a/4954949/59666) I had been using thus far. There's no rocket science in it,
 just a simple script named **desym** that seems to work for me and probably will require more than
 one adjustment to work in general.

The source code is [here on GitHub](https://github.com/bright/desym).

The idea is to put .ipa and .dsym.zip file in the same directory and then execute
```./desym.sh MEMORY_ADDRESS(ES)```. It will automatically unpack dSYM and ipa
into temporary directory, run ```atos```, output results to the console and clean after itself.

What it means for me is that I can just download respective ipa and dSYM files
from Hockeyapp, run **desym** with memory address from the crash log and
see the faulty line right away without manual files juggling.
