
---
layout: post
title: Redhat Linux����root����
categories: Redhat
description: Redhat Linux����root����
keywords: root����
---


1��������������ϵͳѡ�����ʱ��e��
2����Linux16����ĩβ����rd,break����ctrl+x�����Ԯģʽ
3������
```
mount -o remount,rw /sysroot
chroot /sysroot
echo "Ҫ���õ�����" | passwd --stdin root
touch /.autorelabel
exit
reboot
```

4���ȴ�ϵͳ����