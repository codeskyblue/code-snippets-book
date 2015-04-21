## ADB 常用命令

[adb forward交互流程](http://blog.csdn.net/wanghui_nju/article/details/5630570)

- forward

	```
	adb forward tcp:6100 tcp:7100 // 
	```
	
	PC上所有6100端口通信数据将被重定向到手机端7100端口server上

	或者
	
	```
	adb forward tcp:6100 local:logd
	```

	PC上所有6100端口通信数据将被重定向到手机端UNIX类型socket上

### 修改adb监听的端口

	$ export ANDROID_ADB_SERVER_PORT=12345 
	$ adb start-server
	* daemon not running. starting it now on port 12345 *
	* daemon started successfully *
	$ adb devices
	List of devices attached 
	TA2070M5O6  device

	$ adb -P 12345 start-server
	$ adb -P 12345 devices
	# 效果一样



