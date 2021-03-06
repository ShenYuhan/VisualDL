# 常见问题

 [**English**](./faq.md)

## 打开浏览器出现空白或者发生错误
可参照下列步骤排除错误原因
1. 确保启动VisualDL时指定的`logdir`路径正确，并确保其下有符合命名要求（文件名包含`vdlrecords`）的日志文件，可进入`logdir`指定的文件夹先行查看。若未解决，转下步。
2. 确认浏览器及版本符合VisualDL要求，建议使用**最新版本**的chrome及chrome内核浏览器、火狐浏览器，尝试更换浏览器及升级到最新版本。若未解决，转下步。
3. 将`--host`参数指定为`0.0.0.0`或`127.0.0.1`，注意后者仅支持本机查看，若在服务器启动并在异地查看，需使用`0.0.0.0`并确保使用的端口可被外网访问。若未解决，转下步。
4. 关闭并重新打开VisualDL前端页面，或强制刷新浏览器并等待15-30秒。若未解决，转下步。
5. 若是windows机器，可查看是否注册表被修改，参考解决[解决注册表修改导致无法查看日志](https://github.com/PaddlePaddle/VisualDL/issues/834)
若经过上述尝试仍无法解决，可在VisualDL GitHub Issue进行提问[VDL Issue](https://github.com/PaddlePaddle/VisualDL/issues)

## 使用Image、Audio、Text组件仅显示10个样本
为保证使用体验，防止由于数据量过大造成前端页面卡死或崩溃，在展示Image、Audio、Text组件时使用采样算法进行了数据采样后展示。  
尽管数据在前端展示进行了采样，但在日志中保存的数据仍为全部数据，可通过`VisualDL.LogReader`进行全部数据获取，可参考[LogReader使用教程](./components#LogReader)

## 为什么Scalar绘制的曲线是迂回的
当你发现绘制的曲线如下图所示，某个step对应的value不止一个值时，请检查你的脚本，是否在使用`add_scalar`时为一个step重复添加了多次value。
<p align="center">
    <img src="https://user-images.githubusercontent.com/28444161/99496785-de44d280-29af-11eb-8fbd-ebc7a4919f2f.png" width="40%"/>
</p>


## 官方用例报错，LogWriter对象没有mode属性

可以检查一下使用的VisualDL的版本（which visualdl），因为按照此报错来看，大概率是因使用的是Python2，自动安装了VisualDL 1.3版本，但官方示例均基于2.0版本的，故导致此报错。
由于目前VisualDL已经不维护python2了，且现有官方文档上的使用说明都是基于2.0版本的，并且将不再维护旧版本，建议升级至Python3，安装最新版的VisualDL，即不会出现上述问题。


## 如何修改已有的日志文件

不同的需求距有不同的解决思路
1. 如果需要在已有的日志文件中继续添加日志，则在使用LogWriter获取日志生成器时指定`file_name`参数为此日志名，则后续添加数据时都会写入此日志。
2. 如果需要增加或删除日志中某些数据，则可以通过LogReader读取日志并处理后，重新写入到新的文件中，可参考[LogReader使用教程](./components#LogReader)


## VisualDL的采样规则是什么
为了尽量减少数据传输规模且尽可能均匀化采样，同时最小化采样所需的CPU资源占用，VisualDL使用蓄水池采样算法完成对后端所有数据采样后再向前端传输。
关于蓄水池采样的原理和证明，可参考[Reservoir sampling](https://en.wikipedia.org/wiki/Reservoir_sampling)，蓄水池采样能够通过流式采样的方式避免一次性加载所有数据到内存，且保证采样序列尽量平均。
