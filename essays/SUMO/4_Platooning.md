### 先介绍已有的两个方案
#### 方案1
SUMO TraCi提供了车辆编队的插件 [**simpla**](https://sumo.dlr.de/docs/Simpla.html)**<font style="color:#4C16B1;"> 以下内容搬运并翻译自官网介绍：</font>**

---

simpla 是 TraCI Python 客户端的一个可配置的车辆编队插件。它可管理车辆编队的自发形成，并允许为在编队中行驶的车辆定义特定行为。

具体做法是定义编队模式的附加车辆类型，并提供信息，说明正常单独行驶时使用哪种类型，启动排队时使用哪种类型。此外，还可以指定 "追赶 "模式，当前方有潜在队列形成时，可以使用这种模式。

除正常行驶模式外，还可指定多达四种运行模式（详见 "配置 "部分）：

+ <font style="color:rgb(73, 80, 87);">Platoon leader</font>模式（领队车辆的参数设置）
+ <font style="color:rgb(73, 80, 87);">Platoon follower</font>模式（队列内跟随车辆的参数设置）
+ <font style="color:rgb(73, 80, 87);">Platoon catch-up mode</font>模式（在已有队列的一定范围内，可以加入该队列的车辆参数设置）
+ <font style="color:rgb(73, 80, 87);">Platoon catch-up follower</font>模式（已经处于一个队列内，且该队列的领队车辆处于追赶模式）

---

[**simpla**](https://sumo.dlr.de/docs/Simpla.html)** **插件存在无法控制车辆变道的问题，因此只能实现基础的单车道编队场景



#### 方案二
基于  Plexe-SUMO

代码资源：[plexe-pyapi](https://github.com/michele-segata/plexe-pyapi)

案例资源：[https://www.jianshu.com/p/3f5b817ff62f](https://www.jianshu.com/p/3f5b817ff62f)

Plexe 是一个基于 Veins 开发的开源仿真软件，专注于模拟真实场景下的车辆协同驾驶系统。特点是可以提供真实网络环境和车辆动力学仿真。用户可以利用完整的 IEEE 802.11p 和 IEEE 1609.4 DSCR/WAVE 网络协议栈，实现真实的车联网环境。同时，Plexe 支持多种巡航控制跟车模型，可用于分析控制系统、大规模和混合场景，以及网络协议和协同驾驶策略。

Plexe 通过与 SUMO 的 TraCI 接口通信来实现仿真控制。交互控制架构如下图：

![](https://cdn.nlark.com/yuque/0/2024/png/28348597/1711941362491-0ea65f75-b646-4c47-9470-6655ee3d6fab.png)

plexe中，头车（head_car）采用ACC定速巡航，车队中的其他车辆通过周期性地与头车通讯「communicate」（一般100ms）以维持运动策略（加减速、变道）的同步。联网车辆的在网状态（网络拓扑关系）以及车队物理形态通过「topology」以字典形式进行定义，在其中每辆车均有特定编号，一般由"v.0.0"样式构成，中位代表初始化车队，末位代表编号。该编号一般在仿真过程中保持一致。在topology中，会定义头车，以及为车队中的其他车辆分配头车以及在车队中的位置（前车编号）。如下：

```shell
{'v.0.0': {}, 'v.0.1': {'front': 'v.0.0', 'leader': 'v.0.0'}, 'v.0.2': {'front': 'v.0.1', 'leader': 'v.0.0'}, 'v.0.3': {'front': 'v.0.2', 'leader': 'v.0.0'}, 'v.0.4': {'front': 'v.0.3', 'leader': 'v.0.0'}, 'v.0.5': {'front': 'v.0.4', 'leader': 'v.0.0'}, 'v.0.6': {'front': 'v.0.5', 'leader': 'v.0.0'}, 'v.0.7': {'front': 'v.0.6', 'leader': 'v.0.0'}, 'v.0.8': {'front': 'v.0.7', 'leader': 'v.0.0'}, 'v.0.9': {'front': 'v.0.8', 'leader': 'v.0.0'}, 'v.0.10': {'front': 'v.0.9', 'leader': 'v.0.0'}, 'v.0.11': {'front': 'v.0.10', 'leader': 'v.0.0'}, 'v.0.12': {'front': 'v.0.11', 'leader': 'v.0.0'}, 'v.0.13': {'front': 'v.0.12', 'leader': 'v.0.0'}, 'v.0.14': {'front': 'v.0.13', 'leader': 'v.0.0'}}
```

目前看全局只会有一个topology对象维持（需要进一步验证）





加减速设定：

采用set_fixed_acceleration可以实现指定加减速，当activate=True时该加速度会一直保持（如过为负则减至0为止），如果想设置加速到一个目标车速，可以采用如下设置：activate=False，同时设定一个期望巡航车速。

```shell
plexe.set_fixed_acceleration(new_leader_id, False, 3)
plexe.set_cc_desired_speed(new_leader_id, 15)
```

```shell
def set_fixed_acceleration(self, vid, activate, acceleration):
    """
    Tell a vehicle to apply a certain acceleration, or to switch back to
    standard behavior
    :param vid: vehicle id
    :param activate: if true, sets a constant acceleration profile to the
    vehicle, if false, gives the control back to the control algorithms
    :param acceleration: constant acceleration to apply
    """
    self.plexe.set_fixed_acceleration(vid, activate, acceleration)
```





controller设定：

+ <font style="color:rgb(232, 62, 140);">Plexe::DRIVER</font><font style="color:rgb(33, 37, 41);">: let the car be controlled by a human-behavioral model. In the current implementation, Plexe uses the Krauss model</font>
+ <font style="color:rgb(232, 62, 140);">Plexe::ACC</font><font style="color:rgb(33, 37, 41);">: use a standard Adaptive Cruise Control</font>
+ <font style="color:rgb(232, 62, 140);">Plexe::CACC</font><font style="color:rgb(33, 37, 41);">: use the Cooperative Adaptive Cruise Control developed in the California PATH project</font>
+ <font style="color:rgb(232, 62, 140);">Plexe::FAKED_CACC</font><font style="color:rgb(33, 37, 41);">: use the Cooperative Adaptive Cruise Control developed in the California PATH project but use the data set via the</font><font style="color:rgb(33, 37, 41);"> </font><font style="color:rgb(232, 62, 140);">setLeaderVehicleFakeData</font><font style="color:rgb(33, 37, 41);"> </font><font style="color:rgb(33, 37, 41);">and</font><font style="color:rgb(33, 37, 41);"> </font><font style="color:rgb(232, 62, 140);">setFrontVehicleFakeData</font><font style="color:rgb(33, 37, 41);"> </font><font style="color:rgb(33, 37, 41);">methods. This can be useful to bring the vehicle close to a platoon for joining using the CACC</font>
+ <font style="color:rgb(232, 62, 140);">Plexe::PLOEG</font><font style="color:rgb(33, 37, 41);">: use the Cooperative Adaptive Cruise Control developed by Ploeg et. al.</font>
+ <font style="color:rgb(232, 62, 140);">Plexe::CONSENSUS</font><font style="color:rgb(33, 37, 41);">: use the Cooperative Adaptive Cruise Control developed by Santini et. al.</font>
+ <font style="color:rgb(232, 62, 140);">Plexe::FLATBED</font><font style="color:rgb(33, 37, 41);">: use the Cooperative Adaptive Cruise Control developed by Ali et. al.</font>





参考文献：

