# lvresize 命令解释
***
是重新定义逻辑卷的大小，顾名思义就是说可以扩展也可以缩小。    

正常扩展命令为lvextend，而缩减为lvredece，然后执行reseze2fs，两条命令容易出错。    

例如:逻辑卷缩减到10G或者扩展到10，都可以执行以下命令：   

`lvresize -r -L 10G /dev/vg0/centoslv`  

执行完以后，ext系统执行partx -a /dev/vg0/centoslv；xfs则执行xfs_growdfs /dev/vg0/centoslv  


