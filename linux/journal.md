# 如何使用journalctl命令获取Linux日志

现代操作系统，包括 UOS,Ubuntu,Debian,Fedora,Centos 都采用 systemd 进行系统管理。journalctl 是 systemd 其中的日志管理中的一个主要的工具，通常你不需要安装就可以使用。
journalctl 用来查询 systemd-journald 服务收集到的日志。systemd-journald 服务是 systemd init 系统提供的收集系统日志的服务。


## 基本使用方法
sudo journalctl
不带任何选项时，journalctl 输出所有的日志记录：

-- Logs begin at Tue 2020-02-11 09:23:37 CST, end at Sun 2020-02-23 15:50:52 CST. --
2月 11 09:23:37 ldj-PC kernel: microcode: microcode updated early to revision 0x27, d
2月 11 09:23:37 ldj-PC kernel: Linux version 4.19.0-6-amd64 (debian-kernel@lists.debi
2月 11 09:23:37 ldj-PC kernel: Command line: BOOT_IMAGE=/boot/vmlinuz-4.19.0-6-amd64


## 获取更多帮助

可以通过 man page 来获得最直接的帮助文档：man journalctl

## 高级功能

### 保存日志

可以使用重定向功能将日志保存到磁盘中，比如 ~/Documents/ 里。特别是系统有问题，自己解决不了，需要将日志发送给相关开发人员看的时候特别有用。

sudo journalctl > ~/Documents/zhangsan- journal-0223.txt

然后将 zhangsan- journal-0223.txt 通过邮件或者微信等方式发送给开发人员。

### 查看本次启动后的日志
sudo journalctl -b
默认情况下 systemd-journald 服务只保存本次启动后的日志(此时日志保存在内存中，重新启动后丢掉以前的日志)。为了查阅上一次启动的日志或任意一次启动的日志，需要配置 systemd-journald 服务，让其将日志保存到磁盘中，这样下次启动时就可以查询任意时刻系统的日志了。

### 开启持久化日志存储功能
sudo mkdir /var/log/journal
sudo chown root:systemd-journal /var/log/journal
sudo chmod 2775 /var/log/journal
sudo systemctl restart systemd-journald.service
之后 /run/log 下面就没有 journal 的日志了，日志文件被保存在 /var/log/journal 目录下。

### 查看指定时间段的日志
利用 --since 与 --until 选项设定时间段，二者分别负责指定给定时间之前与之后的日志记录。时间值可以使用多种格式，比如下面的格式：

YYYY-MM-DD HH:MM:SS

如果我们要查询 2020年 02 月 23 日下午 15:00 之后的日志：

sudo journalctl --since "2020-02-23 15:00:00"
-- Logs begin at Tue 2020-02-11 09:23:37 CST, end at Sun 2020-02-23 16:14:45 CST. --
2月 23 15:01:08 ldj-PC kernel: snd_hda_codec_realtek hdaudioC0D2: hda_codec_setup_stream: NID=0x2, stream=0x5, channel=0, format=0x4011
2月 23 15:01:08 ldj-PC kernel: snd_hda_codec_realtek hdaudioC0D2: hda_codec_setup_stream: NID=0x3, stream=0x5, channel=0, format=0x4011
2月 23 15:01:08 ldj-PC kernel: snd_hda_codec_realtek hdaudioC0D2: hda_codec_setup_stream: NID=0x2, stream=0x5, channel=0, format=0x4011
2月 23 15:01:09 ldj-PC dbus-daemon[526]: [system] Activating service name='com.deepin.api.SoundThemePlayer' requested by ':1.43' (uid=1000 pid=1457 comm="/usr/lib/deepin-daemon/dde-session-daemon ") (using service
2月 23 15:01:09 ldj-PC dbus-daemon[526]: [system] Successfully activated service 'com.deepin.api.SoundThemePlayer'
2月 23 15:01:16 ldj-PC kernel: snd_hda_codec_realtek hdaudioC0D2: hda_codec_cleanup_stream: NID=0x2
2月 23 15:01:16 ldj-PC kernel: snd_hda_codec_realtek hdaudioC0D2: hda_codec_cleanup_stream: NID=0x3
2月 23 15:01:16 ldj-PC kernel: snd_hda_codec_realtek hdaudioC0D2: hda_codec_cleanup_stream: NID=0x2
2月 23 15:02:55 ldj-PC startdde[1334]: startmanager.go:434: dispatcher.NewApp error: mkdir /sys/fs/cgroup/blkio/2@dde: permission denied
2月 23 15:02:55 ldj-PC daemon/dock[1457]: app_entry.go:239: attach window id: 96468998, wmClass: "deepin-editor" "deepin-editor", wmState: [], wmWindowType: [377], wmAllowedActions: [505 506 507 508 510 511 512 51
2月 23 15:03:07 ldj-PC daemon/dock[1457]: dock_manager_entries.go:144: removeAppEntry id: e10T5e52239f
--since 选项规定了日志打印的时间段的开头，默认它会一直打印到现在。有时候为了方便系统的调试，会使用 --until 选项制定日志打印的时间段的结尾来减少无关日志的显示。

比如，如果我们要查询 2020年 02 月 23 日下午 15:00 之后，2020年 02 月 23 日下午 15:02 之前的日志：

sudo journalctl --since "2020-02-23 15:00:00" --until "2020-02-23 15:02:00"
-- Logs begin at Tue 2020-02-11 09:23:37 CST, end at Sun 2020-02-23 16:22:25 CST. --
2月 23 15:01:08 ldj-PC kernel: snd_hda_codec_realtek hdaudioC0D2: hda_codec_setup_stream: NID=0x2, stream=0x5, channel=0, format=0x4011
2月 23 15:01:08 ldj-PC kernel: snd_hda_codec_realtek hdaudioC0D2: hda_codec_setup_stream: NID=0x3, stream=0x5, channel=0, format=0x4011
2月 23 15:01:08 ldj-PC kernel: snd_hda_codec_realtek hdaudioC0D2: hda_codec_setup_stream: NID=0x2, stream=0x5, channel=0, format=0x4011
2月 23 15:01:09 ldj-PC dbus-daemon[526]: [system] Activating service name='com.deepin.api.SoundThemePlayer' requested by ':1.43' (uid=1000 pid=1457 comm="/usr/lib/deepin-daemon/dde-session-daemon ") (using service
2月 23 15:01:09 ldj-PC dbus-daemon[526]: [system] Successfully activated service 'com.deepin.api.SoundThemePlayer'
2月 23 15:01:16 ldj-PC kernel: snd_hda_codec_realtek hdaudioC0D2: hda_codec_cleanup_stream: NID=0x2
2月 23 15:01:16 ldj-PC kernel: snd_hda_codec_realtek hdaudioC0D2: hda_codec_cleanup_stream: NID=0x3
2月 23 15:01:16 ldj-PC kernel: snd_hda_codec_realtek hdaudioC0D2: hda_codec_cleanup_stream: NID=0x2
另外，journalctl 还能够理解部分相对值及命名简写。例如，大家可以使用 "yesterday"、"today"、"tomorrow" 或者 "now" 等。

比如获取昨天的日志数据可以使用下面的命令：

journalctl --since yesterday
-- Logs begin at Tue 2020-02-11 09:24:15 CST, end at Sun 2020-02-23 15:53:36 CST. --
2月 22 12:28:01 ldj-PC systemd[1362]: Listening on GnuPG cryptographic agent and passphrase cache.
2月 22 12:28:01 ldj-PC systemd[1362]: Reached target Paths.
2月 22 12:28:01 ldj-PC systemd[1362]: Listening on GnuPG cryptographic agent (ssh-agent emulation).
2月 22 12:28:01 ldj-PC systemd[1362]: Starting D-Bus User Message Bus Socket.


### 查看指定级别的日志

可以通过 -p 选项来过滤日志的级别。 在开发人员做开发调试的时候，可以用此方法过滤掉无关日志信息，从而快速定位问题。

可以指定的优先级如下，数字越小，级别越高。： 0: emerg 1: alert 2: crit 3: err 4: warning 5: notice 6: info 7: debug

sudo journalctl -p err
-- Logs begin at Tue 2020-02-11 09:23:37 CST, end at Sun 2020-02-23 16:28:04 CST. --
2月 11 09:23:37 ldj-PC kernel: radeon 0000:01:00.0: failed VCE resume (-110).
2月 11 09:23:48 ldj-PC systemd[1]: Failed to start Rotate log files.
2月 11 09:23:50 ldj-PC NetworkManager[2994]: <error> [1581384230.0053] dispatcher: could not get dispatcher proxy! 为 org.freedesktop.nm_dispatcher 调用 StartServiceByName 出错：GDBus.Error:org.freedesktop.systemd
2月 11 09:24:22 ldj-PC deepin-sync-daemon[4596]: [ERR] bin.go:79: resp body: no token found map[Token:[] Access-Token:[]]
2月 11 09:24:22 ldj-PC deepin-sync-daemon[4596]: [ERR] deepinid.go:178: check token info failed: failed to get token info
2月 11 09:24:22 ldj-PC deepin-sync-daemon[4596]: [ERR] bin.go:79: resp body: no token found map[Token:[] Access-Token:[]]


### 查看某个服务的日志

有时候，只是某个服务崩溃了，此时，可以指定查看此服务的日志。

sudo journalctl -u nginx
-- Logs begin at Tue 2020-02-11 09:23:37 CST, end at Sun 2020-02-23 16:36:58 CST. --
2月 11 10:10:42 ldj-PC systemd[1]: Starting A high performance web server and a reverse proxy server...
2月 11 10:10:42 ldj-PC systemd[1]: Started A high performance web server and a reverse proxy server.
2月 11 11:25:58 ldj-PC systemd[1]: Stopping A high performance web server and a reverse proxy server...
2月 11 11:25:58 ldj-PC systemd[1]: nginx.service: Succeeded.
2月 11 11:25:58 ldj-PC systemd[1]: Stopped A high performance web server and a reverse proxy server.
-- Reboot --


### 查内核日志

如果我们需要查看内核日志，可以指定 -k 选项，这样输出的结果中就只有内核日志了。-k 选项是通过 -b 选项加上匹配条件 "_TRANSPORT=kernel" 实现的。下面是基本的用法：

sudo journalctl -k
-- Logs begin at Tue 2020-02-11 09:23:37 CST, end at Sun 2020-02-23 16:41:46 CST. --
2月 23 15:41:30 ldj-PC kernel: microcode: microcode updated early to revision 0x27, date = 2019-02-26
2月 23 15:41:30 ldj-PC kernel: Linux version 4.19.0-6-amd64 (debian-kernel@lists.debian.org) (gcc version 8.3.0 (Debian 8.3.0-6)) #1 SMP Uos 4.19.67-3deepin (2019-10-24)
2月 23 15:41:30 ldj-PC kernel: Command line: BOOT_IMAGE=/boot/vmlinuz-4.19.0-6-amd64 root=UUID=2f931088-8647-4c50-b8b7-933841178565 ro splash quiet DEEPIN_GFXMODE=
2月 23 15:41:30 ldj-PC kernel: x86/fpu: Supporting XSAVE feature 0x001: 'x87 floating point registers'