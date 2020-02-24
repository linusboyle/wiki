# 简易图床配置与加速

写文章，尤其是非技术类的，总是会有一些展示图片的场合，而上传图床&获得图片连接则是工作流中的重要部分。

和很多应用一样，Linux目前还没有我满意的解决方案。现存的工具有[PicGo](https://github.com/Molunerfinn/PicGo)，[fu](https://github.com/klesh/fu/releases/tag/v1.2.0)等，其主要问题在于不够简洁。我希望能有一个直观的命令行工具来完成这件事，并且它能够整合系统剪贴板。我决定自己写一个小工具来完成这件事，并选择了Github作为图床，用jsdelivr加速。

## 核心

首先需要创建一个新的Github repo，进入[设置页面](https://github.com/settings/tokens)生成一个Personal access tokens，然后就可以使用Github API上传文件了。Github API的第四版换成了GraphQL，它对于大多数人来说比较晦涩，而且普及度也不如REST，因此我这里就用第三版的API。下面的脚本需要Python3和requests：

```python
#!/usr/bin/env python3
# encoding: utf-8
#Author: Zhilei Han <hzl17@mails.tsinghua.edu.cn> 

import requests, base64, json, time, random
import sys, os

def file_name(path):
    salt=random.randint(1, 9999)
    sp=os.path.splitext(path)
    if len(sp) == 2:
        ext = sp[1]
    else:
        ext = ""
    fn=time.strftime("%Y-%m-%d-%H-%M-%S-", time.localtime())+str(salt)+ext
    return fn

def get_base64(fn):
    with open(fn, 'rb') as f:
        return base64.b64encode(f.read()).decode('utf-8')

if __name__ == "__main__":
    if len(sys.argv) <= 1:
        print("No file provided")
        exit(2)
    else:
        path = sys.argv[1]
        tokens = "your token here"
        headers = {"Authorization": 'token '+tokens}

        fn = file_name(path)
        url = "https://api.github.com/repos/<user>/<repo>/contents/upload/"+ fn
        payload = {
        "message": "upload via cmd",
        "content": get_base64(path)
        }

        r = requests.put(url=url,data=json.dumps(payload), headers=headers)
        if r.status_code == 201:
            print('https://cdn.jsdelivr.net/gh/<user>/<repo>/upload/'+fn)

```
在上面的脚本中，填入token，并替换用户名和仓库名即可。

不过，在Python里访问系统剪贴板比较麻烦，需要装一些额外的包。因此这项工作就交给shell来做就行了。这个小包装脚本需要安装xclip：

```bash
#!/usr/bin/env bash
#Author: Zhilei Han <hzl17@mails.tsinghua.edu.cn> 

ghupload.py "$@" | xclip -selection clipboard

if [ $? -eq 0 ] ;then
    notify-send "GH Upload" "Upload Successful" -a "GH Uploader"
else
    notify-send "GH Upload" "Upload Failed" -u critical -a "GH Uploader"
fi

```

## KDE整合

### Dolphin

如果能在文件管理器中直接上传是很方便的，而KDE提供了一种办法自定义动作。想要在Dolphin中添加一项动作的话，只需要在`.local/share/kservices5/ServiceMenus`目录下创建一个desktop文件，并设置好MIME和动作：

```desktop
[Desktop Entry]
Actions=GHUpload;
MimeType=image/jpeg;image/png;
Type=Service
X-KDE-Priority=Toplevel
X-KDE-ServiceTypes=KonqPopupMenu/Plugin
Icon=image-x-generic.png

[Desktop Action GHUpload]
Name=Upload to Github
Name[zh_CN]=上传Github图床
Icon=image-x-generic.png
Exec=ghupload %f
```

效果：
![Dolphin](https://cdn.jsdelivr.net/gh/linusboyle/imgupload/upload/2020-02-14-16-56-55-3117.png)

### Klipper

此外，KDE的剪贴板也允许自定义动作，这次直接在Klipper的配置页面进行操作即可:

![Klipper](https://cdn.jsdelivr.net/gh/linusboyle/imgupload/upload/2020-02-14-16-46-39-3088.png)