# stow

软连接管理器。格式为`stow <option> directory`。默认会将directory的**内容**链接到上一级目录，所以一般会把文件夹都放在主目录下的第一层目录。

+ -S 表示建立链接
+ -D 表示删除链接
+ -R 表示-D + -S 重新建立链接（当有新的文件时使用）