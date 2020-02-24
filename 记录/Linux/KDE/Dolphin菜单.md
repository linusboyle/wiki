# Dolphin菜单
`$HOME/.local/share/kservices5/ServiceMenus`存放服务文件

用desktop格式编写，示例：
```desktop
[Desktop Entry]
Actions=FlatDir;
MimeType=inode/directory;
Type=Service
X-KDE-Priority=Toplevel
X-KDE-ServiceTypes=KonqPopupMenu/Plugin
Icon=inode-directory.png

[Desktop Action FlatDir]
Name=Flatten Directory
Name[zh_CN]=平铺目录
Icon=inode-directory.png
Exec=flat %f

```

添加后用kbuildsycoca5重建缓存。