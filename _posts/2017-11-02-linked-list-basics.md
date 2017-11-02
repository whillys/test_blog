---
layout:     post                    # 使用的布局（不需要改）
title:      linked-list-basics               # 标题 
subtitle:   链表基础 #副标题
date:       2017-11.02              # 时间
author:     whillys                     # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 链表
---

##链表

###链表基础

链表类型：节点和指针（Node and Pointer)
节点是构成链表的基本元素，一个节点包含一个数据域和一个指针域，指针指向链表中下一个节点，定义节点类型如下：

    struct node
    {
    	int data;
    	struct node * next;
    };

struct node* 为指向节点的指针类型。

**BuildOneTwoThree()函数**

下面定义一个简单的函数BuildOneTwoThree()来建立一个链表{1,2,3}
    
    struct node *BuildOneTwoThree()
    {
	    struct node * head = NULL;
	    struct node * two = NULL;
	    struct node * three = NULL;
	    
	    head = malloc(sizeof(struct node));		//在堆上分配三个节点
	    two = malloc(sizeof(struct node));
	    three = malloc(sizeof(struct node));
	    
	    head->data = 1;
	    head->next = second;
	    
	    second->data = 2;
	    second->next = three;
	    
	    three->data = 3;
	    three->next = NULL;
	    
	    return head;
    }

**Length()函数**

    int Length(struct node * head)
    {
	    struct node * current = head;
	    int count = 0;
	    
	    while (current != NULL)
	    {
	    ++count;
	    current = current->next;
	    }
	    
	    return count;
    }

Length是一个简单的函数，但是它展示了链表中两种常见的特性：

1. 把链表的头指针作为入参传给函数，在函数调用过程中，链表头指针的值赋值给函数中的局部变量head，局部变量head也指向链表而不需要复制整个链表。
1. 2. 通过本地指针来迭代链表。

    struct node * current = head;
    while (current != NULL)
    {
    	//do something with *current node
    	current=current->next;
    }


调用Length()

定义函数LengthTest()，在函数中先调用 BuildOneTwoThree建立链表，然后调用Length来获取链表的长度：

    void LengthTest()
    {
    	struct node * myList = BuildOneTwoThree();
    	int len = Length(myList);	//result in len = 3
    }


建立链表

BuildOneTwoThree()是一个很好的指针操作的例子，但它不是构建列表的一般机制。 最好的解决方案将是一个独立的函数，它将一个新节点添加到任何列表中。 然后在我们想要建立的任何列表多次调用该函数。 在进入具体代码之前，我们可以确定在链接列表前面添加单个节点的经典3步链接操作。 3个步骤是：

1. allocate -- 在堆上分配一个节点，将节点的数据域赋值你想要的数据

	struct node * newNode = malloc(sizeof(struct node));
	newNode->data = some_data;
2. link next -- 将新节点的指针域指向当前链表的第一个节点

	newNode->next = head;
3. link head -- 将头结点指向新节点

	head = newNode;

三步操作法的代码实现
函数LinkTest()演示了三步链接：

    void LinkTest()
    {
    	struct node *head = BuildTwoThree();
    	struct node *newNode;
    	newNode = malloc(sizeof(struct node));	//allocate
    	newNode->data = 1;
    
    	newNode->next = head;	//link next
    	head = newNode;			//link head
    }
    
三步链接操作的内存状态如下：
![](https://github.com/whillys/whillys.github.io/blob/master/img/insert_node_with_3_step.png)

**Push函数**

考虑到三步链接，问题是编写一个通用函数，它将单个节点添加到任何列表的头端。 历史上，这个功能被称为"Push()"我们在链表头端加入新节点，这使列表看起来有点像一个堆栈，或者，它可以被称为InsertAtFront（），但我们将使用名称Push()。用c语言实现Push有一个容易犯错的地方，下面通过两个代码示例来展示：

    void WrongPush(struct node * head,int data)
    {
    	struct node * newNode = malloc(struct node));
    	newNode->data = data;
    	
    	newNode->next = head;
    	head = newNode;
    }

