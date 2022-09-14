# linux

Linux 一切皆文件！！！


守护进程 - 开机自动启动进程


sync - 将数据从内存同步到硬盘中

halt               # 关闭系统，等同于 shutdown -h now 和 poweroff
shutdown           # 关机
shutdown -h 10     # 10分钟后关机
shutdown -h now    # 现在关机
shutdown -h 20:25  # 系统会在 20:25 关机
shutdown -h +10    # 10分钟后关机

reboot             # 重启
shutdown -r now    # 立马重启
shutdown -r +10    # 10分钟后重启




【系统目录结构】

/           # 根目录，所有文件都挂载在这个节点下
/bin        # bin 是 Binary 的缩写，这个目录存放着最经常使用的命令
/boot       # 存放Linux系统启动核心文件，包括一些连接文件及镜像文件
/dev        # Device(设备)的缩写，存放的是Linux的外部设备，在Linux中访问设备的方式和访问文件的方式是相同的的
/mnt        # Linux 中用于用户临时挂载别的文件系统（如光驱，本地文件挂载等）
/etc        # 用于存放所有的系统管理所需要的配置文件和子目录
/home       # 用户目录，每个用户都有一个属于自己的目录文件夹，一般目录名是以用户的账号命名的，类似于Win系统的 用户 目录
/lib        # 用于存放系统最基本的动态链接库，其作用类似于Win系统中的dll文件
/lost+found # 一般为空，当系统非法关机后，这里就存放了一些文件
/media      # Linux 会把自动失败的设备（U盘，光驱等等）挂载到这个目录下
/opt        # 给系统额外安装的软件一般存放于该目录，如 oracle/mysql等
/proc       # 系统虚拟目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息
/root       # 系统管理员用户目录
/usr        # 普通用户的目录，用户的很多应用程序和文件都放在这个目录下，类似于Win系统下的 program files 目录
/sbin       # super bin 的意思，这里存放的是系统管理员使用的系统管理程序
/srv        # 用于存放一些服务启动之后需要提取的数据
/sys        # 
/tmp        # 临时文件目录
/usr/bin    # 系统用户使用的应用程序
/usr/sbin   # 超级用户使用的比较高级的管理程序和系统守护程序
/usr/src    # 内核源代码默认的放置目录
/var        # 用于存放系统中的一些频繁变更的数据文件，如日志文件等
/run        # 临时文件系统，存储系统启动以来的信息，当系统重启时，这个目录下的文件应该被删掉或清除
/www        # 存放服务器网站相关的资源




【目录管理】

绝对路径 - 完整真实路径(都是以 / 开头)
相对路径 - 相对于当前目录下相对位置的路径(以 .. 开头)


cd     # 切换目录(change directory)
cd ./  # 切换到当前目录
cd ..  # 切换到上一级目录
cd ~   # 切换到当前用户目录


pwd    # 打印当前用户所在目录(print work directory)


ls     # 列出文件(list files)
➜ ls     
linux  mysql  numpy  pandas  python


ls -a  # 查看全部文件，包括隐藏文件
➜ ls -a                  
.  ..  .pytest_cache  linux  mysql  numpy  pandas  python


ls -la  # 列出所有的文件，包含文件的属性和权限，不包含隐藏文件
➜ ls -la                
total 0
drwxrwxrwx 1 hhb hhb 4096 Feb 27 19:59 .
drwxrwxrwx 1 hhb hhb 4096 Feb 14 21:35 ..
drwxrwxrwx 1 hhb hhb 4096 Dec  7 21:37 .pytest_cache
drwxrwxrwx 1 hhb hhb 4096 Feb 27 19:59 linux
drwxrwxrwx 1 hhb hhb 4096 Feb 14 22:13 mysql
drwxrwxrwx 1 hhb hhb 4096 Dec 12 21:31 numpy
drwxrwxrwx 1 hhb hhb 4096 Dec 12 14:53 pandas
drwxrwxrwx 1 hhb hhb 4096 Jan 10 23:51 python


