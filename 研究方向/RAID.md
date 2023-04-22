## RAID on ZNS

本方向将研究 RAID 技术在 ZNS 上的应用。

### nullblk 的使用和修改

[nullblk](https://www.kernel.org/doc/html/latest/block/null_blk.html) 即 Null block device driver，空块设备（`/dev/nullb*`），用于对各种块层实现进行基准测试。它模拟 `X` GB 大小的块设备。它不执行任何读/写操作，只是在请求队列中将它们标记为完成。

nullblk 已经被合入 Linux Kernel 主线，具体用法可以参考[内核文档](https://www.kernel.org/doc/html/latest/block/null_blk.html)。

由于 ZNS 实物申请需要一段时间，或者无法申请到，所以我们需要一个仿真环境来进行我们的开发流程。nullblk 可以仿真块设备，并可以设置为只能顺序写的模式，正好仿真 ZNS 中的硬件 Zones。

#### 使用 nullblk

在测试中，可以方便地用脚本调用 Linux Kernel 的系统调用来创建 nullblk。

```shell
#!/bin/bash

if [ $# != 4 ]; then
        echo "Usage: $0 <sect size (B)> <zone size (MB)> <nr conv zones> <nr seq zones>"
        exit 1
fi

scriptdir=$(cd $(dirname "$0") && pwd)

modprobe null_blk nr_devices=0 || return $?

function create_zoned_nullb()
{
        local nid=0
        local bs=$1
        local zs=$2
        local nr_conv=$3
        local nr_seq=$4

        cap=$(( zs * (nr_conv + nr_seq) ))

        while [ 1 ]; do
                if [ ! -b "/dev/nullb$nid" ]; then
                        break
                fi
                nid=$(( nid + 1 ))
        done

        dev="/sys/kernel/config/nullb/nullb$nid"
        mkdir "$dev"

        echo $bs > "$dev"/blocksize
        echo 0 > "$dev"/completion_nsec
        echo 0 > "$dev"/irqmode
        echo 2 > "$dev"/queue_mode
        echo 1024 > "$dev"/hw_queue_depth
        echo 1 > "$dev"/memory_backed
        echo 1 > "$dev"/zoned

        echo $cap > "$dev"/size
        echo $zs > "$dev"/zone_size
        echo $nr_conv > "$dev"/zone_nr_conv

        echo 1 > "$dev"/power

        echo mq-deadline > /sys/block/nullb$nid/queue/scheduler

        echo "$nid"
}

nulldev=$(create_zoned_nullb $1 $2 $3 $4)
echo "Created /dev/nullb$nulldev"
```

