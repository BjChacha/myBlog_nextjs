---
draft: true
---

## Linux 挂载硬盘

- 查看当前分区信息
  > df -h
- 查看当前硬盘信息（需要管理员权限）

  > fdisk -l

- 对硬盘进行分区（以`/dev/sdb`为例）

  1. 进入`parted`环境（需要管理员权限）
     > parted /dev/sdb
  1. 查看硬盘信息
     > (parted) p
  1. 创建分区标签信息（以 GPT 为例）
     > (parted) mklabel gpt
  1. 创建分区
     > (parted) mkpart
  1. 输入以下参数
     > Partition name? []? sdb1  
     > File system type? [ext2]? ext4  
     > Start? 0
     > End? 100%
  1. 退出`parted`环境
     > (parted) quit

- 用`mkfs.ext4`格式化分区（需要管理员权限）

  > mkfs.ext4 /dev/sdb1

- （Bouns 环节）创建物理卷和逻辑卷，以将多个分区挂在到同一个文件夹中（均需要管理员权限）

  1. 创建物理卷（以两个分区`sdb1`和`sdc1`为例）
     > pvcreate /dev/sdb1 /deb/sdc1
  2. 创建物理组（以`vg_data`分组名为例）
     > pgcreate vg_data /dev/sdb1 /dev/sdc1
  3. 创建逻辑卷（以`lv_data`卷名、`7.2TB`大小为例）
     > lvcreate -L 7.2T -n lv_data vg_data
  4. 格式化逻辑卷
     > mkfs.ext4 /dev/mapper/vg_data-lv_data

- 挂载（以挂载到`/data`为例）
  > mount /dev/mapper/vg_data-lv_data /data
