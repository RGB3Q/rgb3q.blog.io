#### Intelligent Driver Model (IDM)，即智能驾驶模型，是一种用于描述和模拟车辆跟驰行为的微观交通流模型。它是由德国航空航天中心（DLR）的交通研究所于1995年提出的一种先进的车辆动力学模型。IDM的主要优势在于它能够简洁而有效地捕捉车辆跟驰和加速的动态特征，同时考虑到驾驶员的安全距离偏好和舒适性需求。
### IDM模型的基本假设
1. **安全距离**：车辆会根据自身速度和前车的距离调整速度，以维持一个安全的跟车距离。
2. **舒适性**：车辆加速或减速时会遵循一个舒适的加速度，避免急加速或急刹车的情况。
3. **反应时间**：模型包含了驾驶员的反应时间，但通常简化为即时反应。

### IDM模型的数学表达
IDM模型的加速度公式如下：

$  a(t) = a_0 \left( 1 - \left( \frac{v(t)}{v_0} \right){\delta} - \left( \frac{s*(t)}{s(t)} \right)^2 \right)  $

其中：

+ $ a(t) $表示车辆在时刻(t)的加速度。
+  $ a_0 $是最大加速度，即车辆在没有障碍物影响下的加速度。
+  $ v(t) $是车辆在时刻(t)的速度。
+  $ v_0 $是理想速度，即在没有干扰的情况下驾驶员希望保持的恒定速度。
+  $ \delta $是加速度对速度的敏感度参数，通常取值在1到4之间。
+  $ s^*(t) $是理想的最小安全距离，它随车辆速度和前车速度的变化而变化。
+  $ s(t) $是车辆与前车之间的实际距离。

### 安全距离的计算
理想最小安全距离(s^*(t))由以下公式给出：

$ s^*(t) = s_0 + \max \left( 0, \alpha v(t) - \beta v_{\text{diff}} \right)  $

其中：

+ $ s_0 $是静止时的安全距离。
+ $ \alpha $和  $ \beta $是模型参数，分别控制车辆速度对安全距离的影响和前车速度差对安全距离的影响。
+ $ v_{\text{diff}} $是车辆速度与前车速度之差。



### MOBIL模型介绍（**<font style="color:#FF0000;background-color:rgb(204, 204, 247);">M</font>****<font style="color:rgb(0, 0, 0);background-color:rgb(204, 204, 247);">inimizing </font>****<font style="color:#FF0000;background-color:rgb(204, 204, 247);">O</font>****<font style="color:rgb(0, 0, 0);background-color:rgb(204, 204, 247);">verall </font>****<font style="color:#FF0000;background-color:rgb(204, 204, 247);">B</font>****<font style="color:rgb(0, 0, 0);background-color:rgb(204, 204, 247);">raking decelerations </font>****<font style="color:#FF0000;background-color:rgb(204, 204, 247);">I</font>****<font style="color:rgb(0, 0, 0);background-color:rgb(204, 204, 247);">nduced by </font>****<font style="color:#FF0000;background-color:rgb(204, 204, 247);">L</font>****<font style="color:rgb(0, 0, 0);background-color:rgb(204, 204, 247);">ane changes</font>**）
MOBIL是一个用于模拟交通中车道变换行为的模型，它基于两个主要标准：激励标准和安全标准。

车道变换发生在以下情况下：

1. 潜在的新目标车道更具吸引力，即满足“激励标准”。
2. 车道变换可以安全进行，即满足“安全标准”。



在MOBIL模型中，这两个标准是基于旧车道和预期新车道上的加速度来确定的，这些加速度是通过纵向模型（在本例中为IDM模型）计算得出的。



### 模型方程：
+ **安全标准**：如果目标车道后车（B'）在可能的车道变换后受到的IDM制动减速度（acc'=acc'IDM）不超过某个限制（bsave），则安全标准得到满足。
+ **激励标准**：通过权衡目标车道上自己的优势（通过增加加速度或减少制动减速度来衡量）与对其他驾驶员造成的不利影响（通过减少加速度或增加制动减速度来衡量）。由于人们倾向于自私，我们用一个通常小于1的“礼貌因子”p来加权对其他驾驶员造成的不利影响，激励标准如下：  




### 模型参数及其典型值：
+ **礼貌因子p**：0到0.5之间，用于调整模型的行为。
+ **最大安全减速度bsave**：4 m/s²，必须低于最大减速度约9 m/s²。
+ **车道变换阈值athr**：0.2 m/s²，必须低于任何车辆类型的最低加速度能力（IDM参数a）。
+ **向右车道的偏差Delta b**：0.2 m/s²，仅适用于欧洲交通规则。



### MOBIL模型的主要特点：
+ 与其他车道变换模型通常假设纯粹的自私行为（即p=0）不同，MOBIL可以通过改变这个因子来模拟不同的行为。
+ 特别情况下，当p=1且athr=0时，车道变换会在变换后所有受影响驾驶员的加速度总和增加，或者等效地，整体减速度最小化时发生。





# 使用IDM+MOBIL的方法：
1、构建lane，并在lane中采用链表的形式存储当前的车辆队列状态

2、遍历全部lane中的veh，使用IDM更新veh状态（x,a）

3、结合mobil实现离散变道，并更新lane中的链表

4、检查链表中的头车是否已经驶离当前路段，即veh.x>lane.length，更新上下游链表



### 参考文献：


+ 原始出版物（德语）：M. Treiber 和 D. Helbing，《Realistische Mikrosimulation von Straßenverkehr mit einem einfachen Modell》，16. Symposium "Simulationstechnik ASIM 2002" Rostock, 10.09 - 13.09.2002，由 Djamshid Tavangarian 和 Rolf Grützner 编辑，第514-520页。



[https://traffic-simulation.de/info/info_MOBIL.html](https://traffic-simulation.de/info/info_MOBIL.html)



# 
