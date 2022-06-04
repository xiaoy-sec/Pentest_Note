	端口9200，用shodan查找，通过GET请求来确认
	发送GET请求/_cat/indices?v 列出所有索引
	发送GET请求/_stats/?pretty=1 也可以查到信息
	发送GET请求/_all/_search?q=email 查询email，可以换成username,user,password,token
	要查询特定索引，可以将“_all”替换为要搜索的索引的名称
	另一种有用的技术是通过向“/INDEX_NAME_HERE/_mapping?pretty=1”端点发出 GET 请求来列出所有字段名称
	查询包含特定字段名称的所有值，请使用以下命令“/_all/_search?q=_exists:email&pretty=1”。 这将返回包含名为 email 的字段名称（列）的文档