ls -i # 查看文件i节点号（可以认为是文件的唯一标识符）
➜ ls -i            
28147497671235396 linux  28428972647985931 numpy   24769797950744437 python   7318349394703340 test       5629499534442283 testing
11821949022006849 mysql  23362423067194192 pandas   7318349394703342 target   7881299348124667 test.txt


# Linux 中所有文件都有一个i节点，硬链接不能跨分区，硬链接的文件对应的i节点是相同的

mkdir                             # 创建目录(make directory)
mkdir test                        # 创建一个名称为 test 的文件目录
mkdir -p test1/test1_1/test1_1_1  # 递归创建多个层级的文件夹



rmdir                             # 删除目录(remove directory) - 仅能删除空的目录，如果目录下面存在文件，需要先删除文件在删除目录
rmdir test                        # 删除 test 文件夹
rmdir -p test1/test1_1/test1_1_1  # 递归删除多个层级的文件夹


rm              # 删除文件夹或文件(remove)
rm -f           # 强制删除，忽略不存在的文件，不会出现警告
rm -r           # 递归删除目录
rm -i           # 交互式删除，会询问是否真的需要删除
rm -rf /        # 将根目录下的所有文件强制删除（删库跑路） - 危险命令，不要使用
rm -rf ./test   # 递归删除文件夹 test 及其该文件夹下的所有文件
rm -rf f1 f2 f3 # 删除多个文件(f1,f2,f3)


cp                # 复制文件或目录(copy) - 如果目标文件已经存在，则会覆盖(y)或取消(n)
cp source target  # 复制 source 文件或目录到 target 目录



mv                # 移动文件或目录(move)
mv -f             # 强制移动，忽略不存在的文件，不会出现警告
mv -u             # 只替换已经更新过的文件
mv source target  # 将source文件夹移动到target文件夹
mv source source2 # 将source文件夹重命名为 source2 - 当目标文件夹不存在时为重命名操作




【基本属性】

➜ ls -ll / 
total 1104
lrwxrwxrwx    1 root root       7 Apr 23  2020 bin -> usr/bin
drwxr-xr-x    2 root root    4096 Apr 23  2020 boot
drwxr-xr-x    8 root root    2720 Feb 26 14:15 dev
drwxr-xr-x  129 root root   12288 Feb 26 14:15 etc
drwxr-xr-x    3 root root    4096 Jul  5  2020 home
-rwxr-xr-x    2 root root 1392928 Jul 27  2021 init
lrwxrwxrwx    1 root root       7 Apr 23  2020 lib -> usr/lib
lrwxrwxrwx    1 root root       9 Apr 23  2020 lib32 -> usr/lib32
lrwxrwxrwx    1 root root       9 Apr 23  2020 lib64 -> usr/lib64
lrwxrwxrwx    1 root root      10 Apr 23  2020 libx32 -> usr/libx32
drwx------    2 root root   16384 Apr 11  2019 lost+found
drwxr-xr-x    2 root root    4096 Apr 23  2020 media
drwxr-xr-x    5 root root    4096 Dec 12  2020 mnt
drwxr-xr-x    2 root root    4096 Apr 23  2020 opt
dr-xr-xr-x  138 root root       0 Feb 26 14:15 proc
drwx------    8 root root    4096 Feb 27 19:58 root
drwxr-xr-x    8 root root     160 Feb 26 14:20 run
lrwxrwxrwx    1 root root       8 Apr 23  2020 sbin -> usr/sbin
drwxr-xr-x    2 root root    4096 Apr 10  2020 snap
drwxr-xr-x    2 root root    4096 Apr 23  2020 srv
dr-xr-xr-x   11 root root       0 Feb 26 14:15 sys
drwxrwxrwt 4906 root root  335872 Feb 27 20:26 tmp
drwxr-xr-x   14 root root    4096 Apr 23  2020 usr
drwxr-xr-x   13 root root    4096 Apr 23  2020 var


