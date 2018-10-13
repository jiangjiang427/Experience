              #sed命令学习心得#

***
##先初步介绍下sed##   

sed是一种流编辑器，它一次处理一行内容。处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space）,接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。然后读入下行，执行下一个循环。如果没有使诸如‘D’的特殊命令，那会在两个循环之间清空模式空间，但不会清空保留空间。这样不断重复，直到文件末尾。文件内容并没有改变，除非你使用重定向存储输出。   

##sed练习##  

1.删除centos7系统/etc/grub2.cfg文件中所有以空白开头的行行首的空白字符 

`[root@centos7 ~]#sed -i -r 's/^[[:space:]]*([[:alnum:]].*)/\1/' /etc/grub2.cfg `  

2.删除/etc/fstab文件中所有以#开头，后面至少跟一个空白字符的行的行首的#和空白字符   

`[root@centos7 ~]#sed -i -r 's/^#[[:space:]]+(.*)/\1/' /etc/fstab `  

3.在centos6系统/root/install.log每一行行首增加#号   

`[root@centos6 ~]#sed -i -r 's/(.*)/#\1/' /root/install.log `  

4.在/etc/fstab文件中不以#开头的行的行首增加#号   

`[root@centos7 ~]#sed -i -r 's/^[^#](.*)/#\1/' /etc/fstab`  

5.处理/etc/fstab路径,使用sed命令取出其目录名和基名   

`[root@centos7 ~]#echo /etc/fstab |sed -nr 's@(^.*/)([^/].*/?)@\1@p'`  

`[root@centos7 ~]#echo /etc/fstab |sed -nr 's@(^.*/)([^/].*/?)@\2@p'`    

6.利用sed 取出ifconfig命令中本机的IPv4地址   

`[root@centos7 ~]#ifconfig | sed -nr 's/.*inet (addr:)?(.*)(netmask|Bcast).*/\2/p' | head -n 1 `  

`[root@centos7 ~]#ifconfig | sed -r '2!d;s/.*inet (addr:)?//;s/ .*//'`  

7.统计centos安装光盘中Package目录下的所有rpm文件的以.分隔倒数第二个字段的重复次数   

`[root@centos7 ~]#ls /run/media/root/CentOS\ 7\ x86_64/Packages/ | sed -r 's/.*\.(.*).rpm$/\1/' | sort | uniq -c `  

`[root@centos7 ~]#ls *.rpm | rev | cut -d. -f2 | rev |sort | uniq -c` 

8.统计/etc/init.d/functions文件中每个单词的出现次数，并排序（用grep和sed两种方法分别实现）   

`[root@centos7 ~]#cat /etc/init.d/functions | grep -Eo [[:alpha:]]+ | sort | uniq -c`  

`[root@centos7 ~]#sed -r 's/[^[:alpha:]]+/\n/g' /etc/init.d/functions | sort | uniq -c`  

9.将文本文件的n和n+1行合并为一行，n为奇数行   

`[root@centos7 ~]#sed -n 'N;s/\n//p' f1`

##sed深层次的理解##  

+新建一个test文本
````bash
[root@centos7 test]#cat test.txt 
systemctl_redirect
redhalt5
strstr8

return

+运行sed options为-n 且查找替换后有p选项 
````bash
[root@centos7 test]#sed -nr 's/[^[:alpha:]]+/\n/gp' test.txt
systemctl
redirect
redhalt

strstr

+运行sed options无-n 且查找替换后没有p选项
````bash
[root@centos7 test]#sed -r 's/[^[:alpha:]]+/\n/g' test.txt  
systemctl
redirect
redhalt

strstr


return
+由此可以看出sed的模式空间与打印屏幕之间的关系
