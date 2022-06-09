	攻击者可以更新目标对象的所有者。一旦对象所有者更改为攻击者控制的主体，攻击者就可以以他们认为合适的任何方式操纵对象。这可以通过 Set-DomainObjectOwner（PowerView 模块）来实现。
	此 ACE 可被滥用于即时计划任务攻击，或用于将用户添加到本地管理员组。
	>Set-DomainObjectOwner -Identity 'target_object' -OwnerIdentity 'controlled_principal'