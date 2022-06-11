	枚举 blob
	PS > . C:\Tools\MicroBurst\Misc\InvokeEnumerateAzureBlobs.ps1
	PS > Invoke-EnumerateAzureBlobs -Base <SHORT DOMAIN> -OutputFile azureblobs.txt
	Found Storage Account -  testsecure.blob.core.windows.net
	Found Storage Account -  securetest.blob.core.windows.net
	Found Storage Account -  securedata.blob.core.windows.net
	Found Storage Account -  securefiles.blob.core.windows.net
	列出和下载 blob
	PS Az> Get-AzResource
	PS Az> Get-AzStorageAccount -name <NAME> -ResourceGroupName <NAME>
	PS Az> Get-AzStorageContainer -Context (Get-AzStorageAccount -name <NAME> -ResourceGroupName <NAME>).context
	PS Az> Get-AzStorageBlobContent -Container <NAME> -Context (Get-AzStorageAccount -name <NAME> -ResourceGroupName <NAME>).context -Blob
