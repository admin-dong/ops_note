![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1725810237743-d67a8e74-bf41-4b92-b38b-a169474b9c77.png)

\1. 🌟**定位进程:整体排查: vmstat /top/ps aux 找出哪个进程的W问题 **

\2. 🌟 **找出进程对应线程id : **

\1. **通过top -Hp java进程id找出是哪个java线程的问题 **

1. 或者 show_busy_java_threads.sh 

\3. 🌟**转换**:线程id--->16进制:**问题线程的id 转换为16进制线程id **

\4. 🌟**找出线程的详细信息: jstack (显示java 进程信息) jstack java进程id过滤 java线程的16进制id 与开发沟通 **

\5. **显示jvm信息**: jmap (显示java jvm信息) jmap -heap java**进程id**显示jvm的内存使用情况 

\6. **jvm内存内容导出**:jmap (导出 jvm内存的内容 ) jmap -dump:format=b,file=/root/tomcat.bin pid 

\7. **给开发分析jvm导出文件**:通过 mat(Eclipse Memory Analyzer Tool )分析 windows