---
layout: post
title: '二叉查找树'
subtitle: ''
date: 2018-08-30
categories: 数据结构
cover: ''
tags: 数据结构 算法
---
# 介绍
二叉查找树是一颗空树,或者是具有如下性质的树:
* 若它的左子树不为空,则左子树上的所有节点的值均小于它的根节点的值
* 若它的右子树不空,则右子树上所有节点的值均大于根节点的值
* 它的左右子树也分别为二叉排序树

从它的第三点可以知道,二叉查找树是个递归的概念.另外上述的定义中生成的二叉树是唯一的,但实际应用时,不能保证被查找的数据集中各元素中各元素的关键词互不相同,**所以可以修改性质1中的小于为小于等于,或将性质二的大于改为大于等于,甚至可以同时修改这两个性质**

**从二叉查找树的性质可以推得一个性质:中序遍历二叉树所得到的中序序列是个递增有序序列**这个性质我不怎么清楚.




# 节点
为了支持所有类型的树,二叉树的节点是这样定义的
```Java
    private static class BinaryTreeNode<AnyType> {
        AnyType element;
        BinaryTreeNode<AnyType> lNode;
        BinaryTreeNode<AnyType> rNode;


        BinaryTreeNode(AnyType element) {
            this(element, null, null);
        }

        BinaryTreeNode(AnyType element, BinaryTreeNode<AnyType> lNode, BinaryTreeNode<AnyType> rNode) {
            this.element = element;
            this.lNode = lNode;
            this.rNode = rNode;
        }
    }
```


# insert()方法
插入操作实际上是二叉查找树的最重要的方法,因为构造二叉查找树的过程实际上是把个个元素通过这个方法插入到二叉树中

整个插入的算法也相当简单,也是递归的插入,它思路是这样的,将头节点作为起始参数,与要插入的元素相比较,如果已经有,则不做任何事情,如果大于就递归的往右子树插入,如果小于就往左子树插入,在整个插入过程中,要么不插入,要么作为叶子节点被插入到末尾.

可以这样设计
```Java
    private BinaryTreeNode<AnyType> insert(AnyType x, BinaryTreeNode<AnyType> rootnode) {
        if (rootnode == null) {
            return new BinaryTreeNode<AnyType>(x, null, null);
        }
        
        int v = x.compareTo(rootnode.element);
        
        if (v < 0) {
            rootnode.lNode = insert(x, rootnode.lNode);
        } else if (v > 0) {
            rootnode.rNode = insert(x, rootnode.rNode);
        }else {
            // 不做任何事情,可以删去else,这里写出来是为了方便理解
        }
        return rootnode;
    }
```

# 实现Comparable接口
这颗二叉树是一棵特殊的树,是一棵二叉查找树,既然有查找两字就一定少不了比较,Java中有一个类定义通用的比较,如何比较由这个类来决定.Comparabale这个接口我还不怎么熟悉.所以学一学.

Comparable有一个泛型,这个类型规定实现这个接口的类可以比较的类型,如果不指明,将是所有类型的基类Object类,这意味着实现这个接口的类可以和所有类相比较,这显然是容易引发错误的.
```Java
public class TestClass implements Comparable<TestClass> {

    @Override
    public int compareTo(TestClass o) {
        return 0;
    }
}
```
虽然这个怎么反应大小关系是由开发者决定的,但是有一条潜规则,小于关系通常是负数,大于关系通常是正数,等于关系通常会返回0



# contains()方法
contains()方法的作用是判断某一个值是否存在于这颗二叉搜索树中,如果存在返回true,如果不存在则返回false.在树中实现这个方法是容易的,因为我们可以递归的实现,将头节点作为递归函数的起始参数,如果头节点为空则返回false,这个条件也是递归函数的其中一个跳出条件,通常是这颗二叉查找树没有这个节点使用这个条件跳出,将这个值与头节点比较,相同则返回true,大于则进入右子树递归,小于则进入左子树递归.
```Java
    private boolean contains(AnyType x, BinaryTreeNode<AnyType> rootnode) {
        if (rootnode == null) {
            return false;
        }
        int v = x.compareTo(rootnode.element);
        if (v == 0) {
            return true;
        } else if (v > 0) {
            return contains(x, rootnode.rNode);
        } else {
            return contains(x, rootnode.lNode);
        }
    }
```

# findMin()和findMax()
根据二叉查找树的定义,找到一颗二叉树的最小节点和最大节点其实是很容易的,根据定义,左子树总小于根节点,右子树总是大于根节点,因此只要一直往左找,一直找到最左边的就是最小的节点,同理只要一直往右找,直到找到最右边的节点就是最大的节点.

可以这样设计
```Java
    private BinaryTreeNode<AnyType> finMin(BinaryTreeNode<AnyType> rootnoot) {
        if (rootnoot == null) {
            return null;
        } else if (rootnoot.lNode == null) {
            return rootnoot;
        }
        return finMin(rootnoot.lNode);
    }
```
同理findMax()也可以这样设计,还可以通过非递归的方式来设计
```Java
    private BinaryTreeNode<AnyType> findMax(BinaryTreeNode<AnyType> rootnode){
        if (rootnode!=null){
            while (rootnode.rNode!=null){
                rootnode = rootnode.rNode;
            }
        }
        return rootnode;
    }
```

