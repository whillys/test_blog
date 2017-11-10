---
layout:     post                    # 使用的布局（不需要改）
title:      linked-list-basics               # 标题 
subtitle:   链表基础 #副标题
date:       2017-11-02              # 时间
author:     whillys                     # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 链表
---

## 链表

### 链表基础

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

![](https://imglf3.nosdn.127.net/img/dUwzdFBUbDJxOXR6NUFUbTZDOUZycDZaZHI1NlhiL0JvZklVdW1jUGhHQzhKK1pJd1RLUnd3PT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg)

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

![](https://imglf4.nosdn.127.net/img/dUwzdFBUbDJxOXR6NUFUbTZDOUZydVVwbktISnY5QnZmNmlncDRhNnhJSEhFSTZyWS8ySFZnPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg)

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
![](https://imglf4.nosdn.127.net/img/dUwzdFBUbDJxOXR6NUFUbTZDOUZyZ1c3Yi9NM2FKSGFXTDNSQTFJTWJnMHIxWU9LTVhwYTlnPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg)

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

![](https://imglf5.nosdn.127.net/img/dUwzdFBUbDJxOXR6NUFUbTZDOUZyb3pBWDRBMTRTRkl2RGhwaDAyaVRzeXd6ZUhFUXhZRnN3PT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg)

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


![](https://imglf6.nosdn.127.net/img/dUwzdFBUbDJxOXZPUXRKdndWMmxSZ0UwTDJ6aXlvcG9ibTVVLy9jTjlFRFlYT1VmbEZtdXlBPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg)

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

![](https://imglf6.nosdn.127.net/img/dUwzdFBUbDJxOXR6NUFUbTZDOUZycmF5eHVLTzFZUlZwSDVIZkdTQlNEd3ZWTDdTTnNUZm9BPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg)

临时虚设节点策略和本地引用指针策略不常用，但是它们是你深刻理解指针的好方法。


链表问题

下面是按从易到难的顺序排列的18个链表问题。 前几个是相当的基本，最后几个难度有所提高的。 每个问题都从基本定义以及我们需要完成什么目标开始，许多问题还包括提示或视图。


1. Count()

定义一个Count()函数来统计链表中指定值出现的次数

    int Count(struct node *head, int searchFor)
    {
    	int count = 0;
    	struct node * current = head;
    	while (current != NULL)
    	{
    		if (searchFor == current->data)
    			++count;
    		current = current->next;
    	}
    
    	return count;
    }
    
    void CountTest()
    {
    	struct node * head = BuildOneTwoThree();	//build{1,2，3}
    	int count = Count(head,2);					//return 1 
    }

2. GetNth()

定义一个函数GetNth()，该函数接收一个指向链表的指针和一个正整数，返回链表中以正整数作为索引的节点的data，第一个节点的索引为0，第二个节点的索引为1，以此类推，对于链表{42，13，666}，对于索引1，GetNth()返回13。

    int GetNth(struct node *head,int idx)
    {
    	int len = Length(head);
    	assert(idx >= 0 && idx < len);
    
    	struct node * current = head;
    	int i = 0;
    	while(current != NULL)
    	{
    		if (idx == i)
    			return current->data;
    		++i;
    		current = current->next;
    	}
    
    	//assert(0);		//也可以把断言放到这里来，如果程序运行了这一行代码，说明传入的idx不符合要求
    }
    
    void GetNthTest()
    {
    	struct node *head = BuildOneTwoThree();	//build {1,2,3}
    	int lastNode = GetNth(head,3);			//returns 3
    }

3. DeleteList()

定义一个函数DeleteList()获取一个列表，取消分配给它的所有的内存并将其头指针设置为NULL（空列表）。

    void DeleteList(struct node ** headRef)
    {
    	struct node * current = *headRef;
    	struct node * tmp = NULL;
    	while (current != NULL)
    	{
    		tmp = current->next;	//保存下一个节点
    		free(current);			//删除当前节点
    		current = tmp;			//更新到下一个节点
    	}
    
    	*headRef = NULL;
    }
    
    void DeleteListTest()
    {
    	struct node * head = BuildOneTwoThree(); //build {1,2,3}
    	DeleteList(&head);
    }

因为DeleteList要改变传入的头指针，因此应该把指向头指针的指针作为参数传递给函数，删除列表后的内存视图应该如下图所示：

4. Pop()

定义一个Pop()函数其操作与Push()相反。 Pop接受一个非空列表，删除头节点，并返回头节点的数据。 如果你曾经使用Push()和Pop()，那么我们的链表就会看起来像一个堆栈。 但是，我们提供了更多的通用函数，比如GetNth（），它使我们的链表不仅仅是一个堆栈。 如果没有要弹出的节点，则Pop()应该assert()失败。 下面是一些调用Pop()的示例代码:

    void PopTest()
    {
    	struct node * head = BuildOneTwoThree();	//build {1,2,3}
    	int a = Pop(&head);		//delete "1" and return 1
    	int b = Pop(&head);		//delete "2" and return 2
    	int c = Pop(&head);		//delete "3" and return 3
    	int len = Length(head);	//the list is now empty,so len = 0
    }
    
    int Pop(struct node **headRef)
    {
    	int data;
    	assert(*headRef ！= NULL);
    
    	struct node * current = *headRef;
    	*headRef = current->next;
    	data = current->data;
    	free(current);
    
    	return data;
    }

5. InsertNth()

定义一个函数InsertNth()在链表指定索引处插入节点，指定的索引取值范围应该为[0,length]，length为链表中节点的个数。

    void InsertNthTest()
    {
    	struct node * head = NULL;
    	InsertNth(&head,0,13);		//build {13}
    	InsertNth(&head,1,42);		//build {13,42}
    	InsertNth(&head,1,5);		//build {13,5,42}
    }
    
    void InsertNth(struct node ** headRef,int idx,int data)
    {
    	int len = Length(*headRef);
    	assert(len >= 0 && len <= len);
    
    	if (idx == 0)
    	{
    		Push(headRef,data);
    	}
    	else
    	{
    		int i=0;
    		struct node *current = *headRef;
    		while (i++ < idx - 1)		//注意这里减去了1，这样就不需要维护一个额外的指针指向插入位置前面一个节点
    		{
    			current = current->next;
    		}
    		Push(&(current->next),data);	
    	}
    }

6. SortedInsert()
7. 
定义个一个函数SortedInsert()，该函数接受一个已经排好序的链表（升序排序）和一个指向单节点的指针，将这个单节点插入到链表的适当位置

    void SortedInsert(struct node ** headRef,struct node * newNode)
    {
    	//头指针为空，或者在第一个节点之前插入的情况
    	if (*headRef == NULL || (*headRef)->data >= newNode->data)
    	{
    		newNode->next = *headRef;
    		*headRef = newNode;
    	}
    	else
    	{
    		struct node * current = *headRef;
    		while (current->next != NULL && current->next->data < newNode->data)
    		{
    			current = current->next;
    		}
    		newNode->next = current->next;
    		current->next = newNode;
    	}
    }

虚设节点策略

    void SortedInsertWithDummy(struct node ** headRef,struct node * newNode)
    {
    	//定义一个虚设节点
    	struct node dummy;
    	dummy.next = *headRef;
    	struct node * current = &dummy;
    
    	while (current->next != NULL && current->next->data < newNode->data)
    	{
    		current = current->next;
    	}
    	newNode->next = current->next;
    	current->next = newNode;
    	*headRef = dummy.next;
    }
    

引用指针策略

    void SortedInsertWithLocRef(struct node ** headRef,struct node * newNode)
    {
    	struct node ** current = headRef;
    
    	while (*current != NULL && (*current)->data < newNode->data)
    	{
    		current = &((*current)->next);
    	}
    	newNode->next = *current;
    	*current = newNode;
    }

7. InsertSort()
定义一个函数InsertSort()，通过使用SortedInsert()将一个链表按升序排序，函数通过迭代源链表，将每个节点插入到另一个链表中，

    void InsertSort(struct node **headRef)
    {
    	struct node * result = NULL;
    	struct node * current = *head;
    	struct node * next = NULL;
    	
    	while (current != NULL)
    	{
    		next = current->next;	//保存下一个节点
    		SortedInsert(&result,current);
    		current = current->next;
    	}
    
    	*headRef = result;
    }


8.Append()

定义一个函数Append()接收两个链表参数a,b，将链表b链在链表a最后一个节点后面，例如对于链表a={1,2},b={3,4}，两者链接后其内存状态应该为：

    void Append(struct node **aRef,struct node **bRef)
    {
    	struct node * acurrent = *aRef;
    	struct node * bcurrent = *bRef;
    	if (acurrent == NULL)
    	{
    		*aRef = bcurrent;		
    		*bRef = NULL;
    	}
    	else
    	{
    		while (acurrent->next != NULL)
    		{
    			acurrent = acurrent->next;
    		}
    		acurrent->next = bcurrent;
    		*bRef = NULL;
    	}
    }

9. FrontBackSplit()
10. 
定义一个函数FrontBackSplit()，将一个链表切分成两个子链表，链表前一半节点分到第一个链表，链表后一半节点分到另一个链表，如果链表节点个数为奇数，则多余的节点应该分到第一个子链表，例如对于链表{2，3，5，7，11}，被切分后，第一个子链表应该为{2，3，5}，第二个子链表尾{7，11}，下面用两种方法来实现FrontBackSplit()

第一种方法：通过统计链表中元素个数来切分

    void FrontBackSplit(struct node * source, struct node ** firstSub, struct node ** secondSub)
    {
    	struct node * current = source;
    	int len = Length(current);
    	
    	if (len < 2)
    	{
    		*firstSub = source;
    		*secondSub = NULL;
    	}
    	else
    	{
    		int pos = (len - 1) / 2;			//计算出切分点在哪个位置
    		int i = 0;
    		while (i++ < pos)
    			current = current->next;
    
    		*firstSub = source;
    		*secondSub = current->next;
    		current->next = NULL:
    	}	
    }
	
第二种方法：通过设置两个指针，一个慢指针一次往前移动一个节点，一个快指针一次往前移动两个节点，当快指针为NULL时，慢指针所在的位置就是切分点

    void FrontBackSplit(struct node * source, struct node ** firstSub, struct node ** secondSub)
    {
    	struct node * current = source;
    
    	if (current == NULL || current->next == NULL)		//节点个数少于2的情况
    	{
    		*firstSub = source;
    		*secondSub = NULL;
    	}
    	else
    	{
    		struct node * slow = source;
    		struct node * fast = source->next;
    		while (fast != NULL)
    		{
    			fast = fast->next;	//快指针先移动一个节点位置
    			if (fast != NULL)
    			{
    				slow = slow->next;	//慢指针移动一个位置
    				fast = fast->next;	//快指针再次移动一个位置
    			}
    		}
    
    		*firstSub = source;
    		*secondSub = slow->next;
    		slow->next = NULL;
    	}
    }

10. RemoveDuplicates()
定义一个函数RemoveDuplicates()，删除排好序链表中重复的节点（升序排序），链表应该只迭代一次，因为链表是已经排好序的，所以只要把当前节点和下一个节点比较，如果相等，则删除下一个节点，否则把指针移动到下一个节点。

    void RemoveDuplicates(struct node * head)
    {
    	struct node * current = head;
    	struct node * next = NULL;
    
    	while (current != NULL && current->next != NULL)	//空表和只有一个节点的表不需要删除
    	{
    		if (current->data == current->next->data)	//当前节点和下一个节点相等，删除下一个节点
    		{
    			next = current->next;
    			current->next = current->next->next;
    			free(next);
    		}
    		else
    		{
    			current = current->next;
    		}
    	} 
    }

11. MoveNode()

定义一个函数MoveNode()，它接收两个链表，将其第二个链表中第一个节点插入到第一个链表的头端，例如下面的测试程序，

    void MoveNodeTest()
    {
    	struct node * a = BuildOneTwoThree();
    	struct node * b = BuildOneTwoThree();
    		
    	MoveNode(&a,&b);		
    	//result: a={1,1,2,3}, b={2,3}
    }
    
    void MoveNode(struct node **destRef, struct node ** sourceRef)
    {
    	assert(*sourceRef);	//判断第二个链表是否为空
    
    	struct node * source = *sourceRef;
    	
    	*sourceRef = source->next;
    	source->next = *destRef;
    	*destRef = source;
    }

12. AlternatingSplit()

定义一个函数AlternatingSplit()将一个链表切分到两个子表中，原表中偶数索引处的节点分到第一个子表中，奇数索引处的节点分到第二个子表中.

方法一：使用前面定义的MoveNode来实现，使用这种方法切分的一个特别之处在于元素插入的顺跟它们在原表中的顺序相反

    void AlternatingSplit(struct node * source, struct node ** aRef, struct node **bRef)
    {
    	struct node * current = source;
    	
    	while (current != NULL)
    	{
    		MoveNode(aRef,&current);
    		if (current != NULL)
    			MoveNode(bRef,&current);
    	}
    }

方法二：利用一个虚设的节点来气氛链表，这样子表中节点的数据就不会是逆序的了

    void AlternatingSplit(struct node * source, struct node ** aRef, struct node **bRef)
    {
    	struct node adummy;
    	adummy.next = NULL;
    	struct node ** atmpRef = &adummy.next;
     
    	struct node bdummy;
    	dbummy.next = NULL;
    	struct node **btmpRef = &bdummy.next;
    
    	struct node *current = source;
    	while (current != NULL)
    	{
    		MoveNode(atmpRef,&current);
    		atmpRef = &(*atmpref)->next;
    		if (current != NULL)
    		{
    			MoveNode(btmpRef,&current);
    			btmpRef = &((*btmpref)->next);
    		}
    	}
    	
    	*aRef = adummy.next;
    	*bRef = bdummy.next;
    }


13. ShuffleMerge()

定义一个函数ShuffleMerge()用于将连个链表合并到一个链表，在两个链表中交替的取节点，例如{1，2，3}和{7，13，1}ShuffleMerge()后形成的新链表为{1,7,2,13,3,1}，如果两个链表中任意一个中的节点先取完，另一个链表中剩下的节点都应该加到新链表中。

方法一：使用虚设节点

struct node * ShuffleMerge(struct node *a, struct node *b)
{
	struct node dummy;
	struct node * tail = &dummy;
	dummy.next = NULL;

	while (1)
	{
		if (a == NULL)
		{
			tail->next = b;
			break;
		}

		if (b == NULL)
		{
			tail->next = a;
			break;
		}

		tail->next = a;
		a = a->next;
		tail = tail->next;
		tail->next = b;
		tail = b;
		b = b->next;
	}
	
	return dummy.next;
}

方法二：使用虚设节点和MoveNode()

struct node * ShuffleMerge(struct node *a, struct node *b)
{
	struct node dummy;
	struct node * tail = &dummy;
	dummy.next = NULL;

	while (1)
	{
		if (a == NULL)
		{
			tail->next = b;
			break;
		}

		if (b == NULL)
		{
			tail->next = a;
			break;
		}

		MoveNode(&tail,&a);
		tail = tail->next;
		MoveNode(&tail,&b);
		tail = tail->next;
	}
	
	return dummy.next;
}



14. SortedMerge()

15. MergeSort()

16. SortedIntersect()

17. Reverse()

定义一个函数Reverse()来翻转一个链表(原地翻转，不开辟新节点)，先看下面的测试程序，

    void ReverseTest()
    {
    	struct node *head = BuildOneTwoThree();	//{1,2,3}
    	Reverse(&head);	//head now points to {3,2,1}
    	
    	DeleteList(head);	
    }

方法一：

    void Reverse(struct node ** headRef)
    {
    	struct node * result = NULL;
    	struct node * current = *headRef;
    	struct node * next = NULL;
    
    	while (current != NULL)
    	{
    		next = current->next;		//保存当前节点的下一个节点
    		current->next = result;		//更新当前节点的指向
    		result = current;			//保存当前节点
    		current = next;				//更新当前节点
    	}
    
    	*headRef = result;
    }


方法二：利用前面的MoveNode函数

    void Reverse(struct node ** head)
    {
    	struct node * tmp = NULL;
    
    	while (*head != NULL)
    		MoveNode(&tmp,head);
    
    	*head = tmp;
    }

调用Reverse()来翻转链表{1，2，3}的内存视图应该如下所示：

18. RecursiveReverse()

这个问题很难，只有在熟悉递归的情况下才有可能。这个问题有一个简短有效的递归解决方案。 和以前一样，链表只能迭代一次。 做多遍是比较容易，但速度很慢，所以我们坚持只迭代一次链表。解决这个问题需要真正理解指针代码和递归。

    void RecursiveReverse(struct node **headRef)
    {
    	struct node * first = *headRef;
    	struct node * rest = NULL;
    
    	if (first == NULL)
    		return;	//空表
    
    	rest = first->next;
    	if (rest == NULL)
    		return; //链表中只有一个节点，不需要翻转
    
    	RecursiveReverse(&rest);		//RecursiveReverse(&rest)是一个关键的概念，表示翻转链表中剩余的节点
    
    	first->next->next = first;
    	first->next = NULL;
    	
    	*headRef = rest;
    }

前面定义的函数中用到的基础函数Length(), Push(), BuildOneTwo()其实现代码如下：

    //返回链表中节点的个数
    int Length(struct node * head)
    {
    	int count = 0；
    	struct node * current = head;
    
    	while (current != NULL)
    	{
    		count++;
    		current = current->next;
    	}
    
    	return count;
    }

    //在链表的头端插入一个新节点，该操作使用三步操作法
    void Push(struct node ** headRef,int data)
    {
    	//1、allocate
    	struct node * newNode = malloc(sizeof(strct node));
    	newNode->data = data;
    
    	//2、link next
    	newNode->next = *headRef;
    
    	//3、link head
    	*headRef = newNode;
    }

    //创建链表{1，2，3}
    struct node * BuildOneTwoThree()
    {
    	struct node * head = NULL;
    	
    	Push(&head,3);
    	Push(&head,2);
    	Push(&head,1);
    
    	return head;
    }

**> 参考文档**

[LinkedListBasics](http://cslibrary.stanford.edu/103/LinkedListBasics.pdf)

[LinkedListProblems](http://cslibrary.stanford.edu/105/LinkedListProblems.pdf)
