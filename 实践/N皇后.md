

N*N的矩阵中放置N个皇后，他们不能在同一行或者同一列或者斜着(反斜)的一列。


想法1：
1. dfs搜索 行就是深度
2. 递归 回溯，每次回溯把当前一行全置为0
3. 终止条件 >=N
4. 数组[i][j] = 1 表示存在
5. 如果数组 判断条件 j1+i1 = j2+i2 斜着
6. 判断条件j2-i2 = j1 - i1 反斜
7. i相同或者j相同则 处于同一行 同一列


思路2：
1. dfs搜索 行就是深度
2. 递归 回溯
3. 终止条件 >=N
4. [i][j] = 1 表示存在
5. 判断符合要求 col+step<N && chess[row-step][col+step]==1 撇
6. 判断符合要求 col-step>=0 && chess[row-step][col-step]==1 捺
7. 判断符合要求 每行一个，列不一样
8. 每次回溯要情空当前的放置情况（1置为0）




```java
public class Queen {
    private static final short N=4;        //使用常量来定义，方便之后解N皇后问题
    private static int count=0;            //结果计数器

    public static void main(String[] args) {
       
        //初始化棋盘，全部置0
        short chess[][]=new short[N][N];
        for(int i=0;i<N;i++){
            for(int j=0;j<N;j++){
                chess[i][j]=0;
            }
        }

        //求解
        putQueenAtRow(chess,0);

    }

    private static void putQueenAtRow(short[][] chess, int row) {
        /**
         * 递归终止判断：如果row==N，则说明已经成功摆放了8个皇后
         * 输出结果，终止递归
         */
        if(row==N){
            count++;
            System.out.println("第 "+ count +" 种解：");
            for(int i=0;i<N;i++){
                for(int j=0;j<N;j++){
                    System.out.print(chess[i][j]+" ");
                }
                System.out.println();
            }
            return;
        }

        short[][] chessTemp=chess.clone();

        /**
         * 向这一行的每一个位置尝试排放皇后
         * 然后检测状态，如果安全则继续执行递归函数摆放下一行皇后
         */
        for(int i=0;i<N;i++){
            //摆放这一行的皇后，之前要清掉所有这一行摆放的记录，防止污染棋盘
            for(int j=0;j<N;j++)
                chessTemp[row][j]=0;
            
            chessTemp[row][i]=1;//尝试放置

            if( isSafety( chessTemp,row,i ) ){
                putQueenAtRow(chessTemp,row+1);//如果成功 接下来一行
            }
            //失败则换下一个位置
        }
    }

    private static boolean isSafety(short[][] chess,int row,int col) {
        //判断中上、左上、右上是否安全
        int step=1;
        while(row-step>=0){
            if(chess[row-step][col]==1)                //中上
                return false;
            if(col-step>=0 && chess[row-step][col-step]==1)        //左上
                return false;
            if(col+step<N && chess[row-step][col+step]==1)        //右上
                return false;
            step++;
        }
        return true;
    }
}
```
