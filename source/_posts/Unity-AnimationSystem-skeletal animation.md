---
title: Unity游戏引擎-动画系统-骨骼动画
date: 2022-07-28 10:34:18
img: https://invictusqiu.oss-cn-beijing.aliyuncs.com/e438e072-0dee-4ab9-909e-bcf263d2b67f.png
top: false
cover: false
coverImg: https://invictusqiu.oss-cn-beijing.aliyuncs.com/e438e072-0dee-4ab9-909e-bcf263d2b67f.png
summary: 本期文章将介绍游戏中的骨骼动画，以及用Unity对骨骼动画进行实践操作
categories: Unity
tags:
    - Unity
---

## 骨骼动画
### 骨骼动画的原理
游戏里最常用的动画是骨骼动画，骨骼动画的出现是因为传统的关键帧动画存在一些问题。  
  
#### 传统关键帧动画存在的问题：  
>需要保存更多的数据  
>和游戏环境交互较少  
>难于控制

由于场景动画比较简单，如果场景里的物体也比较简单，使用关键帧方式保存不会出现大问题。**但是游戏中出现的角色普遍是由很复杂的网格组成，网格顶点数量巨大，角色形变也比较大，保存的时候就需要大量数据，占据存储空间**。  

并且游戏跟普通的三维动画、影片不一样，**游戏中的角色需要和游戏中的环境发生交互**，如果角色的网格点是一些离散的三角形顶点，不包含任何语义信息，也就无法和场景发生交互，比如用手抓起武器，都不知道手该用哪一个顶点表示，导致交互很困难。  

对于更进一步的**运动编辑比较难**，如果我们使用顶点保存关键帧信息，我们有一段走路、跑步运动，而我想得到快走这样一个中间动画，这就变得不现实，我们不能通过两种走路跑步的插值来融合得到。

#### 骨骼动画解决问题：  

使用一个中间介质，**中间介质就是骨架**，而角色的真正外观，也就是三维网格，它是依附于骨架的，进行被动地运动，骨架是主动的变化，而外围我们所看到地网格是由于骨架的变化而被动地变化，所以骨骼动画也被称为**隐式动画**。  

在骨骼动画的播放阶段，由于骨架结构简单，只需要经过简单计算就可以得到骨架的姿态，进而利用网格顶点和骨架上每一骨骼的附属关系信息来计算出每个网格顶点的位置，从而得到最终我们能看见的网格的姿态，生成完整的动画，这种方式就是骨骼动画。 
 
