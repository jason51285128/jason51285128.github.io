# AI分布式训练-Master组件

## 需求分析

[分布式训练系统架构](http://wiki.wx.xunlei.cn:8090/pages/viewpage.action?pageId=38316364)

master组件负责：

分发任务信息：
1. 配置信息：节点的拓扑结构
2.任务信息：要训练哪个网络，网络相关数据有哪些，数据来源以及如何下载; 通过pb管理
3.ps与ps之间模型的同步

汇总并上报信息
1、训练状态：准确率、精度、进度、异常等
2、设备状态：可用性、CPU、内存等

上游：control panel




下游： ps



master

监听control panel的请求，完成对模型的初始化，并将模型任务下发到ps

汇总ps的训练结果，上报给control panel