[l] # 链接文档(link file)
[d] # 目录
[-] # 文件
[b] # 块设备文件
[c] # 字符设备文件
[s] # 套接字文件
[p] # 管理文件


r # 读权限(read)
w # 写权限(write)
x # 执行权限(execute)



drwxr-xr-x

0            123         456         789
[文件类型]   [属主权限]   [属组权限]   [其他用户权限]


其中：
    第 1，4，7 位表示 读权限，如果用 r 表示，则有读权限，如果用 - 表示，则没有读权限
    第 2，5，8 位表示 写权限，如果用 w 表示，则有写权限，如果用 - 表示，则没有写权限
    第 3，6，9 位表示 执行权限，如果用 x 表示，则有执行权限，如果用 - 表示，则没有执行权限




NOTE: 注意属性中的属主和属组信息如下：
                [属主] [属组]
drwxr-xr-x    2 root   root    4096 Apr 23  2020 boot




NOTE: -R 表示递归作用于所有子目录

chgrp   # 更改文件属组(change group) - 语法：chgrp [-R] 属组名 文件名
chown   # 更改文件属主(change owner) - 语法：chown [-R] 属主名 文件名


chmod   # 更改文件权限(change mode) - 语法：chmod [-R] 权限 文件名

r: 4
w: 2
x: 1
-: 0

rw- => 4 + 2 = 6     - 可读可写不可执行
rwx => 4 + 2 + 1 = 7 - 可读可写可执行
--- => 0 + 0 + 0 = 0 - 不可读不可写不可执行



更改文件权限的两种方式：
    1. 使用 chmod 命令
    2. 使用 chmod 命令的简写形式（数字方式）


# 方式一：
    u: user
    g: group
    o: others
    a: all(所有的，等价于 ugo)

    +: 添加权限
    -: 删除权限
    =: 设置权限


    chmod ugo+r test.txt                 # 将文件 test.txt 设置为所有人可读
    chmod a+r test.txt                   # 将文件 test.txt 设置为所有人可读
    chmod ug+w,o-w file1.txt file2.txt   # 将文件 file1.txt 和 file2.txt 设置为用户和组可读写，其他用户只读


# 方式二：
    chmod 777 test # 赋予所有用户对文件夹 test 所有权限(可读可写可执行)


NOTE: 效的原因是:chmod改变文件权限是有限制的,它只能在linux分区下生效,否则权限改不了。(因为FAT或NTFS文件系统中根本没有Linux系统中的那些权限的概念)
# WSL 权限问题：https://my.oschina.net/xiaopei/blog/3213340


【文件内容】

➜  test echo "teststart" > test.txt
➜  test echo "testend" >> test.txt


cat         # 由第一行开始显示文件内容(concatenate)
tac         # 与cat相反的过程，由最后一行开始显示文件内容
nl          # 查看文件内容的时候显示行号
head        # 查看头几行内容
head -n 20  # 查看前20行内容
tail        # 查看末几行内容
tail -n 20  # 查看后20行内容

more        # 按页显示内容，仅支持往后翻页（内容多于一页时）                  - 空格翻页，回车看下一行
less        # 按页显示内容，比more更加灵活，可往前或往后翻页（内容多于一页时） 

在使用 less 查看内容时：
    - PgUp向上翻页，PgDn向下翻页，按 q 退出
    - / + 字符，表示向下搜索字符串，n 表示继续搜索下一个，N 表示反向下一个
    - ? + 字符，表示向上搜索字符串，n 表示继续搜索下一个，N 表示反向下一个




➜ cat test.txt
teststart
testend


➜ tac test.txt                                             
testend
teststart


➜nl test.txt        
     1  teststart
     2  testend


➜ more test.txt
➜ less test.txt



ifconfig  # 查看网络配置

