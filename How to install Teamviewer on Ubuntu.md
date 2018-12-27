来自https://blog.csdn.net/u014075470/article/details/80083961
>
### 第一步: 下载 安装包<br />
从官网下载ubuntu的deb安装包 <br />
地址为：<br />
```
https://www.teamviewer.com/en/download/linux/
```

### 第二步: 执行命令
```
>> sudo dpkg -i teamviewer_12.0.71510_i386.deb
## 出现 Processing xxxx表示安装成功
```
### 第三步:接受license

### 第四步：停止teamviewer
```
>> sudo teamviewer --daemon stop
```
### 第五步：修改配置文件
```
## 在文件 /opt/teamviewer/config/global.conf末尾增加以下内容（由于只读限制，所以可能需要用到管理员权限）
## [int32] EulaAccepted = 1 
## [int32] EulaAcceptedRevision = 6 
>> cd /opt/teamviewer/config/
>> ll
>> vim global.conf
## 添加一下内容
## [int32] EulaAccepted = 1 
## [int32] EulaAcceptedRevision = 6 
## 保存退出
>> wq
## 为了一次成功,修改文件后最好是查看一下修改的文件
>> cat global.conf
## 确认添加保存了之后启动teamviewer
```
### 第六步：启动teamviewer
```
>> sudo teamviewer --daemon start
```
### 第七步:设置密码
```
## 设置teamviewer 密码
>> sudo teamviewer --passwd [NEWPASSWORD]
```
### 第八步:获取Teamviewer ID
```
## 获取teamviewer ID
>> teamviewer --info print id
```
