# grep命令学习心得
***
## 什么是grep
        grep: Global search REgular expression and Print out the line  

        作用：文本搜索工具，根据用户指定的“模式”对目标文本逐行进行匹配检查；打印匹配到的行  

        模式：由正则表达式字符及文本字符所编写的过滤条件

## grep用法  

   grep [OPTIONS] PATTERN [FILE...]  
   
      grep root /etc/passwd  
      
      grep "$USER"  /etc/passwd  
      
      grep '$USER'  /etc/passwd  
      
      grep `whoami`  /etc/passwd

 grep命令选项  
 
      --color=auto: 对匹配到的文本着色显示   
      
      -v: 显示不被pattern匹配到的行 相当于排除或取反的意思  
      
    	-i: 忽略字符大小写   
      
	    -n：显示匹配的行号   
      
	    -c: 统计匹配的行数   
      
	    -o: 仅显示匹配到的字符串   
      
	    -q: 静默模式，不输出任何信息   
      
	    -nA #: after, 后#行   
      
	    -nB #: before, 前#行  
      
    	-nC #：context, 前后各#行   
      
      -e：实现多个选项间的逻辑or关系   
      
      rep –e ‘cat ’  -e ‘dog’  file   
      
	    -w ：匹配整个单词   
        
    	-E：使用ERE   
      
	    -F：相当于fgrep，不支持正则表达式   
      
	    -f file: 根据模式文件处理 （后面跟文件）
      
## grep练习题  

* 1.显示/proc/meminfo文件中以大小s开头的行(要求：使用两种方法)  

`[root@centos6 ~]#grep '^[Ss].*' /proc/meminfo`  

`[root@centos6 ~]#grep -i "^[s]" /proc/meminfo`

* 2.显示/etc/passwd文件中不以/bin/bash结尾的行  

`[root@centos6 ~]#grep -v '/bin/bash$' /etc/passwd`

* 3.显示用户rpc默认的shell程序   
  
`[root@centos6 ~]#grep '\<rpc\>' /etc/passwd | cut -d: -f7`

* 4.找出/etc/passwd中的两位或三位数    
   
`[root@centos6 ~]#grep -o '[0-9]\{2,3\}' /etc/passwd`

* 5.显示CentOS7的/etc/grub2.cfg文件中，至少以一个空白字符开头的且后面有非空白字符的行  

`[root@centos7 ~]#grep ^C[[:space:]]\+[^[:space:]]" /etc/grub.conf`

* 6.找出“netstat -tan”命令结果中以LISTEN后跟任意多个空白字符结尾的行  

`[root@centos6 ~]#netstat -tan | grep "\<LISTEN[[:space:]]*"`


