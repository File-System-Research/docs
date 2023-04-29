## RAID on ZNS

本方向将研究 RAID 技术在 ZNS 上的应用。

### 问题

1. RAID 种类和性能？[ZFS在不同 RAID 策略下的性能评测](https://www.liujason.com/article/679.html)

   | 阵列等级             | 磁盘数量 | 总容量  | 写速度  | 随机写速度 | 读速度   |
   | -------------------- | -------- | ------- | ------- | ---------- | -------- |
   | single drive         | 1x 4TB   | 3.7 TB  | 108MB/s | 50MB/s     | 204MB/s  |
   | mirror (raid1)       | 2x 4TB   | 3.7 TB  | 106MB/s | 50MB/s     | 488MB/s  |
   | stripe (raid0)       | 2x 4TB   | 7.5 TB  | 237MB/s | 73MB/s     | 434MB/s  |
   | mirror (raid1)       | 3x 4TB   | 3.7 TB  | 106MB/s | 49MB/s     | 589MB/s  |
   | stripe (raid0)       | 3x 4TB   | 11.3 TB | 392MB/s | 86MB/s     | 474MB/s  |
   | raidz1 (raid5)       | 3x 4TB   | 7.5 TB  | 225MB/s | 56MB/s     | 619MB/s  |
   | 2 striped mirrors    | 4x 4TB   | 7.5 TB  | 226MB/s | 53MB/s     | 644MB/s  |
   | raidz2 (raid6)       | 4x 4TB   | 7.5 TB  | 204MB/s | 54MB/s     | 183MB/s  |
   | raidz1 (raid5)       | 5x 4TB   | 15.0 TB | 469MB/s | 79MB/s     | 598MB/s  |
   | raidz3 (raid7)       | 5x 4TB   | 7.5 TB  | 116MB/s | 45MB/s     | 493MB/s  |
   | 3 striped mirrors    | 6x 4TB   | 11.3 TB | 389MB/s | 60MB/s     | 655MB/s  |
   | raidz2 (raid6)       | 6x 4TB   | 15.0 TB | 429MB/s | 71MB/s     | 488MB/s  |
   | 2 striped 5x raidz   | 10x 4TB  | 30.1 TB | 675MB/s | 109MB/s    | 1012MB/s |
   | raidz3 (raid7)       | 11x 4TB  | 30.2 TB | 552MB/s | 103MB/s    | 963MB/s  |
   | 6 striped mirrors    | 12x 4TB  | 22.6 TB | 643MB/s | 83MB/s     | 962MB/s  |
   | 2 striped 6x raidz2  | 12x 4TB  | 30.1 TB | 638MB/s | 105MB/s    | 990MB/s  |
   | raidz (raid5)        | 12x 4TB  | 41.3 TB | 689MB/s | 118MB/s    | 993MB/s  |
   | raidz2 (raid6)       | 12x 4TB  | 37.4 TB | 317MB/s | 98MB/s     | 1065MB/s |
   | raidz3 (raid7)       | 12x 4TB  | 33.6 TB | 452MB/s | 105MB/s    | 840MB/s  |
   | 2 striped 11x raidz3 | 22x 4TB  | 60.4 TB | 567MB/s | 162MB/s    | 1139MB/s |
   | 22x 4TB | 2 striped 11x raidz3 | 60.4 TB  | 567MB/s  | 162MB/s      | 1139MB/s |
   | 23x 4TB | raidz3 (raid7)       | 74.9 TB  | 440MB/s  | 157MB/s      | 1146MB/s |
   | 24x 4TB | 12 striped mirrors   | 45.2 TB  | 696MB/s  | 144MB/s      | 898MB/s  |
   | 24x 4TB | raidz (raid5)        | 86.4 TB  | 567MB/s  | 198MB/s      | 1304MB/s |
   | 24x 4TB | raidz2 (raid6)       | 82.0 TB  | 434MB/s  | 189MB/s      | 1063MB/s |
   | 24x 4TB | raidz3 (raid7)       | 78.1 TB  | 405MB/s  | 180MB/s      | 1117MB/s |
   | 24x 4TB | striped raid0        | 90.4 TB  | 692MB/s  | 260MB/s      | 1377MB/s |