![骨骼动画](https://invictusqiu.oss-cn-beijing.aliyuncs.com/骨骼动画01.jpg "骨骼动画")  

**骨骼动画的优势**  
>骨架结构简单，可以高效保存动画的关键帧信息。  
>
>骨架本身含有语义信息，可以通过骨架区分出身体的不同部位，从而进行更加灵活的控制，比如我们知道骨架上哪一个骨骼是手部骨骼、腿部骨骼、头部骨骼，从而可以通过控制这些骨骼，来跟周围的环境发生交互。  
>
>得到一些实时计算的动画，这些动画没有事先保存。比如如何通过走路跟跑步融合得到快跑，这种情况只能使用骨骼动画的模式。

![骨骼动画](https://invictusqiu.oss-cn-beijing.aliyuncs.com/骨骼动画02.png)
骨骼动画在播放时，如上图所示，它是分离的，**骨架用来表示动画信息，周围的人物，我们看得见的网格，是依附于骨架的**。由于人物、脊椎动物，天然是由骨架形成的，所以很适合用骨架来模拟。**构成骨骼动画的完整部分，一部分是骨架，另一部分是跟骨架有映射关系的网格，也称为皮肤，通过控制骨架的运动，来控制相应皮肤的变形，形成角色动画，这就是骨骼动画的播放原理。**  



## Unity骨骼动画基础实践

### 准备阶段
在Asset Store上面下载一个具有骨骼动画的模型包，在搜索栏里输入**Unity-Chan**并下载
![Asset Store](https://invictusqiu.oss-cn-beijing.aliyuncs.com/骨骼动画03.png)  

然后将下载好的资源包导入到Unity项目工程里面
![导入资源包](https://invictusqiu.oss-cn-beijing.aliyuncs.com/骨骼动画04.png)  
  
### 实施阶段
一、将模型拖入到场景视图下面
![拖入模型](https://invictusqiu.oss-cn-beijing.aliyuncs.com/骨骼动画05.png)  

二、通过Animator Controller来对骨骼动画模型进行动画状态的控制  
**在 Project 视图下 Creat 一个 Animator Controller** 起名为 **Girl**。然后把 **Girl** 拖拽到模型的 Animator Controller 属性里面  
![创建Animator Controller](https://invictusqiu.oss-cn-beijing.aliyuncs.com/骨骼动画06.png)  

三、双击 Girl 打开这个角色动画的动画状态机界面，可以在里面添加状态，对动画状态进行转换。在 Project 视图下面找到一个动画片段。这里我找到一个等待的的状态，将它拖拽到 Animator Controller窗口底下，橙色为默认播放的动画状态。点击播放角色开始进行闲置动画。
![动画状态机](https://invictusqiu.oss-cn-beijing.aliyuncs.com/骨骼动画07.png)

### 脚本控制
**目标是实现：当用户键盘按下空格键，可以由一个动画状态转换为另外一个动画状态。**  

一、找到另外一个动画状态，拖拽到状态机窗口里面，然后通过建立一个 Transition,把默认动画转换给新的动画片段，添加一个 Trigger 类型（触发器类型）的控制参数，起名为 next，然后点击转换箭头，在转换条件那里把条件设置为 next，当这个参数触发时才发生转换。
![添加控制参数和转换条件](https://invictusqiu.oss-cn-beijing.aliyuncs.com/骨骼动画08.png)  

二、那要怎么发生呢，那就用脚本控制，Creat 一个脚本取名为 AnimaControl，打开脚本编辑
```C#
public class AnimaControl : MonoBehaviour
{
    private Animator anim;      //建立成员变量保存模型的Animator组件
    // Start is called before the first frame update
    void Start()
    {
        anim = GetComponent<Animator>();    //得到当前游戏物体的Animator组件
    }

    // Update is called once per frame
    void Update()
    {
        if(Input.GetKeyDown(KeyCode.Space))     //当用户按下空格键
        {
            anim.SetTrigger("next");            //Trigger设置为next
        }
    }
}
```
保存后将编辑好的脚本拖拽到模型上面，按下空格转换就发生了，此时的转换是需要等待上一个动画结束后才转换的，如果不想等待，就取消掉 Has Exit Time 选项就可以了。
![动画转换](https://invictusqiu.oss-cn-beijing.aliyuncs.com/骨骼动画09.png)

### Event

Event的意思是，当这个动画片段播放到某一帧时，可以为它添加一个响应事件，**而这个事件对应着脚本里的某个函数**。所以在脚本里面可以写一个 public 类型的函数，取名为WaitFinish，我们来做一个实验。
```C#
public void WaitFinish()
    {
        Debug.Log("WaitFinish");       //在控制台输出函数被调用时输出的一句话
    }
```
找到动画片段的初始文件，在初始文件属性里面有一个 Event，拖拽时间轴，把动画片段定位为某一帧，这时候可以添加一个响应事件，事件名字为函数名字，然后 Apply 保存。这意味着这个动画片段无论被应用于哪一个Animator Controller当中，当它播放到那一帧时，都会触发这个函数。
![响应事件](https://invictusqiu.oss-cn-beijing.aliyuncs.com/骨骼动画10.png)

### 重定向

骨骼动画最重要的好处是**可以实现动画数据的重利用**，因为角色的动画只是保存在骨架的结构上，而骨架具有很简单的拓扑结构，如果是同样类型的生物，那么他的骨架很可能是相同的，比如人形，它内部的骨架是一样的，由于最终产生动画的是骨架的变化，所以我们有可能对一个骨架的动画，来驱动不同角色进行相同的运动，这种叫做动画重定向。

#### 动画的重定向
如果我想使用别的三维模型的动画，由于骨骼动画的支持，使得过程很便利。  
我们去Asset Store搜索下载Standard Assets资源，它有一个第三人称控制器，里面已经包含了很多动画片段，有一个很完整的Animator Controller，来控制这些动画片段之间的过渡。  

我们将第三人称的Animator Controller拖拽给模型，这时候播放就会发现她的idle状态已经变为第三人称控制器的idle状态了。
![第三人称Animator Controller](https://invictusqiu.oss-cn-beijing.aliyuncs.com/骨骼动画11.png)

然后我们又找到第三人称控制的脚本，拖拽给模型。由于这个脚本里面使用了RequireComponent，所以我们不光添加了这个脚本，还添加了这个脚本所要求的组件在模型身上，我们调整一下碰撞体大小，使得符合模型的大小。我们运行游戏的话，就会发现ASWD鼠标可以对unity-chan进行控制了。
![第三人称控制脚本](https://invictusqiu.oss-cn-beijing.aliyuncs.com/骨骼动画12.png)

#### 动画状态之间的融合

我们打开unity-chan现在所挂接的Animator Controller，发现它有比较复杂的动画状态以及动画状态之间的转换。我们来看其中一个转换，这个转换是角色跳起后落地到站立的过程，她有一个过渡的过程，这个过程显得角色很自然，我们可以把这个过程拉长，这个过程就会变得很缓慢，但过程仍然很自然。当我们将其调回到原来的融合间距，这个过程会变得更加迅速和自然。
![动画状态之间的融合](https://invictusqiu.oss-cn-beijing.aliyuncs.com/骨骼动画13.png)

## 小结

骨骼动画是用骨骼来保存动画信息，而我们看得见的三维网格，则是依附于骨骼进行被动地运动，这个就是骨骼动画的原理。因为我们保存的骨骼，是跟外貌无关的骨架它的运动，因此，一个骨骼动画，就有可能用来驱动多个不同的角色，这种技术被称为运动的重定向。

>本章一句：  
>这朵世间最美好的玫瑰，星尘为泥，银河滋养。永远不会枯萎，永远在沉静宇宙中盛放。 这是我要给你的，宇宙级别的浪漫。

![](https://invictusqiu.oss-cn-beijing.aliyuncs.com/illust_90738662_20220530_202311.jpg)