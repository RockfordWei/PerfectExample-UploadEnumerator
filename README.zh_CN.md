# Upload Enumerator [简体中文](README.zh_CN.md)

<p align="center">
    <a href="http://perfect.org/get-involved.html" target="_blank">
        <img src="http://perfect.org/assets/github/perfect_github_2_0_0.jpg" alt="Get Involed with Perfect!" width="854" />
    </a>
</p>

<p align="center">
    <a href="https://github.com/PerfectlySoft/Perfect" target="_blank">
        <img src="http://www.perfect.org/github/Perfect_GH_button_1_Star.jpg" alt="Star Perfect On Github" />
    </a>  
    <a href="https://gitter.im/PerfectlySoft/Perfect" target="_blank">
        <img src="http://www.perfect.org/github/Perfect_GH_button_2_Git.jpg" alt="Chat on Gitter" />
    </a>  
    <a href="https://twitter.com/perfectlysoft" target="_blank">
        <img src="http://www.perfect.org/github/Perfect_GH_button_3_twit.jpg" alt="Follow Perfect on Twitter" />
    </a>  
    <a href="http://perfect.ly" target="_blank">
        <img src="http://www.perfect.org/github/Perfect_GH_button_4_slack.jpg" alt="Join the Perfect Slack" />
    </a> 
</p>

<p align="center">
    <a href="https://developer.apple.com/swift/" target="_blank">
        <img src="https://img.shields.io/badge/Swift-3.0-orange.svg?style=flat" alt="Swift 3.0">
    </a>
    <a href="https://developer.apple.com/swift/" target="_blank">
        <img src="https://img.shields.io/badge/Platforms-OS%20X%20%7C%20Linux%20-lightgray.svg?style=flat" alt="Platforms OS X | Linux">
    </a>
    <a href="http://perfect.org/licensing.html" target="_blank">
        <img src="https://img.shields.io/badge/License-Apache-lightgrey.svg?style=flat" alt="License Apache">
    </a>
    <a href="http://twitter.com/PerfectlySoft" target="_blank">
        <img src="https://img.shields.io/badge/Twitter-@PerfectlySoft-blue.svg?style=flat" alt="PerfectlySoft Twitter">
    </a>
    <a href="https://gitter.im/PerfectlySoft/Perfect?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge" target="_blank">
        <img src="https://img.shields.io/badge/Gitter-Join%20Chat-brightgreen.svg" alt="Join the chat at https://gitter.im/PerfectlySoft/Perfect">
    </a>
    <a href="http://perfect.ly" target="_blank">
        <img src="http://perfect.ly/badge.svg" alt="Slack Status">
    </a>
</p>

本示范工程展示了如何在服务器上处理从浏览器上载的文件。为了便于理解，本项目内只包括了一个服务器单元，用于产生HTML文件，便于您在浏览器中查看。

## 问题报告

目前我们已经把所有错误报告合并转移到了JIRA上，因此github原有的错误汇报功能不能用于本项目。

您的任何宝贵建意见或建议，或者发现我们的程序有问题，欢迎您在这里告诉我们。 [http://jira.perfect.org:8080/servicedesk/customer/portal/1](http://jira.perfect.org:8080/servicedesk/customer/portal/1)。

目前问题清单请参考以下链接： [http://jira.perfect.org:8080/projects/ISS/issues](http://jira.perfect.org:8080/projects/ISS/issues)


## 快速开始


请使用 SPM 软件包管理器编译本工程，即在工程目录下执行命令 ```swift build``` 后运行``` .build/debug/UploadEnumerator```。

如果要使用 Xcode 编译，请将编译目标（target）设置为 **URL Routing** 。这样做可以启动 Perfect 服务器。

启动后请用浏览器查看 [http://localhost:8181/](http://localhost:8181/)以检查运行结果。您可以尝试自行增加不同的 URL 路由来试验效果。

## 服务器操作
1. 服务器模块包括以下两个相关文件：
	* **UploadHandler.swift**，位于 `PerfectServerModuleInit` 函数中。注意这个函数是每个 Perfect 服务器模块都必须要实现的；还有一个是 `UploadHandler` 类，用于实现 `PageHandler` 页面句柄协议。
	* **index.mustache**，包含了 HTML 响应的页面模板。
2. 如果您使用 Xcode 编译，并将编译目标（target）设置为 **Upload Enumerator** ，则编译完成后会在工程目录下自动生成一个目录叫做 **PerfectLibraries**。当 Perfect 服务器启动之后，服务器会根据预先设置的工作文件夹路径查看这个目录，并加载所有模块、逐个调用各个模块中的 `PerfectServerModuleInit` 函数。
3. 当 Xcode 编译目标 **Upload Enumerator** 编译完成后， 系统会将 **index.mustache** 文件拷贝到名为 **webroot** 的目录下。这么做的目的是将该目录下的文件默认设置为该网站的网页。
4. `PerfectServerModuleInit` 执行后会为 Web 服务器增加一个根路由，并将该路由关联到对应的闭包，用于逐个处理HTTP请求。
5. 如果 HTTP 请求指向了服务器根目录，则服务器会解析 **index.mustache** 文件。
6. 服务器会调用 `UploadHandler.valuesForResponse` 函数。该函数是`MustachePageHandler`页面模板处理器协议的一部分。调用时服务器会将这个句柄作为参数传递给 HTTP 请求的`MustacheEvaluationContext` 模板上下文语义环境对象，以及`MustacheEvaluationOutputCollector`输出采集对象，这样请求的所有内容就都被传递并保存给处理器句柄。函数 `valuesForResponse`返回值是一个字典对象，能够在mustache模板处理时将变量内容替换为目标数据值。模板处理结果将返回给客户端浏览器。
7. 处理句柄将访问 `WebRequest.fileUploads` 属性。该属性是一个数组，每个上传的文件都对应一个`MimeReader.BodySpec`对象。该对象属性有：
	* field name 字段名
	* file name 文件名
	* file size 文件大小
	* file content type 文件内容类型（由浏览器在上传的时候决定）
	* local temporary file name 本地临时保存的文件名和路径位置
	*  `File` 文件对象包含了真正上载到服务器缓冲的数据
8. 处理器句柄将遍历所有上传的文件并将其有关信息存入字典。字典对象的内容值将用到 mustache 模板上，替换变量并生成最终的 HTML 页面。
9. 随后处理器具柄会遍历查看浏览器发过来的所有的非文件相关参数，其变量名称和数值将以各自的字典形式存放。在结果输出模板上，浏览器之前提交的文件与非文件信息都会被显示出来。
10. 最后，处理器会为“标题”页面增加一个字典，并将结果字典返回到`valuesForResponse`函数。
11. **index.mustache** 模板首先遍历 `{{#files}}` 元素，将每个上传文件内容输出到一个表格，然后再把非文件的参数`{{#params}}`打印成一个清单。模板处理完成后，会将所有HTML结果返回给浏览器。



## 更多内容
关于 Perfect 工程的更多内容，请参考官网 [perfect.org](http://perfect.org).
