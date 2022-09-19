# keyring相关

## gnome-keyring基本知识

1. 安装包 gnome-keyring libpam-gnome-keyring
2. keyring保存密钥的路径：~/.local/share/keyrings
3. login.keyring保存是的密钥内容，一般情况下是加密的，如果把keyring的密码设置为""， 那么会以明文的显示出来