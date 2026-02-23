# Burp Suite intruder爆破模块四种模式的区别

1. sniper(俗称狙击手模式)：只有一个字典（一组payload）进行攻击，但是攻击位置可以有多个，当有多个时，按顺序来，现在第一个位置爆破所有的payload，然后下一个位置。
2. Battering ram（攻城锤）：和sniper一样，只有一个字典（一组payload），但是攻击位置可以有多个。当有多个位置时，多个位置同时使用同一个payload。例如有uid和psd两个位置，如果字典中的第一条是haha，则第一次攻击时会把haha这个值同时赋值给uid和psd连个变量。
3. Pitchfork（草叉模式）：多个位置多个字典。例如，爆破两个位置，一个uid，一个psd，则假设uid的字典为A，psd的字典为B，则当攻击时，第一攻击对应的是A的第一条payload攻击第一个位置，B的第一条攻击对应B的第一条payload。A和B的第一次攻击是同步的。A【1】将会对应B【1】进行attack处理，
4. Cluster bomb（集束炸弹）：多个位置多个字典。攻击时，当A的第一条payload攻击第一个位置，则B的位置会遭受到B字典的全部的数据的攻击。相当于两层循环。

参考：

- https://blog.csdn.net/m0_46315342/article/details/107146670