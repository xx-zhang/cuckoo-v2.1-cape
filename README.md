## CAPE: Malware Configuration And Payload Extraction
- [项目地址](https://github.com/xx-zhang/cuckoo-v2.1-cape)

## 2020-11-27 
- 开始修改
- 出现问题记录
   - 1. kvm安装win-sp3故障.SetUp后一直不出现界面
	- 解耦`libvirt`、`kvm`、`qemu` 和 `libvirt-manager`【放在另外的机器】
	- winxp后续重新安装，开销较小，安装简单。另外开启专门的`sandboxie`沙箱做补充
	- centos7-arm网卡错误。arm在x86下进行虚拟开销较大、可以放弃。
   - 2. 出现问题：suricata无法正常读取。<已解决
   - 3. [X]出现问题：进程获取异常。无法正常获取运行中的进程信息
   - 4. [X]出现问题：mitmproxy没有在capev2中出现进行捕获正常的 https 流量。
   - 5. [X]出现问题：`centos7-base`中脚本异常无法正常响应和反馈-出现了乱七八糟的流量（ftp.centos.org）。 
	- 需要主机的网络策略

