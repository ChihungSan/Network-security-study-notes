# phpinfo输出中Local Value和Master Value

1. Master Value是PHP.ini文件中的内容。
2. Local value 是当前目录中的设置，这个值会覆盖Master Value中对应的值

由于WEB Sever Config或.htaccess的设置,或程序中ini_set()的设置,当前目录中的设置会不同于PHP.ini文 件中的设置

参考：

- https://blog.csdn.net/chengke626/article/details/51031333?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-4-51031333-blog-84369547.235%5Ev43%5Econtrol&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-4-51031333-blog-84369547.235%5Ev43%5Econtrol&utm_relevant_index=7