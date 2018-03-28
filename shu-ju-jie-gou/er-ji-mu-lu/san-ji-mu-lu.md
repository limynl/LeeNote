# 二叉树基本操作
## 主要实现了二叉树的构造，及常见的遍历方法

```
public class BinaryTree {
    public static void main(String[] args) {
        BinaryTree binaryTree = new BinaryTree();
        int[] datas = new int[]{1,2,3,4,5,6,7,8,9};
        List<BinaryNode> nodeList = new ArrayList<BinaryNode>(datas.length);
        binaryTree.createBinaryTree(datas, nodeList);
        System.out.println("前序遍历为：");
        binaryTree.preTraverse(nodeList.get(0));
        System.out.println("\n中序遍历为：");
        binaryTree.inTraverse(nodeList.get(0));
        System.out.println("\n后序遍历为：");
        binaryTree.postTraverse(nodeList.get(0));
        System.out.println("\n层序遍历为：");
        binaryTree.levelTraverse(nodeList.get(0), nodeList.size());

        System.out.println("\n非递归前序遍历：");
        binaryTree.preTraverseByLoop(nodeList.get(0));
        System.out.println("\n非递归中序遍历：");
        binaryTree.inTraverseByLoop(nodeList.get(0));
        System.out.println("\n非递归后序遍历：");
        binaryTree.postTraverseByLoop(nodeList.get(0));
    }

    /**
     * 构造二叉树
     * @param datas 二叉树中各节点的值
     * @param nodeList 二叉树中的各节点
     */
    public void createBinaryTree(int[] datas, List<BinaryNode> nodeList){
        //为各节点添加数据域
        BinaryNode binaryNode = null;
        for(int i = 0; i < datas.length; i++){
            binaryNode = new BinaryNode(datas[i]);
            nodeList.add(binaryNode);
        }

        /**
         * 说明：
         *  1、为各个父节点添加相应的子节点，按照层序的方式添加子节点
         *  2、将二叉树按照完全二叉树编号，根节点的编号为1，若某节点i有左孩子，则其左孩子的编号为2i
         *     若某节点有右孩子，则其右孩子的编号为(2i + 1)
         *  3、二叉树中若有n个节点，那么父节点的个数为(n/2)
         *  4、二叉树中若节点的个数为奇数，那么最后一个父节点有右子节点
         *     若节点的个数为偶数，那么最后一个父节点只有左子节点，没有右子节点
         *  5、list中节点的下标从0开始，因此编号为i的节点存储到下标为(i - 1)的位置
         */
        //nodeList.size() / 2 - 1，减1的原因是，最后一个父节点可能没有右子节点，所以需要单独处理
        for(int i = 0; i < nodeList.size() / 2 - 1; i++){
            //加1的原因是，list从0开始编号
            nodeList.get(i).leftNode = nodeList.get(i * 2 +1);
            nodeList.get(i).rightNode = nodeList.get(i * 2 + 1 + 1);
        }

        //单独处理最后一个父节点
        int lastNode = nodeList.size() / 2 - 1;
        nodeList.get(lastNode).leftNode = nodeList.get(lastNode * 2 + 1);
        //二叉树中节点的总个数为奇数，此时最后一个父节点有右子节点
        if(nodeList.size() % 2 != 0){
            nodeList.get(lastNode).rightNode = nodeList.get(lastNode * 2 + 1 + 1);
        }
    }

    /**
     * 前序递归遍历二叉树
     */
    public void preTraverse(BinaryNode rootNode){
        //递归遍历结束条件
        if(rootNode == null){
            return;
        }else{
            //访问根节点rootNode的数据域
            System.out.print(rootNode.value + ", ");
            //前序递归调用rootNode的左子树
            preTraverse(rootNode.leftNode);
            //前序递归调用rootNode的右子树
            preTraverse(rootNode.rightNode);
        }
    }

    /**
     * 中序递归遍历二叉树
     */
    public void inTraverse(BinaryNode rootNode){
        if(rootNode == null){
            return;
        }else{
            inTraverse(rootNode.leftNode);
            System.out.print(rootNode.value + ", ");
            inTraverse(rootNode.rightNode);
        }
    }

    /**
     * 后序递归调用二叉树
     */
    public void postTraverse(BinaryNode rootNode){
        if(rootNode == null){
            return;
        }else{
            postTraverse(rootNode.leftNode);
            postTraverse(rootNode.rightNode);
            System.out.print(rootNode.value + ", ");
        }
    }

    /**
     * 层序遍历二叉树
     * 思想：
     *  在进行层序遍历时，对某一层的节点访问完后，再按照它们的访问次序对各个节点的左孩子和右孩子顺序访问
     *  因此先访问的节点其左右孩子也要先访问，符合队列的操作特性
     */
    public void levelTraverse(BinaryNode rootNode, int nodeSize){
        //使用顺序队列
        BinaryNode[] nodes = new BinaryNode[nodeSize];
        int front = -1, rear = -1;
        //二叉树为空，直接结束
        if(rootNode == null){
            return;
        }
        //根节点入队列
        nodes[++rear] = rootNode;
        BinaryNode tempNode = null;
        //当队列非空时
        while(front != rear){
            //出队
            tempNode = nodes[++front];
            System.out.print(tempNode.value + ", ");
            //将当前节点的左节点入队列
            if(tempNode.leftNode != null){
                nodes[++rear] = tempNode.leftNode;
            }
            //将当前节点的右节点入队列
            if(tempNode.rightNode != null){
                nodes[++rear] = tempNode.rightNode;
            }
        }
    }

    /**
     * 前序非递归遍历二叉树
     */
    public void preTraverseByLoop(BinaryNode rootNode){
        Stack<BinaryNode> nodeStack = new Stack<BinaryNode>();

        //若根节点和栈均为空，则整个二叉树遍历结束
        while(rootNode != null || !nodeStack.isEmpty()){
            while(rootNode != null){
                System.out.print(rootNode.value + ", ");
                //将根节点入栈
                nodeStack.push(rootNode);
                //准备遍历根节点的左子树
                rootNode = rootNode.leftNode;
            }
            //此时根节点为空，但是栈非空，将根节点弹出，并遍历根节点的左子树
            if(!nodeStack.isEmpty()){
                rootNode = nodeStack.pop();
                rootNode = rootNode.rightNode;
            }
        }
    }

    /**
     * 中序非递归遍历二叉树
     */
    public void inTraverseByLoop(BinaryNode rootNode){
        Stack<BinaryNode> nodeStack = new Stack<BinaryNode>();
        while(rootNode != null || !nodeStack.isEmpty()){
            while(rootNode != null){
                nodeStack.push(rootNode);
                rootNode = rootNode.leftNode;
            }
            if(!nodeStack.isEmpty()){
                //遍历过程中，不能立即访问，只有当它的左子树遍历完毕，才从栈中弹出并访问
                rootNode = nodeStack.pop();
                System.out.print(rootNode.value + ", ");
                rootNode = rootNode.rightNode;
            }
        }
    }

    /**
     * 后序非递归遍历二叉树
     */
    public void postTraverseByLoop(BinaryNode rootNode){
        Stack<BinaryNode> nodeStack = new Stack<BinaryNode>();
        BinaryNode pre = rootNode;
        while(rootNode != null || !nodeStack.isEmpty()){
            while(rootNode != null){
                nodeStack.push(rootNode);
                rootNode = rootNode.leftNode;
            }
            if(!nodeStack.isEmpty()){
                BinaryNode tempNode = nodeStack.peek().rightNode;
                if(tempNode == null || tempNode == pre){
                    rootNode = nodeStack.pop();
                    System.out.print(rootNode.value + ", ");
                    pre = rootNode;
                    rootNode = null;
                }else{
                    rootNode = tempNode;
                }
            }
        }
    }

    /**
     * 节点类
     */
    private static class BinaryNode{
        private int value;
        private BinaryNode leftNode;
        private BinaryNode rightNode;

        public BinaryNode(int value) {
            this(value, null, null);
        }

        public BinaryNode(int value, BinaryNode leftNode, BinaryNode rightNode) {
            this.value = value;
            this.leftNode = leftNode;
            this.rightNode = rightNode;
        }
    }
}




```

