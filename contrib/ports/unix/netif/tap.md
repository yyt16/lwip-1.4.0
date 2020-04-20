[toc]

# 1.基本概念
TUN和TAP设备是Linux内核虚拟网络设备，纯软件实现。    

OS向连接到TUN/TAP设备的用户空间程序发送报文；用户空间程序可像物理口发送报文那像向TUN/TAP口发送报文，在这种情况下，TUN/TAP设备发送（或注入）报文到OS协议栈，就像报文从物理端口收到一样。
# 2.如何工作
 TUN/TAP 为简单的点对点或以太网设备，不是从物理介质接收数据包，而是从用户空间程序接收；不是通过物理介质发送数据包，而是将它们发送到用户空间程序。
 在Linux内核中添加了一个TUN/TAP虚拟网络设备的驱动程序和一个与之相关的字符设备/dev/net/tun，字符设备tun作为用户空间和内核空间交换数据的接口。
  tun/tap驱动程序中包含两部分：字符设备驱动和网卡驱动。利用网卡驱动部分接受来自tcp/ip协议栈的网络分包并发送或者反过来将接收到的网络分包传给协议栈处理。而字符设备驱动部门将网络分包在内核与用户态之间传送，模拟物理链路的数据接受和发送。tun/tap驱动很好的实现了两种驱动的结合。
## 2.1 tun设备的数据走向
1. User Application A通过套接字（socket A）发数据发给使用与eno16777736处于同一个网段ip的应用程序，数据走向为通过socket A发给协议栈，最后通过netdevice子系统中的eno16777736的设备驱动（以太网驱动）发送出去，这个是通过真实的物理网卡发送出去。
2. User Application B通过套接字（socket B）发送数据给使用与nsfocus_tun0处于同一个网段ip的应用程序，数据走向为通过socket B发送给协议栈，最后通过netdevice子系统中的nsfocus_tun0的设备驱动（tun驱动）发送出去。由于tun设备没有对应真实的物理网卡，所以nsfocus_tun0对端收取数据的是User Application C。User Application C通过读写/dev/tun设备文件进行数据的收发。

## 2.2 tap设备的数据走向
1. User Application A通过套接字（socket A）发数据发给使用与eth处于同一个网段ip的应用程序，数据走向为通过socket A发给协议栈，最后通过netdevice子系统中的eno16777736的设备驱动（以太网驱动）发送出去，这个是通过真实的物理网卡发送出去
2. User Application B通过套接字（socket B）发送数据给使用与nsfocus_tap0处于同一个网段ip的应用程序，数据走向为通过socket B发送给协议栈，最后通过netdevice子系统中的nsfocus_tap0的设备驱动（tun驱动）发送出去。由于tun设备没有对应真实的物理网卡，所以nsfocus_tap0对端收取数据的是User Application C。User Application C通过读写/dev/tun设备文件进行数据的收发。

## 2.3 tun、tap比对
* tun 是点对点的设备 ， 而 tap 是一个普通的以太网卡设备 。 也就是说 ，tun 设备其实完全不需要有物理地址的 。 它收到和发出的包不需要 arp， 也不需要有数据链路层的头 。 而 tap 设备则是有完整的物理地址和完整的以太网帧 。
* TUN（Tunel）设备模拟网络层设备，处理三层报文，如IP报文。TAP设备模型链路层设备，处理二层报文，比如以太网帧。TUN用于路由，而TAP用于创建网桥。