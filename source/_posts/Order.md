---
title: Order
date: 2026-02-24 19:55:26
tags:
---
``` bash
 ❯ lsblk -f /dev/nvme0n1p3
```

	NAME		FSTYPE	FSVER	LABEL	UUID					FSAVAIL FSUSE% MOUNTPOINTS
	nvme0n1p3	btrfs		fedora c2b2842e-c3f8-4203-9f4a-c2770ac4c5cb	5.2G	68%	/
``` bash
 ❯ df -T /home
```

	文件系统       类型    1K的块     已用    可用 已用% 挂载点
	/dev/nvme0n1p3 btrfs 19919872 13506716 5408932   72% /
