---
title: 空格还是tab？
date: 2017-09-09 10:40:22
tags: Other
---
代码缩进，其实是个很严肃的问题!
<!-- more -->
在某次向Github提交代码的过程我突然发现，无论我的代码在本地排版的多么完美，提交到GitHub后都会出现缩进不一致的问题，来来回回试过很多次，无论用markdown格式还是直接提交.java文件格式，而这，是在之前从未出现过的, 如下图：
{% asset_img java-error.PNG 缩进错误 %}
转而google，经过多方求证，貌似发现了问题的所在：因为一直以来我写代码时都是习惯以tab进行缩进，在本地，无论是在IDE中还是在sublime之类的文本编辑器中都是默认为4个空格的缩进，但提交到了Github后，会被显示为8个空格，但这不是造成问题的主要原因，问题可能在于我在使用tab的同时可能还不经意间使用了空格进行缩进。
为了验证事实是否如此，我重新写了一遍代码（反正没几行），确保全程只使用了tab进行缩进，最后提交代码的终于缩进正常，我的伪强迫症也得以缓一缓。。。
{% asset_img java.PNG 缩进正确 %}
最后的最后，我发现居然有很大一部分人是在用空格来进行缩进。。。不由觉得自己有几分非主流。其实空格和tab各有利弊，使用空格在各种环境下都有一致的显示效果，这一点在多人合作开发中很重要，而tab在不同的环境下可能会出现格式问题（如果引入了空格会更糟糕），但用起来更舒服。其实格式问题并不是很大，毕竟某些IDE中一个快捷键就能够快速完成排版，保持代码清爽不油腻。
