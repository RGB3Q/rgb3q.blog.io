整体步骤

1、sumo安装：[https://sumo.dlr.de/docs/Downloads.php](https://sumo.dlr.de/docs/Downloads.php)  安装时会默认修改环境变量，安装完成后cmd执行sumo应能查询到版本信息，如下：

![](https://cdn.nlark.com/yuque/0/2023/png/28348597/1702561545935-986a6100-677e-452b-b2a7-73003805d765.png)

2、创建虚拟开发环境（可选择conda env）   `conda create -n sumo_env python=3.9`

3、进入虚拟环境，并安装traci  `pip install traci`

---

<font style="background-color:#FBDE28;">！！！注意：必须先安装sumo安装包，并确保环境变量正确添加，再创建虚拟环境并安装traci</font>