# remove()
移除是二叉查找树中最麻烦的操作,因为不是简单的移除,移除了一个节点以后仍然要保持二叉搜索树的结构.节点可能是三种情况

* 要删除的节点为叶子节点,这种情况是可以直接删除的
* 要删除的节点有一个子树,这种情况直接拼接
* 要删除的节点有两个子树,这种情况用右子树最小的数据替代要删除的节点,这意味要进行两次移除

可以这样设计
```Java
    private BinaryTreeNode<AnyType> remove(AnyType x, BinaryTreeNode<AnyType> rootnode) {
        if (rootnode == null) {
            return rootnode;
        }
        int v = x.compareTo(rootnode.element);
        if (v < 0) {
            rootnode.lNode = remove(x, rootnode.lNode);
        } else if (v > 0) {
            rootnode.rNode = remove(x, rootnode.rNode);
        } else if (rootnode.lNode != null && rootnode.rNode != null) {
            rootnode.element = findMin(rootnode.rNode).element;
            rootnode.rNode = remove(rootnode.element, rootnode.rNode);
        } else {
            rootnode = (rootnode.lNode != null) ? rootnode.lNode : rootnode.rNode;
        }
        return rootnode;
    }
```

# 二叉查找树
```Java
public class BinarySearchTree<AnyType extends Comparable<? super AnyType>> {

    private BinaryTreeNode<AnyType> root;

    public BinarySearchTree() {
        root = null;
    }

    public static class BinaryTreeNode<AnyType> {
        AnyType element;
        BinaryTreeNode<AnyType> lNode;
        BinaryTreeNode<AnyType> rNode;


        BinaryTreeNode(AnyType element) {
            this(element, null, null);
        }

        BinaryTreeNode(AnyType element, BinaryTreeNode<AnyType> lNode, BinaryTreeNode<AnyType> rNode) {
            this.element = element;
            this.lNode = lNode;
            this.rNode = rNode;
        }
    }

    public void insert(AnyType x) {
        root = insert(x, root);
    }

    public boolean contains(AnyType x) {
        return contains(x, root);
    }

    public void printTree() {
        if (isEmpty()) {
            System.out.println("这是一颗空树");
        } else {
            printTree(root);
        }
    }

    public boolean isEmpty() {
        return root == null;
    }

    public BinaryTreeNode<AnyType> findMin() {
        return findMin(root);
    }

    public BinaryTreeNode<AnyType> findMax() {
        return findMax(root);
    }

    public void remove(AnyType x) {
        root = remove(x, root);
    }


    private BinaryTreeNode<AnyType> findMin(BinaryTreeNode<AnyType> rootnoot) {
        if (rootnoot == null) {
            return null;
        } else if (rootnoot.lNode == null) {
            return rootnoot;
        }
        return findMin(rootnoot.lNode);
    }

    private BinaryTreeNode<AnyType> findMax(BinaryTreeNode<AnyType> rootnode) {
        if (rootnode != null) {
            while (rootnode.rNode != null) {
                rootnode = rootnode.rNode;
            }
        }
        return rootnode;
    }

    private void printTree(BinaryTreeNode<AnyType> rootnode) {
        if (rootnode == null) {
            return;
        } else {
            System.out.println(rootnode.element);
            printTree(rootnode.lNode);
            printTree(rootnode.rNode);
        }
    }

    private boolean contains(AnyType x, BinaryTreeNode<AnyType> rootnode) {
        if (rootnode == null) {
            return false;
        }
        int v = x.compareTo(rootnode.element);
        if (v == 0) {
            return true;
        } else if (v > 0) {
            return contains(x, rootnode.rNode);
        } else {
            return contains(x, rootnode.lNode);
        }
    }

    private BinaryTreeNode<AnyType> insert(AnyType x, BinaryTreeNode<AnyType> rootnode) {
        if (rootnode == null) {
            return new BinaryTreeNode<AnyType>(x, null, null);
        }
        int v = x.compareTo(rootnode.element);
        if (v < 0) {
            rootnode.lNode = insert(x, rootnode.lNode);
        } else if (v > 0) {
            rootnode.rNode = insert(x, rootnode.rNode);
        } else {
            // 不做任何事情,可以删去else,这里写出来是为了方便理解
        }
        return rootnode;
    }

    private BinaryTreeNode<AnyType> remove(AnyType x, BinaryTreeNode<AnyType> rootnode) {
        if (rootnode == null) {
            return rootnode;
        }
        int v = x.compareTo(rootnode.element);
        if (v < 0) {
            rootnode.lNode = remove(x, rootnode.lNode);
        } else if (v > 0) {
            rootnode.rNode = remove(x, rootnode.rNode);
        } else if (rootnode.lNode != null && rootnode.rNode != null) {
            rootnode.element = findMin(rootnode.rNode).element;
            rootnode.rNode = remove(rootnode.element, rootnode.rNode);
        } else {
            rootnode = (rootnode.lNode != null) ? rootnode.lNode : rootnode.rNode;
        }
        return rootnode;
    }
    
}
```