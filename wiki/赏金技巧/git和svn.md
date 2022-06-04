	Git 是一个修订控制系统，它包含一个隐藏文件夹“.git”。 充当项目的快照。 每次创建文件时，git 都会对其进行压缩并将其存储到自己的数据结构中。 压缩后的对象将具有唯一的名称、哈希，并将存储在对象目录下。 这意味着可以完全重新创建源代码和存储库中的所有其他内容。 如果您到“https://example.com/.git”并看到内容
	https://github.com/internetwache/GitTools/tree/master/Dumper
	$ ./gitdumper.sh https://example.com/.git/ /outputdirectory/
	.svn同理
	https://github.com/anantshri/svn-extractor