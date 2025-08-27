从[https://zhuanlan.zhihu.com/p/582181618](https://zhuanlan.zhihu.com/p/582181618)搬运

如果只是基于SUMO内置功能实现简单的预编程控制，可以参考下面的框架：

```python
import sys
import os
import optparse
from sumolib import checkBinary 
import traci


# 检测是否已经添加环境变量
if 'SUMO_HOME' in os.environ:
    tools = os.path.join(os.environ['SUMO_HOME'], 'tools')
    sys.path.append(tools)
else:
    sys.exit("please declare environment variable 'SUMO_HOME'")

# sumo自带的，检查gui版本的sumo是否存在
def get_options():
    optParser = optparse.OptionParser()
    optParser.add_option("--nogui", action="store_true",
                         default=False, help="run the commandline version of sumo")
    options, args = optParser.parse_args()
    return options

# 真正的所需操作在这里写
def run():
    step = 0
    while traci.simulation.getMinExpectedNumber() > 0:  # 意思就是还有车需要处理就处理
        traci.simulationStep()  # 仿真一步
        ......
        step += 1
    traci.close()  # 仿真结束 关闭TraCI
    sys.stdout.flush()  # 清除缓冲区


# 主函数
if __name__ == "__main__":
    # 根据下载的sumo决定打开gui版本的sumo还是cmd版本的sumo
    options = get_options()
    if options.nogui:
        sumoBinary = checkBinary('sumo')
    else:
        sumoBinary = checkBinary('sumo-gui')

    # 根据指定路径打开sumocfg文件
    sumocfgfile = "C:\\Users\\RZQ\\Downloads\\Programs\\SUMO\\RoadSim\\simulateOneRout.sumocfg"  # sumocfg文件的位置
    traci.start([sumoBinary, "-c", sumocfgfile])  # 打开sumocfg文件

    # 运行自定义的逻辑
    run()
```



### 核心模块功能


**<font style="color:rgb(17, 17, 17);">addStepListener</font>**<font style="color:rgb(17, 17, 17);"> </font>

**<font style="color:rgb(17, 17, 17);">addStepListener</font>**<font style="color:rgb(17, 17, 17);"> 是 TraCI（Traffic Control Interface）库中的一个功能，它允许你在 SUMO（Simulation of Urban MObility）仿真中添加一个步进监听器。</font>

1. **<font style="color:rgb(17, 17, 17);">功能</font>**<font style="color:rgb(17, 17, 17);">：</font>
    - **<font style="color:rgb(17, 17, 17);">addStepListener</font>**<font style="color:rgb(17, 17, 17);"> </font><font style="color:rgb(17, 17, 17);">用于监控和响应仿真步骤。</font>
    - <font style="color:rgb(17, 17, 17);">每次仿真步骤结束时，它会调用步进监听器的</font><font style="color:rgb(17, 17, 17);"> </font>**<font style="color:rgb(17, 17, 17);">step</font>**<font style="color:rgb(17, 17, 17);"> </font><font style="color:rgb(17, 17, 17);">函数。</font>
    - <font style="color:rgb(17, 17, 17);">此功能允许你在每个时间步骤中执行自定义操作或收集数据。</font>
    - <font style="color:rgb(17, 17, 17);">返回值指示步进监听器是否要保持活动状态。</font>
2. **<font style="color:rgb(17, 17, 17);">示例</font>**<font style="color:rgb(17, 17, 17);">：</font>
    - <font style="color:rgb(17, 17, 17);">假设你正在模拟交通信号灯控制系统。你想在每个仿真步骤结束时检查感应线圈上的车辆数量，并在有车辆通过时将信号灯设置为绿灯。</font>
    - <font style="color:rgb(17, 17, 17);">首先，你需要添加一个步进监听器：</font>

```shell
import traci

class ExampleListener:
    def step(self, t=0):
        # 在每个仿真步骤结束时执行以下操作
        if traci.inductionloop.getLastStepVehicleNumber("0") > 0:
            traci.trafficlight.setRedYellowGreenState("0", "GrGr")
        # 指示步进监听器在下一个步骤中保持活动状态
        return True

listener = ExampleListener()
traci.addStepListener(listener)
```

    - <font style="color:rgb(17, 17, 17);">在连接到仿真后，步进监听器的 </font>**<font style="color:rgb(17, 17, 17);">step</font>**<font style="color:rgb(17, 17, 17);"> 函数将在每个仿真步骤结束时被调用。</font>
    - <font style="color:rgb(17, 17, 17);">如果感应线圈上有车辆通过，它会将信号灯设置为绿灯。</font>

