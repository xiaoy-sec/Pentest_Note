  #### SSRF
	你应该已经知道如何利用 AWS hacking 章节中的 SSRF，所以我不会在这里再次介绍它，因为那部分是相同的。唯一的区别是元数据 url 在 GCP 上略有不同。
	● http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/token
	请注意，如果启用了 V2，您可能还必须发送自定义标头，如下例所示：
	●"Metadata-Flavor: Google"
  #### 源代码泄露
	您可以在应用程序的源代码中找到几种类型的凭据。第一种类型是 API 密钥。如下所示，您的应用程序必须向包含您的 API 密钥的特定谷歌端点发出 HTTP 请求：
	● https://language.googleapis.com/v1/documents:analyzeEntities?key=API_KEY
	如果您正在查看“googleapis.com”的源代码，则可能会发现可能包含 API 密钥的字符串。
	除了 API 密钥，您还可以使用包含凭据的 JSON 文件。您必须对包含凭据的路径进行硬编码，而不是硬编码凭据。如果您有权访问该文件，则可能会破坏这些凭据。
  #### 环境变量
	另一个流行的存储凭证的地方是环境变量。 如下图所示，这些凭据存储在 json 文件中，并放入“GOOGLE_APPLICATION_CREDENTIALS”环境变量中。
	> env
  #### 网络钓鱼
	当所有其他方法都失败时，尝试网络钓鱼。