WrongPush企图使用三步操作来在链表头部插入新节点，第一步分配节点，第二步设置next都没有问题，第三步设置head不会达到预期中的效果，原因在于c语言中的函数调用，实参使用形参的副本，对局部变量head的修改不会影响形参head。

定义函数WrongPushTest()来测试WrongPush

    void WrongPushTest()
    {
    	struct node * head = BuilTwoThree();
    	WrongPush(head,1);	//try to push 1 on front ,but it doesn't work
    }

调用WrongPushTest时的内存布局为：
![](https://github.com/whillys/whillys.github.io/blob/master/img/wrongpush.png)

我们需要Push()来改变一些调用者的内存--即head变量。 允许函数更改其调用者内存的传统方法是将指向调用者内存的指针传递给函数而不是内存中值副本。 所以在C中，要改变调用者的int，请传递一个int *。 要更改struct fraction，请传递strcut fraction *,要更改X，请传递一个X *。 所以在这种情况下，我们要更改的值是struct node *，所以我们传递一个struct node **。 两颗星（**）有点可怕，但是这只是一个直接的规则应用。 刚好我们要更改的值已经有一个星（*），所以改变它的参数有两个（**）。 或者换个方法：头指针的类型是“指向结构节点的指针”。 为了改变这个指针，我们需要传递一个指向它的指针，它将是一个指向结构节点的指针的指针。

正确的Push函数

    void Push(struct node ** headRef,int data)
    {
    	struct node * newNode = malloc(sizeof(struct node));
    	newNode->data = data;
    
    	newNode->next = *headRef;
    	*headRef = newNode;
    }

Push的第一个入参是一个指向链表头指针的指针，头指针被命名为headRef而不是head，提醒它不是一个简单的头指针，而是指向指针的指针。
定义一个测试函数PushTest()，然后画出调用PushTest()时候的内存布局：

    void PushTest()
    {
    	struct node * head = BuildTwoThree();
    	Push(&head,1);
    
    	//head is now the {1,2,3}
    }

内存布局：
![](https://github.com/whillys/whillys.github.io/blob/master/img/Push.png)

一些代码技巧
下面总结一些链表代码的主要技术。 

**1、迭代一个链表**

例如前面定义的Length()函数
    
    int Length(struct node *head)
    {
    	struct node * current = head;
    	int count = 0;
    	while (current != NULL)
    	{
    		++count;
    		current=current->next;
    	}
    	return count;
    }

当迭代一个链表时，定义一个局部变量，被赋值为第一个节点的地址，通过判断current是否为NULL来判断链表末尾。

**2、用指针来改变指针**

当在链表中需要通过函数调用来改变指向某个节点的指针，那么应该把指向“指向节点的指针”的指针作为函数入参，这个指向指针的指针有时候称为“引用指针”。这种技巧的主要步骤如下：
（1）定义一个函数，接收一个指向指针的指针作为入参，例如，想要改变struct node*，则把struct node **作为入参

（2）在调用者中使用'＆'来计算并传递一个指向感兴趣的值的指针。

（3）在被调函数中使用'*'来访问和更改感兴趣的值

下面定义一个简单的函数来将头指针改为NULL，
    
    void ChangeToNull(struct node ** headRef)
    {
    	*headRef = NULL;
    }
    void ChangeCaller()
    {
    	struct node * head1;
    	ChangeToNull(&head1);
    }
    
调用ChangeCaller时的内存状态为：
![](https://github.com/whillys/whillys.github.io/blob/master/img/ChangeCaller.png)

**3、 Build — At Head With Push()**

创建一个链表的最简单的方法是通过调用Push在头端插入新节点，这种方法有一个缺点就是后插入的节点出现在链表前面，如果不需要考虑链表的顺序的话，这是一种非常好的创建链表的方法
    
    struct node * AddAtHead()
    {
    	struct node *head = NULL;
    	int i;
    	for (i=1;i<6;i++)
    		Push(&head,i);
    	return head;
    	//head = {5,4,3,2,1}
    }

**4、 Build — With Tail Pointer**

如果想要在链表尾端插入新节点，这通常需要通过维护一个尾指针（始终指向链表最后一个节点）来实现，将尾指针的.next由NULL改为新节点，下面的视图显示了在链表{1，2}尾端增加节点3的过程:
![](https://github.com/whillys/whillys.github.io/blob/master/img/addattail.png)

这只是一般规则的特殊情况：要插入或删除列表中的一个节点，您需要一个指向该位置之前的节点的指针，因此您可以更改其.next域。 许多列表问题包括在插入或删除点之前将指针推向节点的子问题。 一个例外是如果节点是列表中的第一个,在这种情况下，头指针本身必须更改。 以下示例显示了代码可以处理单个头指针和所有内部情况...

**5、Build — Special Case + Tail Pointer**

考虑通过在链表尾节点后面增加节点的方式来创建链表{1,2,3,4,5}所遇到的问题，问题在于第一个节点是加在头指针后面，其余的节点通过尾指针加在最后一个节点后面，一种简单的处理方法是把链表中的节点分成两类，第一个节点单独作为一类，加在head指针后面，其余的节点作为一类，可以通过一个循环来建立，tail指针始终指向最后一个节点，每个新节点加载tail->next后面，这种解决方法的特点在于将第一个节点作为一个特殊的节点进行处理了，如果不在意这点的话，这个方法还是简单和有效的。

    struct node * BuildWithSpecialCase()
    {
    	struct nod * head = NULL;
    	struct node *tail;
    	int i;
    
    	//第一个节点单独进行处理，然后设置尾指针
    	Push(&head,1);
    	tail = head;
    
    	//其余的节点通过尾指针来处理
    	for (i =1; i < 6; i++)
    	{
    		Push(&(tail->next),i);		//将新节点加在tail->next
    		tail = tail-next;			//更新tail指向尾节点
    	}
    
    	return head;
    }

**6、Build — Dummy Node**

另一种方法通过一个虚设的节点来建立链表{1，2，3，4，5}，这种技巧在于通过虚设的节点，所有的节点都可以加在tail->next，这样，不需要对第一个节点进行单独处理：

    struct node * BuildWithDummyNode()
    {
    	struct node * tail = NULL;
    	struct node dummy;		//虚设的节点作为临时的第一个节点
    	int i;
    
    	tail = &dummy;
    	dummy.next = NULL;
    
    	for(i=1; i<6; i++)
    	{
    		Push(&(tail->next),i);
    		tail = tail->next;
    	}
    
    	//the real result list is now in dummy.next {1,2,3,4,5}
    	return dummy.next;
    }

一些链表实现将虚拟节点保持为列表的永久部分。 对于这个“永久虚拟”策略，空列表不再由NULL指针表示。 相反，每个列表都有一个虚拟节点。 算法跳过所有操作的虚拟节点。 这样分配的虚拟节点在堆上总是存在的，以便在代码中提供上述的方便。
我们这种在栈上分配虚设节点的策略不常见，但是这种方法避免了在堆上维护一个永久的虚设节点。

**7、Build — Local References**

最后，有一种取巧方法来统一所有的节点情况而不使用虚拟节点。 诀窍是使用一个本地“引用指针”，这个"引用指针"始终指向链表中“始终指向最后一个节点的指针”的指针，列表中的所有添加都是通过引用指针来完成的。 引用指针开始指向头指针。 稍后，它指向列表中最后一个节点的.next字段。 详细说明如下。
    
    struct node *BuildWithLocalRef()
    {
    	struct node *head = NULL;
    	strut node ** LastPtrRef = &head;		//开始时指针头指针
    	int i;
    
    	for (i=1;i<6;i++)
    	{
    		Push(LastPtrRef,i);					
    		LastPtrRef = &((*LastPtrRef)->next);
    	}
    	
    	return head;
    }

使用这种技巧的代码比较紧凑，但是理解起来要困难点，调用BuildWithLocalRef时的内存状态是这样子的：
![](https://github.com/whillys/whillys.github.io/blob/master/img/localRef.png)

临时虚设节点策略和本地引用指针策略不常用，但是它们是你深刻理解指针的好方法。

[参考文档](http://cslibrary.stanford.edu/103/LinkedListBasics.pdf)