➜  ifconfig     
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.168.162  netmask 255.255.240.0  broadcast 172.17.175.255
        inet6 fe80::215:5dff:fe7e:653  prefixlen 64  scopeid 0x20<link>
        ether 00:15:5d:7e:06:53  txqueuelen 1000  (Ethernet)
        RX packets 110498  bytes 62243795 (62.2 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 79454  bytes 30379543 (30.3 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 366956  bytes 385108778 (385.1 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 366956  bytes 385108778 (385.1 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0



【Linux链接】

硬链接 - A --- B, 假设B是A的链接，那么他们指向了同一个文件，允许一个文件拥有多个路径，删除了其中一个时，另一个仍然可以正常访问（用户可以通过这种机制建立硬链接到一些重要文件上，防止误删操作）
软连接 - 类似Win系统中的快捷方式，删除了源文件后，快捷方式也不可用了

ln file1 file2     # 将文件file1硬链接到file2
ln -s file1 file2  # 将文件file1软链接到file2


➜ mkdir f      
➜ cd f                                                         
➜  f touch f1
➜  f ln f1 f2
➜  f ln -s f1 f3
➜  f ls -ll     
total 0
-rwxrwxrwx 2 hhb hhb 0 Feb 28 22:02 f1
-rwxrwxrwx 2 hhb hhb 0 Feb 28 22:02 f2
lrwxrwxrwx 1 hhb hhb 2 Feb 28 22:02 f3 -> f1


# NOTE: 以上步骤中，f2 和 f3 都链接到了 f1，所以当 f1 文件内容改变时，f2 和 f3的内容也同步变更了

➜  f echo "i love linux" > f1  
➜  f cat f1              
i love linux
➜  f cat f2                  
i love linux
➜  f cat f3
i love linux

# NOTE: 删除源文件(f1)后，硬链接不受影响，软连接失效

➜  f rm f1
➜  f cat f3 
cat: f3: No such file or directory
➜  f cat f2
i love linux


touch                            # 创建文件
echo                             # 输出内容
echo "i love linux" > test.txt   # 将内容写入 test.txt
echo "i love linux" >> test.txt  # 将内容追加到 test.txt




【VIM编辑器】

vim test.txt       # test.txt 存在时编辑文件，不存在时创建文件

命令模式 -> 编辑模式：按 i
编辑模式 -> 命令模式：先按ESC退出编辑模式
命令模式 -> 底线命令模式：输入冒号


底线命令模式：
q - 退出
w - 保持

:qw - 保存并退出



【账号管理】- Linux 是一个多用户的系统


useradd                           # 新建用户
useradd -m tester                 # 新建用户 tester 并在 /home 目录下自动创建 tester 用户文件夹
useradd -G usergroup -m tester    # 新建用户 tester 并在 /home 目录下自动创建 tester 用户文件夹且将该用户分配到 usergroup 用户组
userdel -r tester                 # 删除用户 tester 并删除/home 目录下tester 用户文件夹
usermod                           # 修改用户属性(修改后 /etc/passwd 配置文件会同步修改)
usermod -d /home/testernew tester # 更新用户 tester 的默认文件夹路径
passwd tester                     # 修改或设置用户 tester 密码(回车后输入密码，安全起见，密码输入不可见)
passwd -l tester                  # 锁定用户 tester (用户被锁定后，不能登录，-l 表示 lock)
passwd -d tester                  # 置空用户 tester 的密码(没有密码也不能登录，-d 表示 delete)
su tester                         # 切换用户为 tester(切换用户后执行 exit 可退到切换前的用户)


➜ ls -ll /home
total 4
drwxr-xr-x 28 hhb hhb 4096 Feb 28 22:46 hhb
useradd: cannot lock /etc/passwd; try again later.
➜ sudo useradd -m tester
[sudo] password for hhb: 
➜ ls -ll /home
total 8
drwxr-xr-x 28 hhb    hhb    4096 Feb 28 22:46 hhb
drwxr-xr-x  2 tester tester 4096 Feb 28 22:46 tester

# 理解一下本质：Linux中一切皆文件，这里的添加用户说白了就是往某一个文件中写入了用户信息 (/etc/passwd)



➜ sudo userdel -r tester 
[sudo] password for hhb: 
userdel: tester mail spool (/var/mail/tester) not found
➜ ls -ll /home
total 4
drwxr-xr-x 28 hhb hhb 4096 Feb 28 23:06 hhb


➜ sudo useradd -m tester   
➜ sudo usermod -d /home/testernew tester
➜ cat /etc/passwd
tester:x:1001:1002::/home/testernew:/bin/sh



[root@huabo home]#

root  -> 当前用户名
huabo -> 主机名
home  -> 用户当前目录
#     -> 超级用户标识
$     -> 普通用户标识


➜ sudo passwd tester
New password: 
Retype new password: 
passwd: password updated successfully


➜ su tester         
Password: 



➜ sudo passwd -l tester                                                          
passwd: password expiry information changed.
➜ su tester            
Password: 
su: Authentication failure



hostname          # 查看主机名
hostname newname  # 修改主机名(修改后重新连接即可生效)



【用户组管理】

groupadd                                        # 创建一个用户组
groupadd usergroup                              # 创建一个用户组 usergroup
groupadd -g 520 usergroup                       # 创建一个用户组 usergroup 并设置用户组 ID 为 520 (如果不指定ID则会自增)

groupdel usergroup                              # 删除用户组 usergroup

groupmod -g 123 usergroup                       # 修改用户组 usergroup 的ID
groupmod -n newgroup usergroup                  # 修改用户组 usergroup 的名称
groupmod -g 123 usergroup -n newgroup usergroup # 同时修改用户组的ID和名称

➜ sudo groupdel usergroup


➜ sudo groupadd usergroup          
[sudo] password for hhb: 
➜ ls -ll /etc/group
-rw-r--r-- 1 root root 1018 Mar  1 21:50 /etc/group
➜ cat /etc/group 
usergroup:x:1003:


➜ sudo groupmod -n newusergroup  usergroup

➜ sudo groupmod -g 520 usergroup



更新某用户所属的用户组：

方式一：切换到需要更新的用户，然后执行 newgrp [新用户组名]
# su tester
$ newgrp usergroup  # 将当前用户属组更新为 usergroup


方式二：新建用户的同时就指定用户组
useradd -G usergroup -m tester

➜ cat /etc/passwd
tester:x:1001:1002::/home/tester:/bin/sh

字段格式说明：
[用户名]:口令(登录密码，不可见，使用x代替):用户标识号:用户组标识号:注释性描述:主目录:登录shell


登录口令: 把真正的加密后的哦那个和口令放到 /etc/shadow 文件中(密码已作加密处理)，保证我们密码的安全性


/etc/passwd #这个文件中的每一行都代表着一个用户，我们可以从这里看出这个用户的主目录在哪里，可以看到该用户所属的用户组
/etc/group  #用户组信息
/etc/shadow #用户密码信息  



【磁盘管理】
df          # 列出文件系统整体的磁盘使用量(disk free)
df -h       # 以人类可读的方式显示磁盘使用量

du          # 查看文件空间使用量(disk usage) 
du -a       # 查看所有文件空间占用量，包括隐藏文件
du -h       # 以人类可读的方式显示查看文件空间使用量
du -sm /*   # 检查根目录下每个目录所占用的容量


NOTE: du 会显示所有子文件，按需使用，不然会返回大量内容


➜ df 
Filesystem     1K-blocks      Used Available Use% Mounted on
/dev/sda       263174212  11142428 238593628   5% /
none             2013672         4   2013668   1% /mnt/wsl
tools          123767236 115735204   8032032  94% /init
none             2011384         0   2011384   0% /dev
none             2013672        12   2013660   1% /run
none             2013672         0   2013672   0% /run/lock
none             2013672         0   2013672   0% /run/shm
none             2013672         0   2013672   0% /run/user
tmpfs            2013672         0   2013672   0% /sys/fs/cgroup
drivers        123767236 115735204   8032032  94% /usr/lib/wsl/drivers
lib            123767236 115735204   8032032  94% /usr/lib/wsl/lib
drvfs          123767236 115735204   8032032  94% /mnt/c
drvfs          123208700 105413632  17795068  86% /mnt/d


➜ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda        251G   11G  228G   5% /
none            2.0G  4.0K  2.0G   1% /mnt/wsl
tools           119G  111G  7.7G  94% /init
none            2.0G     0  2.0G   0% /dev
none            2.0G   12K  2.0G   1% /run
none            2.0G     0  2.0G   0% /run/lock
none            2.0G     0  2.0G   0% /run/shm
none            2.0G     0  2.0G   0% /run/user
tmpfs           2.0G     0  2.0G   0% /sys/fs/cgroup
drivers         119G  111G  7.7G  94% /usr/lib/wsl/drivers
lib             119G  111G  7.7G  94% /usr/lib/wsl/lib
drvfs           119G  111G  7.7G  94% /mnt/c
drvfs           118G  101G   17G  86% /mnt/d


Size      : 当前文件大小
Used      : 已使用的空间
Avail     : 可用空间
Use%      : 已使用空间占比
Mounted on: 挂载目录


➜ du -h
0       ./.pytest_cache/v/cache
0       ./.pytest_cache/v
0       ./.pytest_cache
20K     ./linux
64K     ./mysql
8.0K    ./numpy
16K     ./pandas/splits
100K    ./pandas
8.0K    ./python
0       ./target/source
0       ./target
0       ./test
200K    .


man    # 帮助命令
man df # 列出df命令的帮助文档

clear  # 清屏



【挂载&卸载】
mount /dev/hhb /mnt/hhb     # 将外部设备hhb挂载到 mnt 目录下来实现访问
umount -f /mnt/hhb          # 强制卸载



【进程管理】

1. 在Linux系统中，每一个程序都有自己的一个进程，每一个进程都有一个ID
2. 每一个进程都会有一个父进程
3. 进程可以有两种存在方式：前台运行 和 后台运行
4. 一般的话服务都是后台运行的，基本的程序都是前台运行的


ps        # 查看当前系统中正在执行的各种进程信息（process status）
ps -a     # 显示当前终端运行的所有进程信息
ps -u     # 以用户的信息显示进程
ps -x     # 显示后台运行进程的参数
ps -ef    # 可以查看到父进程信息

ps -aux | grep mysql # 查看mysql相关的进程信息

pstree     # 将所有进程以树状图显示(display a tree of processes)
pstree -p  # 显示父进程ID
pstree -u  # 显示用户组
pstree -pu # 显示用户组和父进程ID


kill -9 [pid]  # 强制终止进程(9是固定参数)



|    # 管道符，将上一个命令执行的结果作为下一条命令的输入
grep # 查找文件中符合条件的字符串


➜ ps -a               
  PID TTY          TIME CMD
 6416 pts/5    00:00:22 du
11659 pts/1    00:00:00 git
11660 pts/1    00:00:00 ssh
12786 pts/5    00:00:00 ps
18488 pts/1    00:00:00 sh
18493 pts/1    00:00:00 sh
18496 pts/1    00:01:20 node
18516 pts/1    00:00:48 node
18560 pts/1    00:00:07 node
18587 pts/1    00:19:20 node
18783 pts/1    00:00:04 node
18939 pts/1    00:02:35 node
20967 pts/1    00:00:02 node



➜ ps -aux | grep mysql
mysql     2995  0.0  0.0   2608  1704 ?        S    Feb28   0:00 /bin/sh /usr/bin/mysqld_safe
mysql     3146  0.4 10.5 2521124 426772 ?      Sl   Feb28   6:22 /usr/sbin/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib/mysql/plugin --log-error=/var/log/mysql/error.log --pid-file=Huabo.pid
hhb      12637  0.0  0.0   8028   736 pts/5    R+   23:22   0:00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn --exclude-dir=.idea --exclude-dir=.tox mysql


NOTE: ps 一般与 grep 搭配使用，只需要关注特定的进程就可以了，过滤掉不需要的信息


➜ pstree
init─┬─init───mysqld_safe───mysqld───39*[{mysqld}]
     ├─2*[init───init───node───6*[{node}]]
     ├─init───init───sh───sh───sh───node─┬─node─┬─zsh─┬─du
     │                                   │      │     └─pstree
     │                                   │      ├─zsh
     │                                   │      └─12*[{node}]
     │                                   ├─node───12*[{node}]
     │                                   ├─node─┬─git───ssh
     │                                   │      ├─git
     │                                   │      ├─node───10*[{node}]
     │                                   │      ├─node───21*[{node}]
     │                                   │      ├─node───6*[{node}]
     │                                   │      └─12*[{node}]
     │                                   └─10*[{node}]
     └─2*[{init}]




nohub /root/test.sh &
nohub /root/test.sh > log.log 2>&1 &

gz: 
压缩：tar -zcvf test.tar.gz *.txt
解压：tar -zxvf test.tar.gz -C abc


bz:
压缩：tar -jcvg test.bz2 *.txt
解压：tar -zxvf test.bz2 -C abc

tar
find
locate

NOTE: Linux 系统中严格区分大小写


tar -zxvf test.tar.gz             # 解压文件


find /test -name test.txt         # 在/test目录下查找test.txt文件
find /test -name "*test*"           # 在/test目录下查找名字中含有test的文件 - 模糊匹配搜索(*匹配任意字符)
find /test -name "test?"          # 在/test目录下查找名字中以test开头且test后有三个字符的文件 - (?匹配单字符)
find /test -iname test            # -iname 表示不区分大小写


1数据块 = 512字节 = 0.5k

100MB = 102400kb = 204800数据块

find / -size +204800              # 查找根目录下文件大小大于100M的文件 (+表示大于, - 表示小于, 不带符号表示等于)
find /home -user hhb              # 查找/home目录下所属用户为 hhb 的所有文件


find /etc -cmin -5                # 在 /etc 目录下查找5分钟内被修改过属性的文件和目录(+表示大于, - 表示小于, 不带符号表示等于)
-amin # 访问时间 access
-cmin # 文件属性 change
-mmin # 文件内容 modify



find /etc -size +163840 -a -size -204800  # 在/etc目录下查找文件大小大于80M小于100M的文件
-a 两个条件同时满足(and)
-o 两个条件仅满足一个即可(or)


find /etc -type f           # 根据文件类型查找
f - 文件
d - 目录
l - 链接


find . -name test.txt -exec ls -l {} \;    # 在当前目录下查找test.txt文件并显示其详细信息({} 表示查询结果，\表示转义符，; 表示命令结束符号)

➜ find . -name test.txt -exec ls -l {} \;
-rwxrwxrwx 1 hhb hhb 18 Feb 28 21:23 ./test/test.txt
-rwxrwxrwx 1 hhb hhb 10 Feb 28 22:29 ./test.txt


-exec/-ok 命令 {} \;  # 对搜索结果执行操作 (-ok 表示询问命令是否需要执行，在一些删除场景中可以二次确认，避免误删)



ps -aux | grep redis

kill -9 {pid}

netstat -tunlp | grep 80

find . -name test.txt -exec ls -l {} \;

ps -aux | grep firefox | awk '{print $2}' | xargs kill -9

killall firefox


➜ find . -name test.txt    
./test/test.txt
./test.txt


find . -inum 7881299348124667                # 查找i节点ID为7881299348124667的文件
find . -inum 7881299348124667 -exec rm {} \; # 查找i节点ID为7881299348124667的文件(有时候文件名包含特殊字符无法删除，可以使用这种方式来删除)




➜ find / -size +204800
/home/hhb/hhbdata/go1.16.7.linux-amd64.tar.gz
/home/hhb/.local/share/kite/kite-v2.20210128.0/lib/libtensorflow.so.1.15.0
/home/hhb/.local/share/kite/kite-v2.20210128.0/kited
/home/hhb/.local/share/kite/kite-v2.20210128.0/linux-unpacked/kite




locate # 在文件资料库中搜索文件，文件资料库定期更新，查找速度很快（/tmp 目录下的文件不在 locate 查找范围内，所有无法通过 locate 查找到）
updatedb # 更新文件资料库（新建一个文件后，需要执行该命令来更新文件资料库，才能通过 locate 查找到）


locate test.txt    # 查找test.txt文件
locate -i test.txt # 忽略大小写查找

which   # 搜索命令所在目录及别名信息
whereis # 搜索命令所在目录及帮助文档路径

➜ which cp
/usr/bin/cp

➜ whereis cp   
cp: /usr/bin/cp /mnt/d/Software/Dev/Git/usr/bin/cp.exe /usr/share/man/man1/cp.1.gz



grep -iv [指定字符串] [文件路径] # 在文件中搜索字符串匹配的行并输出
grep -i                        # 不区分大小写
grep -v                        # 排除指定字符串

➜ grep hello test.txt
hello vim



【ping】 测试网络连通性

需要关注丢包率（0% packet loss），丢包率越高，表示网络环境越差

➜ ping www.baidu.com    
PING www.a.shifen.com (180.101.49.11) 56(84) bytes of data.
64 bytes from 180.101.49.11 (180.101.49.11): icmp_seq=1 ttl=50 time=32.0 ms
64 bytes from 180.101.49.11 (180.101.49.11): icmp_seq=2 ttl=50 time=34.1 ms
64 bytes from 180.101.49.11 (180.101.49.11): icmp_seq=3 ttl=50 time=37.1 ms
64 bytes from 180.101.49.11 (180.101.49.11): icmp_seq=4 ttl=50 time=28.7 ms


ping -c 3 www.baidu.com    # 指定ping次数

➜  learning ping -c 3 www.baidu.com
PING www.a.shifen.com (180.101.49.11) 56(84) bytes of data.
64 bytes from 180.101.49.11 (180.101.49.11): icmp_seq=1 ttl=50 time=12.9 ms
64 bytes from 180.101.49.11 (180.101.49.11): icmp_seq=2 ttl=50 time=24.8 ms
64 bytes from 180.101.49.11 (180.101.49.11): icmp_seq=3 ttl=50 time=14.9 ms

--- www.a.shifen.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2064ms
rtt min/avg/max/mdev = 12.932/17.535/24.783/5.186 ms



【ifconfig】 查看和设置网络信息
➜ ifconfig     
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.174.199  netmask 255.255.240.0  broadcast 172.17.175.255
        inet6 fe80::215:5dff:fe7e:6a0  prefixlen 64  scopeid 0x20<link>
        ether 00:15:5d:7e:06:a0  txqueuelen 1000  (Ethernet)
        RX packets 23229  bytes 6617537 (6.6 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 17111  bytes 6637700 (6.6 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 81431  bytes 91608162 (91.6 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 81431  bytes 91608162 (91.6 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0



last           # 列出目前与过去登入系统的用户登录时间信息
lastlog        # 列出最后登录用户信息
lastlog -u 123 # 列出指定用户uid的最后登录信息


traceroute www.baidu.com # 显示数据包到主机间的路径，用于跟踪路由信息


netstat       # 显示网络相关信息
netstat -tlun # 查看本机监听的端口
netstat -an   # 查看本机的所有网络连接
netstat -rn   # 查看本机路由表