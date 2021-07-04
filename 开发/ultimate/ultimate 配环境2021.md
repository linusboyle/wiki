# ultimate 配环境2021

首先按照[官方wiki](https://github.com/ultimate-pa/ultimate/wiki/Installation)的步骤进行配置。下面的流程适用于2021年的ultimate开发版本（baseline commit：fb64588fcb487699d5566f24aa277c301b8275d3），后续如果发生变化，也可以试试，但不保证成功。

在加载platform的时候，需要通畅的vpn连接。最好不用代理（在preference->general->network connection里可以设置，eclipse似乎不遵守kde的全局代理设置）。位置在德国最好。

platform完全加载之后，应该大部分的error就解决了。如果剩下和JAXB有关的错（错误里有jaxb什么的），应该是来源于系统的jaxb和第三方库里的jaxb冲突。在`trunk/source/Library-UltimateModel/META-INF/MANIFEST.MF`里，做如下修改：

```
-Require-Bundle: javax.xml.bind;bundle-version="2.2.0";visibility:=reexport
+Import-Package: com.sun.xml.bind.v2;version="2.2.0"
```

这里把依赖的包指定为了第三方的，之后应该就不会有相关的错了。clean之后build all。

如果还有问题，可以试试在所有project上右击，选择`refresh`进行刷新，更新某些缓存。如此反复。