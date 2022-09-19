# Git

## git操作不走代理

git config --global --unset https.proxy
git config --global --unset http.proxy

## git https 设置代理

```shell
# 在~/.gitconfig中添加：
[http "https://github.com"]
        proxy = proxy02.uniontech.com:3128
```

## gcc编译的文件使用gdb调试

gcc 编译命令里面需要加上-ggdb3参数
例如：gcc -o test -ggdb3 test.c -lcrypt

## vscode下载慢

1.在浏览器的下载页面点击F12，查看下载，将域名修改为vscode.cdn.azure.cn，然后再下载就很快了
2.在终端使用apt更新速度也i

## linux 软连接

ln -s 源文件 目标文件。

## 打印qt的wayland日志

QT_LOGGING_RULES="qt.qpa.wayland=true"
