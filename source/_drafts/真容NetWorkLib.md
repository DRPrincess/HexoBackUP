---
title: 真容NetWorkLib
tags:
---

# NetWorkLib

### 功能说明：
网络请求通用库

### 使用的第三方库

|第三方库|GitHub链接|
|----|----|
|Okhttp|https://github.com/square/okhttp/|
|Gson|https://github.com/google/gson|

### 类说明：

- utils : 需要使用的工具类
|类名|作用|
|---|----|
|EncryptUtil|约定的密码加密算法|
|ResultCallback|接口访问回调接口|
|ProgressCallback|文件下载监听回调接口|
- bean : Gson 解析用的实体类
- fapi : 网络相关配置
- callback ：接口访问回调
|类名|作用|
|---|----|
|FapiCallback|接口访问回调接口|
|ResultCallback|接口访问回调接口|
|ProgressCallback|文件下载监听回调接口|

- request ：放置网络请求
|类名|作用|
|---|----|
|FapiCallback|接口访问回调接口|

|通过 buildType 控制服务器网址|Api|
|页面路由相关|ApiConfig|
|不同APP的 PackageType|ChemaoSdkConfig|
|约定的密码加密算法|EncryptUtil|
|app错误日志记录接口|ErrorReport|
|接口访问回调接口|FapiCallback，ResultCallback|
|Fapi接口请求函数|FapiClient|
|请求基类|FapiRequest|
|接口返回统一格式 Bean|FapiResponse，StatusCode|
|文件下载监听回调接口|ProgressCallback|
