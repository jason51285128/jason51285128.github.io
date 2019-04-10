---
layout: post
title: go中如何获得程序的火焰图
categories: go
---

# go中如何获得程序的火焰图

火焰图能够直观的看出程序的耗时分布，对于指导程序调优有重要参考价值。下面介绍
go语言中如何获取程序的火焰图。

1.  对CPU进行采样，在程序中添加以下代码：

    ````go
    import (
        "net/http"
        _ "net/http/pprof"
    )
    go func() {
	    http.ListenAndServe("0.0.0.0: 60600", nil)
	}()
    ````
    **pprof**库是go中的性能分析库，以web服务的形式，提供cpu采样，堆栈跟踪，go routine采样等服务，不向
    程序发送性能分析请求时，不影响程序性能。

1.  获取cpu采样数据

    ````shell
    wget http://localhost:60600/debug/pprof/profile?seconds=5
    ````

    上面的命令，对cpu进行5s采样，采样后的数据保存为文件*profile_seconds=5*


1.  下载火焰图渲染工具和脚本

    ````shell
    go get github.com/uber/go-torch
    export PATH=$GOPATH/bin:$PATH
    #注意，要在cpu采样文件的目录执行git clone，否在需要将FlameGraph中的脚本添加到PATH中
    git clone https://github.com/brendangregg/FlameGraph.git
    ````

1.  生成火焰图

    ````shell
    go-torch xxx.bin profile_seconds=5
    ````

    其中*xxx.bin*为进行cpu采样的可执行文件，profile_seconds=5为cpu采样数据，命令执行后，生成火焰图，文件
    名为torch.svg，可用浏览器直接打开。

    火焰图实例：

    ![]({{site.baseurl}}/assets/images/flamegraph_demo.png)

