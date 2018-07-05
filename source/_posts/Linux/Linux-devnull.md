---
title: Linux /dev/null
categories: D-Linux
permalink: linux-/dev/nul
translate_title: linux-/dev/null
date: 2015-05-08 11:43:33
tags:
description:
---
## /dev/null
On UNIX, this is a virtual-file that can be written to. Data written to this file gets discarded.   

It is similar to the file call NUL on Windows machines. Key point: When rooting a machine, intruders will often redirect logging to /dev/null For example, the command ln -s /dev/null .bash_history will cause the system to stop logging bash commands.   

Culture: In the vernacular, means much the same thing as black hole.  

It discards all the data written to it and sends EOF (End of File) character to any process reading data from it. 