

在一个N*M的矩阵中，布满X个雷，要求随机分布。

>思路：

1. 先让X个雷填满前X个位置：


```
//6个雷
 1 1 1 1 
 1 1 0 0 
 0 0 0 0
 0 0 0 0
```
2. 随机进行矩阵多次交换:

```
随机[i1][j1]和[i2][j2]交换
```

>代码

```java

  public static void Solve(int[][] pos,int x,int swapTime){
        Random r1 = new Random();
        int m = pos[0].length;
        int n = pos.length;
        
        for(int i=0;i<x;i++){ 前N个置为1标识有雷
            int row = i/m;
            int col = i%n
            pos[row][col] = 1;
        }

      
        for(int k = 0;k<swapTime;k++){
        //也可以用时间左随机数
//            int row1=(int)(System.nanoTime()%m);
//            int row2=(int)(System.nanoTime()%m);
//            int co1=(int)(System.nanoTime()%n);
//            int co2=(int)(System.nanoTime()%n);
            int row1=r1.nextInt(n);
            int row2=r1.nextInt(n);
            int co1=r1.nextInt(m);
            int co2=r1.nextInt(m);

            int temp = pos[row1][co1];
            pos[row1][co1] = pos[row2][co2];
            pos[row2][co2] = temp;
        }

    }


```


