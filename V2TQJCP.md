# Aws - 识别 Aws::SDKOptions 对象

## 目录
- [上级 &#8673;](./笔记-逆向.md)  

## 正文
* 内存布局  
  在 `64bit release` 模式下, 编译后的 `SDKOptions` 大小为 `2c0h` 字节, 内存布局如下:  
  | Name                  | Offset    | Size    | Specification |
  | --------------------- | --------- | ------- | ------------- |
  | `loggingOptions.logLevel` | 0h | 4h ||
  | `loggingOptions.defaultLogPrefix` | 8h | 8h ||
  | `loggingOptions.logger_create_fn` | 10h | 40h ||
  | `memoryManagementOptions.memoryManager` | 50h | 8h ||
  | `httpOptions.httpClientFactory_create_fn` | 58h | 40h ||
  | `httpOptions.initAndCleanupCurl` | 98h | 1h ||
  | `httpOptions.installSigPipeHandler` | 99h | 1h ||
  | `cryptoOptions.md5Factory_create_fn` | a0h | 40h ||
  | `cryptoOptions.sha256Factory_create_fn` | e0h | 40h ||
  | `cryptoOptions.sha256HMACFactory_create_fn` | 120h | 40h ||
  | `cryptoOptions.aes_CBCFactory_create_fn` | 160h | 40h ||
  | `cryptoOptions.aes_CTRFactory_create_fn` | 1a0h | 40h ||
  | `cryptoOptions.aes_GCMFactory_create_fn` | 1e0h | 40h ||
  | `cryptoOptions.aes_KeyWrapFactory_create_fn` | 220h | 40h ||
  | `cryptoOptions.secureRandomFactory_create_fn` | 260h | 40h ||
  | `cryptoOptions.initAndCleanupOpenSSL` | 2a0h | 1h ||
  | `monitoringOptions.customizedMonitoringFactory_create_fn` | 2a8h | 18h ||
