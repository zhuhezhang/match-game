﻿@[TOC](目录)
# 1.问题描述与要求
在盘中放着n根火柴，A和B两人轮流从盘中取火柴，规定每次可取一根、二根或三根，不可不取也不可多取，谁拿走最后一根便算谁输。这就是所谓的取火柴游戏。其实，这种游戏如同下棋，双方都有可能取胜。为了自己取胜，就必须每走一步（即取一次火柴）都要动动脑筋，为自己的最后胜利创造条件，也就是说，每走一步都要有个较好的对策。
我们可以利用树结构，把取火柴游戏的过程描述出来。为方便起见，假定n=6。开始时有六根火柴，A先走的话，他可有拿1根、2根或3根火柴三种不同的走法。如果把原始状态——6根火柴，作为根结点，A的三种不同走法将产生盘中剩5根、4根、3根火柴三种状态，可用根结点的三个子结点表示（如图2所示）。A走以后，不论A走哪一步，B接着走时，他也可在A取剩的火柴中拿走1根、2根或3根，因此共有3×3＝9种可能性，再可产生9个子结点，接下来A再走，……。由于火柴的根数是有限的，而且每走一步都要减少些火柴，因此火柴总会取完，游戏随之结束。整个过程可用一棵树来表示。这棵树反映了A和B双方所有可能的对策，因此可以称为对策树。为了要战胜对手，当然应该选择获胜机会最大的移动，为了描述获胜机会的大小，可以引入一个评价函数，每个结点的评价函数值可以用来衡量先手或后手时选择移动到此步的获胜机会。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308232459750.png#pic_center)

图1 三种走法对应的父结点与子结点的示意图
要求：利用树的链式存储结构，设计算法实现两个人、人与计算机的取火柴游戏。输出游戏过程，将树的结构显示在屏幕上。
扩展要求：实现取火柴游戏的可视化界面。
# 2.需求分析
题目主要要求利用树的链式存储结构设计一个取火柴游戏，规定每次至少取一根，最多取三根火柴，谁取到最后一根火柴则为输家。游戏过程在树上进行，游戏模式分为人机对战模式、双人对战模式。对于人机模式，需要设置一个评价函数来决定机器如何取火柴，即如何取火柴机器本身赢的概率会大一些。并且输出游戏过程、树的结构，最好能够实现游戏的可视化界面。
# 3.设计
## 3.1设计思想
由于每次取火柴有三种情况，且考虑到后续各个函数功能的实现，所以利用链式三叉树作为树结构来实现。程序主要有建立三叉树模块、执行模块以及评价函数模块。为了方便查找函数代码，将文件分为三个：分别是包含结点信息、含有各个操作函数队的树类的头文件、实现各个函数功能的cpp文件以及负责函数调用的主函数文件。其中主函数会直接利用类对象调用执行函数（也就是游戏开始），执行函数里面首先会询问输入火柴总数，接着调用函数创建三叉树，在执行过程中也会调用评价函数来计算概率，以便机器更好的选择。
## 3.2设计表示
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308232705741.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzk0NjMz,size_16,color_FFFFFF,t_70#pic_center)

## 3.3详细设计
先说树结点信息结构体：包含结点值、一个标志变量(1则表示无效)、三个结点指针以及用来初始化的构造函数。再就是树类：包含创建树(Create_tree)、计算获胜概率的评价函数(Odds)、一个运行函数(Run)以及一个指向树根结点的root指针。
先说创建树函数：利用递归创建三叉树，先是新建一个结点并赋初值，若是结点值n > 0，则递归调用创建其三个孩子结点；若n < 0，则令其标志变量置为1，也即是无效结点；若n = 0，则什么只是创建结点，其他不做。然后是计算获胜概率的评价函数：如果树非空，则一直递归，且每次递归树的层数i都加1，递归到叶子结点时判断结点值是否为0，若是再判断从根结点到此处的根的个数是否为奇数，若是则分别代表根结点到当前结点的边数为基数的个数变量和叶子结点为0的个数都加1，否则只有叶子结点个数为0的加1，对于中右子树亦是如此，其中左子树递归部分伪代码如右图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308232826889.png#pic_center)

紧接着是执行部分，这部分比较简单：首先是输入火柴总数，根据火柴总数调用函数创建三叉树，输入指令选择对战模式（1、人机对战 2、人机对战），若输入错误则利用goto语句返回重新输入。这里以人机对战模式来说明：整个游戏放在一个while大循环里面进行，游戏根据树结点逐个进行，若结点指针数据ptr->number < 1则游戏结束，人获胜，并且break出大循环；否则人输入取火柴数，根据输入的火柴数决定指针指向其哪个孩子，若为不合法输入则返回重新输入，取过之后若火柴数量小于1，则机器获胜，并且退出循环；否则轮到机器取火柴。首先机器根据评价函数判断取多少它赢得概率大一些，取完之后返回大循环上面继续游戏。
程序最后是主函数，也很简单：利用类对象调用执行函数Run并且将它们放置在一个循环里面即可。
# 4.用户手册
 游戏首先会提示输入火柴总数，选择人机和双人中的任意一种模式玩，然后轮流输入取火柴数，火柴取完后游戏结束，系统会询问是否继续玩，输入Y游戏继续，重新输入火柴总数，步骤和上面相同，若输入N则游戏结束。
# 5.测试数据及测试结果
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308232834939.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzk0NjMz,size_16,color_FFFFFF,t_70#pic_center)

# 6.源程序清单
matchgame_head.h     //头文件，包含结点信息结构体和封装所有函数声明的类
matchgame_fun.cpp    //类里面所有函数的定义
matchgame_main.cpp   //主函数，调用执行函数执行

