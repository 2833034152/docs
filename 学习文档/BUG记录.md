BUG：

二次 powoff 之后JLink 读取失败了，pow on 只是开关打开供电，但断电再次上电需要 重新设置下（ JLink 和 芯片相连的细节）才行：

解决方法：二级、三级复位解决。

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTcxMjUyMzM4MF19
-->