# Git
## git操作不走代理
git config --global --unset https.proxy
git config --global --unset http.proxy

## 开启wayland debug日志
在终端：export WAYLAND_DEBUG=1
程序：qsetenv("WAYLAND_DEBUG","1");

## gcc编译的文件使用gdb调试
gcc 编译命令里面需要加上-ggdb3参数
例如：gcc -o test -ggdb3 test.c -lcrypt

## vscode下载慢
在浏览器的下载页面点击F12，查看下载，将域名修改为vscode.cdn.azure.cn，然后再下载就很快了
