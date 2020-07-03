# bash

## 多行字符串

```bash
cat << EOF
1232
1231e41
124
EOF
```

## 进程替换

```bash
cat <(echo "afa")
```

这样，会用后面进程的输出替换前面进程的标准输入