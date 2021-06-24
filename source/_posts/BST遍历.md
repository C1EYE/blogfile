---
title: BST遍历
date: 2021-05-16 23:48:11
tags: 算法,Java
category: 算法
---

## 前序遍历

思路：

1. 创建一个栈，从根节点开始
2. 访问一个节点，这里传入了一个访问器把访问的实现留给用户，同时把**右孩子节点入栈**
3. 继续向下访问左孩子节点，重复2直到访问完左子树（node==null）
4. 如果栈非空，出栈一个节点，重复2
5. 否则结束遍历



```java
public void preorder(Visitor<E> visitor) {
		if(visitor == null || root == null){
			return;
		}
		Node<E> node = root;

		Stack<Node<E>> stack = new Stack<>();

		while(true){
			if(node!=null){
				if(visitor.visit(node.element)) {
                    return;
                }
				if(node.right!=null){
					stack.push(node.right);
				}
				node = node.left;
			}else{
				if(stack.isEmpty()){
					return;
				}
				node = stack.pop();
			}
		}
}
```



- 显然时间复杂度就是O(n)，空间复杂度O(n)



## 中序遍历

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210514185053.png" alt="image-20210514185053824" style="zoom:50%;" />



思路：

1. 创建一个栈
2. 入栈当前节点，node = node.left
3. 如果**当前节点为空并且栈不为空**，出栈一个节点，访问它并将node指向这个节点的右节点，重复2
4. 否则遍历结束



```java
public void inorder(Visitor<E> visitor) {
	    if(visitor==null||root == null){
            return;
        }
        Node<E> node = root;
        Stack<Node<E>> stack = new Stack<>();
	    while (true){
	        if(node!=null){
                stack.push(node);
                node = node.left;
            }else {
	            if(stack.isEmpty()){
                    return;
                }
                node = stack.pop();
	            if(visitor.visit(node.element)){
                    return;
                }
				node = node.right;

            }

	    }
	   
```



## 后序遍历



<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210514194526.png" alt="image-20210514194526599" style="zoom:33%;" />

思路:

1. 创建一个栈，将根节点入栈
2. 如果栈不为空，peek栈顶元素
3. 如果这个节点**不是叶子节点**也**不是上一个出栈的叶子节点的父节点**（prev），将这个节点的右孩子节点和左孩子节点入栈（前提是不为空）
4. 否则出栈该节点，访问并维护prev指向，重复2，3
5. 栈空遍历结束



```java
public void postorder(Visitor<E> visitor) {
        if (visitor == null || root == null) {
            return;
        }

        Stack<Node<E>> stack = new Stack<>();
        stack.push(root);
        Node<E> top;
        Node<E> prev = null;
        while (!stack.isEmpty()) {
            top = stack.peek();

            if (top.isLeaf() || (prev != null && prev.parent == top)) {
                prev = stack.pop();
                if (visitor.visit(prev.element)) {
                    return;
                }
            } else {
                if (top.right != null) {
                    stack.push(stack.peek().right);
                }
                if (top.left != null) {
                    stack.push(top.left);
                }
            }


        }
    }
```



## 前序遍历的另一种方法

思路:

1. 创建栈，根节点入队
2. 如果栈不为空，出栈节点并访问
3. 入栈节点右孩子节点和左孩子节点（前提不为空），重复2
4. 栈空遍历结束

```java
 public void preorder2(Visitor<E> visitor) {
        if (visitor == null || root == null) {
            return;
        }

        Stack<Node<E>> stack = new Stack<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            Node<E> node = stack.pop();
            if(visitor.visit(node.element)){
                return;
            }
            if(node.right!=null){
                stack.push(node.right);
            }
            if(node.left!=null){
                stack.push(node.left);
            }

        }
```

总结：

> 总的来说使用非递归遍历树主要就是先到达叶子节点，然后处理边界问题,后序遍历稍特殊，需要判断是否为叶子节点来确保遍历所有节点。



- 这里的visotor没什么必要，这是用来在递归算法里标记是否结束