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

## deepin导出日志
```shell
#!/bin/bash

username=$(whoami)
if [ "$username" == "root" ];then
	echo -en "请以非sudo的方式运行此脚本\n"
	exit 0
fi

#lines=98

enable_debug_log(){
	##安装systemd-coredump
	echo " 安装systemd-coredump"
	# tail -n +$lines $0 > /tmp/systemd-coredump.deb
	# sudo dpkg -i /tmp/systemd-coredump.deb
	# apt policy systemd-coredump
	sudo apt install systemd-coredump -y
	##dde-session-daemon & startdde
	sudo bash -c "echo 'export DDE_DEBUG_LEVEL=debug' >> /etc/profile "
	echo " 开启dde debug log-level "
	##pulseaudio
	echo " 开启pulseaudio debug log-level "
	sudo sed -i "s/; log-target = auto/; log-target = journal/g" /etc/pulse/daemon.conf
	sudo sed -i "s/; log-level = notice/; log-level = 4/g" /etc/pulse/daemon.conf
	sudo sed -i "s/; log-meta = no/; log-meta = 1/g" /etc/pulse/daemon.conf
	sudo sed -i "s/; log-time = no/; log-time = 1/g" /etc/pulse/daemon.conf
	sudo sed -i "s/; log-backtrace = 0/; log-backtrace = 0/g" /etc/pulse/daemon.conf
	##networkmanager
	echo " 开启网络模块debug log-level "
	sudo sed -i "s/ExecStart=\/usr\/sbin\/NetworkManager --no-daemon/ExecStart=\/usr\/sbin\/NetworkManager --log-level=TRACE --no-daemon /g" /lib/systemd/system/NetworkManager.service
	sudo sed -i "s/ExecStart=\/sbin\/wpa_supplicant -u -s -O \/run\/wpa_supplicant/ExecStart=\/sbin\/wpa_supplicant -u -K -dd -O \/run\/wpa_supplicant/g" /lib/systemd/system/wpa_supplicant.service
	sudo systemctl daemon-reload
	##kernerl log
	echo " 开启内核日志:loglevel=7"
	sudo sed -i 's/quiet//g' /etc/default/grub
	sudo sed -i 's/loglevel=0/loglevel=7/g' /etc/default/grub
	sudo mount -o remount,rw /boot
	sudo update-grub
	echo " 设置完毕，重启系统后生效 "
	exit 1
}

disable_debug_log(){
	##安装systemd-coredump
	echo " 卸载systemd-coredump"
	# tail -n +$lines $0 > /tmp/systemd-coredump.deb
	# sudo dpkg -i /tmp/systemd-coredump.deb
	# apt policy systemd-coredump
	sudo apt purge systemd-coredump -y
	##dde-session-daemon & startdde
	sudo sed -i "s/export DDE_DEBUG_LEVEL=debug/d" /etc/profile
	echo " 关闭dde debug log-level "
	##pulseaudio
	echo " 关闭pulseaudio debug log-level "
	sudo sed -i "s/; log-target = journal/; log-target = auto/g" /etc/pulse/daemon.conf
	sudo sed -i "s/; log-level = 4/; log-level = notice/g" /etc/pulse/daemon.conf
	sudo sed -i "s/; log-meta = 1/; log-meta = no/g" /etc/pulse/daemon.conf
	sudo sed -i "s/; log-time = 1/; log-time = no/g" /etc/pulse/daemon.conf
	sudo sed -i "s/; log-backtrace = 0/; log-backtrace = 0/g" /etc/pulse/daemon.conf
	##networkmanager
	echo " 关闭网络模块debug log-level "
	sudo sed -i "s/ExecStart=\/usr\/sbin\/NetworkManager --log-level=TRACE --no-daemon/ExecStart=\/usr\/sbin\/NetworkManager --no-daemon /g" /lib/systemd/system/NetworkManager.service
	sudo sed -i "s/ExecStart=\/sbin\/wpa_supplicant -u -K -dd -O \/run\/wpa_supplicant/ExecStart=\/sbin\/wpa_supplicant -u -s -O \/run\/wpa_supplicant/g" /lib/systemd/system/wpa_supplicant.service
	sudo systemctl daemon-reload
	##kernerl log
	echo " 关闭内核日志:loglevel=0"
	sudo sed -i 's/loglevel=7/quiet loglevel=0/g' /etc/default/grub
	sudo mount -o remount,rw /boot
	sudo update-grub
	echo " 重置完毕，重启系统后生效 "
	exit 1
}
export_logs(){
	mkdir logs -v
	mkdir coredumps -v
	sudo journalctl -b -0 > logs/journal-b0.log
	sudo journalctl -b -1 > logs/journal-b-1.log
	sudo coredumpctl > logs/coredumpctl.log
	sudo dmesg -T > logs/dmesg.log
	sudo journalctl -b /usr/bin/startdde > logs/journal-startdde.log
	sudo journalctl -b /usr/lib/deepin-daemon/dde-session-daemon > logs/journal-dde-session-daemon.log
	sudo journalctl -b 0 -u wpa_supplicant > logs/journal-wpa-supplicant.log
	sudo journalctl -b 0 -u NetworkManager > logs/journal-NetworkManager.log
	sudo cp -vr --parents /var/lib/systemd/coredump coredumps/
	cp -vr --parents $HOME/deepin-recovery-gui.log logs/
	cp -vr --parents $HOME/.kwin*.log logs/
	cp -vr --parents $HOME/.cache/deepin logs/
	cp -vr --parents $HOME/.cache/uos logs/
	rm -rf logs$HOME/.cache/deepin/deepin-deepinid-client/
	journalctl --user-unit pulseaudio.service > logs/pulse.log
	sudo cp -vr --parents /var/log/ logs/
	ps -aux > logs/ps-list.info
	top -n 1 > logs/top.info
	sudo chown -R $username:$username logs/ coredumps/
	tar czvf logsall.tar.gz logs/ coredumps/
	#calculate the size of logs
	echo "日志容量大小统计："
	du -h logsall.tar.gz
	du -h -d1 ./
	exit 1
}

clean_logs(){
	sudo rm -rf /var/lib/systemd/coredump/*
	rm -rf logs$HOME/.cache/deepin/deepin-deepinid-client/
	sudo rm -rf /var/log/syslog*
	sudo rm -rf /var/log/message*
	sudo rm -rf /var/log/kern.log*
	echo "清除完成"
	exit 1
}

help(){
	echo "脚本使用方法: $(basename $0) [-e] [-d] [-r] [-c]"
	echo "导出全量日志: $(basename $0) -e " 
	echo "重置日志等级: $(basename $0) -r " 
	echo "清空大文件日志: $(basename $0) -c "
	echo "开启debug log-level: $(basename $0) -d "
}

while getopts ':derc' OPTION; do
  case "$OPTION" in
    d)
      enable_debug_log
      ;;
    e)
      export_logs
      ;;
	r)
	  disable_debug_log
	  ;;
	c)
	  clean_logs
	  ;;
    ?)
      help 
      exit 1
      ;;
  esac
done

help
exit 1

```
