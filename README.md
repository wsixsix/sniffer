# sniffer
## 项目功能
基于jpcap实现的网络嗅探器,项目主要主要功能为网络抓包，可以抓取5层协议的数据包，包括TCP、UDP、ICMP、IP、ARP等常见协议，
并支持按照协议、源IP、目的IP或关键字对抓取的包筛选。项目另外实现了基于Java Swing的GUI，便于操作。

## 项目所需依赖
windows平台下需要下载安装winpcap与jpcap \
winpcap官网下载即可 \
jpcap 64位附百度网盘连接
```aidl
https://pan.baidu.com/s/1olfe7TZkAXCgWNIUleFNJw?pwd=ccnd
```
下载解压后将jpcap.dll文件放在安装路径
```aidl
jdk\jre\bin
```
将jpcap.jar文件添加到项目依赖即可 \
GUI界面的实现基于idea自带swing ui designer，需确保 UI Designer插件可用

## 项目结构
项目主要由两部分组成，分别为 \
control包，实现后台抓包与分析 \
show包，实现GUI界面

## 项目启动
项目的启动入口为UIForm.java文件下的main函数，直接运行即可

## 更新日志
1.更新PacketAnalyze.java,解决编译时的Exception in thread "Thread-2" java.lang.ClassCastException: class java.net.Inet4Address cannot be cast to class java.lang.String (java.net.Inet4Address and java.lang.String are in module java.base of loader 'bootstrap')的报错信息。

## 相关说明
### 运行时问题
抓包抓多了的时候就会像这样报错，说已经打开255个设备,java.10.I0Exception Create at jpcap.Jpcapcaptor.<init>(JpcapCapton.java:34) at jpcap.Jpcapcaptor.openDevice(Jpcapcaptor.java:67) at src.controll.PacketCapture.run(PacketCapture.java:96)

### 相关知识点
首先JpcapCaptor.openDevice是Jpcap库中的一个方法，用于打开网络接口设备（即网卡设备）。使用此方法可以获取到网络接口的实例，以便进一步进行网络数据包的捕获和分析。

以下是使用JpcapCaptor.openDevice方法的一般步骤：

获取网络接口列表：首先，你需要获取本机的网络接口列表。这可以通过Jpcap提供的JpcapCaptor.getDeviceList()方法完成。该方法返回一组NetworkInterface对象，每个对象包含了对应网络接口的一些信息，如名称、描述、IP地址、MAC地址以及数据链路层名称和描述。
选择要打开的网络接口：从获取到的网络接口列表中，选择你想要打开的网络接口。这通常基于你的需求，例如，你可能想要选择连接到外部网络的接口。
打开网络接口：使用JpcapCaptor.openDevice()方法打开选择的网络接口。该方法需要几个参数，包括你想要打开的网卡设备实例、数据包的最大捕获长度、是否开启混杂模式以及抓包的超时时间。混杂模式允许你的程序捕获到所有经过网络接口的数据包，而不仅仅是那些目的地址是本机的数据包。
以下是JpcapCaptor.openDevice方法的一个示例代码片段：

java
NetworkInterface[] devices = JpcapCaptor.getDeviceList(); // 获取网络接口列表  
int index = // 设置你想要打开的网卡设备的索引;  
JpcapCaptor captor = JpcapCaptor.openDevice(devices[index], 65535, false, 20); // 打开网络接口设备
在上述代码中，devices[index]是你想要打开的网络接口设备实例，65535是数据包的最大捕获长度（这里设置为最大值），false表示不开启混杂模式，20是抓包的超时时间（单位为毫秒）。

通常意味着在尝试打开网络接口时，系统已经达到了允许同时打开的网络设备数量的上限。在大多数操作系统中，同时打开的网络设备数量是有限制的，而这个限制通常是为了避免资源耗尽或系统不稳定。

### 解决方法
要解决这个问题，你可以尝试以下几种方法：
1.关闭其他程序：确保没有其他程序（如其他网络监控工具或VPN软件）正在使用或占用网络接口。关闭这些程序后，尝试再次使用JpcapCaptor.openDevice打开网络接口。

2.重启计算机：有时，简单地重启计算机可以释放被占用的网络接口资源。重启后，再次尝试打开网络接口。

3.检查代码中的错误：确保你的代码中没有重复打开同一个网络接口的情况。每次调用JpcapCaptor.openDevice后，都应该相应地调用captor.close()来关闭接口，以避免资源泄露。

4.检查权限问题：在某些情况下，没有足够的权限可能导致无法打开网络接口。确保你的程序以管理员或具有足够权限的用户身份运行。

5.减少同时打开的网络接口数量：如果你的程序需要同时监控多个网络接口，尝试减少同时打开的网络接口数量。你可以设计一种策略，比如按顺序打开和关闭接口，或者只打开必要的接口。

6.更新或替换库：如果上述方法都不奏效，可能是你使用的Jpcap库版本存在兼容性问题或已知bug。考虑更新到最新版本的Jpcap，或者寻找其他网络包捕获库作为替代，比如Pcap4J或WinPcap/Npcap。

7.检查系统限制：某些操作系统对可以同时打开的网络接口数量有严格的限制。你可以通过查看操作系统的文档或联系技术支持来了解这些限制，并尝试调整系统设置以允许打开更多的网络接口。

8.检查硬件问题：在某些情况下，网络接口硬件本身的问题也可能导致无法打开设备。检查网络接口卡（NIC）是否正常工作，或者尝试使用不同的网络接口。


## 参考资料
1.参考Jpcap JAVA捕捉并分析网络数据包 https://blog.csdn.net/arjick/article/details/4525474 

2.参考JpcapManager源码 http://code.google.com/p/ken-javaframeword/source/browse/trunk/JavaFramework2.0/src/com/shine/framework/Jpcap/JpcapManager.java


