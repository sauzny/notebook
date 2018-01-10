# java工具KeyTool生成服务端和客户端使用的证书

http://blog.csdn.net/zmken497300/article/details/53186730

```
创建目录 D:/java_keytool_space/
```

## 1. 为服务器生成证书

```
keytool -genkey -alias webdemo -keypass webdemopasswd -keyalg RSA -keysize 1024 -validity 365 -keystore D:/java_keytool_space/webdemo.keystore -storepass webdemopasswd
```

```
您的名字与姓氏是什么?
  [Unknown]:  WebDemo
您的组织单位名称是什么?
  [Unknown]:  www.webdemo.com
您的组织名称是什么?
  [Unknown]:  webdemo
您所在的城市或区域名称是什么?
  [Unknown]:  TJ
您所在的省/市/自治区名称是什么?
  [Unknown]:  TJ
该单位的双字母国家/地区代码是什么?
  [Unknown]:  ZH
CN=WebDemo, OU=www.webdemo.com, O=webdemo, L=TJ, ST=TJ, C=ZH是否正确?
  [否]:  y
```

## 2. 为客户端生成证书

```
keytool -genkey -alias client -keypass webdemopasswd -keyalg RSA -keysize 1024 -validity 365 -storetype PKCS12 -keystore D:/java_keytool_space/client.p12 -storepass webdemopasswd
```

```
您的名字与姓氏是什么?
  [Unknown]:  WebDemo
您的组织单位名称是什么?
  [Unknown]:  www.webdemo.com
您的组织名称是什么?
  [Unknown]:  webdemo
您所在的城市或区域名称是什么?
  [Unknown]:  TJ
您所在的省/市/自治区名称是什么?
  [Unknown]:  TJ
该单位的双字母国家/地区代码是什么?
  [Unknown]:  ZH
CN=WebDemo, OU=www.webdemo.com, O=webdemo, L=TJ, ST=TJ, C=ZH是否正确?
  [否]:  y
```

## 3. 让服务器信任客户端证书

```
keytool -export -alias client -keystore D:/java_keytool_space/client.p12 -storetype PKCS12 -keypass webdemopasswd -file D:/java_keytool_space/client.cer

keytool -import -v -file D:/java_keytool_space/client.cer -keystore D:/java_keytool_space/webdemo.keystore -storepass webdemopasswd

```

```
查看命令：（看也看不懂，不用看）
keytool -list -v -keystore D:/java_keytool_space/webdemo.keystore
```

## 4. 让客户端信任服务器证书

```
keytool -keystore D:/java_keytool_space/webdemo.keystore -export -alias webdemo -file D:/java_keytool_space/server.cer
```