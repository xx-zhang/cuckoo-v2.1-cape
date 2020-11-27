## KVM使用高阶教程

```bash
virt-install \
    --name xp \
    --memory 2048 \
    --vcpus sockets=1,cores=1,threads=2 \
    --cdrom=/data/iso/winxp-sp3.iso \
    --os-variant=windows \
    --disk /data/drivers/virtio-win-0.1.101_x86.vfd,device=floppy \
    --disk /data/vms/winxp-sp3.qcow2,bus=virtio,size=100 \
    --network bridge=virbr0,model=virtio \
    --noautoconsole \
    --accelerate \
    --hvm --virt-type kvm\
    --graphics vnc,listen=0.0.0.0,port=5903 \
    --cpu host-passthrough \
    --force --autostart


virt-install \
    --name win7 \
    --memory 2048 \
    --vcpus sockets=1,cores=1,threads=2 \
    --disk /data/vms/win7.qcow2,bus=virtio,format=qcow2,size=100 \
    --cdrom=/data/iso/cn_windows_7_professional_with_sp1_x64_dvd_u_677031.iso \
    --os-variant=windows \
    --disk /data/drivers/virtio-win-0.1.126_amd64.vfd,device=floppy  \
    --network bridge=virbr0,model=virtio \
    --noautoconsole \
    --accelerate \
    --hvm --virt-type kvm\
    --graphics vnc,listen=0.0.0.0,port=5907 \
    --cpu host-passthrough \
    --force --autostart


virt-install \
    --name win10-sandbox \
    --memory 2048 \
    --vcpus sockets=1,cores=1,threads=2 \
    --disk /data/vms/win10.qcow2,bus=virtio,format=qcow2,size=100 \
    --cdrom=/data/iso/win10.iso \
    --os-variant=windows \
    --disk /data/drivers/virtio-win-0.1.185_amd64.vfd,device=floppy  \
    --network bridge=virbr0,model=virtio \
    --noautoconsole \
    --accelerate \
    --hvm --virt-type kvm\
    --graphics vnc,listen=0.0.0.0,port=5910 \
    --cpu host-passthrough 

# Install Centos7 

virt-install \
--name=centos7-base \
--vcpus=2 \
--memory=2048 \
--os-type=linux --os-variant=rhel7  \
--location=/data/iso/CentOS-7-x86_64-Minimal-2003.iso \
--disk path=/data/vms/centos7-base.qcow2,size=30,format=qcow2 \
--network bridge=virbr0 \
--graphics none \
--extra-args='console=ttyS0,target_type=serial' \
--force
```

## 快照错误
```bash

qemu-img convert -f raw -O qcow2 /vdir/c1.raw c1.qcow2
\\ \ \ \修改了还是错误！

root@cuckoo:/data/vms# virsh snapshot-create win10
error: unsupported configuration: internal snapshot for disk fda unsupported for storage type raw

## 当替换以上命令，重启libvertd即可。

## 列出快照和删除快照
$ virsh snapshot-list "VM-Name"
$ virsh snapshot-delete "VM-Name" 1234567890

```

## 磁力链接
```
## win xp sp2 
ed2k://|file|en_win_xp_pro_x64_with_sp2_vl_X13-41611.iso|628168704|5573EEA1F40FE32E46F4615B6A4E71D8|/

## win7 
ed2k://|file|cn_windows_7_professional_x64_dvd_x15-65791.iso|3341268992|3474800521D169FBF3F5E527CD835156|/

```

### 快照功能测试
```
# 创建快照
virsh snapshot-create win10
# 查看当前快照
virsh snapshot-current  win10

## NOTE
# 使用virsh或者xml启动时候，一定确保你的qcow2所在的行在最前面。
1.kvm克隆
kvm 虚拟机有两部分组成：img镜像文件和xml配置文件（/etc/libvirt/qemu
克隆命令：virt-clone -o rhel6- 71 -n xuegod63-kvm2 -f /var/lib/libvirt/images/xuegod63-kvm2.img

virt-clone -o 原虚拟机 -n 新虚拟机 -f 新img文件
对比配置文件，将两份xml文件做diff对比，里面只修改了name、img、Mac 3个位置信息
克隆完成后，需要修改新虚拟机的网卡配置，并删除/etc/udev/rule.d/70-*-net文件，

2.快照(snapshot)
kvm默认格式为raw格式，如需要修改镜像文件格式。需要配置xml文件
查看镜像文件格式qemu-ig info 镜像文件

1）、转换格式（将raw格式转换为qcow2格式）
qemu-img convert -f raw -O qrow2 /var/lib/libvert/images/xuegod63-kvm2.img
需要修改xml文件virsh edit 虚拟机

2）、创建快照
qemu-img snapshot-create 虚拟机（可以用snapshot-create-as指定快照名称）

3）、快照管理
qemu-img snapshot-list

4）、恢复快照

查看虚拟机状态：virsh domstate xuegod63-kvm2
恢复快照：virsh snapshot-revert 虚拟机 快照名
查看当前快照: virsh snapshot-current xuegod63-kvm2
快照目录：/var/lib/libvert/qemu/snapshot/虚拟机
删除快照： virsh snapshot-delete 虚拟机 快照名
```


## 解决shutdown问题
- 参考 https://blog.csdn.net/qq_42388880/article/details/106751790
- 在 win10.xml 下的device增加下面的内容；
```
 <channel type='unix'>
      <source mode='bind'/>
      <target type='virtio' name='org.qemu.guest_agent.0'/>
      <address type='virtio-serial' controller='0' bus='0' port='1'/>
 </channel>
```


## 第二种启动windows的方法
```bash
qemu-img  create -f qcow2 win7-base.img 40G

virt-install -n win7-cuckoo \
    --vcpus=2 \
    --ram=1024 \
    --os-type=windows \
    --os-variant=windows \
    -c /data/iso/win7.iso \
    --network bridge=virbr0,model=virtio \
    --disk path=/data/imgs/virtio-win-0.1.126_amd64.vfd,device=floppy  \
    --disk path=/data/imgs/win7-cuckoo.qcow2,format=qcow2,bus=virtio \
    --graphics vnc,listen=0.0.0.0,port=5912 \
    --noautoconsole
```

## 快照问题2
```bash

virsh snapshot-create-as \
    --domain win7 win7-init \
     --disk-only --diskspec vda,snapshot=external,file=/data/vms/win7.qcow2 --atomic
```


## 2020-11-27
```bash

virt-install \
    --virt-type kvm \
    --name winxp3-base \
    --ram 2048 \
    --os-type=windows \
    --os-variant=winxp \
    --disk path=/data/vms/winxp3-base.qcow2,format=qcow2,bus=virtio,cache=none,size=15 \
    --disk path=/data/drivers/virtio-win-0.1.101_x86.vfd,device=floppy \
    --network bridge=virbr0,model=virtio \
    --cdrom /data/iso/winxp-sp3.iso \
    --graphics vnc,listen=0.0.0.0,port=5905  \
    --noautoconsole
```
