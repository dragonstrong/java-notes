**<font size=6>算法</font>**



[toc]

# 一. 动态规划(DP)

**类型：**

**1、dp数组本身就是最后结果**

**2、求出所有点，然后dp数组求全局最大,** 

**如 [11、 **[最大正方形](https://leetcode.cn/problems/maximal-square/)] 

还有如[14、**[最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)**] ，定义$dp[i]$为以$nums[i]$结尾的最长递增子序列的长度，全局最大为dp数组的最大值。

**3、二维动态规划**

涉及两个数组

[12、**[最长公共子序列](https://leetcode.cn/problems/longest-common-subsequence/)(**二维动态规划)]

[**[剑指 Offer 47. 礼物的最大价值](https://leetcode.cn/problems/li-wu-de-zui-da-jie-zhi-lcof/)**]

## 1. 完全背包，有顺序(leetcode 377)

[力扣](https://leetcode-cn.com/problems/combination-sum-iv/)

```markdown
给你一个由不同整数组成的数组 nums ，和一个目标整数 target 。
请你从 nums 中找出并返回总和为 target 的元素组合的个数。

示例：
输入：nums = [1,2,3], target = 4
输出：7
解释：
所有可能的组合为：
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1
(2, 2）
(3, 1)
请注意，顺序不同的序列被视作不同的组合。
```

![Untitled](assets/Untitled.png)

有顺序的组合背包问题固定公式：

新建一个数组

```java
int[] dp=new int[target+1];   //dp[i]表示和为i的组合方案个数  0-target
```

假设和$i$的组合方案个数表示为$dp[i]$，则遍历数组$nums$，若元素$nums[j]<=i$，则可在和为$i-num[j]$的组合最后插入$num[j]$，使新组合和为$i$，故公式如下:

```java
dp[i] +=dp[i-nums[j]];
```

边界初值：

```java
dp[0]=1;  //和为0的组合只有一种，就是一个都不取
```

分析：

```
nums=[1,2,3]  target=4

和为0： []
和为1：[1]          在和为0的基础上加1
和为2：[1 1]        在和为1的基础上加1
       [2]         在和为0的基础上加2
和为3：[1 1 1] [2 1] 
      [1 2] 
      [3]
和为4：[1 1 1 1] [2 1 1] [1 2 1] [3 1]   在和为3的基础上加1
      [1 1 2] [2 2]                      在和为2的基础上加2
      [1 3]                              在和为1的基础上加3
```

完整代码：

```java
class Solution   //16:40-16:52
{
    public int combinationSum4(int[] nums, int target) 
    {
        int[] dp=new int[target+1];
        dp[0]=1;
        for(int i=1;i<=target;i++)
        {
            int count=0;
            for(int j:nums)
            {
                if(j<=i)
                count +=dp[i-j];      
            }
            dp[i]=count;
        }
       return dp[target];
    }
}
```

时间复杂度：

$O(target*n)$                             $n$为数组长度

## 2. 组合问题（无顺序，要避免重复）(518 零钱兑换)

[力扣](https://leetcode-cn.com/problems/coin-change-2/)

```markdown
示例

输入：amount = 5, coins = [1, 2, 5]
输出：4
解释：有四种方式可以凑成总金额：
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1
```

思路还是和上一个一样，但要避免重复。因此先遍历数组元素，后遍历总和。（先遍历数组元素保证硬币出现的顺序固定：即每种组合中按照1→2→5的顺序出现，2只会出现在1之后，5只会出现在1、2之后，因此总和为3的组合中没有{2，1}，从而避免和{1，2}重复）

```java
class Solution {
    public int change(int amount, int[] coins) 
    {
        int[] dp=new int[amount+1];
        dp[0]=1;
        for(int i=0;i<coins.length;i++)  //先遍历数组元素
        for(int j=coins[i];j<=amount;j++) //后遍历总和
        {
            dp[j] +=dp[j-coins[i]];
        }
        return dp[amount];
    }
}
```

![Untitled](assets/Untitled 1.png)

## 3. 零钱兑换

![Untitled](assets/Untitled 2.png)

![Untitled](assets/Untitled 3.png)

```java
public class Solution {
    public int coinChange(int[] coins, int amount) {
        int max = amount + 1;
        int[] dp = new int[amount + 1];   
        Arrays.fill(dp, max);  //除dp[0]都初始化一个较大值  方便后面比较和判断是否更新
        dp[0] = 0;
        for (int i = 1; i <= amount; i++) {
            for (int j = 0; j < coins.length; j++) {
                if (coins[j] <= i) {
                    dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1);  
                }
            }
        }
        return dp[amount] > amount ? -1 : dp[amount];  //没有更新说明找不到组合
    }
}
```

## 4. 剪绳子

![Untitled](assets/Untitled 4.png)

状态转移方程

对绳长$n$的绳子，可分为$i+(n-i)$，其中$1\le i<n$：

- 若$n-i$不再分  ，则乘积为$i*(n-i)$
- 若$n-i$再分，则乘机为$i*dp[n-i]$

故状态转移方程：

$dp[n]=\mathop{max}\limits_{1\le i<n}\ (i*(n-i),i*dp[n-i])$

```java
class Solution 
{
    public int cuttingRope(int n) 
    {
        int[] dp=new int[n+1];
        //边界条件
        dp[0]=0;
        dp[1]=0;
        dp[2]=1;

        int max_mul=0;
        for(int i=3;i<=n;i++)
        {
            for(int j=1;j<i;j++)
            {
                max_mul=Math.max(max_mul,Math.max(j*(i-j),j*dp[i-j]));
            }
            dp[i]=max_mul;
        }
        return dp[n];
    }
}
```

**dfs写法:严重超时，此处要准确把握dfs中的递归点（调用之后的返回点  最大最小都是这种套路，需要还原现场）**

```java
class Solution 
{

    int target=1;
    int mul_max=1;
    int num=0;
    public int cuttingRope(int n) 
    {
        dfs(n);
        return mul_max;
    }

    public void dfs(int n)
    {
        if(n<0)
            return;
        if(n==0&&num!=1)
        {
            mul_max=Math.max(mul_max,target);
            return;
        }

        for(int i=1;i<=n;i++)
        {
            target *=i;
            num++;
            dfs(n-i);
            target /=i;  //准确把握调用之后的返回点  最大最小都是这种套路，这叫还原现场
            num--;
        }
    }
}
```

## 5. 最小路径和

![Untitled](assets/Untitled 5.png)

**dfs超时 ，一看数组这么大就不要用dfs**

```java
class Solution 
{
    public int minPathSum(int[][] grid) 
    {
       int[][] dp=new int[grid.length][grid[0].length];
       dp[0][0]=grid[0][0];
       for(int i=0;i<grid.length;i++)
       {
           for(int j=0;j<grid[0].length;j++)
           {
               if(i==0&&j==0)
               continue;
               if(j==0)  //菜逼 边界错误检查这么久 缺少continue  执行完if里的语句后还会执行最后一句 
               {
                   dp[i][j]=dp[i-1][j]+grid[i][j];
                   continue;
               }
               
               if(i==0)
               {
                   dp[i][j]=dp[i][j-1]+grid[i][j];
                   continue;
               }
               
               dp[i][j]=Math.min(dp[i][j-1],dp[i-1][j])+grid[i][j];
           }
       }
       return dp[grid.length-1][grid[0].length-1];

    }
}
```

**注意：if 不写else时的情况，后面的还要执行（很容易出错），如果不执行后面，勤快点加个continue**

## 7. 二维动态规划

**[剑指 Offer 47. 礼物的最大价值](https://leetcode.cn/problems/li-wu-de-zui-da-jie-zhi-lcof/)**

在一个 m*n 的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于 0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格、直到到达棋盘的右下角。给定一个棋盘及其上面的礼物的价值，请计算你最多能拿到多少价值的礼物？

```
示例1：
输入:[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]输出:12
解释: 路径 1→3→5→2→1 可以拿到最多价值的礼物
```

提示：

- `0 < grid.length <= 200`
- `0 < grid[0].length <= 200`

**dfs超时**

```java
class Solution  
{
    int max=0;
    int total=0;
    public int maxValue(int[][] grid) 
    {
        int m=grid.length,n=grid[0].length;
        int[][] dp=new int[m][n];  //dp[i][j]代表从（0，0）到（i，j）能获得的最大礼物值
]        dp[0][0]=grid[0][0];  
        for(int i=1;i<n;i++)
        dp[0][i]=dp[0][i-1]+grid[0][i];
        for(int j=1;j<m;j++)
        dp[j][0]=dp[j-1][0]+grid[j][0];

        for(int i=1;i<m;i++)
        for(int j=1;j<n;j++)
            dp[i][j]=Math.max(dp[i-1][j],dp[i][j-1])+grid[i][j];
        
        return dp[m-1][n-1];
    }
}
```

## 8. **[连续子数组的最大和](https://leetcode.cn/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/)**

![Untitled](assets/Untitled 6.png)

思路：

定义dp[i]为以nums[i]结尾的连续子数组最大和，则

$$
dp[i]=\begin{cases} dp[i-1]+nums[i]  \quad dp[i-1]>=0 \\nums[i] \qquad \qquad \qquad dp[i-1]<0 \end{cases}
$$

数组dp的最大值即为全局最大

```java
class Solution  //7:09-
{
    public int maxSubArray(int[] nums) 
    {
        int res=nums[0]; //全局最大
        int[] dp=new int[nums.length];
        dp[0]=nums[0];
        for(int i=1;i<nums.length;i++)
        {
           dp[i]=dp[i-1]>=0?dp[i-1]+nums[i]:nums[i];      
           res=Math.max(res,dp[i]);                                                                                                         
        }
        return res;
    }
}
```

## 9. **乘积最大子数组**

![Untitled](assets/Untitled 7.png)

对比和最大，任然是定义dp[i]为以nums[i]结尾的乘积极值，但是需要保存两个：一个最大值、一个最小值，以应对正负性（比如nums[i]为负数，乘一个最小负数得正）

【-2，3，-4】

【2，3，4】

```java
class Solution 
{
    public int maxProduct(int[] nums) 
    {
        int[][] dp=new int[nums.length][2]; //dp[i][0]表示以nums[i]结尾的乘机最大值  dp[0][1]为最小值 
        dp[0][0]=nums[0];
        dp[0][1]=nums[0];
        int res=dp[0][0];
        for(int i=1;i<nums.length;i++)
        {
        
            dp[i][0]=Math.max(Math.max(nums[i],dp[i-1][0]*nums[i]),dp[i-1][1]*nums[i]);
            res=Math.max(res,dp[i][0]);
            dp[i][1]=Math.min(Math.min(nums[i],dp[i-1][0]*nums[i]),dp[i-1][1]*nums[i]);
        } 

        return res;

    }
}
```

## 10. **[圆圈中最后剩下的数字](https://leetcode.cn/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/)**

[16、**[剑指 Offer 62. 圆圈中最后剩下的数字](https://leetcode.cn/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/)**](https://www.notion.so/16-Offer-62-0c804beb48544c849aa6905aab38b6c7?pvs=21) 

## 11.   **[打家劫舍](https://leetcode.cn/problems/house-robber/)（简单，作为参考）**

## 12.  **[最大正方形](https://leetcode.cn/problems/maximal-square/)**

![Untitled](assets/Untitled 8.png)

思路：

- $dp[i][j]$代表以$(i,j)$为右下角且只包含1 的正方形的边长最大值，遍历矩阵所有格子，dp数组的最大值就是最大边长。

转移方程

- $matrix[i][j]=$0，$dp[i][j]=0$
- 第一行和第一列，若$matrix[i][j]=1$，则$dp[i][j]=1$
- 对于$1<=i<matrix.length,\quad 1<=j<matrix[0].length \quad$,若$matrix[i][j]=1$，则$dp[i][j]=min(dp[i-1][j],dp[i-1][j-1],dp[i][j-1])+1$（由其上方、左方和左上方的三个相邻位置的$dp$值决定）
- 示例：

![Untitled](assets/Untitled 9.png)

```java
class Solution  //14:30-  19:46-
{
    public int maximalSquare(char[][] matrix) 
    { 
        if(matrix.length==0||matrix[0].length==0)
        return 0;
        else
        {
            int col=matrix.length,row=matrix[0].length;
            int[][] dp=new int[col][row]; //dp[i][j]表示以matrix[i][j]为右下角的最大矩形边长
            for(int i=0;i<row;i++)
                dp[0][i]=(matrix[0][i]=='0')?0:1;
            for(int i=1;i<col;i++)
                dp[i][0]=(matrix[i][0]=='0')?0:1;
            
            for(int i=1;i<col;i++)
            {
                for(int j=1;j<row;j++)
                {
                    if(matrix[i][j]=='0')
                    dp[i][j]=0;
                    else
                    dp[i][j]=Math.min(Math.min(dp[i-1][j],dp[i-1][j-1]),dp[i][j-1])+1;
                    
                }
            }

            int res=0;
            for(int i=0;i<col;i++)
                for(int j=0;j<row;j++)
                     res=Math.max(res,dp[i][j]);
         
            return res*res;
 
        }
    }  
}
```

## 13. **[最长公共子序列](https://leetcode.cn/problems/longest-common-subsequence/)(**二维动态规划)

![Untitled](assets/Untitled 10.png)

思路：

（1）定义dp数组

$dp[i][j]$：表示$text1[0:i-1]和text2[0:j-1]$最长公共子序列的长度

(2)边界条件和转移方程

$dp[i][j]=0 \quad (i=0或j=0)$

转移方程

- 当$text1[i-1]=text2[j-1]$时，$dp[i][j]=dp[i-1][j-1]+1$，例如ac与bc的最长子序列等于a与b的最长子序列加1
- 当$text1[i-1]!=text2[j-1]$时，$dp[i][j]=max(dp[i-1][j],dp[i][j-1])$，例如acf与bce的最后长子序列等于：acf与bc的最长子序列、ac与bce的最长子序列   中的最大值

```java
class Solution 
{
    public int longestCommonSubsequence(String text1, String text2) 
		{
	        int M = text1.length();
	        int N = text2.length();
	        int[][] dp = new int[M + 1][N + 1];
	        for (int i = 1; i <= M; ++i) 
					{
	            for (int j = 1; j <= N; ++j) 
					    {
	                if (text1.charAt(i - 1) == text2.charAt(j - 1)) 
	                    dp[i][j] = dp[i - 1][j - 1] + 1;
									else 
	                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
	            }
	        }
	        return dp[M][N];
		}
}
```

参考解答：

[力扣](https://leetcode.cn/problems/longest-common-subsequence/solution/fu-xue-ming-zhu-er-wei-dong-tai-gui-hua-r5ez6/)

## 14. 一维**[接雨水](https://leetcode.cn/problems/trapping-rain-water/)**

![Untitled](assets/Untitled 11.png)

思路：

![Untitled](assets/Untitled 12.png)

```java
class Solution
{
    public int trap(int[] height)
    {
        int len=height.length;
        int[] leftMax=new int[len];  //leftMax[i]代表下标i及左边的最大柱高
        int[] rightMax=new int[len];   //rightMax[i]代表下标i及右边的最大柱高
        leftMax[0]=height[0];
        rightMax[len-1]=height[len-1];

        for(int i=1;i<len;i++)
        leftMax[i]=Math.max(leftMax[i-1],height[i]);
        for(int i=len-2;i>=0;i--)
        rightMax[i]=Math.max(rightMax[i+1],height[i]);
        int res=0;

        for(int i=0;i<len;i++)
        res +=Math.min(leftMax[i],rightMax[i])-height[i];  **//下标i处能接雨水量（关键）**
        return res;
    }
}
```

## 15. **[最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)**

![Untitled](assets/Untitled 13.png)

套路：

定义$dp[i]$：以$nums[i]$结尾的最长递增子序列的长度，全局最大为dp数组的最大值

转移方程：

$dp[i]=max(dp[j])+1,其中0\le j<i且nums[j]<nums[i]$ 

```java
class Solution  //9:36-10:06
{
    public int lengthOfLIS(int[] nums)  
    {
        int[] dp=new int[nums.length]; //dp[i]为以nums[i]结尾的最长递增子序列的长度
        dp[0]=1;
        int res=dp[0];
        for(int i=1;i<nums.length;i++)
        {
            //往前搜 找到比nums[i]小的nums[j]且dp[j]最大的
             int j=i-1;
             int max=0;
            for(;j>=0;j--)
            {
                if(nums[j]<nums[i])
                {
                    max=Math.max(dp[j],max);
                }    
            }
            dp[i]=max+1;
            res=Math.max(res,dp[i]);
        }
        return res;
    }
}
```

## 16. **[子集](https://leetcode.cn/problems/subsets/)（组合类问题）**

![Untitled](assets/Untitled 14.png)

思路：$nums[0:i]$的组合A等于$nums[0:i-1]$组合B+B中的每个元素加上num[i]

以[1,2,3]为例：

```java
开始：[]    
[1]: 加上  [1]变成：  [] [1]
[1,2]: 加上[2] [1,2]  变成： [] [1] [2] [1,2]
[1,2,3]: 加上[3] [1,3] [2,3] [1,2,3]
```

```java
class Solution  //8:52-10:23
{
    public List<List<Integer>> subsets(int[] nums) 
    {
        List<List<Integer>> list=new ArrayList<>();

        if(nums.length==0)
        {
            list.add(new ArrayList<Integer>());
            return list;
        }
        else
        {
            list.add(new ArrayList<Integer>());
            for(int i=0;i<nums.length;i++)
            {
                int size=list.size();
                for(int j=0;j<size;j++)
                {
                    //特别注意下面要深拷贝，不然最后输出的所有元素都一样，第一次提交就是因为这里没过
                    List<Integer> tem=new ArrayList<Integer>(list.get(j)); 
                    tem.add(nums[i]);
                    list.add(tem);
                }
            }
            return list;
        }
    }
}
```

## 17. **[全排列](https://leetcode.cn/problems/permutations/)**

![Untitled](assets/Untitled 15.png)

这种题目最需要**注意的地方就是深浅拷贝**

```java
class Solution 
{
    public List<List<Integer>> permute(int[] nums) 
    {
        if(nums.length==1)
        {
            List<List<Integer>> list=new ArrayList<>();
            List<Integer> tem=new ArrayList<>();
            tem.add(nums[0]);
            list.add(tem);
            return list;
        }
        else
        {
            int[] sub=new int[nums.length-1];
            System.arraycopy(nums,0,sub,0,sub.length);
            List<List<Integer>> tem=permute(sub);
            List<List<Integer>> res=new ArrayList<>();
            for(int i=0;i<tem.size();i++)
            {
                List<Integer> t=new ArrayList<>(tem.get(i));
                for(int j=0;j<=t.size();j++)
                {
                    t.add(j,nums[nums.length-1]);
                    res.add(new ArrayList<>(t));  **//特别注意这里的深拷贝**
                    t.remove(j);
                }
            }
           
            return res;
        }
    }
}
```

## 18. **[买卖股票的最佳时机 III](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/)**

![Untitled](assets/Untitled 16.png)

![Untitled](assets/Untitled 17.png)

暴力解法：

```java
class Solution 
{
    public int maxProfit(int[] prices) 
    {
        if(prices.length==1)
        return 0;
        else
        {
            int res=0;
            for(int i=0;i<prices.length-1;i++)  //以prices[i]为界劈开
            {
                int[] a=new int[i+1];
                int[] b=new int[prices.length-i];
                System.arraycopy(prices,0,a,0,a.length);
                System.arraycopy(prices,i,b,0,b.length);
							  //下面不改进，只将max_sigle改为dp，时间复杂度改善很小
                int reward=max_sigle(a)+max_sigle(b); 
                res=Math.max(res,reward);
            }
            return  res;
        }
    }

     public static int max_sigle(int[] prices)
    {
        if(prices.length<=1)
        return 0;
        else
        {
            int res=0;
            for(int i=0;i<prices.length-1;i++)
            {
                for(int j=i+1;j<prices.length;j++)
                {
                    int tem=prices[j]-prices[i];
                    if(tem>0)
                    res=Math.max(res,tem);
                }
            }
            return  res;
        }
    }
}
```

![Untitled](assets/Untitled 18.png)

**双向dp**

记$len=prices.length$,遍历数组$prices$, 以$price[i]$为界劈成2个数组：$prices[0:i]$和$prices[i:len-1]$。
从左往右$dp$得到数组$left$,其中$left[i]$为$prices[0:i]$一次交易的最大值，
从右往左$dp$得到数组$right$,其中$right[i]$为$prices[i:len-1]$一次交易的最大值。
$left[i]+right[i]$就是以$prices[i]$劈开的最大收益。

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n=prices.length;
        int[] left=new int[n]; // 从左往右dp, left[i]表示prices[0:i]一笔交易最大利润  
        int minValue=prices[0];
        left[0]=0;
        for(int i=1;i<n;i++){
            if(prices[i]-minValue>0){
                left[i]=Math.max(prices[i]-minValue,left[i-1]);
            }else{
                left[i]=left[i-1];
            }
            minValue=Math.min(minValue,prices[i]);
        }

        int[] right=new int[n]; // 从右往左dp，right[i]表示prices[i:n-1]一笔交易最大利润
        int maxValue=prices[n-1];
        right[n-1]=0;
        for(int i=n-2;i>=0;i--){
            if(maxValue-prices[i]>0){
                right[i]=Math.max(maxValue-prices[i],right[i+1]);
            }else{
                right[i]=right[i+1];
            }
            maxValue=Math.max(maxValue,prices[i]);
        }

        int res=0;
        for(int i=0;i<n;i++){  // 遍历，以price[i]为界分成两个数组：prices[0:i]和prices[i:len-1]
            System.out.println(left[i]+" "+right[i]);
            res=Math.max(res,left[i]+right[i]);
        }
        return res;
    }
}
```

![Untitled](assets/Untitled 19.png)

## 19. **跳跃游戏 VI  （DP+滑动窗口）**

![Untitled](assets/Untitled 20.png)

![Untitled](assets/Untitled 21.png)

**该题主要考察优先队列求滑动窗口最大值（不用滑动窗口会超时），dp非常好想。**

```java
class Solution 
{
    public int maxResult(int[] nums, int k) 
    {
       int n=nums.length; 
       int[] dp=new int[n];  //dp[i]：从0->i的最大和
       dp[0]=nums[0];

       //用优先队列：存dp元素和下标。求滑动窗口dp[i-k:i-1]的最大值
       PriorityQueue<int[]> queue=new PriorityQueue<>(
           new Comparator<int[]>()
           {
               //自定义比较器：优先队列元素的优先级
               public int compare(int[] o1,int[] o2)
               {
                   return o2[0]-o1[0];
               }
           }
       );

       queue.add(new int[]{dp[0],0});
       int max=0;  //dp[i-k:i-1]的最大值
       for(int i=1;i<n;i++)
       {   
            if(queue.size()<=k)
            max=queue.peek()[0];
            else
            {
                //判断最大值下标是否落在滑动窗口内 【i-k,i-1】
                while(queue.peek()[1]<i-k)
                {
                    queue.remove();
                }
                max=queue.peek()[0];
            }

            dp[i]=max+nums[i];
            queue.add(new int[]{dp[i],i});
       }
       return dp[n-1];
    }   
}
```

## 20. 预测赢家

[2、预测赢家（486）](%E7%AE%97%E6%B3%95%204c486dd119b24f67aff1b9e9dcdd1813.md) 

## 21. **最长等差数列**

![Untitled](assets/Untitled 22.png)

![Untitled](assets/Untitled 23.png)

![Untitled](assets/Untitled 24.png)

```java
class Solution 
{
    public int longestArithSeqLength(int[] nums) 
    {
        int n=nums.length;   
        int[][] dp=new int[n][1001]; //dp[i][j]表示以nums[i]结尾且公差为d的最长等差子序列长度,公差由[-500,500]变换到[0,1000]
        int res=0;
        for(int i=1;i<n;i++) 
        {
            for(int j=0;j<i;j++)
            {
                int d=nums[i]-nums[j]+500;  //公差为d
                dp[i][d]=dp[j][d]+1; //原来[...,nums[j]],现在将nums[i]加入其中                
                res=Math.max(res,dp[i][d]);
            }
        }
        return res+1;
    }
}
```

暴力也能过

```java
class Solution 
{
    HashSet<Integer> set=new HashSet<>();//存已经遍历过的公差
    int res=0;
    int len=0;
    public int longestArithSeqLength(int[] nums) 
    {
        int n=nums.length;
        for(int i=0;i<n-1;i++)
        {
            for(int j=i+1;j<n;j++)
            {
                int d=nums[j]-nums[i];
                //if(!set.contains(d))
                {
                    set.add(d);
                    len=2;
                    dfs(i,j,d,nums);
                }
            }
        }
        return res;
    }
    public void dfs(int last,int i,int d,int[] nums) // 上一下标 当前下标 公差
    {
        int n=nums.length;
        int index=i+1;
        while(index<n)
        {
            if(nums[index]-nums[i]==d)
            break;
            index++;
        }
        if(index<n)
        {
            len++;
            dfs(i,index,d,nums);
        }
        else
        {
            res=Math.max(res,len);
            return;
        }
    }
}
```

## 22.  最大矩形

[https://leetcode.cn/problems/maximal-rectangle/description/](https://leetcode.cn/problems/maximal-rectangle/description/)

给定一个仅包含 `0` 和 `1` 、大小为 `rows x cols` 的二维二进制矩阵，找出只包含 `1` 的最大矩形，并返回其面积。

**示例 1：**

![https://assets.leetcode.com/uploads/2020/09/14/maximal.jpg](https://assets.leetcode.com/uploads/2020/09/14/maximal.jpg)

```
输入：matrix = [["1","0","1","0","0"],["1","0","1","1","1"],["1","1","1","1","1"],["1","0","0","1","0"]]
输出：6
解释：最大矩形如上图所示。

```

**示例 2：**

```
输入：matrix = [["0"]]
输出：0

```

**示例 3：**

```
输入：matrix = [["1"]]
输出：1

```

**提示：**

- `rows == matrix.length`
- `cols == matrix[0].length`
- `1 <= row, cols <= 200`
- `matrix[i][j]` 为 `'0'` 或 `'1'`

思路：暴力

用int[][] width=new int[m][n]维护, 其中width[i][j]表示 第i行，以下标[i,j]为终点的最大连续1的个数。示例1中：

```java
width[1][0]=1
width[1][1]=0
width[2][2]=1
width[2][3]=2
width[2][4]=3
```

以图中黄色1为右下角坐标的矩形（全1），向上拓展：

高度为 1: 面积S1=4*1=width[2][3]

![Untitled](assets/Untitled 25.png)

高度为2：面积S2=2*2=min(width[2][3], width[1][3]) *2

![Untitled](assets/Untitled 26.png)

高度为3：面积S3=2*3=min(width[2][3], width[1][3], width[0][3]) *3

![Untitled](assets/Untitled 27.png)

综上：以黄色1为右下角坐标的矩形（全1）最大面积为max(S1, S2, S3)=6

遍历所有全1 节点，以其为矩形右下角坐标，找出全局最大面积即可：

```java
class Solution {
    public int maximalRectangle(char[][] matrix) {
        int m=matrix.length,n=matrix[0].length;
        int[][] width=new int[m][n];

        int maxArea=0;
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(matrix[i][j]=='1'){
                    if(j==0){
                        width[i][0]=1;
                    }else{
                        width[i][j]=width[i][j-1]+1;
                    }
                }else{
                    width[i][j]=0;
                }

                maxArea=Math.max(maxArea,width[i][j]); // 矩形面积
                int minWidth =width[i][j]; // 矩形宽
                for(int up=i-1;up>=0;up--){  // 向上扩展
                    int height=i-up+1;  // 矩形高
                    minWidth =Math.min(width[up][j],minWidth);
                    maxArea=Math.max(maxArea,height*minWidth );
                }
            }
        }
        return maxArea;
    }
}
```

## 23. [分割数组的最大值](https://leetcode.cn/problems/split-array-largest-sum/)

给定一个非负整数数组 `nums` 和一个整数 `k` ，你需要将这个数组分成 `k` 个非空的连续子数组。

设计一个算法使得这 `k` 个子数组各自和的最大值最小。

**示例 1：**

```
输入：nums = [7,2,5,10,8], k = 2
输出：18
解释：
一共有四种方法将 nums 分割为 2 个子数组。 
其中最好的方式是将其分为 [7,2,5] 和 [10,8] 。
因为此时这两个子数组各自的和的最大值为18，在所有情况中最小。
```

**示例 2：**

```
输入：nums = [1,2,3,4,5], k = 2
输出：9
```

**示例 3：**

```
输入：nums = [1,4,4], k = 3
输出：4
```

 

**提示：**

- `1 <= nums.length <= 1000`
- `0 <= nums[i] <= 106`
- `1 <= k <= min(50, nums.length)`



参考题解：https://leetcode.cn/problems/split-array-largest-sum/solutions/242909/er-fen-cha-zhao-by-liweiwei1419-4/

![image-20240516233858446](assets/image-20240516233858446.png)

第 1 步：定义状态$$dp[i][k]$$表示：将前缀区间 $$[0, i] $$被分成 $$k $$段的各自和的最大值的最小值，则前缀区间$$ [0, j]$$ （这里$$ j < i$$） 被分成$$ k - 1$$ 段各自和的最大值的最小值为 $$dp[j][k - 1]$$。即：第一维是第$$ k$$ 个分割的最后一个元素的下标 ，第二维是分割的段数。

第 2 步：状态转移方程
$$dp[i][k]=max⁡(dp[j][k−1],  rangeSum(j+1,i))$$
这里 $$rangeSum(j+1,i)$$表示数组 $$nums[j + 1..i] $$的区间和，它可以先计算出所有前缀和，然后以 $$O(1)$$的方式计算出区间和。

上面的状态转移方程中，$$j$$ 是第 $$k - 1$$ 个段最后一个元素的下标，因此取值范围为$$[k - 2, i-1]$$

第 3 步：初始化

- 由于要找最小值，初值赋值为Integer.MAX_VALUE；
- 分割段数为 1 ，即不分割的情况，所有的前缀和就是依次的状态值。

第 4 步：输出

$$dp[len][k]$$，根据状态定义，这是显然的。

下面给出题目中的示例$$ [7, 2, 5, 10, 8]$$ 的状态计算表，为了更突出一般性，把 $$m$$ 设置成为数组的长度 $$5$$：

![image-20240516235105836](assets/image-20240516235105836.png)

编码：

根据以上分析，三层循环：

- 阶段：分割段数从 $$1$$增长到$$m$$ 

- 状态：前缀区间 $$[0, i]$$ 的状态值，由于 $$i$$ 要被分成 $$k$$ 段，前缀区间里至少要有 $$k $$个元素，最小前缀区间 $$k$$ 个元素的最后一个元素的下标为 $$k - 1$$，故$$ i$$ 从$$ k - 1$$ 开始到 $$len - 1$$；

- 选择：枚举第$$ k - 1 $$段的最后一个元素的下标，根据上面的分析，从$$ k - 2$$ 到 $$i - 1$$。

  

```java
class Solution {
    public int splitArray(int[] nums, int k) {
        int n=nums.length;
        int[][] dp=new int[n][k+1]; // dp[i][k] 表示将下标i（含）之前的数组分成k段，各自和最大值的最小值
        for(int[] a:dp){
            Arrays.fill(a,Integer.MAX_VALUE);
        }
        
        int[] preSum=new int[n];
        preSum[0]=nums[0];
        for(int i=1;i<n;i++){
            preSum[i]=preSum[i-1]+nums[i];
        }

        // 分成1段
        for(int i=0;i<n;i++){
            dp[i][1]=preSum[i];
        }

        for(int m=2;m<=k;m++){  // 从分成2段开始，逐渐递增到k段
            for(int i=m-1;i<n;i++){ // 最后一段的终点下标
                for(int j=m-2;j<i;j++){  // 第m-1段的终点下标
                    dp[i][m]=Math.min(dp[i][m],Math.max(dp[j][m-1],preSum[i]-preSum[j]));
                }  
            }
        }
        return dp[n-1][k];
    }
}
```





## 24. [编辑距离](https://leetcode.cn/problems/edit-distance/)

给你两个单词 `word1` 和 `word2`， *请返回将 `word1` 转换成 `word2` 所使用的最少操作数* 。

你可以对一个单词进行如下三种操作：

- 插入一个字符
- 删除一个字符
- 替换一个字符

 

**示例 1：**

```
输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```

**示例 2：**

```
输入：word1 = "intention", word2 = "execution"
输出：5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```

 

**提示：**

- `0 <= word1.length, word2.length <= 500`
- `word1` 和 `word2` 由小写英文字母组成

思路:

操作等价：

- A插入一个字符和B删除一个字符等价
- A删除一个字符和B插入一个字符等价
- A修改一个字符和B修改一个字符等价

因此可以简化为如下3种操作：

- A插入一个字符
- B插入一个字符
- A修改一个字符

以A=horse  B=ros为例：

- A插入一个字符：假设horse到ro的编辑距离是a，那么horse到ros的编辑距离最大为a+1
- B中插入一个字符：假设hors到ros的编辑距离是b，那么horse到ros的编辑距离最大为b+1
- A中修改一个字符：假设hors到ro的编辑距离是c，那么horse到ros的编辑距离最大为c+1

详细证明：https://dl.acm.org/doi/pdf/10.1145/321796.321811

设$dp[i][j]$为： $word1[0:i]$到$word2[0:j]$的编辑距离，那么有递推公式：

- $word1.charAt(i)==word2.charAt(j)$:    $dp[i][j]=min(dp[i][j-1]+1,dp[i-1][j]+1,dp[i-1][j-1])$
- $word1.charAt(i)!=word2.charAt(j)$:    $dp[i][j]=min(dp[i][j-1]+1,dp[i-1][j]+1,dp[i-1][j-1]+1)$



```java
class Solution {
    public int minDistance(String word1, String word2) {
        int m=word1.length(),n=word2.length();
        if(m==0||n==0){
            return m==0?n:m;
        }
        int[][] dp=new int[m][n];  //dp[i][j]: word1[0:i]到word2[0:j]的最小编辑距离
        // 填充边界
        for(int i=0;i<Math.max(m,n);i++){ 
            if(i<m){
                dp[i][0]=word1.substring(0,i+1).indexOf(word2.charAt(0))!=-1?i:i+1;
            }
            if(i<n){
                dp[0][i]=word2.substring(0,i+1).indexOf(word1.charAt(0))!=-1?i:i+1;   
            } 
        }
        for(int i=1;i<m;i++){
            for(int j=1;j<n;j++){
                dp[i][j]=word1.charAt(i)==word2.charAt(j)?Math.min(Math.min(dp[i][j-1]+1,dp[i-1][j]+1),dp[i-1][j-1]):Math.min(Math.min(dp[i][j-1]+1,dp[i-1][j]+1),dp[i-1][j-1]+1);    
            }
        }
        return dp[m-1][n-1];
    }
}
```

## 25. [分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)

给你一个 **只包含正整数** 的 **非空** 数组 `nums` 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

 

**示例 1：**

```
输入：nums = [1,5,11,5]
输出：true
解释：数组可以分割成 [1, 5, 5] 和 [11] 。
```

**示例 2：**

```
输入：nums = [1,2,3,5]
输出：false
解释：数组不能分割成两个元素和相等的子集。
```

 

**提示：**

- `1 <= nums.length <= 200`
- `1 <= nums[i] <= 100`



关键：将该问题转化为 ”**<font color=red>是否存在和为数组元素综合一半的组合</font>**“  ，设nums元素总和为sum，则寻找有无方案可使和为target=sum/2。

剪枝：排除下列情况直接返回false

- sum为奇数
- 数组长度小于2
- 数组中最大元素大于target



接下来使用动态规划求数组nums中是否存在组合使得和为target。

定义状态数组 $dp[n][target+1]$， 其中$dp[i][j]$表示”**从下标$0~i$ 是否存在组合使得和为$j$**“

转移方程如下：

- 若$nums[i]>j$: 必不可能选下标$nums[i]$，$dp[i][j]=dp[i-1][j]$
- 若$num[i]\leq j$： $nums[i]$可选可不选，任一满足即可，$dp[i][j]=dp[j-1][j-nums[i]]$  |  $dp[i-1][j]$



初始化：

- 所有都置$false$
- 和为0都满足，$dp[i][0]=true$
- 若$nums[0]<=target$，有$dp[0][nums[0]]=true$

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int n=nums.length;
        int sum=0,maxNum=0;
        for(int i=0;i<n;i++){
            sum +=nums[i];
            maxNum=Math.max(maxNum,nums[i]);
        }

        int target=sum/2;  // 找出是否可由nums中的元素组成目标和target
        if(sum%2!=0||n<2||maxNum>target){ // 总和为奇数 或数组长度小于2 或数组元素最大值大于target 必不可能
            return false;
        }
       
        // 默认false
        boolean[][] dp=new boolean[n][target+1]; // dp[i][j]: 从下标0~i 是否存在组合使得和为j
        for(int i=0;i<n;i++){
            dp[i][0]=true;
        }
        if(nums[0]<=target){
            dp[0][nums[0]]=true;
        }

        for(int i=1;i<n;i++){
            for(int j=1;j<=target;j++){
                if(nums[i]>j){ // 不选nums[i]
                    dp[i][j]=dp[i-1][j];
                }else { //  nums[i]可选可不选
                    dp[i][j]=dp[i-1][j]|dp[i-1][j-nums[i]];
                }
            }
        }
        return dp[n-1][target];
    }
}
```

## 26.  [最长有效括号](https://leetcode.cn/problems/longest-valid-parentheses/)

给你一个只包含 `'('` 和 `')'` 的字符串，找出最长有效（格式正确且连续）括号

子串

的长度。



**示例 1：**

```
输入：s = "(()"
输出：2
解释：最长有效括号子串是 "()"
```

**示例 2：**

```
输入：s = ")()())"
输出：4
解释：最长有效括号子串是 "()()"
```

**示例 3：**

```
输入：s = ""
输出：0
```

 

**提示：**

- `0 <= s.length <= 3 * 104`
- `s[i]` 为 `'('` 或 `')'`



思路：常规判断有效括号（遍历）的方法也能过，但时间复杂度太高且需要注意细节，下面用动态规划，下图为两种方法的耗时对比：

<img src="assets/image-20240625162932422.png" alt="image-20240625162932422" style="zoom:50%;" />



$dp[i]$ ：表示以下标 $i$字符结尾的最长有效括号的长度

当s.charAt(i)='('时，$dp[i]=0$，合法括号只需考虑s.charAt(i)==')'的情况：

- s.charAt(i-1)=='('$时，$$dp[i]=dp[i-2]+2$
- s.charAt(i-1)==')'时，以$s.charAt(i-1)$结尾的合法括号开始下标为$i-dp[i-1]$，若其前一个字符$s.charAt(i-dp[i-1]-1)$为')'，则$dp[i]=0$；否则$s.charAt(i)$和$s.charAt(i-dp[i-1]-1)$可以组一对括号，同时拼接上以下标$i-dp[i-1]-2$结尾的有效括号，因此$dp[i]=dp[i-1]+2+dp[i-dp[i-1]-2]$



```java
class Solution {
    public int longestValidParentheses(String s) {
        int n=s.length();
        if(n<2){
            return 0;
        }
        int[] dp=new int[n]; // dp[i]: 以下标i结尾的最长有效括号字串长度
        int maxLength=0; // 总体最长有效括号
        for(int i=1;i<n;i++){
            if(s.charAt(i)==')'){
                if(s.charAt(i-1)=='('){
                    dp[i]=(i-2>=0?dp[i-2]:0)+2;
                }else if(i-dp[i-1]-1>=0&&s.charAt(i-dp[i-1]-1)=='('){
                    dp[i]=dp[i-1]+2+(i-dp[i-1]-2>=0?dp[i-dp[i-1]-2]:0);
                }
            }
            maxLength=Math.max(maxLength,dp[i]);
        }
        return maxLength; 
    }
}
```



## 27.[给墙壁刷油漆](https://leetcode.cn/problems/painting-the-walls/)

给你两个长度为 `n` 下标从 **0** 开始的整数数组 `cost` 和 `time` ，分别表示给 `n` 堵不同的墙刷油漆需要的开销和时间。你有两名油漆匠：

- 一位需要 **付费** 的油漆匠，刷第 `i` 堵墙需要花费 `time[i]` 单位的时间，开销为 `cost[i]` 单位的钱。
- 一位 **免费** 的油漆匠，刷 **任意** 一堵墙的时间为 `1` 单位，开销为 `0` 。但是必须在付费油漆匠 **工作** 时，免费油漆匠才会工作。

请你返回刷完 `n` 堵墙最少开销为多少。

 

**示例 1：**

```
输入：cost = [1,2,3,2], time = [1,2,3,2]
输出：3
解释：下标为 0 和 1 的墙由付费油漆匠来刷，需要 3 单位时间。同时，免费油漆匠刷下标为 2 和 3 的墙，需要 2 单位时间，开销为 0 。总开销为 1 + 2 = 3 。
```

**示例 2：**

```
输入：cost = [2,3,4,2], time = [1,1,1,1]
输出：4
解释：下标为 0 和 3 的墙由付费油漆匠来刷，需要 2 单位时间。同时，免费油漆匠刷下标为 1 和 2 的墙，需要 2 单位时间，开销为 0 。总开销为 2 + 2 = 4 。
```

 

**提示：**

- `1 <= cost.length <= 500`
- `cost.length == time.length`
- `1 <= cost[i] <= 106`
- `1 <= time[i] <= 500`



# 二. 深度优先搜索(DFS, Depth First Search)

**总结：dfs就是不断往下搜，搜到底（设置终止条件）。且不能重复访问（已经访问过的点，return)。适合解决存不存在、给定和种类、路径之类的题目**

恢复现场与否：再体会

恢复：

不恢复：题3 机器人

## 1. 矩阵中的路径

[力扣](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)

![Untitled](assets/Untitled 28.png)

![Untitled](assets/Untitled 29.png)

```java
class Solution //23:01-23:19
{
    boolean is_exist=false;
    public boolean exist(char[][] board, String word) 
    {
        int m=board.length,n=board[0].length;
        for(int i=0;i<m;i++)
        {
            for(int j=0;j<n;j++)
            {
                if(word.charAt(0)==board[i][j])
                {
                    int[][] visited=new int[m][n];
                    dfs(i,j,board,visited,word);
                    if(is_exist)
                    return true;
                }
            }
        }
        return is_exist;
    }

    public void dfs(int i,int j,char[][] board,int[][] visited,String word)
    {
        int m=board.length,n=board[0].length;
        if(i<0||i>=m||j<0||j>=n)  //越界
        return;
        if(visited[i][j]==1) //已访问
        return; 
        if(word.length()==1&&word.charAt(0)==board[i][j]) //匹配
        {
            is_exist=true;
            return;
        }
        if(word.charAt(0)!=board[i][j]) //第一个字母就不匹配
        return;
        visited[i][j]=1;
        dfs(i-1,j,board,visited,word.substring(1));
        dfs(i+1,j,board,visited,word.substring(1));
        dfs(i,j-1,board,visited,word.substring(1));
        dfs(i,j+1,board,visited,word.substring(1));
        visited[i][j]=0;
    }
}
```

上面的代码有些测试用例已超时，优化如下：**探索4个方向时任意方向找到了都返回**

```jsx
class Solution {
    boolean res=false;
    public boolean wordPuzzle(char[][] grid, String target) {
        int m=grid.length,n=grid[0].length;
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(target.charAt(0)==grid[i][j]){
                    int[][] visited=new int[m][n];
                    dfs(grid,i,j,visited,target);
                    if(res){
                        return true;
                    }
                }
            }
        }
        return res;
    }

    public void dfs(char[][] grid, int i,int j,int[][] visited,String target){
        int m=grid.length,n=grid[0].length;
        if(target.equals("")){
            res=true;
            return;
        }
        if(i<0||i>m-1||j<0||j>n-1||visited[i][j]==1){
            return;
        }

        visited[i][j]=1;
        int[][] dir={{0,-1},{0,1},{-1,0},{1,0}};
        if(target.charAt(0)==grid[i][j]){
            for(int[] direction:dir){
                dfs(grid,i+direction[0],j+direction[1],visited,target.substring(1));
                if(res){
                    return;  // 找到了就即刻返回，不用等探索完4个方向，不然最后2个测试用例超时
                }
            }
        }
        visited[i][j]=0;
    }
}
```

## 2. 新员工考试（华为机试)

![Untitled](assets/Untitled 30.png)

![Untitled](assets/Untitled 31.png)

```java
/**
 * @Author qiang.long
 * @Date 2024/03/29
 * @Description 新员工考试  10:27-10:48
 * 共10+10+5=25题
 * 10*2
 * 10*4
 * 5*8
 *
 **/
public class Main {
    static int res=0;
    public static void main(String[] args) {

        int[] score=new int[25];
        for(int i=0;i<10;i++){
            score[i]=2;
        }
        for(int i=10;i<20;i++){
            score[i]=4;
        }
        for(int i=20;i<25;i++){
            score[i]=8;
        }

        for(int N=0;N<=100;N++){
            dfs(0,0,score,N,0);
            System.out.println("solution-"+N+":"+res);
            res=0;
        }
    }
    /**
     * @description:
     * @param:
     * @param i 第i道题
     * @param scoreSum 总分
     * @param score 单分
     * @param target 目标分数
     * @param wrong 错题数
     * @return: void
     **/
    public static void dfs(int i,int scoreSum,int[] score,int target,int wrong){
        if(wrong>=3||i>24){
            if(target==scoreSum){
                res++;
            }
            return;
        }
        dfs(i+1,scoreSum+score[i],score,target,wrong);
        dfs(i+1,scoreSum,score,target,wrong+1);
    }
    
}
```

## 3. 机器人的运动范围

![Untitled](assets/Untitled 32.png)

![Untitled](assets/Untitled 33.png)

**不用恢复现场**

## 4. 课程表

![Untitled](assets/Untitled 34.png)

感觉下面写的有问题，能过完全是运气 （每换一个起点搜，set要清零，不知道当初怎么想的）

```java
class Solution 
{

    boolean is=false;  
    HashSet<Integer> set=new HashSet<>(); //存修过的课程编号
    public boolean canFinish(int numCourses, int[][] prerequisites) 
    {
        if(prerequisites.length==0)  //没有限制
        return true;
        else
        {
            for(int i=0;i<numCourses;i++)   //每个初始位置都尝试  只要存在就行
            {
                dfs(numCourses,i,prerequisites,set);
            }
            return is;
        }
    }

    public void dfs(int numCourses,int i,int[][] prerequisites,HashSet<Integer> set) //第i门课能不能修
    {
        if(set.size()==numCourses)  //找到
        {
            is=true;   
            return;
        }
        if(set.contains(i))   //修过的课程不再访问
        return;
        for(int m=0;m<prerequisites.length;m++)  //遍历每一条规则
        {
            if(prerequisites[m][0]==i)  //找到课程i相关的规则
            {
                if(!set.contains(prerequisites[m][1])) //没修前置课程
                return;
            }
        }
        //可以修课程i
        set.add(i);
        
        for(int j=0;j<numCourses;j++)   //继续修下一门课
        {
            if(j!=i)
            dfs(numCourses,j,prerequisites,set);
        }
    }
}
```

拓扑排序解法:

```java
class Solution 
{
    public boolean canFinish(int numCourses, int[][] prerequisites) 
    {

        int[] in_degree=new int[numCourses]; //入度数组
        List<List<Integer>> list=new ArrayList<>();  //节点指向的节点序列
        for(int i=0;i<numCourses;i++)
        list.add(new ArrayList<>());

        for(int[] tem:prerequisites)
        {
            list.get(tem[1]).add(tem[0]);
            in_degree[tem[0]]++;
        }
        LinkedList<Integer> link=new LinkedList<>();
        for(int i=0;i<numCourses;i++)
        {
            if(in_degree[i]==0)
            link.add(i);
        }

        HashSet<Integer> set=new HashSet<>();

        while(!link.isEmpty())
        {
            Integer tem=link.remove();
            set.add(tem);

            List<Integer> ss=list.get(tem);

            for(Integer o:ss)
            {
                in_degree[o]--;
                if(in_degree[o]==0)
                link.add(o);
            }
        }
        return set.size()==numCourses;
    }
}
```

## 5. **[岛屿数量](https://leetcode.cn/problems/number-of-islands/)**

![Untitled](assets/Untitled 35.png)

```java
class Solution {
    public int numIslands(char[][] grid) {
        int m=grid.length,n=grid[0].length;
        int count=0;
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(grid[i][j]=='1'){
                    dfs(grid,i,j);
                    count++;
                }
            }
        }
        return count;
    }
    public void dfs(char[][] grid,int i,int j){ // i,j 当前坐标
        int m=grid.length,n=grid[0].length;
        if(!illegal(i,j,m,n)||grid[i][j]=='a'||grid[i][j]=='0'){ // 边界条件返回
            return;
        }
        grid[i][j]='a'; // 打标记：已访问
        dfs(grid,i-1,j);
        dfs(grid,i+1,j);
        dfs(grid,i,j-1);
        dfs(grid,i,j+1);
    }
    // 坐标是否合法
    public boolean illegal(int i,int j,int m,int n){
        return i>=0&&i<m&&j>=0&&j<n;
    }
}
```

并查集 

```jsx
//并查集  超时49/49  不能用quick find  要用路径压缩和加权
class Solution //9:21-
{
    public int numIslands(char[][] grid) 
    {
        int m=grid.length,n=grid[0].length;
        UF uf=new UF(m*n);
        for(int i=0;i<m-1;i++) //向右和向下union 免去重复和界限判断
        {
            for(int j=0;j<n-1;j++)
            {
                if(grid[i][j]=='1')
                {
                    if(grid[i][j+1]=='1')
                    uf.union(i*n+j,i*n+j+1);
                    if(grid[i+1][j]=='1')
                    uf.union(i*n+j,(i+1)*n+j);
                }
                else
                uf.set_id[i*n+j]=-1;
            }
        }
        //最右边一列往下union
        for(int i=0;i<m-1;i++)
        {
            if(grid[i][n-1]=='1'&&grid[i+1][n-1]=='1')
            uf.union(i*n+n-1,(i+1)*n+n-1);
            if(grid[i][n-1]=='0')
            uf.set_id[i*n+n-1]=-1;
        }
        //最下边一列往右union
        for(int j=0;j<n-1;j++)
        {
            if(grid[m-1][j]=='1'&&grid[m-1][j+1]=='1')
            uf.union((m-1)*n+j,(m-1)*n+j+1);
            if(grid[m-1][j]=='0')
            uf.set_id[(m-1)*n+j]=-1;
        }
        if(grid[m-1][n-1]=='0')
        uf.set_id[m*n-1]=-1;

        HashSet<Integer> set=new HashSet<>();
        for(int i=0;i<uf.set_id.length;i++)
            set.add(uf.set_id[i]);
        return set.contains(-1)?set.size()-1:set.size();
        
    }
    public boolean axis_legal(int i,int j,int m,int n)
    {
        return i>=0&&i<m&&j>=0&&j<n;
    }
    
}

class UF
{
    int[] set_id;
    public UF(int n)
    {
        set_id=new int[n]; 
        for(int i=0;i<n;i++)
        set_id[i]=i;
    }

    public int find(int i) //节点i对应的连通分量
    {
        return set_id[i];
    }

    public boolean is_connected(int i,int j) //是否联通
    {
        return find(i)==find(j);
    }
    public void union(int i,int j) //连接节点i和j
    {
        int id1=find(i);
        int id2=find(j);
        if(id1==id2)
        return;
        for(int k=0;k<set_id.length;k++)
        {
            if(set_id[k]==id2)
            set_id[k]=id1;
        }
    }
}
```

bfs

```jsx
//bfs  超时39/49  有时间再优化
class Solution //9:02-9:18
{
    
    public int numIslands(char[][] grid) 
    {
        int res=0;
        int m=grid.length,n=grid[0].length;

        int[][] visited=new int[m][n];
        for(int i=0;i<m;i++)
        {
            for(int j=0;j<n;j++)
            {
                if(grid[i][j]=='1'&&visited[i][j]==0)
                {
                    LinkedList<int[]> list=new LinkedList<>();
                    list.add(new int[]{i,j});
                    while(!list.isEmpty())
                    {
                        int[] tem=list.remove();
                        int k=tem[0],l=tem[1];
                        visited[k][l]=1;
                        if(axis_legal(k-1,l,m,n)&&visited[k-1][l]==0&&grid[k-1][l]=='1')
                        list.add(new int[]{k-1,l});
                        if(axis_legal(k+1,l,m,n)&&visited[k+1][l]==0&&grid[k+1][l]=='1')
                        list.add(new int[]{k+1,l});
                        if(axis_legal(k,l-1,m,n)&&visited[k][l-1]==0&&grid[k][l-1]=='1')
                        list.add(new int[]{k,l-1});
                        if(axis_legal(k,l+1,m,n)&&visited[k][l+1]==0&&grid[k][l+1]=='1')
                        list.add(new int[]{k,l+1});
                    }
                    res++;
                }
            }
        }

        return res;  
    }

    public boolean axis_legal(int i,int j,int m,int n)
    {
        return i>=0&&i<m&&j>=0&&j<n;
    }
    
}
```

## 6. 二叉树中和为某一值的路径

[力扣](https://leetcode-cn.com/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)

![Untitled](assets/Untitled 36.png)

```java
class Solution {
    LinkedList<List<Integer>> sum=new LinkedList<List<Integer>>(); //总路径
    LinkedList<Integer> path=new LinkedList<Integer>(); //单条路径
    public List<List<Integer>> pathSum(TreeNode root, int target) 
    {
        dfs(root,target);
        return sum;
    }
    public void dfs(TreeNode root,int target)  //传入一颗子树和目标和值  
    {
        if(root==null)  //递归终止条件
        return;
        path.offer(root.val);  //将root节点值加入路径
        target -=root.val;  //总和减去已经遍历的节点值
        if(root.left==null&&root.right==null&&target==0) 
        sum.offer(new LinkedList<Integer>(path));  
      //**注意体会深拷贝 ，防止后面修改path把sum里的元素修改** 以及此处不要return 
        
        dfs(root.left,target); //递归调用  判断左子树
        dfs(root.right,target); //递归调用 判断右子树
        path.pollLast();   //**恢复现场 不满足弹出前面加的**  
    }
}
```

## 7. **[剑指 Offer 68 - II. 二叉树的最近公共祖先](https://leetcode.cn/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/)**

![Untitled](assets/Untitled 37.png)

dfs:

```java
class Solution  ///10:01-
{
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) 
    {
        if(root==null||root==p||root==q)
        return root;  //终止条件
        TreeNode left=lowestCommonAncestor(root.left,p,q); //往左搜
        TreeNode right=lowestCommonAncestor(root.right,p,q); //往右搜
        if(left==null)  //不在左子树
        return right;
        if(right==null) //不在右子树
        return left;

        return root;  //返回本身
        
    }
}
```

用map好理解一点的：

```jsx
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
 //22:32-22:55
class Solution 
{
    HashMap<TreeNode,TreeNode> map=new HashMap<>();//存节点对应的父节点
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) 
    {
        if(root==null)
        return null;
        if(p==null)
        return q;
        if(q==null)
        return p;

        pre_order(root);  //前序遍历构建map存父节点

        ArrayList<TreeNode> list1=new ArrayList<>(); //存从p到root节点的路径
        list1.add(p);
        TreeNode work=p;
        while(work!=root)
        {
            list1.add(map.get(work));
            work=map.get(work);  //往父节点方向
        }
        
        //从p往上到root节点  若经过的节点在list1中 存在  则为最近公共祖先
        work=q;  
        while(work!=root)
        {
            if(list1.contains(work))
            return work;
            work=map.get(work);
        }
        return root;
    }

    public void pre_order(TreeNode root)
    {
        if(root==null)
        return;

        if(root.left!=null)
        map.put(root.left,root);
        if(root.right!=null)
        map.put(root.right,root);
        pre_order(root.left);
        pre_order(root.right);
    }
}
```

## 8.  **[省份数量](https://leetcode.cn/problems/number-of-provinces/)**

![Untitled](assets/Untitled 38.png)

（1）法一：DFS

不一定有显示的边界条件（碰到边界return），也可以是搜到尽头自动停止

```java
class Solution //22:44-22:57  dfs
{
    public int findCircleNum(int[][] isConnected) 
    {
        int n=isConnected.length;
        int[] visited=new int[n];
        int res=0;
        for(int i=0;i<n;i++)
        {
            if(visited[i]==0)
            {
                dfs(i,isConnected,visited);
                res++;
            }
        }
        return res;
    }

    public void dfs(int i,int[][] isConnected,int[] visited)
    {
        int n=isConnected.length;
        if(visited[i]==1)
        return;
        visited[i]=1;
        for(int j=0;j<n;j++)
        {
            if(isConnected[i][j]==1&&i!=j)
            dfs(j,isConnected,visited);
        }
    }
}
```

（2）法二：BFS

```java
class Solution  //9:46-9:53
{
    public int findCircleNum(int[][] isConnected) 
    {
        int col=isConnected.length,row=isConnected[0].length;
        int[] isvisited=new int[col];  // isvisited[i]为第i个城市是否被访问过
        int count=0;
       
        for(int i=0;i<col;i++)  //遍历每一个城市
        {
            if(isvisited[i]==0)  //从没被访问过的城市开始搜
            { 
                LinkedList<Integer> list=new LinkedList<>();
                list.add(i);
                isvisited[i]=1;
                while(!list.isEmpty())
                {
                    Integer tem=list.remove();
                    for(int j=0;j<row;j++)
                    {
                        if(tem!=j&&isvisited[j]==0&&isConnected[tem][j]==1)
                        {
                            list.add(j);
                            isvisited[j]=1;
                        }
                    }
                }
               count++;
            }
        }
        return count;       
    }
}
```

(3)法三：并查集

```java
class Solution  
{
    public int findCircleNum(int[][] isConnected) 
    {
        int num_city=isConnected.length;
        UF uf=new UF(num_city);
        for(int i=0;i<num_city;i++)
        {
            for(int j=i+1;j<num_city;j++)  
            {
                if(isConnected[i][j]==1)  
                {
                    uf.union(i,j);  
                }
            }
        }
        HashSet<Integer> set=new HashSet<>();
        for(int id:uf.id)
        set.add(id);
        return set.size();    
    }
}

class UF  //并查集
{
    protected int[] id; //id[i]为第i个节点所属的连通集合代号
    public UF(int N)   //创建N个节点
    {
        id = new int[N];
        for (int i = 0; i < N; i++)   //初始时将所有的节点都设为不连通（id号相同为连通）
            id[i] = i;
    }

    public boolean connected(int p, int q)  //判断节点p、q是否连通
    {
        return id[p]== id[q];
    }

    public int find(int p)  //查询节点p所属连通集合代号
    {
        return id[p];
    }

    public void union(int p, int q)  //合并p、q两个节点所在的集合
    {
        int id_p=find(p);
        int id_q=find(q);  //这个一定要先保存，不然q改完之后，它之后的就没法改了
        if(id_p!=id_q)
        {
            for(int i=0;i<id.length;i++)  //将q所在连通集合中的所有节点id改为p连通集合的id
            {
                if(id[i]==id_q)
                    id[i]=id_p;
            }
        }
    }
}
```

## 9. **[路径总和 III](https:/!/leetcode.cn/problems/path-sum-iii/)（简单）**

![Untitled](assets/Untitled 39.png)

**思路：对每一个节点进行dfs（设该节点为路径起点就可避免重复）**

**注意：求和sum要设为long ，否则数据太大时不通过（最后一个用例）**

```java
class Solution  //21:04-22:29
{
    long sum=0;  //设为int最后一个测试用例会溢出
    int count=0;
    public int pathSum(TreeNode root, int targetSum) 
    {
        if(root==null)
        return 0;
        LinkedList<TreeNode> list=new LinkedList<>();
        list.add(root);
        while(!list.isEmpty())
        {
            TreeNode tem=list.remove();
            sum=0;
            dfs(tem,targetSum);
            if(tem.left!=null)
            list.add(tem.left);
            if(tem.right!=null)
            list.add(tem.right);
        }
        return count;

    }

    public void dfs(TreeNode node,int targetSum)
    {
        if(node==null)
        return;
        sum +=node.val;
        if(sum==targetSum)
        count++;
        dfs(node.left,targetSum);
        dfs(node.right,targetSum);
        sum -=node.val;
    }
}
```

![Untitled](assets/Untitled 40.png)

**前缀和**：避免重复计算

![Untitled](assets/Untitled 41.png)

```java

 //前缀和
class Solution {
    public int ans;
    public int pathSum(TreeNode root, int targetSum) {
        Map<Long, Integer> prefix = new HashMap<>(); //键：前缀和 值：对应的数量
        //当targetSum 等于某个节点值时，curPrefix - targetSum = 0,当前节点自己就算做一条符合条件的路径，所以也要计数
        prefix.put(0L, 1);
        dfs(root, prefix, 0L, targetSum);
        return ans;
    }

    //先序遍历
    private void dfs (TreeNode root, Map<Long, Integer> prefix, long curPrefix, int targetSum) {
        //递归终止的条件
        if (root == null) {
            return;
        }
        //当前节点的前缀和
        curPrefix += root.val;
        //查看是否有curPrefix - targetSum的前缀和已经存在
        int cnt = prefix.getOrDefault(curPrefix - targetSum, 0);
        ans += cnt;
        //记录前缀和
        prefix.put(curPrefix, prefix.getOrDefault(curPrefix, 0) + 1);
        //遍历左子树
        dfs(root.left, prefix, curPrefix, targetSum);
        //遍历右子树
        dfs(root.right, prefix, curPrefix, targetSum);
        //因为先序遍历是遍历根、左、右，即当前节点及其所有子节点，所以当遍历完当前节点和其所以子节点之后，当前节点的前缀和就没有用了，就需要把map里的记录删除，否则会影响其他子树的计算。跟当前节点没有路径关系的节点，不需要当前节点的前缀和
        prefix.put(curPrefix, prefix.getOrDefault(curPrefix, 0) - 1);
    }
}
```

![Untitled](assets/Untitled 42.png)

## 10. **[螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/)**

![Untitled](assets/Untitled 43.png)

```java
//最常规的方法，每轮起点都是(start,start),长m,高n,
//下一轮的start=start+1, m=m-2, n=n-2,但是代码又臭又长
//这里演示dfs解法
class Solution 
{
    int n,m;
    List<Integer> ans = new LinkedList<>();
    //                          右、    下、    左、    上
    int[][] dir = new int[][]{{0, 1},{1, 0},{0, -1},{-1, 0}};
    boolean[][] visited;
    public List<Integer> spiralOrder(int[][] matrix) 
    {
        n = matrix.length;
        m = matrix[0].length;
        visited = new boolean[n][m];

        dfs(0, 0, 0, matrix);
        return ans;
    }

    public void dfs(int x, int y, int u, int[][] matrix)
    {
        //第一次的边界是数组本身边界，后面是上一轮遍历过的数围起的墙
        if(x >= n || x < 0 || y >= m || y < 0 || visited[x][y]) return;
        
        visited[x][y] = true;
        ans.add(matrix[x][y]);
        // 如果走的通，一直往u方向走
        int a = x + dir[u][0], b = y + dir[u][1];
        dfs(a, b, u, matrix);
        
        // 换下一个方向
        u = (u + 1) % 4; 
        dfs( x + dir[u][0], y + dir[u][1], u, matrix);

    }
}
```

## 11. **[二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)**

二叉树中的 **路径** 被定义为一条节点序列，序列中每对相邻节点之间都存在一条边。同一个节点在一条路径序列中 **至多出现一次** 。该路径 **至少包含一个** 节点，且不一定经过根节点。

**路径和** 是路径中各节点值的总和。

给你一个二叉树的根节点 `root` ，返回其 **最大路径和** 。

 

**示例 1：**

![img](assets/exx1.jpg)

```
输入：root = [1,2,3]
输出：6
解释：最优路径是 2 -> 1 -> 3 ，路径和为 2 + 1 + 3 = 6
```

**示例 2：**

![img](assets/exx2.jpg)

```
输入：root = [-10,9,20,null,null,15,7]
输出：42
解释：最优路径是 15 -> 20 -> 7 ，路径和为 15 + 20 + 7 = 42
```

 

**提示：**

- 树中节点数目范围是 `[1, 3 * 104]`
- `-1000 <= Node.val <= 1000`



思路：都是遍历所有节点，然后以它为顶点，设当前节点为node, 求出它的左子树单边最大路径和leftMax，右子树单边最大路径和rightMax，则以node为顶点的最大路径和为$root.val+Math.max(0,leftMax)+Math.max(0,rightMax)$。遍历所有节点可找到全局最大。

法一：一遍dfs

```java
class Solution {
    public int maxPathSum(TreeNode root) {
        maxPathSigle(root);
        return res;
    }
    int res=Integer.MIN_VALUE;
    public int maxPathSigle(TreeNode root){ // 单边最大路径和
        if(root.left==null&&root.right==null){
            res=Math.max(res,root.val);
            return root.val;
        }
        int leftMax=root.left==null?0:maxPathSigle(root.left);
        int rightMax=root.right==null?0:maxPathSigle(root.right);
        
        res=Math.max(res,root.val+Math.max(0,leftMax)+Math.max(0,rightMax));
        return root.val+Math.max(Math.max(0,leftMax),Math.max(0,rightMax));
    }
}
```

![image-20240706200341013](assets/image-20240706200341013.png)

法二：先求以node节点开始的单向最大路径和，bfs遍历所有顶点，有重复dfs

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    HashMap<TreeNode,Integer> cache=new HashMap<>();
    public int maxPathSum(TreeNode root) {
        int res=Integer.MIN_VALUE;
        LinkedList<TreeNode> list=new LinkedList<>();
        list.add(root);
        while(!list.isEmpty()){
            TreeNode node=list.remove();
            int leftMax=0;
            if(node.left!=null){
                list.add(node.left);
                leftMax=getMax(node.left);
            }
            int rightMax=0;
            if(node.right!=null){
                list.add(node.right);
                rightMax=getMax(node.right);
            }

            res=Math.max(res,node.val+Math.max(leftMax,0)+Math.max(rightMax,0));
        }

        return res;
    }

    int maxValue=Integer.MIN_VALUE;
    int tem=0;
    public void dfs(TreeNode node){  // 求以node节点开始的单向最大路径和
        if(node==null){
            return;
        }
        tem +=node.val;
        maxValue=Math.max(tem,maxValue);
        if(node.left!=null){
            dfs(node.left);
        }

        if(node.right!=null){
            dfs(node.right);
        }
        tem -=node.val;
    }

    public int getMax(TreeNode node){
        Integer res=cache.get(node);
        if(res==null){
            maxValue=Integer.MIN_VALUE;
            tem=0;
            dfs(node);
            cache.put(node,maxValue);
        }

        return cache.get(node);
    }

}
```

![image-20240706200359977](assets/image-20240706200359977.png)



## 12. 字节笔试

[字节笔试 9.25 3/4_笔经面经_牛客网](https://www.nowcoder.com/discuss/1063783?channel=-1&source_id=discuss_terminal_nctrack&trackId=undefined)

![Untitled](assets/Untitled 48.png)

```java
public class Main
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        String ss=input.nextLine();

        int N=Integer.parseInt(ss.substring(0,1)); //N组数据
        char[][] a=new char[N][];
        HashMap<Character,ArrayList<Character>> map=new HashMap<>();  ///键：父节点  值：子节点列表

        HashSet<Character> set1=new HashSet<>();  //所有字母
        HashSet<Character>  set2=new HashSet<>(); //子
        for(int i=0;i<N;i++)
        {
            String s=input.nextLine();
            String[] t=s.split(" ");
            for(int j=0;j<t.length-1;j++)
            {
                char p=t[j].charAt(0);
                char c=t[j+1].charAt(0);
                if(!map.containsKey(p))
                {
                    ArrayList<Character> tem=new ArrayList<>();
                    tem.add(c);
                    map.put(p,tem);
                }
                else
                {
                    ArrayList<Character> tem=map.get(p);
                    tem.add(c);
                    map.put(p,tem);
                }

            }
        }

        Set<Map.Entry<Character,ArrayList<Character>>> set=map.entrySet();

        Iterator<Map.Entry<Character,ArrayList<Character>>> ite=set.iterator();
        while(ite.hasNext())
        {
            Map.Entry<Character,ArrayList<Character>> tem=ite.next();
            System.out.print(tem.getKey()+": <");
            ArrayList<Character> list=tem.getValue();
            set1.add(tem.getKey());
            for(int i=0;i<list.size();i++)
            {
                set1.add(list.get(i));
                set2.add(list.get(i));
                if(i!=list.size()-1)
                System.out.print(list.get(i)+",");
                else
                System.out.print(list.get(i));

            }
            System.out.println(">");
        }
        Character parent='a';  //根
        for(Character o:set1)
        {
            if(!set2.contains(o))
            {
                parent=o;
                break;
            }
        **}**

        //bfs统计各层节点个数
        ArrayList<Integer> res=new ArrayList<>();//各层的节点数  从0开始
        res.add(1);
        LinkedList<Character> linkedList=new LinkedList<>();
        linkedList.add(parent);

        while(!linkedList.isEmpty())
        {
            int count=linkedList.size();
            int count_child=0;
            for(int k=0;k<count;k++)
            {
                Character tem=linkedList.remove();
                ArrayList<Character> tt=map.get(tem);
                if(tt!=null)
                {
                    count_child +=tt.size();
                    for(int m=0;m<tt.size();m++)
                        linkedList.add(tt.get(m));
                }
            }
            res.add(count_child);
        }
        System.out.println("各层节点个数：");
        for(Integer r:res)
            System.out.print(r+" ");
    }
}

//多叉树
class Node
{
    public Character val;
    public ArrayList<Node> children=new ArrayList<>();
    public Node(Character val)
    {
        this.val=val;
    }

    public void addNode(Node n)
    {
        children.add(n);
    }
}
```

## 13. **组合总和 II**

![Untitled](assets/Untitled 49.png)

![Untitled](assets/Untitled 50.png)

```java
//dfs
class Solution 
{
    
    List<List<Integer>> res=new ArrayList<>();
    List<Integer> list=new ArrayList<>();
    TreeMap<Integer,Integer> map=new TreeMap<>();  //TreeMap能根据Key的compareTo方法排序   键：数字  值：出现的次数  因此map键从小到大排序
    public List<List<Integer>> combinationSum2(int[] candidates, int target) 
    {
        for(int a:candidates)
        {
            if(map.containsKey(a))
            map.put(a,map.get(a)+1);
            else
            map.put(a,1);
        }

        int[][] nums=new int[map.size()][2];    //将map存入数组  nums[i][0] 为键 nums[i][1]为值
        Set<Map.Entry<Integer,Integer>> set=map.entrySet();
        Iterator<Map.Entry<Integer,Integer>> ite=set.iterator();
        int i=0;
        while(ite.hasNext())
        {
            Map.Entry<Integer,Integer> tem=ite.next();
            nums[i][0]=tem.getKey();
            nums[i][1]=tem.getValue();
            i++;
        }

        dfs(0,nums,target);
        return res;
    }

    public void dfs(int i,int[][] nums,int target) 
    {
        if(target==0)
        {
            if(!res.contains(list))
            res.add(new ArrayList<>(list));
            return;
        }
        if(i>nums.length-1||target<nums[i][0])
        return;

        for(int j=0;j<=nums[i][1];j++)  //每个位置选或者不选 假如(1,4) 表示1出现4次，那么j可选0-4(0为不选，其他为选择1的次数)
        {
            for(int k=0;k<j;k++)
            list.add(nums[i][0]);
            dfs(i+1,nums,target-j*nums[i][0]);
            for(int k=0;k<j;k++)
            list.remove((Integer)nums[i][0]);
        }
    
    }  
}
```



## 14. [二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

[百度百科](https://baike.baidu.com/item/最近公共祖先/8918834?fr=aladdin)中最近公共祖先的定义为：“对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（**一个节点也可以是它自己的祖先**）。”

 

**示例 1：**

![img](assets/binarytree.png)

```
输入：root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出：3
解释：节点 5 和节点 1 的最近公共祖先是节点 3 。
```

**示例 2：**

![img](assets/binarytree.png)

```
输入：root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出：5
解释：节点 5 和节点 4 的最近公共祖先是节点 5 。因为根据定义最近公共祖先节点可以为节点本身。
```

**示例 3：**

```
输入：root = [1,2], p = 1, q = 2
输出：1
```

 

**提示：**

- 树中节点数目在范围 `[2, 105]` 内。
- `-109 <= Node.val <= 109`
- 所有 `Node.val` `互不相同` 。
- `p != q`
- `p` 和 `q` 均存在于给定的二叉树中。



思路：用map存储每个节点对应父节点的集合，其实就是从根节点到当前 节点的路径，dfs走一遍就可构建map。拿到p、q节点对应的父节点列表parent1和parent2，从后往前遍历判断当前节点是否在另一个list中，在直接返回。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    ArrayList<TreeNode> tem=new ArrayList<>();
    HashMap<TreeNode,ArrayList<TreeNode>> map=new HashMap<>(); //存储当前节点的父节点集合
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        dfs(root);
        ArrayList<TreeNode> parent1=map.get(p);
        ArrayList<TreeNode> parent2=map.get(q);
        for(int i=parent1.size()-1;i>=0;i--){
            if(parent2.contains(parent1.get(i))){
                return parent1.get(i);
            }
        }
        return null;  
    }

    public void dfs(TreeNode node){ // dfs构造map
            if(node==null){
                return;
            }
            tem.add(node);
            map.put(node,new ArrayList<>(tem));
            dfs(node.left);
            dfs(node.right);
            tem.remove(tem.size()-1);
        }   
}
```









# 三. 广度优先搜索（BFS, Breadth-First Search）

**这种题目都是用LinkedList，后插头出，循环条件list非空： 适用于树的逐层遍历、从某一点逐层往外扩之类的题**

## 1. **[腐烂的橘子](https://leetcode.cn/problems/rotting-oranges/)**

在给定的 m x n 网格 grid 中，每个单元格可以有以下三个值之一：

- 值 0 代表空单元格；
- 值 1 代表新鲜橘子；
- 值 2 代表腐烂的橘子。

每分钟，腐烂的橘子 周围 4 个方向上相邻 的新鲜橘子都会腐烂。

返回 直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 -1 。

![Untitled](assets/Untitled 51.png)

```markdown
输入：grid = [[2,1,1],[1,1,0],[0,1,1]]
输出：4
```

实例2

```markdown
输入：grid = [[2,1,1],[0,1,1],[1,0,1]]
输出：-1
解释：左下角的橘子（第 2 行， 第 0 列）永远不会腐烂，因为腐烂只会发生在 4 个正向上。
```

示例3

```markdown
输入：grid = [[0,2]]
输出：0
解释：因为 0 分钟时已经没有新鲜橘子了，所以答案就是 0 。
```

提示：

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 10`
- `grid[i][j]` 仅为 `0`、`1` 或 `2`

思路：BFS都是用LinkedList，遍历grid，把所有腐烂的橘子加入列表，同时统计好橘子数量，接下来while循环弹出（list非空），注意是多源同时腐烂，所以**要弹出上一层所有腐烂橘子，而不是一个**，

直到list为空，统计最后好橘子的个数，若还有好橘子，返回-1，否则返回时间。

```java
class Solution  //16:15-
{
    public int orangesRotting(int[][] grid) {
        int fresh=0,m=grid.length,n=grid[0].length;
        LinkedList<int[]> list=new LinkedList<>();
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(grid[i][j]==2){
                    list.add(new int[]{i,j});
                }else if(grid[i][j]==1){
                    fresh++;
                }
            }
        }
        int sum=list.size()+fresh; // 总数
        int count=0;  //弹出计数

        if(fresh>0&&list.size()==0){ // 有新鲜橘子但没腐烂橛子 
            return -1;
        }
        if(fresh==0){ // 没新鲜橘子
            return 0;
        }
 
        int res=0;
        // BFS
        while(!list.isEmpty()){
            int len=list.size();
            res++;
            for(int k=0;k<len;k++){  // 一层层腐烂
                int[] tem=list.remove();
                count++;
                int[][] direction={{-1,0},{1,0},{0,-1},{0,1}};
                for(int[] dir:direction){
                    int xNext=tem[0]+dir[0];
                    int yNext=tem[1]+dir[1];
                    if(illegal(xNext,yNext,m,n)&&grid[xNext][yNext]==1){
                    list.add(new int[]{xNext,yNext});
                    grid[xNext][yNext]=-1;
                    }
                }
            }
        }
        return count==sum?res-1:-1;
    }
    // 坐标是否合法
    public boolean illegal(int i,int j,int m,int n)
    {
        return i>=0&&i<m&&j>=0&&j<n;
    }
}
```

## 2. **[钥匙和房间](https://leetcode.cn/problems/keys-and-rooms/)**

有 n 个房间，房间按从 0 到 n - 1 编号。最初，除 0 号房间外的其余所有房间都被锁住。你的目标是进入所有的房间。然而，你不能在没有获得钥匙的时候进入锁住的房间。当你进入一个房间，你可能会在里面找到一套不同的钥匙，每把钥匙上都有对应的房间号，即表示钥匙可以打开的房间。你可以拿上所有钥匙去解锁其他房间。给你一个数组 rooms 其中 rooms[i] 是你进入 i 号房间可以获得的钥匙集合。如果能进入 所有 房间返回 true，否则返回 false。

示例1：

```markdown
输入：rooms = [[1],[2],[3],[]]
输出：true
解释：
我们从 0 号房间开始，拿到钥匙 1。
之后我们去 1 号房间，拿到钥匙 2。
然后我们去 2 号房间，拿到钥匙 3。
最后我们去了 3 号房间。
由于我们能够进入每个房间，我们返回 true。
```

示例2：

```markdown
输入：rooms = [[1,3],[3,0,1],[2],[0]]
输出：false
解释：我们不能进入 2 号房间。
```

提示：

```java
n == rooms.length
2 <= n <= 1000
0 <= rooms[i].length <= 1000
1 <= sum(rooms[i].length) <= 3000
0 <= rooms[i][j] < n
所有 rooms[i] 的值 互不相同
```

```java
class Solution 
{
    public boolean canVisitAllRooms(List<List<Integer>> rooms) {
        HashSet<Integer> set=new HashSet<>();
        int n=rooms.size();
        LinkedList<Integer> list=new LinkedList<>();
        // 进房间0拿到钥匙
        for(Integer o:rooms.get(0)){
            list.add(o);
        }
        set.add(0);
        // BFS
        while(!list.isEmpty()){
            int size=list.size();
            for(int i=0;i<size;i++){
                Integer tem=list.remove();
                set.add(tem);
                for(Integer next:rooms.get(tem)){ // 下一房间
                    if(!set.contains(next)){
                        list.add(next);
                    }
                }
            }
        }

        return set.size()==n;
    }
}
```

## 3. **[二叉树最大宽度](https://leetcode.cn/problems/maximum-width-of-binary-tree/)**

给定一个二叉树，编写一个函数来获取这个树的最大宽度。树的宽度是所有层中的最大宽度。这个二叉树与满二叉树（full binary tree）结构相同，但一些节点为空。每一层的宽度被定义为两个端点（该层最左和最右的非空节点，两端点间的null节点也计入长度）之间的长度。

![Untitled](assets/Untitled 52.png)

![Untitled](assets/Untitled 53.png)

![Untitled](assets/Untitled 54.png)

```java
class Solution {
    public int widthOfBinaryTree(TreeNode root) {  
        int res=0;
        LinkedList<NodeWithIndex> list=new LinkedList<>();
        list.add(new NodeWithIndex(root,0));
        while(!list.isEmpty()){
            int len=list.size();
            int l=0,r=0;
            for(int i=0;i<len;i++){
                NodeWithIndex tem=list.remove();
                if(i==0){
                    l=tem.index;
                }
                if(i==len-1){
                    r=tem.index;
                }
                if(tem.node.left!=null){
                    list.add(new NodeWithIndex(tem.node.left,2*tem.index));
                }
                if(tem.node.right!=null){
                    list.add(new NodeWithIndex(tem.node.right,2*tem.index+1));
                }   
            }
        res=Math.max(res,r-l+1);
    }
    return res;
}
}

class NodeWithIndex{
    TreeNode node;
    Integer index;
    public NodeWithIndex(TreeNode node,Integer index){
        this.node=node;
        this.index=index; //节点下标  每层节点下标都从0开始
    }
}
```

利用规律：

**左节点的编号2*i，右节点编号2*i+1 (每一层编号从0开始)**

超时解法： null节点用空格表示，去掉String两边的空格，通过72/116

```jsx
class Solution {
    public int widthOfBinaryTree(TreeNode root) {  // null用“ ”表示，去掉两边的空格
        int depth=0,res=0;
        LinkedList<TreeNode> list=new LinkedList<>();
        list.add(root);
        while(!list.isEmpty()){
            int len=list.size();
            String s="";
            boolean allNull=true;
            for(int i=0;i<len;i++){
                TreeNode node=list.remove();
                if(node==null){
                    s=s+" ";
                    list.add(null);
                    list.add(null);

                }else{
                    allNull=false;
                    s=s+"1";
                    list.add(node.left);
                    list.add(node.right);
                }
            }
        res=Math.max(res,s.trim().length());
        if(allNull){
            break;
        }
    }
    return res;
}
}
```

## 4. **[奇偶树](https://leetcode.cn/problems/even-odd-tree/)**

[](https://leetcode-cn.com/problems/even-odd-tree/)

![Untitled](assets/Untitled 55.png)

![Untitled](assets/Untitled 56.png)

思路和实现比较简单 就不贴题解

## 5. 剑指offer 之字形打印二叉树

[力扣](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)

![Untitled](assets/Untitled 57.png)

比较简单（正常层序遍历加层深度判断）

## 6. **[序列化二叉树](https://leetcode.cn/problems/xu-lie-hua-er-cha-shu-lcof/)（困难，特别留意）**

![Untitled](assets/Untitled 58.png)

说明：

**题目没有声明二叉树中不含重复元素，所以不能用（前序、中序、后序）中的两种进行编码** 。

[4、重建二叉树](https://www.notion.so/4-fc9743be8c8b4df09c987ba87df4c57c?pvs=21) 

正确思路为：

1、编码：**带null节点的层序遍历, 2次bfs**

（1）定义一个NodewithDepth类，进行第1次bfs，求出二叉树的最大深度max_depth （层：0~maxdepth-1）

(2)第2次bfs，将null节点加入，生成编码字符串：

遍历时若当前节点为null，根据所在层是不是最后一层决定加不加左右子节点null进list

不是最后一层，list加两个null子节点进去；

是，不加

2、解码：**由层序遍历重建，类似华为机试**

(1)将字符串数组用空格split得到字符串数组s

(2)s[0]就是根节点值，然后根据数学规律获取左右子树的字符数组，再调用自身重建左右子树，加到root节点并返回。

自己写的：超时

![Untitled](assets/Untitled 59.png)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Codec 
{
    // Encodes a tree to a single string.
    public String serialize(TreeNode root) 
    {
        if(root==null)
        return "";
        LinkedList<NodewithDepth> list=new LinkedList<>();
        int max_depth=0;  //最大深度 从0开始
        
        //第1次bfs ：求二叉树的最大深度
        list.add(new NodewithDepth(root,0));
        while(!list.isEmpty())
        {
            NodewithDepth tem=list.remove();
            max_depth=Math.max(max_depth,tem.depth); 
            if(tem.node.left!=null)
            list.add(new NodewithDepth(tem.node.left,tem.depth+1));
            if(tem.node.right!=null)
            list.add(new NodewithDepth(tem.node.right,tem.depth+1)); 
        }
        
        //第2次bfs ：生成具有null节点的层序遍历，结果放入字符串s
         String s="";
        list.add(new NodewithDepth(root,0));
        while(!list.isEmpty())
        {
            NodewithDepth tem=list.remove();
            if(tem.node==null)  //当前节点为null
            {
                s=s+"null"+" ";
                if(tem.depth!=max_depth)  //不是最后一层，继续加左右节点（null)
                {
                    list.add(new NodewithDepth(null,tem.depth+1));
                    list.add(new NodewithDepth(null,tem.depth+1));
                }
            }
            else //当前节点不为null
            {
                s=s+tem.node.val+" ";
                if(tem.depth!=max_depth) //不是最后一层
                {
                    list.add(new NodewithDepth(tem.node.left,tem.depth+1));
                    list.add(new NodewithDepth(tem.node.right,tem.depth+1));
                }      
            }
        }
        System.out.println(s.substring(0,s.length()-1)); //去掉最后最后一个空格
        return s.substring(0,s.length()-1); 
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) 
    {
        if(data.equals(""))
        return null;

        String[] s=data.split(" ");
        return rebuile(s); 
    }

    public TreeNode rebuile(String[] s)
    {
        if(s.length==0||s[0].equals("null")) //空或者根节点为null
        return null;
        if(s.length==1) 
            return new TreeNode(Integer.parseInt(s[0]));
        
        int n=(int)(Math.log(s.length+1)/Math.log(2)); //深度:0~n-1
        //根节点值
        int root=Integer.parseInt(s[0]);
        //获得左右子树数组
        String[] left=new String[(int)(s.length-1)/2];
        String[] right=new String[(int)(s.length-1)/2];
        int index1=0,index2=0,index3=1;
        for(int i=1;i<n;i++) //第i层
        {
            int len=(int)Math.pow(2,i-1);
            for(int j=0;j<len;j++)
                left[index1++]=s[index3++];
            for(int j=0;j<len;j++)
                right[index2++]=s[index3++];       
        }

        TreeNode ROOT=new TreeNode(root);
        ROOT.left=rebuile(left);
        ROOT.right=rebuile(right);
        return ROOT;
    } 
}

class NodewithDepth
{
    TreeNode node;
    int depth;
    public NodewithDepth(TreeNode node,int depth)
    {
        this.node=node;
        this.depth=depth;
    }
}
```

参考别人：

```java
public class Codec {

    // Encodes a tree to a single string.
   
//把null也加入层序遍历，示例输出[1,2,3,null,null,4,5,null,null,null,null,]
    public String serialize(TreeNode root) 
    {
        if(root == null)
            return "";
        
        StringBuilder res = new StringBuilder();
        res.append("[");
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while(!queue.isEmpty())
        {
            TreeNode node = queue.remove();
            if(node != null)
            {
                res.append("" + node.val);
                queue.add(node.left);
                queue.add(node.right);
            }
            else
                res.append("null");
            
            res.append(",");
        }
        res.append("]");
        return res.toString();
    }

    // Decodes your encoded data to tree.
   //由层序遍历建树
    public TreeNode deserialize(String data) 
    {
        if(data == "")
            return null;

        //正则表达式分割字符串
        String[] dataList = data.substring(1, data.length() - 1).split(","); 
        
        TreeNode root = new TreeNode(Integer.parseInt(dataList[0]));
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        int i = 1;
        while(!queue.isEmpty())
        {
            TreeNode node = queue.remove();
            if(!"null".equals(dataList[i]))
            {
                node.left = new TreeNode(Integer.parseInt(dataList[i]));
                queue.add(node.left);
            }
            i++;
            if(!"null".equals(dataList[i]))
            {
                node.right = new TreeNode(Integer.parseInt(dataList[i]));
                queue.add(node.right);
            }
            i++;
        }
        return root;
    }
}
```

## 7. **蛇梯棋（909）**

给你一个大小为 `n x n` 的整数矩阵 `board` ，方格按从 `1` 到 $n^2$ 编号，编号遵循 [转行交替方式](https://baike.baidu.com/item/%E7%89%9B%E8%80%95%E5%BC%8F%E8%BD%AC%E8%A1%8C%E4%B9%A6%E5%86%99%E6%B3%95/17195786) ****，**从左下角开始** （即，从 `board[n - 1][0]` 开始）每一行交替方向。

玩家从棋盘上的方格 `1` （总是在最后一行、第一列）开始出发。

每一回合，玩家需要从当前方格 `curr` 开始出发，按下述要求前进：

- 选定目标方格 `next` ，目标方格的编号符合范围 `[curr + 1, min(curr + 6,` $n^2$`)]` 。
    - 该选择模拟了掷 **六面体骰子** 的情景，无论棋盘大小如何，玩家最多只能有 6 个目的地。
- 传送玩家：如果目标方格 `next` 处存在蛇或梯子，那么玩家会传送到蛇或梯子的目的地。否则，玩家传送到目标方格 `next` 。
- 当玩家到达编号 $n^2$ 的方格时，游戏结束。

`r` 行 `c` 列的棋盘，按前述方法编号，棋盘格中可能存在 “蛇” 或 “梯子”；如果 `board[r][c] != -1`，那个蛇或梯子的目的地将会是 `board[r][c]`。编号为 `1` 和 $n^2$ 的方格上没有蛇或梯子。

注意，玩家在每回合的前进过程中最多只能爬过蛇或梯子一次：就算目的地是另一条蛇或梯子的起点，玩家也 **不能** 继续移动。

- 举个例子，假设棋盘是 `[[-1,4],[-1,3]]` ，第一次移动，玩家的目标方格是 `2` 。那么这个玩家将会顺着梯子到达方格 `3` ，但 **不能** 顺着方格 `3` 上的梯子前往方格 `4` 。

返回达到编号为 $n^2$ 的方格所需的最少移动次数，如果不可能，则返回 `-1`。

![Untitled](assets/Untitled 60.png)

![Untitled](assets/Untitled 61.png)

![Untitled](assets/Untitled 62.png)

思路

![Untitled](assets/Untitled 63.png)

```java
class Solution  //21:31-
{
     int res=Integer.MAX_VALUE;
     int load=0;
     boolean reach=false;
    public int snakesAndLadders(int[][] board) 
    {
        int res=0;
        int n=board.length;
        int direct=1;
        HashMap<Integer,Integer> map=new HashMap<>(); //编号-board值
        int num=1;
        for(int i=n-1;i>=0;i--)//行
        {
            if(direct==1) //向右遍历
            {
                for(int j=0;j<n;j++)
                {
                    map.put(num,board[i][j]);
                    num++;
                }
                direct=-1; //改变方向
            }
            else
            {
                for(int j=n-1;j>=0;j--) //向左遍历
                {
                    map.put(num,board[i][j]);
                    num++;
                }
                direct=1;
            }
        }
 
        num=1; //当前位置
        int[] visited=new int[n*n+1];
        LinkedList<int[]> list=new LinkedList<>();
        list.add(new int[]{1,0});
        while(!list.isEmpty())
        {
            int[] p=list.remove();
            for(int j=p[0]+1;j<=p[0]+6&&j<=n*n;j++)
            {
                int next=j;
                if(map.get(j)!=-1)
                    next=map.get(j);
                    
                if(next==n*n)
                return p[1]+1;
                if(visited[next]==0)
                {
                    visited[next]=1;
                    list.add(new int[]{next,p[1]+1});
                }         
            }
        }
        return -1;  
    }
}
```

# 四. 记忆化搜索（DP+递归）

和简单递归（dfs）的区别：

**递归过程中会将求得的中间结果保存起来（比如HashMap），减少子问题的重复计算**

重点看

**1、大礼包**

**2、统计所有可行路径**

## 1. 大礼包(难)

[](https://leetcode.cn/problems/shopping-offers/description/)

![Untitled](assets/Untitled 64.png)

![Untitled](assets/Untitled 65.png)

思路：

1、建立合格礼包列表 filterSpecial：总价小于正常价

2、递归dfs

传入正常价格、大礼包、购买需求，返回最小花费

```java
public int dfs(List<Integer> price,List<List<Integer>> filterSpecial,List<Integer> currNeeds);
```

首先用一个HashMap存储  < 需求，最小花费> 键值对，减少重复递归。

逐个遍历大礼包，若当前大礼包不超出购买需求，则该礼包可以使用，更新购买需求，

则

![Untitled](assets/Untitled 66.png)

```java
class Solution 
{

    //键：购买需求 值：最小花费
    HashMap<List<Integer>,Integer> map=new HashMap<>();   

    public int shoppingOffers(List<Integer> price, List<List<Integer>> special, List<Integer> needs) 
    {
        //去除不合格的礼包
        List<List<Integer>> filterSpecial=new ArrayList<>();
        for(List<Integer> tem:special)
        {
            int n=tem.size();
            //求大礼包正常价格
            int sum=0;
            for(int i=0;i<n-1;i++)
            sum +=tem.get(i)*price.get(i);

            if(tem.get(n-1)<=sum)  //合格大礼包
            filterSpecial.add(tem);
        }

        return dfs(price,filterSpecial,needs);
    }

    public int dfs(List<Integer> price,List<List<Integer>> filterSpecial,List<Integer> currNeeds)
    {
        if(!map.containsKey(currNeeds))
        {
            //最大花费为正常购买，不用大礼包
            int min_cost=0;
            for(int i=0;i<currNeeds.size();i++)
            min_cost +=currNeeds.get(i)*price.get(i);

            //逐个遍历大礼包
            for(List<Integer> sp:filterSpecial)
            {
                //买了这个大礼包后的消费需求（更新）
                List<Integer> nextNeeds=new ArrayList<>(); 
                for(int j=0;j<sp.size()-1;j++)
                {
                    if(currNeeds.get(j)<sp.get(j))
                    break;

                    nextNeeds.add(currNeeds.get(j)-sp.get(j));
                }
                if(nextNeeds.size()==currNeeds.size())  //当前礼包不超出预算范围
                {
                    min_cost=Math.min(min_cost,sp.get(sp.size()-1)+dfs(price,filterSpecial,nextNeeds)); //递归买剩下的
                }
            }

            map.put(currNeeds,min_cost);
        }
        return map.get(currNeeds);
    }
   
}
```

## 2. **统计所有可行路径**

![Untitled](assets/Untitled 67.png)

![Untitled](assets/Untitled 68.png)

思路：

用  函数dfs求从start到finish，油量为fuel的路线总数

```java
int  dfs(int[] locations, int start, int finish, int fuel)；
```

$int[][]$ dp数组存储状态，其中$dp[start][fuel]$表示：从start到finish且油量为fuel的路线总数

遍历数组$location$选中转站$i$：

当$i!=start$且$cost=Math.abs(locations[i]-locations[start])<=fuel$时表示中转站可用

$dp[start][fuel]=\sum_idp[i][fuel-cost]$

当$finish=start$时，不动也是一种路线，方案总数+1

剪枝：

两点之间直线最短，当$Math.abs(locations[finish]-locations[start])>fuel$时，路线总数必为0。

```java
class Solution 
{
    int[][] dp;  //dp[i][fuel]:从起点i到终点且汽油为fuel的路线总数
    public int countRoutes(int[] locations, int start, int finish, int fuel) 
    {
        dp=new int[locations.length][fuel+1];
        for(int[] a:dp)
        Arrays.fill(a,-1);  //所有都填充 -1 
        return dfs(locations,start,finish,fuel);
        
    }
    public int dfs(int[] locations, int start, int finish, int fuel)
    {
        int Mod=(int)Math.pow(10,9) + 7;
        if(dp[start][fuel]==-1) //之前没有探索过
        {
            //剪枝：两点直线最短，如果直接都到不了，中转站的方式更到不了
            if(Math.abs(locations[finish]-locations[start])>fuel)
            {
                dp[start][fuel]=0;
                return 0;
            }
            
            //选中转站: start->i->finish
            int count=0;
            for(int i=0;i<locations.length;i++)
            {
                int cost=Math.abs(locations[i]-locations[start]); //从start到i
                if(i!=start&&cost<=fuel)
                {
                    count +=dfs(locations,i,finish,fuel-cost);
                    count %=Mod;
                }
            }
            if(start==finish) 
            count++;
            dp[start][fuel]=count%Mod;
        }
        return dp[start][fuel];  //探索过直接返回
    }
}
```

## 3. 移除盒子

![Untitled](assets/Untitled 69.png)

**3个小时写了一坨屎山，还超时，疯了（题解也是又臭又长，不说人话，之后再看）**

![Untitled](assets/Untitled 70.png)

## 4.  **[打家劫舍 III](https://leetcode.cn/problems/house-robber-iii/)**

![Untitled](assets/Untitled 71.png)

题解：

[力扣](https://leetcode.cn/problems/house-robber-iii/solution/san-chong-fang-fa-jie-jue-shu-xing-dong-tai-gui-hu/)

```java
public int rob(TreeNode root) {
    HashMap<TreeNode, Integer> memo = new HashMap<>();
    return robInternal(root, memo);
}

public int robInternal(TreeNode root, HashMap<TreeNode, Integer> memo) {
    if (root == null) return 0;
    if (memo.containsKey(root)) return memo.get(root);
    int money = root.val;

    if (root.left != null) {
        money += (robInternal(root.left.left, memo) + robInternal(root.left.right, memo));
    }
    if (root.right != null) {
        money += (robInternal(root.right.left, memo) + robInternal(root.right.right, memo));
    }
    int result = Math.max(money, robInternal(root.left, memo) + robInternal(root.right, memo));
    memo.put(root, result);
    return result;
}
```

记忆化搜索解决递归超时：

```java
class Solution 
{
    HashMap<TreeNode,Integer> map=new HashMap<>();
    public int rob(TreeNode root) 
    {
        if(!map.containsKey(root))
        {
            if(root==null)
            {
                map.put(root,0);
                return  0;
            }
            if(root.left==null&&root.right==null)
            {
                map.put(root,root.val);
                return root.val;
            }
            int root_select=root.val;
            if(root.left!=null)
            root_select +=rob(root.left.left)+rob(root.left.right);
            if(root.right!=null)
            root_select +=rob(root.right.left)+rob(root.right.right);

            int root_no_select=rob(root.left)+rob(root.right);
            map.put(root,Math.max(root_select,root_no_select));
            return map.get(root);
        }
        else
        return map.get(root);
        
    }
}
```

## 5. **[水壶问题](https://leetcode.cn/problems/water-and-jug-problem/)**

![Untitled](assets/Untitled 72.png)

# 五. DFS与BFS多重解法、转换

## 1.  **[被围绕的区域](https://leetcode.cn/problems/surrounded-regions/)**

![Untitled](assets/Untitled 73.png)

(1) DFS

从非边界O开始搜，找到所有与之相连的O并加入ArrayList,搜完若没有发现边界O，则里面的O都置X

```java
class Solution  //14:48-14:58
{
    boolean find=false;
    ArrayList<Grid> list=new ArrayList<>();
    public void solve(char[][] board) 
    {
        int col=board.length,row=board[0].length;

        int[][] visited=new int[col][row];

        for(int i=0;i<col;i++)
        {
            for(int j=0;j<row;j++)
            {
                if(board[i][j]=='O'&&i>0&&i<col-1&&j>0&&j<row-1&&visited[i][j]==0)
                {
                    list.clear();
                    find=false;
                    dfs(i,j,visited,board);
                    if(!find)
                    {
                        for(Grid g:list)
                        {
                            board[g.x][g.y]='X';
                        }
                    }
                }
            }
        }
    }

    public void dfs(int i,int j,int[][] visited,char[][] board)
    {
        int col=board.length,row=board[0].length;
        if(i<0||i>=col||j<0||j>=row||visited[i][j]==1)
        return;
        if(board[i][j]=='X')
        return;
        visited[i][j]=1;
        list.add(new Grid(i,j));
        if(i==0||i==col-1||j==0||j==row-1)
        find=true;  //找到边界为O的  标记
        dfs(i-1,j,visited,board);
        dfs(i+1,j,visited,board);
        dfs(i,j-1,visited,board);
        dfs(i,j+1,visited,board);
    }
}

class Grid
{
    public int x;
    public int y;
    public Grid(int x,int y)
    {
        this.x=x;
        this.y=y;
    }
}
```

(2) BFS

从边界O开始搜起，凡是与边界O相连的O都加入LinkedList并且标记为#，之后遍历board,还保留的O置X，被标记的#还原成O

```java
class Solution
{
    public class Pos
    {
        int i;
        int j;
        Pos(int i, int j)
        {
            this.i = i;
            this.j = j;
        }
    }
    public void solve(char[][] board)
    {
        if (board == null || board.length == 0) return;
        int m = board.length;
        int n = board[0].length;
        for (int i = 0; i < m; i++)
        {
            for (int j = 0; j < n; j++)
            {
                // 从边缘第一个是o的开始搜索
                boolean isEdge = i == 0 || j == 0 || i == m - 1 || j == n - 1;
                if (isEdge && board[i][j] == 'O')
                    bfs(board, i, j);
            }
        }

        for (int i = 0; i < m; i++)
        {
            for (int j = 0; j < n; j++)   //还保留的O，不和边缘O相连
            {
                if (board[i][j] == 'O')
                {
                    board[i][j] = 'X';
                }
                if (board[i][j] == '#') //标记为#，与边缘O相连
                {
                    board[i][j] = 'O';
                }
            }
        }
    }

    public void bfs(char[][] board, int i, int j)
    {
        Queue<Pos> queue = new LinkedList<>();
        queue.add(new Pos(i, j));
        board[i][j] = '#';
        while (!queue.isEmpty())
        {
            Pos current = queue.poll();
            // 上
            if (current.i - 1 >= 0 && board[current.i - 1][current.j] == 'O')
            {
                queue.add(new Pos(current.i - 1, current.j));
                board[current.i - 1][current.j] = '#';
                // 没有continue.
            }
            // 下
            if (current.i + 1 <= board.length - 1 && board[current.i + 1][current.j] == 'O')
            {
                queue.add(new Pos(current.i + 1, current.j));
                board[current.i + 1][current.j] = '#';
            }
            // 左
            if (current.j - 1 >= 0 && board[current.i][current.j - 1] == 'O')
            {
                queue.add(new Pos(current.i, current.j - 1));
                board[current.i][current.j - 1] = '#';
            }
            // 右
            if (current.j + 1 <= board[0].length - 1 && board[current.i][current.j + 1] == 'O')
            {
                queue.add(new Pos(current.i, current.j + 1));
                board[current.i][current.j + 1] = '#';
            }
        }
    }
}
```

## 2. [**[省份数量](https://leetcode.cn/problems/number-of-provinces/)**]

# 六. 递归

## 1. **[排列序列](https://leetcode.cn/problems/permutation-sequence/)**

![Untitled](assets/Untitled 74.png)

![Untitled](assets/Untitled 75.png)

思路：

1、先求全排列再排序，超时

**2、直接确定数字(不需要求全排列)**

![Untitled](assets/Untitled 76.png)

```java
class Solution //4:24-
{
    public String getPermutation(int n, int k) 
    {
        int[] nums=new int[n];
        for(int i=1;i<=n;i++)
        nums[i-1]=i;
        return solution(nums,k);  
    }

    public String solution(int[] nums, int k) //有序数组nums 返回第k个
    {
        int n=nums.length;
        if(n==1)
        return nums[0]+"";

        int sub=muti(n-1);  //上一层的数量
        int b=k/sub;  
        int c=k%sub;
        
        String s="";
        for(int i=1;i<=n;i++)
        s=s+i;
        int [] rest=new int[n-1];
        if(c==0)
            c=sub;     
        else
            b=b+1; //第1位数字

        int prefix=nums[b-1];  //第1位
        //递归求后续
        System.arraycopy(nums,0,rest,0,b-1);
        System.arraycopy(nums,b,rest,b-1,n-b);

        return prefix+""+solution(rest,c);
    }

    public int muti(int n) //阶乘
    {
        if(n==1)
        return 1;
        else
        return n*muti(n-1);
    }
}
```

![Untitled](assets/Untitled 77.png)

超时代码：

```java
class Solution //10：20-
{
    public String getPermutation(int n, int k) 
    {
        List<List<Integer>> res=get(n); //全排列
        int[] a=new int[res.size()];
        int m=0;
        for(List<Integer> o:res)
        {
            String s="";
            for(Integer i:o)
            s=i+s;
            System.out.println(s);

            a[m++]= Integer.parseInt(s);
            
        }

        Arrays.sort(a);  //排序
        return a[k-1]+"";

    }
    public List<List<Integer>> get(int n)
    {
        if(n==1)
        {
            List<Integer> list=new ArrayList<>();
            list.add(1);
            List<List<Integer>> res=new ArrayList<>();
            res.add(list);

            return res;
        }
        else
        {
            List<List<Integer>> res=new ArrayList<>();
            List<List<Integer>> list=get(n-1);
            for(List<Integer> o:list)
            {
                for(int j=0;j<n;j++)
                {
                    o.add(j,n);
                    res.add(new ArrayList<>(o));
                    o.remove(j);
                }
                
            }
            return res;
        }
    }
}
```

![Untitled](assets/Untitled 78.png)

## 2. 预测赢家（486）

给你一个整数数组 `nums` 。玩家 1 和玩家 2 基于这个数组设计了一个游戏。玩家 1 和玩家 2 轮流进行自己的回合，玩家 1 先手。开始时，两个玩家的初始分值都是 `0` 。每一回合，玩家从数组的任意一端取一个数字（即，`nums[0]` 或 `nums[nums.length - 1]`），取到的数字将会从数组中移除（数组长度减 `1` ）。玩家选中的数字将会加到他的得分上。当数组中没有剩余数字可取时，游戏结束。如果玩家 1 能成为赢家，返回 `true` 。如果两个玩家得分相等，同样认为玩家 1 是游戏的赢家，也返回 `true` 。你可以假设每个玩家的玩法都会使他的分数最大化。

**示例 1：**

```
输入：nums = [1,5,2]
输出：false
解释：一开始，玩家 1 可以从 1 和 2 中进行选择。
如果他选择 2（或者 1 ），那么玩家 2 可以从 1（或者 2 ）和 5 中进行选择。如果玩家 2 选择了 5 ，那么玩家 1 则只剩下 1（或者 2 ）可选。
所以，玩家 1 的最终分数为 1 + 2 = 3，而玩家 2 为 5 。
因此，玩家 1 永远不会成为赢家，返回 false 。
```

**示例 2：**

```
输入：nums = [1,5,233,7]
输出：true
解释：玩家 1 一开始选择 1 。然后玩家 2 必须从 5 和 7 中进行选择。无论玩家 2 选择了哪个，玩家 1 都可以选择 233 。
最终，玩家 1（234 分）比玩家 2（12 分）获得更多的分数，所以返回 true，表示玩家 1 可以成为赢家。
```

**提示：**

- `1 <= nums.length <= 20`
- `0 <= nums[i] <= 107`

法1：递归

```java
class Solution 
{
    public boolean PredictTheWinner(int[] nums) 
    {
        return pure_score(nums,0,nums.length-1)>=0;
    }

    public int pure_score(int[] nums,int i,int j) //面对下标[i,...,j]的盘面当前决策者的最大净得分
    {
        if(i==j)
        return nums[i];
        int s1=nums[i]-pure_score(nums,i+1,j); //选择左边的净得分，剩下的[i+1,j]让对手选（对手也是最佳策略），因此相对自己来说就是负分
        int s2=nums[j]-pure_score(nums,i,j-1); //选择右边的净得分
        return Math.max(s1,s2); //站在上帝视角做最佳选择
    }
}
```

法2：动态规划

![Untitled](assets/Untitled 79.png)

```java
//动态规划:由递归演变
class Solution 
{
    public boolean PredictTheWinner(int[] nums) 
    {
       int n=nums.length;
       int[][] dp=new int[n][n]; //dp[i][j]：面对盘面[i,...，j]最大净得分
       for(int i=0;i<n;i++)
       dp[i][i]=nums[i];
       for(int i=n-2;i>=0;i--)
       {
           for(int j=i+1;j<n;j++)
               dp[i][j]=Math.max(nums[i]-dp[i+1][j],nums[j]-dp[i][j-1]);
       }
       return dp[0][n-1]>=0;
    }

}
```

## 3. 不同的二叉搜索树

[力扣](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

![Untitled](assets/Untitled 80.png)

```jsx
class Solution {
    public List<TreeNode> generateTrees(int n) {
        return generate(1,n);
    }

    public List<TreeNode> generate(int start, int end){
        List<TreeNode> res=new ArrayList<>();
        if(start>end){
            res.add(null);
            return res;
        }
        for(int i=start;i<=end;i++){ // 枚举所有根节点
           List<TreeNode> left=generate(start,i-1);
           List<TreeNode> right=generate(i+1,end);
           for(TreeNode l:left){
            for(TreeNode r:right){
                TreeNode root=new TreeNode(i);
                root.left=l;
                root.right=r;
                res.add(root);
            }
           }
        }
        return res;
    }
}
```

# 七. 正则表达式

## 1. 剑指offer20 表示数值的字符串

![Untitled](assets/Untitled 81.png)

```java
class Solution {
    public boolean isNumber(String s) 
    {
        return s.matches("\\s*[+-]?((\\d+)|(\\d*\\.\\d+)|(\\d+\\.\\d*))([eE][+-]?\\d+)?\\s*");
    }
}
```

## 2.  **字符串转换整数 (atoi)**

![Untitled](assets/Untitled 82.png)

![Untitled](assets/Untitled 83.png)

正则表达式参考：

[Java 正则表达式](https://www.runoob.com/java/java-regular-expressions.html)

双斜杠：java正则表达式表转义。

^[\\+\\-]?\\d+

^ 表示匹配字符串开头，我们匹配的就是 '+'  '-'  号

[] 表示匹配包含的任一字符，比如[0-9]就是匹配数字字符 0 - 9 中的一个

? 表示前面一个字符出现零次或者一次，这里用 ? 是因为 '+' 号可以省略

\\d 表示一个数字 0 - 9 范围

表示前面一个字符出现一次或者多次，\\d+ 合一起就能匹配一连串数字了

```java
//需要导入包
import java.util.regex.*;
class Solution {
    public int myAtoi(String str) {
        //清空字符串开头和末尾空格（这是trim方法功能，事实上我们只需清空开头空格）
        str = str.trim();
        //java正则表达式
        Pattern p = Pattern.compile("^[\\+\\-]?\\d+");
        Matcher m = p.matcher(str);
        int value = 0;
        //判断是否能匹配
        if (m.find()){
            //字符串转整数，溢出
            try {
                value = Integer.parseInt(str.substring(m.start(), m.end()));
            } catch (Exception e){
                //由于有的字符串"42"没有正号，所以我们判断'-'
                value = str.charAt(0) == '-' ? Integer.MIN_VALUE: Integer.MAX_VALUE;
            }
        }
        return value;
    }
}
```

# 八.  位运算

## 1.  **[剑指 Offer 56 - I. 数组中数字出现的次数](https://leetcode.cn/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)**

一个整型数组 `nums`里除两个数字之外，其他数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。

```
示例1：
输入：nums = [4,1,4,6]
输出：[1,6] 或 [6,1]

示例2：
输入：nums = [1,2,10,4,1,4,3,3]
输出：[2,10] 或 [10,2] 
```

**限制：**

- `2 <= nums.length <= 10000`

解题思路：

由于空间复杂度的限制，排除HashSet去重



位运算：

1、异或

$0\oplus N=N(展开为二进制)$ 

$N\oplus N=0$

2、设整型数组 $nums$中出现一次的数字为 $x$，出现两次的数字为$a,a,b,b,...$，即$nums=[a,a,b,b,...,x]$,利用性质1有：
$$
a\oplus a\oplus b\oplus b\oplus ... \oplus x=0\oplus 0\oplus ... \oplus x=x
$$

```java
class Solution {
    public int singleNumber(int[] nums) {
        int res=0;
        for(int i:nums){
            res=res^i;
        }
        return res;
    }
}
```



## 2. **[剑指 Offer 15. 二进制中1的个数](https://leetcode.cn/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)**

![Untitled](assets/Untitled 84.png)

思路：

设n的二进制为：011110001010……11，则判断某一位是否为1可和 该位为1其他位都为0的二进制数相与，若结果不为0则改为1，否则为0.

java中的位运算语法：

```java
a<<i;  //表示a的二进制数左移i位
a>>i;  //右移
```

```java
public class Solution   
{
    public int hammingWeight(int n) 
    {

        int num=0;
        int a=1;
        for(int i=0;i<32;i++)
        {
             if((n & (1<<i)) != 0)   //a<<i  表示a的二进制数左移i位  >>为右移
             num++;   
        }
        return num;
    }
}
```

## 3. **[剑指 Offer 65. 不用加减乘除做加法](https://leetcode.cn/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/)**

![Untitled](assets/Untitled 85.png)

![Untitled](assets/Untitled 86.png)

![Untitled](%E7%AE%97%E6%B3%95%204c486dd119b24f67aff1b9e9dcdd1813/Untitled.jpeg)

```java
class Solution //12:08-
{
    public int add(int a, int b) 
    {

      int sum=a^b; //无进位和
      int acc=(a&b)<<1; //进位  a&b一定要有括号，否则会错
   
      while(acc!=0)
      {
          a=sum^acc;
          b=(sum&acc)<<1;
          sum=a; 
          acc=b;  
      }
      return sum;
    }
}
```

# 九. 双指针

## 1. **[剑指 Offer 25. 合并两个排序的链表](https://leetcode.cn/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/)**

其实就是归并排序中合并的变种，把数组换成了链表（**熟练掌握，15min中内写完，面试常考**）

对比数组的归并排序：[**4、归并排序**](https://www.notion.so/4-fa6151466b4f4e9996a68572a43103d5?pvs=21) 

```java
class Solution   //21:20-21:47
{
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) 
    {
        if(l1==null)
        return l2;
        else if(l2==null)
        return l1;
        else
        {
            ListNode index1=l1,index2=l2,index3=null,res=null;
            while(index1!=null&&index2!=null)
            {
                if(index1.val<index2.val)
                {
                    if(index1==l1&&index2==l2)
                    {
                         res=new ListNode(index1.val);
                         index3=res;
                    }
                    else
                    {
                        index3.next=new ListNode(index1.val);
                        index3=index3.next;
                    }
                    index1=index1.next;
                }
                else
                {
                    if(index1==l1&&index2==l2)
                    {
                         res=new ListNode(index2.val);
                         index3=res;
                    }
                    else
                    {
                        index3.next=new ListNode(index2.val);
                        index3=index3.next;
                    }

                    index2=index2.next;
                }
            }

            if(index1!=null)
                index3.next=index1;
        

            if(index2!=null)
                index3.next=index2;
            
            return res;
        }
    }
}
```

## 2. **[剑指 Offer 57. 和为s的两个数字](https://leetcode.cn/problems/he-wei-sde-liang-ge-shu-zi-lcof/)**

![Untitled](assets/Untitled 87.png)

算法流程：

- 初始化： 双指针 $left,right$分别指向数组 $nums$的左右两端
- 计算和 $s = nums[left] + nums[right]$

 若$s<target$，则$left$向右移；

 若$s>target$，则$right$向左移；

若$s=target$, 返回数组$\{ nums[left],nums[right]\}$。

- 返回空数组

```java
class Solution   //20:11-20:43
{   //20:48-
    public int[] twoSum(int[] nums, int target) 
    {

        if(nums.length==1)
        return new int[0];
        else
        {
            int left=0,right=nums.length-1;
            while(left<right)
            {
                if(nums[left]+nums[right]>target)
                right--;
                else if(nums[left]+nums[right]<target)
                left++;
                else
                    return new int[] {nums[left],nums[right]};
            }
            return new int[0];      //注意返回空数组怎么写  
        }
      
    }
}
```

## 3. **三数之和**

![Untitled](assets/Untitled 88.png)

![Untitled](assets/Untitled 89.png)

先排序

暴力：$O(n^3)$,且list.contains去重耗时较长

双指针：$O(n^2)$

最左边固定，后面两个数用双指针

算法：

（1）变量说明

nums[i]：3个数中的最小数

nums[left]:第2大，从i+1开始

nums[right]：最大数

（2）初始状态

left=i+1

right=nums.length-1

(3)移动策略

对每个固定的i，

sum<0  ：left右移并越过重复的数

sum>0:    right左移并越过重复的数

sum=0：left- -, right++，并越过重复的数

（4）剪枝

nums[i]>0: 直接跳出第一重循环，因为后面的都比它大

i>0&&nums[i]==nums[i-1]:跳过i，防止重复

[力扣](https://leetcode.cn/problems/3sum/solutions/11525/3sumpai-xu-shuang-zhi-zhen-yi-dong-by-jyd/)

![Untitled](assets/Untitled 90.png)

```java
class Solution 
{
    public List<List<Integer>> threeSum(int[] nums) 
    {
        List<List<Integer>>  res=new ArrayList<>();
        Arrays.sort(nums);
        int n=nums.length;
        for(int i=0;i<nums.length-2;i++)
        {
            //剪枝
            if(i>0&&nums[i]==nums[i-1])
            continue;
            if(nums[i]>0)
            break;
            
            int left=i+1;
            int right=n-1;
            while(left<right)
            {
                int sum=nums[i]+nums[left]+nums[right];
                if(sum<0)
                {
                    while(left<right&&nums[left]==nums[left+1])
                    {
                        left++;
                    }
                    left++;
                }
                else if(sum>0)
                {
                    while(left<right&&nums[right-1]==nums[right])
                    {
                        right--;
                    }
                    right--;
                }
                else
                {
                    List<Integer> tem=Arrays.asList(nums[i],nums[left],nums[right]);
                    res.add(tem);
                    while(left<right&&nums[left]==nums[left+1]&&nums[right-1]==nums[right])
                    {
                        left++;
                        right--;
                    }
                    left++;
                    right--;
                }
            }
        }

        return res;

    }
}
```

## 4. **删除有序数组中的重复项**

![Untitled](assets/Untitled 91.png)

[力扣](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/solutions/728105/shan-chu-pai-xu-shu-zu-zhong-de-zhong-fu-tudo/)

```java
class Solution 
	{
    public int removeDuplicates(int[] nums) 
		{
        int n = nums.length;
        if (n == 0) 
            return 0;
        
        int fast = 1, slow = 1;
        while (fast < n) 
				{
            if (nums[fast] != nums[fast - 1]) 
						{
                nums[slow] = nums[fast];
                ++slow;
            }
            ++fast;
        }
        return slow;
    }
}

```

## 5. 环形链表

https://leetcode.cn/problems/linked-list-cycle-ii/description/

给定一个链表的头节点  `head` ，返回链表开始入环的第一个节点。 *如果链表无环，则返回 `null`。*

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（**索引从 0 开始**）。如果 `pos` 是 `-1`，则在该链表中没有环。**注意：`pos` 不作为参数进行传递**，仅仅是为了标识链表的实际情况。

**不允许修改** 链表。

 

**示例 1：**

![img](assets/circularlinkedlist.png)

```
输入：head = [3,2,0,-4], pos = 1
输出：返回索引为 1 的链表节点
解释：链表中有一个环，其尾部连接到第二个节点。
```

**示例 2：**

![img](assets/circularlinkedlist_test2.png)

```
输入：head = [1,2], pos = 0
输出：返回索引为 0 的链表节点
解释：链表中有一个环，其尾部连接到第一个节点。
```

**示例 3：**

![img](assets/circularlinkedlist_test3.png)

```
输入：head = [1], pos = -1
输出：返回 null
解释：链表中没有环。
```

 

**提示：**

- 链表中节点的数目范围在范围 `[0, 104]` 内
- `-105 <= Node.val <= 105`
- `pos` 的值为 `-1` 或者链表中的一个有效索引

 

**进阶：**你是否可以使用 `O(1)` 空间解决此题？



**<font color=red>法1：快慢指针</font>**

![image-20240515085903347](assets/image-20240515085903347.png)

快指针fast一次走2步，慢指针slow一次走1步，若有环必相遇（模拟操场）。设相遇点为B，慢指针所走长度为***l+a***， 即图中OA-> AB； 快指针为***l+n(a+c)+a***, 即快指针比慢指针多走n个环的长度。又因为速度是慢指针的2倍，故***l+n(a+c)+a=2*(l+a)**，-->    ***l=c+(n-1)(a+c)***，即相遇后再额外使用一个指针 current=head，从O出发，慢指针从B出发，二者步幅相同，必相遇于点A。

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */

public class Solution {
    public ListNode detectCycle(ListNode head) {
        if(head==null){
            return null;
        }
        ListNode slow=head,fast=head;
        while(fast!=null&&fast.next!=null&&fast.next.next!=null){
            slow=slow.next;
            fast=fast.next.next;
            if(slow==fast){
                ListNode current=head;  // 另起一指针从O出发
                while(current!=slow){
                    current=current.next;
                    slow=slow.next;
                }
                return slow;
            }
        }
        return null;
    }
}
```



法2：HashSet

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if(head==null){
            return null;
        }
        HashSet<ListNode> set=new HashSet<>();
        ListNode current=head.next;
        set.add(head);
        while(current!=null){
            if(set.contains(current)){
                return current;
            }else{
                set.add(current);
            }
            current=current.next;
        }
        return null;
        
    }
}
```

# 十. 并查集

## 1. **[省份数量](https://leetcode.cn/problems/number-of-provinces/)**

![Untitled](assets/Untitled 92.png)

**并查集知识点请参考**：

[算法 - 并查集 | CS-Notes](http://www.cyc2018.xyz/%E7%AE%97%E6%B3%95/%E5%9F%BA%E7%A1%80/%E7%AE%97%E6%B3%95%20-%20%E5%B9%B6%E6%9F%A5%E9%9B%86.html#%E5%89%8D%E8%A8%80)

```java
class Solution //15:36-15:58
{
    public int findCircleNum(int[][] isConnected) 
    {
        int n=isConnected.length; //节点个数
        UF uf=new UF(n);
        for(int i=0;i<n;i++)
        {
            for(int j=i+1;j<isConnected[0].length;j++)
            {
                if(isConnected[i][j]==1)
                    uf.union(i,j);  //连接节点i  j    
            }
        }
        
        //统计联通分量个数
        HashSet<Integer> set=new HashSet<>();
        for(int i=0;i<uf.id.length;i++)
            set.add(uf.id[i]);
        
        return set.size();

    }
}

class UF //并查集
{

    public int[] id;  //id[i]表示节点i所在的联通分量编号
    public UF(int n) //构造含n个节点的并查集
    {
        id=new int[n]; 
        for(int i=0;i<n;i++)
        id[i]=i;  //初始化：所有节点都孤立，共n个联通分量
    }

    public boolean connected(int p,int q)  //节点p和节点q是否相连
    {
        return find(p)==find(q); 
    }
    public int find(int p) //查找节点p所在联通分量编号
    {
        return id[p];
    }

    public void union(int p,int q) //连接节点p和节点q
    {
        int set_p=find(p);  //p所在联通分量编号
        int set_q=find(q);  //q所在联通分量编号
        if(set_p==set_q)
        return;  //本身就同属一个联通分量，不操作

        for(int i=0;i<id.length;i++)  //将q所在联通分量（集合）中的所有节点的连通分量编号改为set_p
        {
            if(set_q==id[i])
            id[i]=set_p;
        }
    }
}
```

```java
class Solution {
    public int findCircleNum(int[][] isConnected) {
        UF uf=new UF(isConnected.length,isConnected);
        uf.merge();
        return uf.orgCount();
    }
}

class UF{

    int num;
    int cities[];  // 每个城市对应的集合编号
    int[][] connected;  //连接矩阵
    public UF(int num,int[][] connected){
        this.num=num;
        this.cities=new int[num];

        for(int i=0;i<num;i++){
            cities[i]=i;   // 初始化每个城市都不相连
        }
        this.connected=connected;
    }

    public void union(int i,int j){ // 将城市i和j相连
       int org=cities[i];
       for(int k=0;k<num;k++){
        if(cities[k]==org){
            cities[k]=cities[j];
        }
       }
    }

    public void merge(){
        for(int i=0;i<num;i++){
            for(int j=i+1;j<num;j++){
                if(connected[i][j]==1){
                    union(i,j);
                }
            }
        }
    }

    public int orgCount(){
        HashSet<Integer> set=new HashSet<>();
        for(int i:cities){
            set.add(i);
        }
        return set.size();
    }

}
```

## 2. **面试题 17.07. 婴儿名字**

每年，政府都会公布一万个最常见的婴儿名字和它们出现的频率，也就是同名婴儿的数量。有些名字有多种拼法，例如，John 和 Jon 本质上是相同的名字，但被当成了两个名字公布出来。给定两个列表，一个是名字及对应的频率，另一个是本质相同的名字对。设计一个算法打印出每个真实名字的实际频率。注意，如果 John 和 Jon 是相同的，并且 Jon 和 Johnny 相同，则 John 与 Johnny 也相同，即它们有传递和对称性。

在结果列表中，选择 **字典序最小** 的名字作为真实名字。

**示例：**

```
输入：names = ["John(15)","Jon(12)","Chris(13)","Kris(4)","Christopher(19)"], synonyms = ["(Jon,John)","(John,Johnny)","(Chris,Kris)","(Chris,Christopher)"]
输出：["John(27)","Chris(36)"]
```

**提示：**

- `names.length <= 100000`

```java
class Solution  //17:19-18:26  19:31-19:52
{
    public String[] trulyMostPopular(String[] names, String[] synonyms) 
    {
        int m=names.length,n=synonyms.length;
        HashMap<String,Integer> map1=new HashMap<>(); //每个单词出现的次数
        String[] nums=new String[m+2*n];//存点（names+synonyms ）
        HashMap<String,Integer> map2=new HashMap<>();//存nums中单词对应的下标
        int k=0;
        for(;k<names.length;k++)
        {
            String s=names[k];
            int index1=s.indexOf('(');
            String name=s.substring(0,index1);
            int freq=Integer.parseInt(s.substring(index1+1,s.length()-1));
            nums[k]=name;
            map1.put(name,freq);
            map2.put(name,k);
        }
        for(String s:synonyms)
        {
            String[] num=s.split(",");
            String name1=num[0].substring(1);
            String name2=num[1].substring(0,num[1].length()-1);
            if(!map2.containsKey(name1))
            {
                map2.put(name1,k);
                nums[k++]=name1;
            }
            if(!map2.containsKey(name2))
            {
                map2.put(name2,k);
                nums[k++]=name2;
            }
        }
        
        //以names+synonyms中所有的点（去重后）建立并查集
        UF uf=new UF(k); 
        for(String s:synonyms)
        {
            String[] num=s.split(",");
            String name1=num[0].substring(1);
            String name2=num[1].substring(0,num[1].length()-1);
            int index1=map2.get(name1),index2=map2.get(name2);
            uf.union(index1,index2);
        }
        
        //只统计0~m-1（names中的点）
        HashMap<Integer,TreeSet<String>> map3=new HashMap<>();  //连通分量统计
        for(int i=0;i<m;i++)
        {
            int id_scorce=uf.id[i];
            if(!map3.containsKey(id_scorce))
            map3.put(id_scorce,new TreeSet<String>());
            map3.get(id_scorce).add(nums[i]);
        }
        String[] res=new String[map3.size()];
        k=0;
        Set<Map.Entry<Integer,TreeSet<String>>>  ss=map3.entrySet();
        Iterator<Map.Entry<Integer,TreeSet<String>>> ite=ss.iterator();
        while(ite.hasNext())
        {
            Map.Entry<Integer,TreeSet<String>> tem=ite.next();
            TreeSet<String> value=tem.getValue();
            int j=0;
            String name="";
            int sum=0;
            for(String s:value)
            {
                if(j==0)
                name=s;
                j++;
                sum +=map1.get(s);
            }
            res[k++]=name+"("+sum+")";  
        }
        return res;
    }
}

class UF  //并查集
{
    public int[] id;
    public UF(int n) //n个节点
    {
        id=new int[n];
        for(int i=0;i<n;i++)
        id[i]=i;
    }
    public int get_id(int i) //获取节点对应的联通分量编号
    {
        return id[i];
    }
    public void union(int i,int j) //联通节点i和j
    {
        int id1=get_id(i);
        int id2=get_id(j);
        if(id1!=id2) //将id2所属联通分量中的所有点改掉
        {
            for(int k=0;k<id.length;k++)
            {
                if(id[k]==id2)
                id[k]=id1;
            }
        }
    }
}
```

# 十一. 滑动窗口

## 1.  **[滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)**

![Untitled](assets/Untitled 93.png)

![Untitled](assets/Untitled 94.png)

**补优先队列的基础知识：**

元素出队顺序根据优先级从大到小。**优先级由Comparator接口中的compare方法返回值决定。**

示例：

（1) 优先级定义为：`按数组元素大小倒序排列，当元素相同时，下标大的排在前面`

![Untitled](assets/Untitled 95.png)

![Untitled](assets/Untitled 96.png)

（2）优先级定义为：`按数组元素大小倒序排列，当元素相同时，下标小的排在前面`

![Untitled](assets/Untitled 97.png)

![Untitled](assets/Untitled 98.png)

思路：

**维护一个优先队列，队列中的元素为nums数组中的元素和下标**组成的数组：$int$[] $\{nums[i],$$i \}$，优先级定义为根据nums元素值大小倒序排列。因此**每次从队列中移除的都是队列中的最大值**。

算法如下：

（1）将nums数组中前k个数加入队列

（2）窗口右侧从i=k起，一直移动到nums数组末尾。移动过程中取出（但不移除）队列中的最大值，**当最大值落在滑动窗口【i-k+1,i】左侧时，移除最大值，直到它落在窗口内**，此时就是满足要求的最大值。

```java
class Solution 
{
    public int[] maxSlidingWindow(int[] nums, int k) 
    {
        //起一个优先队列，存放的元素是nums数组的值和下标：(nums[i],i) 
        //优先级按元素大小降序排列：队首元素就是最大值
        PriorityQueue<int[]> queue=new PriorityQueue<>(
            new Comparator<int[]>()
            {
                public int compare(int[] o1,int[] o2)
                {
                    return o2[0]-o1[0];
                }
            }
            );

        //将前k个元素加入优先队列，
        for(int i=0;i<k;i++)
        queue.add(new int[]{nums[i],i});

        int[] res=new int[nums.length-k+1];
        res[0]=queue.peek()[0];

        for(int i=k;i<nums.length;i++)
        {
            queue.add(new int[]{nums[i],i});
            //优先队列中的最大值所在下标落在滑动窗口【i-k+1,i】左侧时，将队首元素移除（因为它并不是窗口里的最大值）
            while(queue.peek()[1]<i-k+1)
            {
                queue.remove();
            }
            //队列里的最大元素落在滑动窗口内  
            res[i-k+1]=queue.peek()[0];
        }
        return res;
    }
}
```

## 2. **最小覆盖子串(lc 76)**

给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` 所有字符的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 。

**注意：**

- 对于 `t` 中重复字符，我们寻找的子字符串中该字符数量必须不少于 `t` 中该字符数量。
- 如果 `s` 中存在这样的子串，我们保证它是唯一的答案。

**示例 1：**

```
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
```

**示例 2:**

```
输入: s = "a", t = "aa"
输出: ""
解释: t 中两个字符 'a' 均应包含在 s 的子串中，
因此没有符合条件的子字符串，返回空字符串。
```

**提示：**

- `1 <= s.length, t.length <= 10^5`
- `s` 和 `t` 由英文字母组成

![76_fig1 00_00_00-00_00_30.gif](%E7%AE%97%E6%B3%95%204c486dd119b24f67aff1b9e9dcdd1813/76_fig1_00_00_00-00_00_30.gif)

**滑动窗口**：两个指针，**一个用于延伸现有窗口的 r 指针，一个用于收缩窗口的 l 指针**。在任意时刻，只有一个指针运动，另一个保持静止。我们在 s上滑动窗口，通过移动 r指针不断扩张窗口。当窗口包含 t 全部所需的字符后，如果能收缩，我们就收缩窗口直到得到最小窗口。

判断当前的窗口包含 t 所有字符：用哈希表统计字母出现次数

```java
class Solution
{
    Map<Character, Integer> winMap = new HashMap<Character, Integer>();  //滑动窗口
    Map<Character, Integer> tMap = new HashMap<Character, Integer>();  //t字符串
    public String minWindow(String s, String t)
    {
        //统计t字符串每个字符的个数
        for (int i = 0; i < t.length(); i++)
        {
            if(tMap.containsKey(t.charAt(i)))
                tMap.put(t.charAt(i),tMap.get(t.charAt(i))+1);
            else
                tMap.put(t.charAt(i),1);
        }

        //初始时  l r 置默认值
        int l = 0, r = -1;  //窗口左右边界
        int min_win = Integer.MAX_VALUE, resL = -1, resR = -1;  //最小窗口

        while (r < s.length())
        {
            r++;  //向右拓展
            if (r < s.length() && tMap.containsKey(s.charAt(r)))
                winMap.put(s.charAt(r), winMap.getOrDefault(s.charAt(r), 0) + 1);

            while (valid() && l <= r) //满足条件,窗口收缩,左指针右移
            {
                if (r - l + 1 < min_win)  //更新满足条件的最小窗口
                {
                    min_win = r - l + 1;
                    resL = l;
                    resR = r + 1;
                }
                if (tMap.containsKey(s.charAt(l)))
                    winMap.put(s.charAt(l), winMap.get(s.charAt(l)) - 1);

                l++; //收缩
            }
        }
        return resL == -1 ? "" : s.substring(resL, resR);
    }

    public boolean valid()
    {
        Set<Map.Entry<Character, Integer>> set=tMap.entrySet();
        Iterator<Map.Entry<Character, Integer>> ite=set.iterator();
        while (ite.hasNext())
        {
            Map.Entry<Character, Integer> tem = ite.next();
            if (!winMap.containsKey(tem.getKey())||winMap.get(tem.getKey())<tem.getValue())
                return false;
        }
        return true;
    }
}
```



官方解答：类似于上面这种写法时间复杂度也只能击败13%  (上面写法656ms，官解235ms)，关键是每移动一个都字符都调valid将tMap里的字符从头遍历到尾

![image-20240706084745853](assets/image-20240706084745853.png)



![image-20240706084420839](assets/image-20240706084420839.png)



优化：找到第一个覆盖字串后valid判断滑动窗口内的字符串是否为覆盖字串只需上一个变动的字母

```java
class Solution
{
    Map<Character, Integer> winMap = new HashMap<Character, Integer>();  //滑动窗口内字符统计
    Map<Character, Integer> tMap = new HashMap<Character, Integer>();  //t字符串
    boolean exist=false;
    public String minWindow(String s, String t)
    {
        //统计t字符串每个字符的个数
        for (int i = 0; i < t.length(); i++){
            tMap.put(t.charAt(i),tMap.getOrDefault(t.charAt(i),0)+1); 
        }

        //初始时  l r 置默认值
        int l = 0, r = -1;  //窗口左右边界
        int min_win = Integer.MAX_VALUE, resL = -1, resR = -1;  //最小窗口
        char c='0';
        while (r < s.length()){  // 拓展右边界
            r++;  //向右拓展
            if (r < s.length() && tMap.containsKey(s.charAt(r))){
                winMap.put(s.charAt(r), winMap.getOrDefault(s.charAt(r), 0) + 1);
                
                while (l <= r&&valid(c)) {//找到覆盖字串,收缩窗口左边界，左指针右移
                    exist=true;
                    if (r - l + 1 < min_win) { //更新满足条件的最小窗口
                        min_win = r - l + 1;
                        resL = l;
                        resR = r + 1;
                    }
                    if (tMap.containsKey(s.charAt(l))){
                        winMap.put(s.charAt(l), winMap.get(s.charAt(l)) - 1);
                    }                     
                    l++; //左边界向右收缩
                    c=s.charAt(l-1); // 下一次valid判断时只需判断字符c的情况即可（左边界往右收缩过程中只有它在变动）
                }
               
                if(l>0){
                // s = "ADOBECODEBANC", t = "ABC" 第一次匹配到ADOBEC后，往右收缩左边界l=1,指向D，A字母缺少，因此往右扩张右边界只需要判断A字母的情即可
                    c=s.charAt(l-1);  
                }else{
                    exist=false;
                }   
            }
        }
        return resL == -1 ? "" : s.substring(resL, resR);
    }

    // 判断窗口内的字符串是否为t的覆盖字串
    public boolean valid(char c){
        if(!exist){  // 还没找到第一个满足条件的覆盖字串
            Set<Map.Entry<Character, Integer>> set=tMap.entrySet();
            Iterator<Map.Entry<Character, Integer>> ite=set.iterator();
            while (ite.hasNext()){
                Map.Entry<Character, Integer> tem = ite.next();
                if (!winMap.containsKey(tem.getKey())||winMap.get(tem.getKey())<tem.getValue())
                    return false;
            }
            return true;
        }else{ // 找到了满足条件的覆盖字串，收缩边界过程中只需要判断变动的字符
            return tMap.get(c)==null||(tMap.get(c)!=null&&winMap.get(c)>=tMap.get(c));
        }
    }
}
```

优化后耗时44ms，击败35%，感觉得找其他方法

![image-20240706084248488](assets/image-20240706084248488.png)





## 3. [合并 K 个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/)

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

**示例 1：**

```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

**示例 2：**

```
输入：lists = []
输出：[]
```

**示例 3：**

```
输入：lists = [[]]
输出：[]
```

**提示：**

- `k == lists.length`
- `0 <= k <= 10^4`
- `0 <= lists[i].length <= 500`
- `10^4 <= lists[i][j] <= 10^4`
- `lists[i]` 按 **升序** 排列
- `lists[i].length` 的总和不超过 `10^4`

思路：使用优先队列存链表，按链表头节点由小到大排列，因此queue的队首链表的头节点对应的值必然最小。当队列不为空时循环：移除队首链表ListNode tem，取出头节点加到结果ListNode res的尾部，tem=tem.next，若tem不为null，重新将tem加入队列。

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        int n=lists.length;
        ListNode[] currentNode=lists;
        PriorityQueue<ListNode> queue=new PriorityQueue<>((o1,o2)->{
            return o1.val-o2.val;
        });

        for(ListNode node:lists){
            if(node!=null){
                queue.add(node);
            }
        }

        ListNode res=null,current=res;
        while(!queue.isEmpty()){
            ListNode tem=queue.remove();
            if(res==null){
                res=new ListNode(tem.val);
                current=res;
            }else{
                current.next=new ListNode(tem.val);
                current=current.next;
            }
            tem=tem.next;
            if(tem!=null){
                queue.add(tem);
            }
        }
        return res;
    }
}
```



## 4. [长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

给定一个含有 `n` 个正整数的数组和一个正整数 `target` **。**

找出该数组中满足其总和大于等于 `target` 的长度最小的 

**子数组**

`[numsl, numsl+1, ..., numsr-1, numsr]` ，并返回其长度**。**如果不存在符合条件的子数组，返回 `0` 。

**示例 1：**

```
输入：target = 7, nums = [2,3,1,2,4,3]
输出：2
解释：子数组 [4,3] 是该条件下的长度最小的子数组。
```

**示例 2：**

```
输入：target = 4, nums = [1,4,4]
输出：1
```

**示例 3：**

```
输入：target = 11, nums = [1,1,1,1,1,1,1,1]
输出：0
```

 

**提示：**

- `1 <= target <= 109`
- `1 <= nums.length <= 105`
- `1 <= nums[i] <= 105`

 

**进阶：**

- 如果你已经实现 `O(n)` 时间复杂度的解法, 请尝试设计一个 `O(n log(n))` 时间复杂度的解法。



定义两个指针 $start 和end$ 分别表示子数组（滑动窗口窗口）的开始位置和结束位置，维护变量 $sum$ 存储子数组中的元素和（即从 $nums[start]$ 到 $nums[end]$ 的元素和）。

初始状态下，$start$ 和 $end$ 都指向下标 0，$sum$ 的值为 0。

每一轮迭代，将$ nums[end]$ 加到 $sum$，如果$ sum≥target$，则更新子数组的最小长度（此时子数组的长度是 $end−start+1$，然后将 $nums[start]$ 从 $sum$中减去并将 $start$右移，直到$ sum<target$，在此过程中同样更新子数组的最小长度。在每一轮迭代的最后，将 $end$ 右移。



![image-20240603001616003](assets/image-20240603001616003.png)



![image-20240603001710521](assets/image-20240603001710521.png)

![image-20240603001727863](assets/image-20240603001727863.png)



![image-20240603001744095](assets/image-20240603001744095.png)



![image-20240603001801998](assets/image-20240603001801998.png)

![image-20240603001816061](assets/image-20240603001816061.png)

![image-20240603001832342](assets/image-20240603001832342.png)

![image-20240603001845815](assets/image-20240603001845815.png)



![image-20240603001900373](assets/image-20240603001900373.png)

![image-20240603001914309](assets/image-20240603001914309.png)

![image-20240603001928429](assets/image-20240603001928429.png)

![image-20240603001942141](assets/image-20240603001942141.png)



```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int n=nums.length;
        long sum=0;
        int start=0,end=0,res=Integer.MAX_VALUE;

        while(end<n){
            sum +=nums[end];
            while(sum>=target){
                res=Math.min(res,end-start+1);
                sum -=nums[start];
                start++;
            }
            end++;
        }
        return res==Integer.MAX_VALUE?0:res;
    }  
}
```





# 十二. 桶

## 1. **[任务调度器](https://leetcode.cn/problems/task-scheduler/)**

![Untitled](assets/Untitled 99.png)

![Untitled](assets/Untitled 100.png)

思路：

从数量最多的任务开始考虑

[力扣](https://leetcode.cn/problems/task-scheduler/solution/tong-zi-by-popopop/)

```java
class Solution 
{
    public int leastInterval(char[] tasks, int n) 
    {
         if (tasks==null || tasks.length==0)
            return 0;
        
        int len = tasks.length;
      //统计任务的频率
        int[] counter = new int[26];
        for (int i = 0; i < len; i++) 
            counter[tasks[i]-'A']++;
        
        //从頻率最高的开始处理，它满足了间距其他都能满足
        Arrays.sort(counter);
        //行數,貪心 將頻率最大的任務，排成一列，中間間隔 n
        //贪心策略，尽可能让cpu每个周期执行满任务，不要有空闲存在
        //使cpu利用率达到最高
        /*
            AAABBB
            n=2            这里要等待 2 个时间片，所以是两列
     一个cpu周期        A | B  N
     一个cpu周期        A | B  N
     一个cpu周期        A | B
         */
        int row = counter[25]-1;  //0全到前面去了 取最大频率
        //空格总数
        int space = row*n;
        for (int i = 24; i >=0 ; i--)  //填坑
            space-=Math.min(counter[i],row);
        
        return space>0?space+tasks.length:tasks.length;
    }
}
```

# 十三. 有向图的拓扑排序

## 1. [课程表 II](https://leetcode.cn/problems/course-schedule-ii/description/?favorite=2ckc81c#:~:text=210.-,%E8%AF%BE%E7%A8%8B%E8%A1%A8%20II,-%E4%B8%AD%E7%AD%89)

![Untitled](assets/Untitled 101.png)

![Untitled](assets/Untitled 102.png)

思路：参考官方题解法2

[力扣](https://leetcode.cn/problems/course-schedule-ii/solutions/249149/ke-cheng-biao-ii-by-leetcode-solution/)

有向图的拓扑排序

![Untitled](assets/Untitled 103.png)

**有向图拓扑排序定义：**

一个包含 $n$个节点的有向图 $G$，给出节点编号的一种排列，若满足：

对于 G 中的任意一条有向边 $(u,v)$，在排列中$u$都出现在$v$的前面。

则称该排列是图 G的拓扑排序。

1、若图中存在环，则拓扑排序的种类为0

2、满足条件的拓扑排序种类可能不止一种，例如当图中没有任何边时，任意排列都满足。

因此可以对该问题建模：

1、将$n$ 门课程建模为$n$个节点，节点节点编号0~n-1，依赖修课程0前必须先修课程1建模为存在一条有向边$(1,0)$

2、根据依赖建图

用$List<List<Integer>> edges$存有向边，$edges.get(i)$表示节点$i$指向的节点列表

用数组$int[]\quad in\_degree=new\quad int[n]$存每个节点的入度

用$int[]\quad res=new\quad int[n]$存结果

3、bfs遍历该有向图

```java
LinkedList<Integer> list=new Linkedlist<>();
```

遍历$in\_degree$，将所有入度为0的节点加入list

然后bfs逐层遍历，弹出最前面的入度为0的节点，存入res数组(下标index从0开始)，并将该节点从图中删去，所有它指向节点的入度减1，若更改后存在新的入度为0的节点，将其加入list

4、$index!=n$表明有环，存在节点入度永不为0，返回空数组

否则返回res

```java
class Solution 
{
    public int[] findOrder(int numCourses, int[][] prerequisites) 
    {
        //图的拓扑排序  bfs解决，(u,v)表示从u到v的有向边，课程建模成顶点，设修课程A之前要先修课程B，则用有向边(B,A)建模
        //当一个顶点的入度为0时表示它没有先修课程要求，因此可以排在前面
        //之后将入度为0的顶点从图中去掉，同时去掉与之相连的顶点之间的有向边，判断与之相连的顶点入度是否为0，若为0加入LinedList

        List<List<Integer>> edges=new ArrayList<>(); //存储有向边
        int[] in_degree=new int[numCourses]; //存储顶点的入度
        int[] res=new int[numCourses]; //存储结果 

        for(int i=0;i<numCourses;i++)
            edges.add(new ArrayList<Integer>());  //初始化：所有顶点孤立
        
       //建图，将依赖建模成有向边
        for(int i=0;i<prerequisites.length;i++)
        {
            edges.get(prerequisites[i][1]).add(prerequisites[i][0]); 
            in_degree[prerequisites[i][0]]++; 
        }

        LinkedList<Integer> list=new LinkedList<>();

        for(int i=0;i<numCourses;i++)
        {
            if(in_degree[i]==0)//将入度为0的节点加入队列
            list.add(i);
        }
        int index=0;  //从下标0开始记录 入度为0的节点 对应res数组的构建

        //bfs
        while(!list.isEmpty())
        {
            Integer tem=list.remove(); //弹出入度为0的节点
            res[index++]=tem;
            //节点tem指向的节点列表  
            List<Integer> direct_nodes=edges.get(tem); 
            //删除节点tem 并将与之相连的边删除
            for(Integer o:direct_nodes)
            {
                in_degree[o]--;
                if(in_degree[o]==0)  //将新的入度为0的节点加入list
                list.add(o);
            }
        }
        if(index!=numCourses)  //到不了numCourses,说明有环，有的节点入度永远不为0
        return new int[]{};
        else
        return res;
    }
}
```

## 2. 有向无环图节点的所有祖先

![Untitled](assets/Untitled 104.png)

![Untitled](assets/Untitled 105.png)

![Untitled](assets/Untitled 106.png)

法1：思路与课程表2类似 

从入度为0的节点开始bfs遍历

```java
class Solution 
{
    public List<List<Integer>> getAncestors(int n, int[][] edges) 
    {
        //建入度数组
        int[] in_degree=new int[n];
        List<List<Integer>> edge_list=new ArrayList<>();// 第i个节点指向的节点列表

        for(int i=0;i<n;i++)
        edge_list.add(new ArrayList<>());

        for(int[] i:edges) //建图
        {
            edge_list.get(i[0]).add(i[1]);
            in_degree[i[1]]++;
        }

        List<TreeSet<Integer>> res=new ArrayList<>(); //结果
        for(int i=0;i<n;i++)
        res.add(new TreeSet<>());

        LinkedList<Integer> list=new LinkedList<>();

        //bfs从入度为0的节点开始遍历
        for(int i=0;i<n;i++)
        {
            if(in_degree[i]==0)
            list.add(i);
        }
        while(!list.isEmpty())
        {
            Integer tem=list.remove();
            for(Integer o:edge_list.get(tem))
            {
                //从图中去除tem节点
                in_degree[o]--;  
                if(in_degree[o]==0)
                list.add(o);
                //更新tem指向节点的祖先节点列表
                TreeSet<Integer> t=res.get(tem);
                if(t.size()!=0)
                {
                    for(Integer z:t)
                    res.get(o).add(z);
                }
                res.get(o).add(tem);     
            }
        }

        List<List<Integer>> res1=new ArrayList<>(); //结果，TreeSet转化为List
        for(int i=0;i<n;i++)
        res1.add(new ArrayList<>());
        int j=0;
        for(TreeSet<Integer> o:res)
        {
            if(o.size()!=0)
            {
                for(Integer k:o)
                res1.get(j).add(k);
            }
            j++;
        }

        return res1;
    }
}
```

![Untitled](assets/Untitled 107.png)

法2：

从节点一层一层反向推，每个节点都要bfs一次，超时

![Untitled](assets/Untitled 108.png)

```java
class Solution 
{
    public List<List<Integer>> getAncestors(int n, int[][] edges) 
    {
        //建入度数组
        int[] in_degree=new int[n];
        List<List<Integer>> edge_list=new ArrayList<>();// 指向第i个节点的节点列表

        for(int i=0;i<n;i++)
        edge_list.add(new ArrayList<>());

        for(int[] i:edges)
        {
            edge_list.get(i[1]).add(i[0]);
            in_degree[i[1]]++;
        }

        List<List<Integer>> res=new ArrayList<>(); //结果
        for(int i=0;i<n;i++)
        res.add(new ArrayList<>());

        for(int i=0;i<n;i++)
        {
            if(in_degree[i]!=0)
            {
                TreeSet<Integer> set=new TreeSet<>();
                List<Integer> pre=edge_list.get(i);
                LinkedList<Integer> linklist=new LinkedList<>();
                for(Integer o:pre)
                linklist.add(o);
                while(!linklist.isEmpty())
                {
                    Integer tem=linklist.remove();
                    set.add(tem);
                    List<Integer> ppre=edge_list.get(tem);
                    if(ppre.size()!=0)
                    {
                        for(Integer oo:ppre)
                        linklist.add(oo);
                    }
                }
                for(int ooo:set)
                res.get(i).add(ooo);
  
            }
        }

        return res;

    }
}
```

# 十四. 未分类

## 1. **插入区间**

![Untitled](assets/Untitled 109.png)

算法思路：

1、遍历$intervals$中的每个区间$intervals[i]$

2、

（1）若$newInterval[0]>intervals[i][1]$，说明插入的区间在当前区间右侧，将当前区间加入结果

（2）若$newInterval[1]>intervals[i][0]$，说明插入的区间在当前区间左侧，退出循环，后面的不会有交集

（3）否则，当前区间必与插入区间相交，两区间合并（更新newInterval）

```java
newInterval[0]=Math.min(newInterval[0],intervals[i][0]);
newInterval[1]=Math.max(newInterval[1],intervals[i][1]);
```

```java
class Solution 
{
    public int[][] insert(int[][] intervals, int[] newInterval) 
    {
        int n=intervals.length;
        int[][] res=new int[n+1][2];  //合并后的区间个数最大为n+1
        int j=0;
        int i=0;
        for(;i<n;i++)
        {
            if(newInterval[0]>intervals[i][1]) //在插入区间的左侧，直接加入
            res[j++]=intervals[i];  
            else if(newInterval[1]<intervals[i][0]) //在插入区间的右侧，退出循环
                break;
            else  //相交  更新newInterval
            {
                newInterval[0]=Math.min(newInterval[0],intervals[i][0]);
                newInterval[1]=Math.max(newInterval[1],intervals[i][1]);
            }
        }

        res[j++]=newInterval; 
        for(int k=i;k<n;k++)  //将后续区间加入
        res[j++]=intervals[k];

        int[][] res1=new int[j][2]; //取出返回
        for(int m=0;m<j;m++)
        res1[m]=res[m];
        return res1;
     
    }

}
```

# 十五. 双链表

多练练，总用ArrayList接会生疏，还是经常指来指去

## 1.  **设计浏览器历史记录**

![Untitled](assets/Untitled 110.png)

![Untitled](assets/Untitled 111.png)

![Untitled](assets/Untitled 112.png)

```java
class BrowserHistory 
{
    Node  head;
    Node current;

    public BrowserHistory(String homepage) 
    {
        head=new Node(homepage);
        current=head;
    }
    
    public void visit(String url) 
    {
        current.next=new Node(url);
        current.next.previous=current;    
        current=current.next;
    }
    
    public String back(int steps) 
    {
        int k=0;
        while(current!=head&&k<steps)
        {
            current=current.previous;
            k++;
        }
        return current.val;

    }
    
    public String forward(int steps) 
    {
        int k=0;
        while(current.next!=null&&k<steps)
        {
            current=current.next;
            k++;
        }
        return current.val;

    }
}

class Node //双向链表节点
{
    public String val;
    public Node next;
    public Node previous;

    public Node(String val)
    {
        this.val=val;
    }
}
```



## 2.  LRU缓存（阿里社招二面手撕）

请你设计并实现一个满足 [LRU (最近最少使用) 缓存](https://baike.baidu.com/item/LRU) 约束的数据结构。

实现 `LRUCache` 类：

- `LRUCache(int capacity)` 以 **正整数** 作为容量 `capacity` 初始化 LRU 缓存
- `int get(int key)` 如果关键字 `key` 存在于缓存中，则返回关键字的值，否则返回 `-1` 。
- `void put(int key, int value)` 如果关键字 `key` 已经存在，则变更其数据值 `value` ；如果不存在，则向缓存中插入该组 `key-value` 。如果插入操作导致关键字数量超过 `capacity` ，则应该 **逐出** 最久未使用的关键字。

函数 `get` 和 `put` 必须以 `O(1)` 的平均时间复杂度运行。

 

**示例：**

```
输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4
```

 

**提示：**

- `1 <= capacity <= 3000`
- `0 <= key <= 10000`
- `0 <= value <= 105`
- 最多调用 `2 * 105` 次 `get` 和 `put`



关键：

- 双向链表+HashMap实现（LinkedHashMap）
- 双向链表的节点Node存: key、value，前驱prev，后继next；借助HashMap<key, Node> 保证时间复杂度为O（1）
- 每次访问（get或put操作）都将该节点移动到头节点，以确保尾节点是最久未访问的。当size超过容量capacity时直接删除尾节点和map里对应的键值对即可。

```java
class LRUCache 
{
    private int capacity;
    int size;
    Node head;
    Node tail;
    class Node{
        public int key;
        public int value;
        public Node prev;
        public Node next;
        public Node(){
        }
        public Node(int key,int value){
            this.key=key;
            this.value=value;
        }
    }

    HashMap<Integer,Node> cache=new HashMap<>();
    public LRUCache(int capacity) 
    {
       this.capacity=capacity;
       this.size=0;
       // 创建两个哑节点，省去判空
       this.head=new Node();
       this.tail=new Node();
       this.head.next=this.tail;
       this.tail.prev=this.head;
    }
    
    public int get(int key) 
    {
        Node node=cache.get(key);
        if(node==null){
            return -1;
        }else{
            // 删除并移到头节点
            deleteNode(node);
            addToHead(node);
            return node.value;
        }
    }

    public void addToHead(Node node){
        head.next.prev=node;
        node.next=head.next;
        head.next=node;
        node.prev=head;
    }

    public void deleteNode(Node node){
        node.prev.next=node.next;
        node.next.prev=node.prev;
    }
    
    public void put(int key, int value) 
    {
        Node node=cache.get(key);
        Node tem=null;
        if(node==null){
            tem=new Node(key,value);
            size++;
        }else{  // 更新值
        node.value=value;
        tem=node;
        //  删除该节点
        deleteNode(node);
        }

        //加到头节点
        addToHead(tem);
        cache.put(key,tem);
        if(size>capacity){ // 超出最大容量,移除尾节点
        Node delete=tail.prev;
        deleteNode(delete);
        delete.next=null;
        delete.prev=null;
        cache.remove(delete.key);
        size--;
        }
    }
}
```





# 十六. 单调栈

## 1.  **移掉 K 位数字**

![Untitled](assets/Untitled 113.png)

## 2.  [每日温度](https://leetcode.cn/problems/daily-temperatures/)

给定一个整数数组 `temperatures` ，表示每天的温度，返回一个数组 `answer` ，其中 `answer[i]` 是指对于第 `i` 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。**要求时间复杂度$O(n)$**

**示例 1:**

```
输入: temperatures = [73,74,75,71,69,72,76,73]
输出: [1,1,4,2,1,1,0,0]
```

**示例 2:**

```
输入: temperatures = [30,40,50,60]
输出: [1,1,1,0]
```

**示例 3:**

```
输入: temperatures = [30,60,90]
输出: [1,1,0]
```

 

**提示：**

- `1 <= temperatures.length <= 105`
- `30 <= temperatures[i] <= 100`





思路：**单调栈**

用栈存储尚未找到下一次温度更高 元素的下标

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int n=temperatures.length;
        int[] res=new int[n];
        LinkedList<Integer> stack=new LinkedList<>(); //栈里存尚未找到下一次温度更高 元素的下标
        for(int i=0;i<n;i++){
            while(!stack.isEmpty()&&temperatures[i]>temperatures[stack.peek()]){
                res[stack.peek()]=i-stack.pop();  // 当前温度大于它，出栈并计算天数
            }
            stack.push(i);
        }
        return res;
    }
}
```



模拟：$temperatures = [73,74,75,71,69,72,76,73]$

![image-20240624171707040](assets/image-20240624171707040.png)

注意：LinkedList没加锁，比Stack快，Stack加了锁（继承自Vector，线程安全）。

使用LinkedList：

<img src="assets/image-20240624171959082.png" alt="image-20240624171959082" style="zoom: 50%;" />

使用Stack:

<img src="assets/image-20240624171930407.png" alt="image-20240624171930407" style="zoom:50%;" />

<img src="assets/image-20240624171843511.png" alt="image-20240624171843511" style="zoom:50%;" />



## 3. [柱状图中最大的矩形](https://leetcode.cn/problems/largest-rectangle-in-histogram/)

给定 *n* 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

求在该柱状图中，能够勾勒出来的矩形的最大面积。

 

**示例 1:**

![img](assets/histogram.jpg)

```
输入：heights = [2,1,5,6,2,3]
输出：10
解释：最大的矩形为图中红色区域，面积为 10
```

**示例 2：**

![img](assets/histogram-1.jpg)

```
输入： heights = [2,4]
输出： 4
```

 

**提示：**

- `1 <= heights.length <=105`
- `0 <= heights[i] <= 104`



暴力枚举引入：

法1：宽固定，求最小高

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int n=heights.length;
        int res=0;
        for(int i=0;i<n;i++){
            int minHeight=heights[i];
            for(int j=i;j>=0;j--){
                minHeight=Math.min(minHeight,heights[j]);
                res=Math.max(minHeight*(i-j+1),res);
            }
        }
        return res;

    }
}
```



法2：高固定，左右外扩求最大宽

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int n=heights.length;
        int res=Integer.MIN_VALUE;
        for(int i=0;i<n;i++){
            // 向左扩散求左边界
            int leftIndex=i-1;
            while(leftIndex>=0&&heights[leftIndex]>=heights[i]){
                leftIndex--;
            }
            // 向右扩散求右边界
            int rightIndex=i+1;
            while(rightIndex<n&&heights[rightIndex]>=heights[i]){
                rightIndex++;
            }
            // 求面积
            res=Math.max(res,(rightIndex-leftIndex-1)*heights[i]);  
        }
        return res;
    }
}
```



上面两种暴力枚举时间复杂度都是$O(n^2)$



下面基于 法2，使用单调栈进行优化到$O(n)$

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int n=heights.length;
        // 固定柱子高度height[i]，以它为中心向左右外扩，下标left[i]+1~right[i]-1的柱子高度都不小于height[i]
        // 面积为height[i]*(right[i]-left[i]-1)
        int[] left=new int[n]; //left[i]：小于柱子高度height[i]的左边界下标  
        int[] right=new int[n]; //right[i]：小于柱子高度height[i]的右边界下标 
        LinkedList<Integer> list=new LinkedList<>();
        for(int i=0;i<n;i++){
            while(!list.isEmpty()&&heights[list.peek()]>=heights[i]){ // 将左边大于等于heights[i]的都弹出
                list.pop();
            }
            left[i]=list.isEmpty()?-1:list.peek(); // 左边界
            list.push(i);
        }

        list.clear();
        for(int i=n-1;i>=0;i--){
            while(!list.isEmpty()&&heights[list.peek()]>=heights[i]){ // 将右边大于等于heights[i]的都弹出
                list.pop();
            }
            right[i]=list.isEmpty()?n:list.peek(); // 右边界
            list.push(i);
        }

        int res=Integer.MIN_VALUE;
        for(int i=0;i<n;i++){
            res=Math.max(res,(right[i]-left[i]-1)*heights[i]);  // 面积
        }
        return res;
    }
}
```





# 十七. 栈

## 1. 有效的括号

![Untitled](assets/Untitled 114.png)

思路：

新建HashMap，键为右括号，值为对应的左括号。

使用栈，遍历字符串中每个字符，当当前字符为左括号时入栈，否则进行匹配：看栈顶元素是否为对应的左括号，如果不是直接返回false；若是则弹出栈顶元素，这一对成功匹配。遍历完satck为空则字符串有效，否则无效。

举例出入栈过程：

( [  ] ) { }

![Untitled](assets/Untitled 115.png)

```java
class Solution 
{
    public boolean isValid(String s) 
    {
        HashMap<Character,Character> map=new HashMap<>(); //键：右括号  值：对应的左括号
        map.put(')','(');
        map.put('}','{');
        map.put(']','[');

        if(s.length()%2!=0)
        return false;
        Stack<Character> stack=new Stack<>(); //栈中只存左括号，遇到当前元素是右括号时进行匹配：弹出栈顶对应的左括号

        for(int i=0;i<s.length();i++)
        {
            char c=s.charAt(i);
            if(!map.containsKey(c)) //当前为左括号
                stack.push(c);
            else                    //当前为右括号
            {
                if(stack.isEmpty()||stack.peek()!=map.get(c)) //栈顶左括号与当前右括号不匹配
                return false;
                stack.pop();  //匹配 ，则弹出对应左括号
            }
        }
        return stack.isEmpty();     
    }
}
```





# 十八. 贪心

## 1. 最小会议室数量（联通数科笔试）

[算法训练--最少会议室_java萌新凌的博客-CSDN博客_最少会议室](https://blog.csdn.net/qq_57205114/article/details/123899267?csdn_share_tail=%7B%22type%22%3A%22blog%22%2C%22rType%22%3A%22article%22%2C%22rId%22%3A%22123899267%22%2C%22source%22%3A%22unlogin%22%7D)

# 十九. 排序+二分变种

1、**供暖器（475）**

冬季已经来临。 你的任务是设计一个有固定加热半径的供暖器向所有房屋供暖。在加热器的加热半径范围内的每个房屋都可以获得供暖。现在，给出位于一条水平线上的房屋 `houses` 和供暖器 `heaters` 的位置，请你找出并返回可以覆盖所有房屋的最小加热半径。

**说明**：所有供暖器都遵循你的半径标准，加热的半径也一样。

![Untitled](assets/Untitled 116.png)

![Untitled](assets/Untitled 117.png)

思路：先对heaters排序，然后针对每个houses[i]，使用二分查找（变种）找到离它最近的炉子，结果为所有半径的最大值。

```jsx
//排序+二分（变种）：先对heaters排序，然后对每个houses[i]找离它最近的炉子
class Solution 
{
    public int findRadius(int[] houses, int[] heaters) 
    {

        int res=Integer.MIN_VALUE;
        int n=heaters.length-1;
        Arrays.sort(heaters);

        for(int i=0;i<houses.length;i++)
        {
            if(houses[i]<=heaters[0])
            res=Math.max(res,heaters[0]-houses[i]);
            else if(houses[i]>=heaters[n])
            res=Math.max(res,houses[i]-heaters[n]);
            else
            {
                int left=find(heaters,houses[i]);
                int distance=Math.min(houses[i]-heaters[left],heaters[left+1]-houses[i]);
                res=Math.max(res,distance);
            }
        }  
        return res;
    }

    public int find(int[] heaters,int target) //target在heaters范围内用二分
    {
        int left=0,right=heaters.length-1;
        while(left<=right)
        {
            int med=(int)(left+right)/2;
            if(target<heaters[med])
            right=med-1;
            else if(target>heaters[med])
            left=med+1;
            else
            return med;
        }
        return right; //返回靠近target偏小的那个的下标
    }
}
```

# 二十. 最短路径

## 1. **前往目标的最小代价（2662）**

![Untitled](assets/Untitled 118.png)

![Untitled](assets/Untitled 119.png)

![Untitled](assets/Untitled 120.png)

Dijkstra算法： [**Dijkstra算法**](https://www.notion.so/Dijkstra-e30f0a3dc1b04d2eac90220af1df132d?pvs=21) 

# 二一. 状态压缩

## 1.  **贴纸拼词（691）**

[https://leetcode.cn/problems/stickers-to-spell-word/description/](https://leetcode.cn/problems/stickers-to-spell-word/description/)



# 二二. 大小堆

## 1. [数据流的中位数](https://leetcode.cn/problems/find-median-from-data-stream/)

**中位数**是有序整数列表中的中间值。如果列表的大小是偶数，则没有中间值，中位数是两个中间值的平均值。

- 例如 `arr = [2,3,4]` 的中位数是 `3` 。
- 例如 `arr = [2,3]` 的中位数是 `(2 + 3) / 2 = 2.5` 。

实现 MedianFinder 类:

- `MedianFinder() `初始化 `MedianFinder` 对象。
- `void addNum(int num)` 将数据流中的整数 `num` 添加到数据结构中。
- `double findMedian()` 返回到目前为止所有元素的中位数。与实际答案相差 `10-5` 以内的答案将被接受。

**示例 1：**

```
输入
["MedianFinder", "addNum", "addNum", "findMedian", "addNum", "findMedian"]
[[], [1], [2], [], [3], []]
输出
[null, null, null, 1.5, null, 2.0]

解释
MedianFinder medianFinder = new MedianFinder();
medianFinder.addNum(1);    // arr = [1]
medianFinder.addNum(2);    // arr = [1, 2]
medianFinder.findMedian(); // 返回 1.5 ((1 + 2) / 2)
medianFinder.addNum(3);    // arr[1, 2, 3]
medianFinder.findMedian(); // return 2.0
```

**提示:**

- `-105 <= num <= 105`
- 在调用 `findMedian` 之前，数据结构中至少有一个元素
- 最多 `5 * 104` 次调用 `addNum` 和 `findMedian`



思路：

插入排序维持有序可解，但时间复杂度高，此处利用大小对进行优化

创建小顶堆A：存较大的一半，堆顶元素为最小值，假设为a

创建大顶堆B：存较小的一半，堆顶元素为最大值，假设为b



若A B元素数量相等，则中位数为(a+b)/2.0；若A B元素数量不相等，则中位数为a b其中一个，取决于如何插入。总之**都可以通过<font color=red>获取堆顶元素计算中位数</font>。**

时间复杂度: $O(n log n)$



```java
class MedianFinder {
    PriorityQueue<Integer> A;
    PriorityQueue<Integer> B;
    public MedianFinder() {
        A=new PriorityQueue<>((o1,o2)->o1-o2); // 小顶堆，存较大的一半，堆顶元素为最小值
        B=new PriorityQueue<>((o1,o2)->o2-o1); // 大顶堆，存较小的一半，堆顶元素为最大值
    }
    
    public void addNum(int num) {
        if(A.size()==B.size()){
            A.add(num);
            B.add(A.poll()); // 将A中的最小值放入B中，平衡A B元素个数，使得二者元素个数相差不超过1
        }else{
            B.add(num);
            A.add(B.poll()); // 将B中的最大值放入A中，平衡A B元素个数
        }
          
    }
    
    public double findMedian() {
        return A.size()!=B.size()?B.peek():(A.peek()+B.peek())/2.0;
    }
}
```



但是时间复杂度感觉还是不是很好，官解103ms ，上面的代码122ms， 插入排序527ms，插入排序用二分查找优化328ms



<img src="assets/image-20240624212131825.png" alt="image-20240624212131825" style="zoom:50%;" />







# 二三. 其他

## 1. [删除有序数组中的重复项 II](https://leetcode.cn/problems/remove-duplicates-from-sorted-array-ii/)

给你一个有序数组 `nums` ，请你**[ 原地](http://baike.baidu.com/item/原地算法)** 删除重复出现的元素，使得出现次数超过两次的元素**只出现两次** ，返回删除后数组的新长度。

不要使用额外的数组空间，你必须在 **[原地 ](https://baike.baidu.com/item/原地算法)修改输入数组** 并在使用 O(1) 额外空间的条件下完成。

 

**说明：**

为什么返回数值是整数，但输出的答案是数组呢？

请注意，输入数组是以**「引用」**方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:

```
// nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
int len = removeDuplicates(nums);

// 在函数里修改输入数组对于调用者是可见的。
// 根据你的函数返回的长度, 它会打印出数组中 该长度范围内 的所有元素。
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

 

**示例 1：**

```
输入：nums = [1,1,1,2,2,3]
输出：5, nums = [1,1,2,2,3]
解释：函数应返回新长度 length = 5, 并且原数组的前五个元素被修改为 1, 1, 2, 2, 3。 不需要考虑数组中超出新长度后面的元素。
```

**示例 2：**

```
输入：nums = [0,0,1,1,1,1,2,3,3]
输出：7, nums = [0,0,1,1,2,3,3]
解释：函数应返回新长度 length = 7, 并且原数组的前七个元素被修改为 0, 0, 1, 1, 2, 3, 3。不需要考虑数组中超出新长度后面的元素。
```

 

**提示：**

- `1 <= nums.length <= 3 * 104`
- `-104 <= nums[i] <= 104`
- `nums` 已按升序排列



思路：

为了让解法更具有一般性，我们将原问题的「保留 2 位」修改为「保留 k 位」。

对于此类问题，我们应该进行如下考虑：

由于是保留 k 个相同数字，对于前 k 个数字，我们可以直接保留
对于后面的任意数字，能够保留的前提是：与当前写入的位置前面的第 k 个元素进行比较，不相同则保留
举个🌰，我们令 k=2，假设有如下样例

[1,1,1,1,1,1,2,2,2,2,2,2,3]

首先我们先让前 2 位直接保留，得到 1,1
对后面的每一位进行继续遍历，能够保留的前提是与当前位置的前面 k 个元素不同（答案中的第一个 1），因此我们会跳过剩余的 1，将第一个 2 追加，得到 1,1,2
继续这个过程，这时候是和答案中的第 2 个 1 进行对比，因此可以得到 1,1,2,2
这时候和答案中的第 1 个 2 比较，只有与其不同的元素能追加到答案，因此剩余的 2 被跳过，3 被追加到答案：1,1,2,2,3

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        return process(nums,2);
    }

    public int process(int[] nums,int k){
        int u=0;  // 有效下标
        for(int i:nums){
            if(u<k||nums[u-k]!=i){
                nums[u++]=i;
            }
        }
        return u;
    }
}
```



## 2. [轮转数组](https://leetcode.cn/problems/rotate-array/)

给定一个整数数组 `nums`，将数组中的元素向右轮转 `k` 个位置，其中 `k` 是非负数。要求空间复杂度为 `O(1)` 的 **原地** 算法

**示例 1:**

```
输入: nums = [1,2,3,4,5,6,7], k = 3
输出: [5,6,7,1,2,3,4]
解释:
向右轮转 1 步: [7,1,2,3,4,5,6]
向右轮转 2 步: [6,7,1,2,3,4,5]
向右轮转 3 步: [5,6,7,1,2,3,4]
```

**示例 2:**

```
输入：nums = [-1,-100,3,99], k = 2
输出：[3,99,-1,-100]
解释: 
向右轮转 1 步: [99,-1,-100,3]
向右轮转 2 步: [3,99,-1,-100]
```

 

**提示：**

- `1 <= nums.length <= 105`
- `-231 <= nums[i] <= 231 - 1`
- `0 <= k <= 105`



思路：

1.将$nums[0:n-1]$翻转

2.将$nums[0:k-]$​翻转

3.将$nums[k:n-1]$翻转

示例：

![image-20240602103523957](assets/image-20240602103523957.png)

```java
class Solution {
    public void rotate(int[] nums, int k) {
        int n=nums.length;
        k=k%n;
        rverse(nums,0,nums.length-1);
        rverse(nums,0,k-1);
        rverse(nums,k,n-1);
    }

    public void rverse(int[] nums, int start, int end){
        int index1=start,index2=end;
        while(index1<=index2){
            int tem=nums[index1];
            nums[index1]=nums[index2];
            nums[index2]=tem;
            index1++;
            index2--;
        }
    }
}
```



## 3. [分发糖果](https://leetcode.cn/problems/candy/)

`n` 个孩子站成一排。给你一个整数数组 `ratings` 表示每个孩子的评分。

你需要按照以下要求，给这些孩子分发糖果：

- 每个孩子至少分配到 `1` 个糖果。
- 相邻两个孩子评分更高的孩子会获得更多的糖果。

请你给每个孩子分发糖果，计算并返回需要准备的 **最少糖果数目** 。

 

**示例 1：**

```
输入：ratings = [1,0,2]
输出：5
解释：你可以分别给第一个、第二个、第三个孩子分发 2、1、2 颗糖果。
```

**示例 2：**

```
输入：ratings = [1,2,2]
输出：4
解释：你可以分别给第一个、第二个、第三个孩子分发 1、2、1 颗糖果。
     第三个孩子只得到 1 颗糖果，这满足题面中的两个条件。
```

 

**提示：**

- `n == ratings.length`
- `1 <= n <= 2 * 104`
- `0 <= ratings[i] <= 2 * 104`



思路：

可以将「相邻的孩子中，评分高的孩子必须获得更多的糖果」拆分为两个规则:

左规则：当 $ratings[i−1]<ratings[i] $时，$i$ 号学生的糖果数量将比 $i−1$ 号孩子的糖果数量多。

右规则：当 $ratings[i]>ratings[i+1]$ 时，$i$ 号学生的糖果数量将比 $ i+1$号孩子的糖果数量多。

遍历该数组两次，处理出每一个学生分别满足左规则或右规则时，最少需要被分得的糖果数量。每个人最终分得的糖果数量即为这两个数量的最大值。



```java
class Solution {
    public int candy(int[] ratings) {
        // 1010111102
        // 2121211212
        // 1121211112 满足从左往右规则
        // 2121111211 满足从右往左规则

        int n=ratings.length;
        if(n==0){
            return 0;
        }
        int[] left=new int[n];  // 从左往右  left[i] :第i个小孩最小糖果
        left[0]=1;
        for(int i=1;i<n;i++){
            left[i]=ratings[i]-ratings[i-1]>0?left[i-1]+1:1;
        }

        int[] right=new int[n];  // 从右往左  right[i] :第i个小孩最小糖果
        right[n-1]=1;
        int res=Math.max(left[n-1],right[n-1]);
        for(int i=n-2;i>=0;i--){
            right[i]=ratings[i]-ratings[i+1]>0?right[i+1]+1:1;
            res +=Math.max(left[i],right[i]); // 取最大值
        }

        return res;  
    }
}

```

# 二四. 前缀树（字典树）

## 1. [实现 Trie (前缀树)](https://leetcode.cn/problems/implement-trie-prefix-tree/)

**[Trie](https://baike.baidu.com/item/字典树/9825209?fr=aladdin)** **前缀树** 或 **字典树** 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补完和拼写检查。

请你实现 Trie 类：

- `Trie()` 初始化前缀树对象。
- `void insert(String word)` 向前缀树中插入字符串 `word` 。
- `boolean search(String word)` 如果字符串 `word` 在前缀树中，返回 `true`（即，在检索之前已经插入）；否则，返回 `false` 。
- `boolean startsWith(String prefix)` 如果之前已经插入的字符串 `word` 的前缀之一为 `prefix` ，返回 `true` ；否则，返回 `false` 。

 

**示例：**

```
输入
["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
[[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]]
输出
[null, null, true, false, true, null, true]

解释
Trie trie = new Trie();
trie.insert("apple");
trie.search("apple");   // 返回 True
trie.search("app");     // 返回 False
trie.startsWith("app"); // 返回 True
trie.insert("app");
trie.search("app");     // 返回 True
```

 

**提示：**

- `1 <= word.length, prefix.length <= 2000`
- `word` 和 `prefix` 仅由小写英文字母组成
- `insert`、`search` 和 `startsWith` 调用次数 **总计** 不超过 `3 * 104` 次





### 1.1 前缀树的构造

关键：**<font color=blue size=3>前缀树是每个节点都有26个子节点的26叉树，分别对应26个小写字母，通过节点是否为null标志对应的字母是否存在</font>**，从入口节点逐渐往下检索，不匹配直接返回。



![image-20240302095546093.png](assets/1709344595-HaJKeO-image-20240302095546093.png)

每个节点都有26个子节点，分别代表a-z 26个小写字母，用长度为26的Node数组存储，Node本身不存字符，而是通过对应下标的Node是否为null判断该字符是否存在。上图中，第0层为入口节点（根节点），作为搜索的入口；第1层为所有字符串的第一个字母节点集合，图中仅画出了已有字符串的第一个字母，其实每个节点（第1层为根节点的子节点集合）都有26个子节点，但是不存在的字符对应的节点为null；第二层为已有字符串的第二个字母，后面依此类推.....



### 1.2 加入一个字符串

从root节点出发，假设当前节点为 node，当前要加入的字符为 ch：如果当前节点 node 的子节点列表中，ch 对应的下标节点为null，则新建一个节点；然后更新 node 为子节点，加入下一个字符；

![image-20240121233200906.png](assets/1705852091-LnbIrQ-image-20240121233200906.png)



### 1.3 查找一个前缀

从root节点出发，假设当前节点为 node，当前要查找的字符为 ch：如果当前节点 node 的子节点列表中，ch对应的下标位置节点为null，说明该字符不存在，退出查找；否则更新 node 为子节点，查找下一字符；

![image-20240121233831238.png](assets/1705852091-YnaVlW-image-20240121233831238.png)



### 1.4 标志位标记完整字符串

通过以上方式可以快速找到一个前缀，但如何确定它是一个完整的字符串还是某个字符串的前缀呢？

<img src="assets/1708854943-VdkgQm-image-20240121234320181.png" alt="image-20240121234320181.png" style="zoom:50%;" />

此时可为每个节点引入一个标志 isEnd 标记，插入字符串时将最后一个字符对应的节点的标志位置为 True。

当我们查找一个完整字符串时，最后一个字符对应的节点不为null，且节点标志位为 true，则表明是一个完整的字符串。

<img src="assets/1705852091-TvIojy-image-20240121234502211.png" alt="image-20240121234502211.png" style="zoom:50%;" />

### 1.5 前缀树实现

```java
class Trie {
    Node root; //根节点，作为搜索入口

    public Trie() {
        root=new Node();
    }
    
    public void insert(String word) {
        // 从根节点开始
        Node current=root;
        // 逐个插入每个字符
        for(int i=0;i<word.length();i++){
            int index=word.charAt(i)-'a'; // 字符对应的下标
            if(current.children[index]==null){ // 不重复插入
                current.children[index]=new Node(); // 将字符对应的节点从null置为非null
            }
            current=current.children[index]; // 转向子节点
        }
        current.isEnd=true;  // 最后一个节点的isEnd置为true，表示一个完整的字符串
    }
    
    public boolean search(String word) {
        Node lastNode=searchPrefix(word);
        return lastNode!=null&&lastNode.isEnd;  // 前缀存在且标志位为true才表示完整的字符串
    }
    
    public boolean startsWith(String prefix) {
        return searchPrefix(prefix)!=null;
    }

    public Node searchPrefix(String prefix){
        // 从根节点开始
        Node current=root;
        for(int i=0;i<prefix.length();i++){
            int index=prefix.charAt(i)-'a';
            if(current.children[index]==null){ // 字符对应下标节点不存在，直接中断搜索
                return null;
            }
            current=current.children[index];
        }
        return current;  //  前缀存在，返回最后一个字符对应的节点
    }
}

class Node{ // 字母对应的节点
    Node[] children; // 子节点 ，26个，对应26个字母
    boolean isEnd; //是否为字符串的最后一个字母

    public Node(){
        children=new Node[26]; // 预制26个null节点
        isEnd=false; // 标志位为false
    }
}
```





## 2. [单词搜索 II](https://leetcode.cn/problems/word-search-ii/)

给定一个 `m x n` 二维字符网格 `board` 和一个单词（字符串）列表 `words`， *返回所有二维网格上的单词* 。

单词必须按照字母顺序，通过 **相邻的单元格** 内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母在一个单词中不允许被重复使用。

 

**示例 1：**

![img](assets/search1.jpg)

```
输入：board = [["o","a","a","n"],["e","t","a","e"],["i","h","k","r"],["i","f","l","v"]], words = ["oath","pea","eat","rain"]
输出：["eat","oath"]
```

**示例 2：**

![img](assets/search2.jpg)

```
输入：board = [["a","b"],["c","d"]], words = ["abcb"]
输出：[]
```

 

**提示：**

- `m == board.length`
- `n == board[i].length`
- `1 <= m, n <= 12`
- `board[i][j]` 是一个小写英文字母
- `1 <= words.length <= 3 * 104`
- `1 <= words[i].length <= 10`
- `words[i]` 由小写英文字母组成
- `words` 中的所有字符串互不相同



按[单词搜索](https://leetcode.cn/problems/word-search/) dfs逐个判断words中每个字符是否在网格中的套路，铁定超时。

![image-20240709094912477](assets/image-20240709094912477.png)

思路：字典树+dfs

为减少重复搜索：先基于words构建字典树，然后遍历网格，若$board[i][j]$​​为words中字符串的第一个字符，则开始dfs搜索，dfs过程中维护一个临时字符串tem，记录从搜索开始到当前位置经历的路径，只有tem在前缀树中才往下搜素（且不要重复判断tem是否在前缀树中，比如搜了ab在树中，再搜abc，就重复遍历了ab，只要判断新增单元格的字母是否是上一个单元格对应前缀树结点的子结点即可）。官解592ms，差不多。

![image-20240709094939526](assets/image-20240709094939526.png)

```java
class Solution {
    HashMap<Character,HashSet<String>> map=new HashMap<>(); 
    Tree tree=new Tree();
    List<String> res=new ArrayList<>();
    public List<String> findWords(char[][] board, String[] words) {
        int m=board.length,n=board[0].length;
        for(String s:words){
            if(map.get(s.charAt(0))==null){
                HashSet<String> tem=new HashSet<>();
                tem.add(s);
                map.put(s.charAt(0),tem);
            }else{
                map.get(s.charAt(0)).add(s);
            }
            tree.insert(s);  // 以words构建字典树
        }

        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(map.get(board[i][j])!=null){  // 只搜还在待搜列表中的字符串（比如'a'在board中，但以它开头的字符串都已经判断完了，那么就不再搜）
                    current=tree.root;
                    dfs(i,j,board,"");
                }
            }
        }
        return res;
    }

    // o a b n
    // o t a e
    // a h k r
    // a f l v
    Node current=tree.root;
    public void dfs(int i,int j,char[][] board,String s){
        int m=board.length,n=board[0].length;
        if(!valaid(i,j,m,n)||board[i][j]=='1'||map.isEmpty()){
            return;
        }
        char c=board[i][j];  
        board[i][j]='1'; // 已访问标记
        String tem=s+c;
        // tem是words构成字典树中的前缀  && words中还有以tem第一个字母开头的未搜字符串  ->  才往下继续搜
        if(current.children[c-'a']!=null&&map.get(tem.charAt(0))!=null){  // 不要重复搜索tem是否在前缀树中（会有重复，比如搜了ab在树中，再搜abc，就重复遍历了ab，只要判断新增单元格的字母是否是上一个单元格对应前缀树结点的子结点即可）
            Node node=current;
            current=current.children[c-'a'];
            if(current.isEnd&&map.get(tem.charAt(0)).contains(tem)){ 
                res.add(tem);
                // 确定存在就从待搜索列表中移除，避免重复搜索
                map.get(tem.charAt(0)).remove(tem);  
                if(map.get(tem.charAt(0)).isEmpty()){  
                    map.remove(tem.charAt(0));
                }        
            }
            
            int[][] dir={{-1,0},{1,0},{0,-1},{0,1}};
            for(int[] d:dir){
                dfs(i+d[0],j+d[1],board,tem);        
            }
            current=node; 
        }
        board[i][j]=c;
    }

    public boolean valaid(int i,int j,int m,int n){
        return i>=0&&i<m&&j>=0&&j<n;
    }

}

// 字典树
class Tree{
    Node root;  //根节点，作为搜索入口
    public Tree(){
        root=new Node();
    }
    // 插入字符串
    public void insert(String s){ 
        Node current=root;
        for(int i=0;i<s.length();i++){
            if(current.children[s.charAt(i)-'a']==null){
                current.children[s.charAt(i)-'a']=new Node();
            }
            current=current.children[s.charAt(i)-'a'];
        }
        current.isEnd=true;
    }
    
    // 下面这些都用不上，会造成重复搜
    public boolean search(String s){
        Node res=searchPrefix(s);
        return res!=null&&res.isEnd;
    }

    public boolean startWith(String prefix){
        return searchPrefix(prefix)!=null;
    }

    public Node searchPrefix(String prefix){
        Node current=root;
        for(int i=0;i<prefix.length();i++){
            if(current.children[prefix.charAt(i)-'a']==null){
                return null;
            }
            current=current.children[prefix.charAt(i)-'a'];
        }
        return current;
    } 
}


class Node{
    Node[] children;  // 对应26个字母
    boolean isEnd; // 字符串结束标志
    public Node(){
        children=new Node[26];
    }
}
```







# Hot100 重刷

## 1. LRU缓存（阿里社招二面手撕）

请你设计并实现一个满足 [LRU (最近最少使用) 缓存](https://baike.baidu.com/item/LRU) 约束的数据结构。

实现 `LRUCache` 类：

- `LRUCache(int capacity)` 以 **正整数** 作为容量 `capacity` 初始化 LRU 缓存
- `int get(int key)` 如果关键字 `key` 存在于缓存中，则返回关键字的值，否则返回 `-1` 。
- `void put(int key, int value)` 如果关键字 `key` 已经存在，则变更其数据值 `value` ；如果不存在，则向缓存中插入该组 `key-value` 。如果插入操作导致关键字数量超过 `capacity` ，则应该 **逐出** 最久未使用的关键字。

函数 `get` 和 `put` 必须以 `O(1)` 的平均时间复杂度运行。

 

**示例：**

```
输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4
```

 

**提示：**

- `1 <= capacity <= 3000`
- `0 <= key <= 10000`
- `0 <= value <= 105`
- 最多调用 `2 * 105` 次 `get` 和 `put`



关键：

- 双向链表+HashMap实现（LinkedHashMap）
- 双向链表的节点Node存: key、value，前驱prev，后继next；借助HashMap<key, Node> 保证时间复杂度为O（1）
- 每次访问（get或put操作）都将该节点移动到头节点，以确保尾节点是最久未访问的。当size超过容量capacity时直接删除尾节点和map里对应的键值对即可。

```java
class LRUCache {
    HashMap<Integer,Node> cache;
    int capacity; // 容量
    Node head; // 头节点
    Node tail; // 尾节点
    public LRUCache(int capacity) {
        this.capacity=capacity;
        cache=new HashMap<>();
        head=new Node();  // 建两个哑节点省去判空 
        tail=new Node();
        head.next=tail;
        tail.pre=head;
    }
    
    public int get(int key) {
        Node node=cache.get(key);
        if(node==null){
            return -1;
        }else{
            // 删掉node节点并加到链表头
            deleteNode(node);
            addToHead(node);
            return node.value;
        }
    }
    
    public void put(int key, int value) {
        Node node=cache.get(key);
        if(node!=null){
            // 更新值并移到头节点
            node.value=value;
            deleteNode(node);
            addToHead(node);
        }else{
            Node tem=new Node(key,value);
            addToHead(tem);
            cache.put(key,tem);
            if(cache.size()>capacity){ // 超出最大容量,移除尾节点
                Node lastNode=tail.pre;
                deleteNode(lastNode);
                cache.remove(lastNode.key);
            }
        }

    }
    public void deleteNode(Node node){
        node.pre.next=node.next;
        node.next.pre=node.pre;
        node.next=null;
        node.pre=null;
    }

    public void addToHead(Node node){
        node.next=head.next;
        head.next.pre=node;
        head.next=node;
        node.pre=head;
    }
}

class Node{
    Integer key;
    Integer value;
    Node next;
    Node pre;
    public Node(){}
    public Node(int key,int value){
        this.key=key;
        this.value=value;
    }
}
```



## 2.[和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)

给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回 *该数组中和为 `k` 的子数组的个数* 。

子数组是数组中元素的连续非空序列。要求时间复杂度为O（n)



**示例 1：**

```
输入：nums = [1,1,1], k = 2
输出：2
```

**示例 2：**

```
输入：nums = [1,2,3], k = 3
输出：2
```

 

**提示：**

- `1 <= nums.length <= 2 * 104`
- `-1000 <= nums[i] <= 1000`
- `-107 <= k <= 107`



暴力枚举时间复杂度$O(n^2)$



思路：前缀和+哈希表

时间复杂度$O(n)$ , 空间复杂度$O(n)$

设下标$i$的前缀和为$prefixSum[i]$，则下标$[j:i]$的和为$prefixSum[i]-prefixSum[j-1]$,对于以下标$i$结尾的子序列，若$prefixSum[i]-prefixSum[j-1]=k$则满足条件，因此问题转化为在下标$0 \leq j<i$中寻找满足条件的前缀和$prefixSum[i]-k$。

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        HashMap<Integer,Integer> map=new HashMap<>();  // key-前缀和  value:出现的次数
        map.put(0,1);  //  预置前缀和为0出现的次数为1  举例： 3 4 7 ....  k=7, 那么prefixSum[1]=3+4=7  找7-7=0出现的次数  1
        int count=0;  //最终结果
        int prefixSum=0; // 前缀和
        for(int i=0;i<nums.length;i++){
            prefixSum +=nums[i];
            Integer tem=map.get(prefixSum-k); //找前缀和prefixSum-k出现的次数，prefixSum-(prefixSum-k)=k
            if(tem!=null){
                count +=tem;
            }
            map.put(prefixSum,map.getOrDefault(prefixSum,0)+1); // 可能下标i和j的前缀和相同
        }
        return count;
        
    }
}
```



## 3.[找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/)

给定两个字符串 `s` 和 `p`，找到 `s` 中所有 `p` 的 **异位词** 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

**异位词** 指由相同字母重排列形成的字符串（包括相同的字符串）。

 

**示例 1:**

```
输入: s = "cbaebabacd", p = "abc"
输出: [0,6]
解释:
起始索引等于 0 的子串是 "cba", 它是 "abc" 的异位词。
起始索引等于 6 的子串是 "bac", 它是 "abc" 的异位词。
```

 **示例 2:**

```
输入: s = "abab", p = "ab"
输出: [0,1,2]
解释:
起始索引等于 0 的子串是 "ab", 它是 "ab" 的异位词。
起始索引等于 1 的子串是 "ba", 它是 "ab" 的异位词。
起始索引等于 2 的子串是 "ab", 它是 "ab" 的异位词。
```

 

**提示:**

- `1 <= s.length, p.length <= 3 * 104`
- `s` 和 `p` 仅包含小写字母



方法一：用HashMap统计每个字母出现的次数，比较两个Map中的元素是否相等，同时用第三个Map缓存判断过的字符串

注意：**<font color=red size=3>判断两个Integer数字是否相等用equals，包括所有的包装类型，==是判断引用地址是否相等，只有在缓存数组中的才相等（比如12)，而不在的是判断地址（如1000）, 二者是不相等的。</font>**

方法二：滑动串口，关键点：

- 使用长度为26的数组统计字符的数量，判断两个数组是否相等调Arrays.equals
- 串口长度p字符串的长度，每次s的窗口往右滑动一格



```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> res=new ArrayList<>();
        int sLen=s.length(),pLen=p.length();
        if(sLen<pLen){
            return res;
        }
        // 统计每个字母出现的次数
        int[] sCount=new int[26];
        int[] pCount=new int[26];

        for(int i=0;i<pLen;i++){  
            sCount[s.charAt(i)-'a']++;   // 窗口长度pLen
            pCount[p.charAt(i)-'a']++;
        }
        if(Arrays.equals(sCount,pCount)){ // 直接调Arrays.equals判断数组中所有元素是否相等，不用自己再写循环
            res.add(0);
        }
        // s的窗口每次往右移动一格
        for(int i=1;i<=sLen-pLen;i++){
            sCount[s.charAt(i-1)-'a']--;  
            sCount[s.charAt(i+pLen-1)-'a']++;
            if(Arrays.equals(sCount,pCount)){
            res.add(i);
            }
        }
        return res;
    }
}
```





## 4. [除自身以外数组的乘积](https://leetcode.cn/problems/product-of-array-except-self/)

给你一个整数数组 `nums`，返回 数组 `answer` ，其中 `answer[i]` 等于 `nums` 中除 `nums[i]` 之外其余各元素的乘积 。

题目数据 **保证** 数组 `nums`之中任意元素的全部前缀元素和后缀的乘积都在 **32 位** 整数范围内。

请 **不要使用除法，**且在 `O(n)` 时间复杂度内完成此题。

 

**示例 1:**

```
输入: nums = [1,2,3,4]
输出: [24,12,8,6]
```

**示例 2:**

```
输入: nums = [-1,1,0,-3,3]
输出: [0,0,9,0,0]
```

 

**提示：**

- `2 <= nums.length <= 105`
- `-30 <= nums[i] <= 30`
- **保证** 数组 `nums`之中任意元素的全部前缀元素和后缀的乘积都在 **32 位** 整数范围内

 

**进阶：**你可以在 `O(1)` 的额外空间复杂度内完成这个题目吗？（ 出于对空间复杂度分析的目的，输出数组 **不被视为** 额外空间。）



思路：前缀积+后缀积

假设前缀积为prefixMul， 后缀积为suffixMul，那么除nums[i]的积等于prefixMul[i-1]*suffixMul[i+1]

空间复杂度O(n)

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n=nums.length;
        int[] prefixMul=new int[n];  // 前缀积
        int[] suffixMul=new int[n];  // 后缀积
        int mul=1;
        for(int i=0;i<n;i++){
            mul *=nums[i];
            prefixMul[i]=mul;
        }
        mul=1;
        for(int i=n-1;i>=0;i--){
            mul *=nums[i];
            suffixMul[i]=mul;
        }

        int[] res=new int[n];
        res[0]=suffixMul[1];
        res[n-1]=prefixMul[n-2];
        for(int i=1;i<n-1;i++){
            res[i]=prefixMul[i-1]*suffixMul[i+1];  // 左前缀积*右前缀积
        }
        return res;
    }
}
```



进阶：将后缀积先用结果数组保存

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n=nums.length;
        int[] suffixMul=new int[n];  // 后缀积 ，复用输出数组
        int mul=1;
        for(int i=n-1;i>=0;i--){
            mul *=nums[i];
            suffixMul[i]=mul;
        }
        suffixMul[0]=suffixMul[1];
    
        mul=1;
        for(int i=0;i<n-2;i++){
            mul *=nums[i];
            suffixMul[i+1]=mul*suffixMul[i+2];
        }
        
        suffixMul[n-1]=mul*nums[n-2];
        return suffixMul;
    }
}
```



## 5.[两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/)

给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

**示例 1：**

![img](assets/swap_ex1.jpg)

```
输入：head = [1,2,3,4]
输出：[2,1,4,3]
```

**示例 2：**

```
输入：head = []
输出：[]
```

**示例 3：**

```
输入：head = [1]
输出：[1]
```

 

**提示：**

- 链表中节点的数目在范围 `[0, 100]` 内
- `0 <= Node.val <= 100`



思路：递归，要有整体思维

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head==null||head.next==null){
            return head;
        }

        ListNode newHead=head.next;
        head.next=swapPairs(newHead.next);
        newHead.next=head;
        return newHead;  
    } 
}
```



## 6.[随机链表的复制](https://leetcode.cn/problems/copy-list-with-random-pointer/)

给你一个长度为 `n` 的链表，每个节点包含一个额外增加的随机指针 `random` ，该指针可以指向链表中的任何节点或空节点。

构造这个链表的 **[深拷贝](https://baike.baidu.com/item/深拷贝/22785317?fr=aladdin)**。 深拷贝应该正好由 `n` 个 **全新** 节点组成，其中每个新节点的值都设为其对应的原节点的值。新节点的 `next` 指针和 `random` 指针也都应指向复制链表中的新节点，并使原链表和复制链表中的这些指针能够表示相同的链表状态。**复制链表中的指针都不应指向原链表中的节点** 。

例如，如果原链表中有 `X` 和 `Y` 两个节点，其中 `X.random --> Y` 。那么在复制链表中对应的两个节点 `x` 和 `y` ，同样有 `x.random --> y` 。

返回复制链表的头节点。

用一个由 `n` 个节点组成的链表来表示输入/输出中的链表。每个节点用一个 `[val, random_index]` 表示：

- `val`：一个表示 `Node.val` 的整数。
- `random_index`：随机指针指向的节点索引（范围从 `0` 到 `n-1`）；如果不指向任何节点，则为 `null` 。

你的代码 **只** 接受原链表的头节点 `head` 作为传入参数。

 

**示例 1：**

![img](assets/e1.png)

```
输入：head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
输出：[[7,null],[13,0],[11,4],[10,2],[1,0]]
```

**示例 2：**

![img](assets/e2.png)

```
输入：head = [[1,1],[2,1]]
输出：[[1,1],[2,1]]
```

**示例 3：**

**![img](assets/e3.png)**

```
输入：head = [[3,null],[3,0],[3,null]]
输出：[[3,null],[3,0],[3,null]]
```

 

**提示：**

- `0 <= n <= 1000`
- `-104 <= Node.val <= 104`
- `Node.random` 为 `null` 或指向链表中的节点。



思路：回溯+哈希表

```java
class Solution {
    Map<Node, Node> cachedNode = new HashMap<Node, Node>();

    public Node copyRandomList(Node head) {
        if (head == null) {
            return null;
        }
        if (!cachedNode.containsKey(head)) {
            Node headNew = new Node(head.val);
            cachedNode.put(head, headNew);
            headNew.next = copyRandomList(head.next);
            headNew.random = copyRandomList(head.random);
        }
        return cachedNode.get(head);
    }
}
```





## 7.[对称二叉树](https://leetcode.cn/problems/symmetric-tree/)

给你一个二叉树的根节点 `root` ， 检查它是否轴对称。

 

**示例 1：**

![img](assets/1698026966-JDYPDU-image.png)

```
输入：root = [1,2,2,3,4,4,3]
输出：true
```

**示例 2：**

![img](assets/1698027008-nPFLbM-image.png)

```
输入：root = [1,2,2,null,3,null,3]
输出：false
```

 

**提示：**

- 树中节点数目在范围 `[1, 1000]` 内
- `-100 <= Node.val <= 100`



思路：判断左右子树是否镜像对称

如果同时满足下面的条件，两个树互为镜像：

- 它们的两个根结点具有相同的值
- 每个树的右子树都与另一个树的左子树镜像对称



```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return check(root, root);
    }

    public boolean check(TreeNode p, TreeNode q) {
        if (p == null && q == null) {
            return true;
        }
        if (p == null || q == null) {
            return false;
        }
        return p.val == q.val && check(p.left, q.right) && check(p.right, q.left);
    }
}
```



思路二：先保存一份副本，然后将root翻转得到新树newRoot，若root和newRoot结构一致则对称

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        TreeNode back=copy(root);
        TreeNode newRoot=inverse(root);
        return judge(back,newRoot);
    }
    // 保存副本
    public TreeNode copy(TreeNode root){
        if(root==null){
            return null;
        }
        if(root.left==null&&root.right==null){
            return new TreeNode(root.val);
        }
        TreeNode newRoot=new TreeNode(root.val);
        newRoot.left=copy(root.left);
        newRoot.right=copy(root.right);
        return newRoot;
    }
    
    // 翻转二叉树

    public TreeNode inverse(TreeNode root){
        if(root==null||(root.left==null&&root.right==null)){
            return root;
        }

        TreeNode tem=inverse(root.right);
        root.right=inverse(root.left);
        root.left=tem;
        return root;
    }

    // 判断两棵树是否相等
    public boolean judge(TreeNode node1,TreeNode node2){
        if(node1==null&&node2==null){
            return true;
        }
        if((node1==null&&node2!=null)||(node1!=null&&node2==null)){
            return false;
        }
        return node1.val==node2.val&&judge(node1.left,node2.left)&&judge(node1.right,node2.right);
    }

}
```





## 8.[二叉树的直径](https://leetcode.cn/problems/diameter-of-binary-tree/)（最长路径）

**<font color=red size=3>拼多多三面，求二叉树中的最长路径并打印出路径</font>**

给你一棵二叉树的根节点，返回该树的 **直径** 。

二叉树的 **直径** 是指树中任意两个节点之间最长路径的 **长度** 。这条路径可能经过也可能不经过根节点 `root` 。

两节点之间路径的 **长度** 由它们之间边数表示。

 

**示例 1：**

![img](assets/diamtree.jpg)

```
输入：root = [1,2,3,4,5]
输出：3
解释：3 ，取路径 [4,2,1,3] 或 [5,2,1,3] 的长度。
```

**示例 2：**

```
输入：root = [1,2]
输出：1
```

 

**提示：**

- 树中节点数目在范围 `[1, 104]` 内
- `-100 <= Node.val <= 100`



思路：遍历所有节点，找出以它为顶点的最长路径就可得到整体最长。而以节点A为顶点的最长路径=A的左子树到叶子节点的最长路径+A的右子树到叶子节点的最长路径+2（假设左右子树都存在）

因此可以先求出从某顶点到叶子节点的最长路径

官解：

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    int ans;
    public int diameterOfBinaryTree(TreeNode root) {
        ans = 1;
        depth(root);
        return ans - 1; // 路经长=顶点数-1
    }
    // 求一棵树的最大深度（根节点到叶子节点的最大节点数量）
    public int depth(TreeNode node) {
        if (node == null) {
            return 0; // 访问到空节点了，返回0
        }
        int L = depth(node.left); // 左儿子为根的子树的深度
        int R = depth(node.right); // 右儿子为根的子树的深度
        ans = Math.max(ans, L+R+1); // 计算d_node即L+R+1 并更新ans
        return Math.max(L, R) + 1; // 返回该节点为根的子树的深度
    }
}
```



自己写的：

```java
class Solution {
    HashMap<TreeNode,Integer> cache=new HashMap<>(); // 缓存

    public int diameterOfBinaryTree(TreeNode root) {
        if(root==null){
            return 0;
        }
        if(root.left==null&&root.right==null){
            return 0;
        }
      
        int longestDiameter=0;
        LinkedList<TreeNode> list=new LinkedList<>();
        list.add(root);
        // 遍历所有节点，以它为顶点的最长路径，得到整体最长
        while(!list.isEmpty()){
            TreeNode tem=list.remove();
            int temDiameter=0;
            if(tem.left!=null){
                if(cache.get(tem.left)==null){
                    cache.put(tem.left,maxDiameter(tem.left)); // 左子树的最长路径
                }
                temDiameter +=1+cache.get(tem.left); // 加上左子树路径长
                list.add(tem.left);
            }
             if(tem.right!=null){
                if(cache.get(tem.right)==null){
                    cache.put(tem.right,maxDiameter(tem.right));
                }
                temDiameter  +=1+cache.get(tem.right);  // 加上右子树路径长
                list.add(tem.right);
            }

            longestDiameter=Math.max(longestDiameter,temDiameter); 
        }
        return longestDiameter;
    }

    // 求一棵树从根节点到叶子节点的最长路径
    int maxDepth=0;
    int depth=-1;
    public int maxDiameter(TreeNode node){
        if(node==null){
            return 0;
        }
        if(node.left==null&&node.right==null){
            return 0;
        }
        maxDepth=0;
        depth=-1;
        dfs(node);
        return maxDepth;
    }

    public void dfs(TreeNode node){
        depth +=1;
        maxDepth=Math.max(maxDepth,depth);
        if(node.left!=null){
            dfs(node.left);
        }
        if(node.right!=null){
            dfs(node.right);
        }
        depth -=1;
    }
}
```



## 9.[将有序数组转换为二叉搜索树](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/)

给你一个整数数组 `nums` ，其中元素已经按 **升序** 排列，请你将其转换为一棵 **平衡** 二叉搜索树。

**示例 1：**

![img](assets/btree1.jpg)

```
输入：nums = [-10,-3,0,5,9]
输出：[0,-3,9,-10,null,5]
解释：[0,-10,5,null,-3,null,9] 也将被视为正确答案：
```

**示例 2：**

![img](assets/btree.jpg)

```
输入：nums = [1,3]
输出：[3,1]
解释：[1,null,3] 和 [3,1] 都是高度平衡二叉搜索树。
```

 

**提示：**

- `1 <= nums.length <= 104`
- `-104 <= nums[i] <= 104`
- `nums` 按 **严格递增** 顺序排列



思路：平衡二叉树为左右子树深度相差不超过1

**因此每次选择数组中间的数作为根节点**即可，无需自己写平衡翻转代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    //TreeNode root; // 根节点
    public TreeNode sortedArrayToBST(int[] nums) {
        int n=nums.length;
        int med=n/2;
        if(n==0){
            return null;
        }
        if(n==1){
            return new TreeNode(nums[0]);
        }

        TreeNode root=new TreeNode(nums[med]); // 每次选中间值作为根节点就可保证平衡
        int[] leftSub=new int[med];
        System.arraycopy(nums,0,leftSub,0,med);
        int[] rightSub=new int[n-med-1];
        System.arraycopy(nums,med+1,rightSub,0,n-med-1);
        root.left=sortedArrayToBST(leftSub);
        root.right=sortedArrayToBST(rightSub);
        return root;
    }
}
```





## 10. [分割回文串](https://leetcode.cn/problems/palindrome-partitioning/)

给你一个字符串 `s`，请你将 `s` 分割成一些子串，使每个子串都是 **回文串**   返回 `s` 所有可能的分割方案。

**示例 1：**

```
输入：s = "aab"
输出：[["a","a","b"],["aa","b"]]
```

**示例 2：**

```
输入：s = "a"
输出：[["a"]]
```

 

**提示：**

- `1 <= s.length <= 16`
- `s` 仅由小写英文字母组成



思路:  设字符串长度为$n$，则最多可切$n-1$刀，dfs穷举所有的切法可能。记上一刀在下标$j$ ，当前刀在下标$i$，有$1\leq j<i\leq n-1$,被这两刀分隔出来的字符串为$s.substring(j,i)$, 

- 当前刀$i$切： 前提是$s.substring(j,i)$为回文串，并将$s.substring(j,i)$加入临时列表，上一刀更新为$i$, 同时继续穷举下一刀$i+1$
- 当前刀$i$不切：没有限制，上一刀不更新，并继续穷举下一刀$i+1$
- 走到$i=n$时表示切到了最后，前面的都是回文串，此时再判断最后一个字符串$s.substring(j,n)$是否为回文串，若是表示该方案可行。



法一：dfs+判断回文串用双指针   耗时14ms ，打败7%   (**自己的**)

判断回文串用双指针会有重复判断，虽然用了缓存存

```java
class Solution {
    public List<List<String>> partition(String s) {
        int n=s.length();
        if(n==1){
            List<List<String>> res=new ArrayList<>();
            res.add(Arrays.asList(s));
            return res;
        }

        dfs(0,1,s);
        return res;
    }

    HashMap<String,Boolean>  cache=new HashMap<>(); // 存储判断过的字符串
    List<String> tem=new ArrayList<>();
    List<List<String>> res=new ArrayList<>();
    public void dfs(int j,int i,String s){ // j：上一次切的下标 下标i之前切不切  1<=i<=n-1  (最后一个字符串留待最后判断-> 因为不切是不加进去的，统一二者)
        int n=s.length();
        if(i>n-1){
            String t=s.substring(j,n);
            if(illegal(t)){
                tem.add(t);
                res.add(new ArrayList<>(tem));
                tem.remove(tem.size()-1);
            }
            return;
        }

        String sub=s.substring(j,i);

        if(illegal(sub)){ // 下标i切（前提是j->i 字符串为合法回文串，否则中断）
            tem.add(sub);
            dfs(i,i+1,s);
            tem.remove(tem.size()-1);
        }
        // 下标i不切
        dfs(j,i+1,s); 
        
    }


    public boolean illegal(String s){
        int n=s.length();
        if(n==1){
            cache.put(s,true);
        }
        if(cache.get(s)!=null){
            return cache.get(s);
        }
        
        int left=0,right=n-1;
        while(left<right){
            if(s.charAt(left)==s.charAt(right)){
                left++;
                right--;
            }else{
                cache.put(s,false);
                return false;
            }
        }
        cache.put(s,true);
        return true;
    }
}
```



法二：dfs+判断回文串用动态规划    耗时14ms ，打败99%  （**官解**）

判断回文串用动态规划，设字符串$s，dp[i][j]$表示$s.substring(i,j+1)$是否为回文串，有递推公式：

```java
dp[i][j]=dp[i+1][j-1]&&s.charAt(i)==s.charAt(j)
```



```java
class Solution {
    public List<List<String>> partition(String s) {
        int n=s.length();
        if(n==1){
            List<List<String>> res=new ArrayList<>();
            res.add(Arrays.asList(s));
            return res;
        }

        boolean[][] dp=new boolean[n][n]; //dp[i][j]： 下标i~j是否为回文串
        for(int i=0;i<n;i++){
            Arrays.fill(dp[i],true);
        }
        for(int i=n-2;i>=0;i--){
            for(int j=i+1;j<n;j++){
                dp[i][j]=dp[i+1][j-1]&&s.charAt(i)==s.charAt(j); // 特殊：当字符串长度为2时，i+1>j-1 预置dp[i+1][j-1]=true
            }
        }
        
        dfs(0,1,s,dp);
        return res;
    }

    HashMap<String,Boolean>  cache=new HashMap<>(); // 存储判断过的字符串
    List<String> tem=new ArrayList<>();
    List<List<String>> res=new ArrayList<>();
    public void dfs(int j,int i,String s,boolean[][] dp){ // j：上一次切的下标 下标i之前切不切  1<=i<=n-1  (最后一个字符串留待最后判断-> 因为不切是不加进去的，统一二者)
        int n=s.length();
        if(i>n-1){
            String t=s.substring(j,n);
            if(dp[j][n-1]){
                tem.add(t);
                res.add(new ArrayList<>(tem));
                tem.remove(tem.size()-1);
            }
            return;
        }

        String sub=s.substring(j,i);
        if(dp[j][i-1]){ // 下标i切（前提是j->i 字符串为合法回文串，否则中断）
            tem.add(sub);
            dfs(i,i+1,s,dp);
            tem.remove(tem.size()-1);
        }
        // 下标i不切
        dfs(j,i+1,s,dp); 
    }
}
```



## 11.[括号生成](https://leetcode.cn/problems/generate-parentheses/)

数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

**示例 1：**

```
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
```

**示例 2：**

```
输入：n = 1
输出：["()"]
```

 

**提示：**

- `1 <= n <= 8`



思路：

- 判断是否为合法括号：

从左往右遍历字符串s，用count记录左括号的数量，遇到 **'('**  自增，遇到 **')'** 自减，若$count<0$必非法返回

- dfs穷举所有可能，共$2*n$个位置，每个位置只有 **'('** 和 **')'** 两种可能 ，且合法括号首位必须为 **'('** 和 **')'** ,实际只有$2*n-2$个位置需要判断，共$2^{2*n-2}$种可能



```java
class Solution {
    public List<String> generateParenthesis(int n) {
        dfs("(",2,n);
        return res;   
    }

    // dfs遍历所有可能，代替2*n重循环 且合法的括号首末必是 '(' 和')'  因此实际只需探索2^(2n-2)种可能
    List<String> res=new ArrayList<>();
    public void dfs(String s,int i,int n){  // i：下标位置
        if(i>2*n-1){
            if(illegal(s+")")&&!res.contains(s+")")){  // 字符串s为回文串且res中不包含
                res.add(s+")");
            }
            return;
        }

        dfs(s+"(",i+1,n);  // 下标i处为'('
        dfs(s+")",i+1,n);  // 下标i处为')'
    }
    
    // 判断字符串是否为合法括号：count记录左括号的数量，遇到'(' 自增，遇到')'自减，若count<0必非法返回
    public boolean illegal(String s){
        int count=0;  // 左括号数量
        for(int i=0;i<s.length();i++){
            if(s.charAt(i)=='('){
                count++;
            }else{
                count--;
            }
            if(count<0){
                return false;
            }
        }
        return count==0;
    }
}
```





## 12.[寻找旋转排序数组中的最小值](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)

已知一个长度为 `n` 的数组，预先按照升序排列，经由 `1` 到 `n` 次 **旋转** 后，得到输入数组。例如，原数组 `nums = [0,1,2,4,5,6,7]` 在变化后可能得到：

- 若旋转 `4` 次，则可以得到 `[4,5,6,7,0,1,2]`
- 若旋转 `7` 次，则可以得到 `[0,1,2,4,5,6,7]`

注意，数组 `[a[0], a[1], a[2], ..., a[n-1]]` **旋转一次** 的结果为数组 `[a[n-1], a[0], a[1], a[2], ..., a[n-2]]` 。

给你一个元素值 **互不相同** 的数组 `nums` ，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的 **最小元素** 。

你必须设计一个时间复杂度为 `O(log n)` 的算法解决此问题。

 

**示例 1：**

```
输入：nums = [3,4,5,1,2]
输出：1
解释：原数组为 [1,2,3,4,5] ，旋转 3 次得到输入数组。
```

**示例 2：**

```
输入：nums = [4,5,6,7,0,1,2]
输出：0
解释：原数组为 [0,1,2,4,5,6,7] ，旋转 3 次得到输入数组。
```

**示例 3：**

```
输入：nums = [11,13,15,17]
输出：11
解释：原数组为 [11,13,15,17] ，旋转 4 次得到输入数组。
```

 

**提示：**

- `n == nums.length`
- `1 <= n <= 5000`
- `-5000 <= nums[i] <= 5000`
- `nums` 中的所有整数 **互不相同**
- `nums` 原来是一个升序排序的数组，并进行了 `1` 至 `n` 次旋转



思路：二分查找

旋转后仅有两种形态：

- 不旋转：直线上升，最小值为$nums[0]$
- 旋转：左段的最小值必大于右段最大值，全局最小在中间，可视化的折线图如下

<img src="assets/3.png" alt="fig3" style="zoom:60%;" />

因此采用如下算法：$low$记录数组的最小下标，$high$记录最大下标，$pivot=（low+high）/2$为中间下标，当$low<high$时，不断比较$nums[pivot]$和$nums[high]$的大小，

- 若$nums[pivot]>nums[high]$：则表明最小值在$pivot$右边，此时令$low=pivot+1$
- 若$nums[pivot]\leq  nums[high]$：则表明最小值在$pivot$左边，此时令$high=pivot$

当$low=high$时，仅1个元素也即全局最小元素。

```java
class Solution {
    public int findMin(int[] nums) {
        return binarySearch(nums);
    }
    
    public int binarySearch(int[] nums){
        int left=0,right=nums.length-1;
        while(left<right){
            int med=(left+right)/2;
            if(nums[med]>nums[right]){
                left=med+1;
            }else{
                right=med;
            }
        }
        return nums[left];

    }
}
```



## 13. [最小栈](https://leetcode.cn/problems/min-stack/)

设计一个最小栈类，要求 `push` ，`pop` ，`top`和 `getMin` 操作的**时间复杂度都为$O(1)$**，其中`getMin`为获取栈中的最小元素。

实现 `MinStack` 类:

- `MinStack()` 初始化栈对象。
- `void push(int val)` 将元素val推入栈。
- `void pop()` 删除栈顶部的元素。
- `int top()` 获取栈顶部的元素。
- `int getMin()` 获取栈中的最小元素。

 

**示例 1:**

```
输入：
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

输出：
[null,null,null,null,-3,null,0,-2]

解释：
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.
```

 

**提示：**

- `-231 <= val <= 231 - 1`
- `pop`、`top` 和 `getMin` 操作总是在 **非空栈** 上调用
- `push`, `pop`, `top`, and `getMin`最多被调用 `3 * 104` 次



思路：利用一个辅助栈，用于同步主栈中的最小元素

```java
class MinStack {
    
    Stack<Integer> stack=new Stack<>();
    Stack<Integer> minStack=new Stack<>(); // 栈顶总保存stack中的最小值

    public MinStack() {
        minStack.push(Integer.MAX_VALUE);
    }
    
    public void push(int val) {
        stack.push(val);
        minStack.push(Math.min(minStack.peek(),val));
    }
    
    // 栈顶总保存stack中的最小值  二者保持一致
    public void pop() {
        stack.pop(); 
        minStack.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }
}
```



## 14.[每日温度](https://leetcode.cn/problems/daily-temperatures/)

给定一个整数数组 `temperatures` ，表示每天的温度，返回一个数组 `answer` ，其中 `answer[i]` 是指对于第 `i` 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。**要求时间复杂度$O(n)$**

**示例 1:**

```
输入: temperatures = [73,74,75,71,69,72,76,73]
输出: [1,1,4,2,1,1,0,0]
```

**示例 2:**

```
输入: temperatures = [30,40,50,60]
输出: [1,1,1,0]
```

**示例 3:**

```
输入: temperatures = [30,60,90]
输出: [1,1,0]
```

 

**提示：**

- `1 <= temperatures.length <= 105`
- `30 <= temperatures[i] <= 100`





思路：**单调栈**

用栈存储尚未找到下一次温度更高 元素的下标

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int n=temperatures.length;
        int[] res=new int[n];
        LinkedList<Integer> stack=new LinkedList<>(); //栈里存尚未找到下一次温度更高 元素的下标
        for(int i=0;i<n;i++){
            while(!stack.isEmpty()&&temperatures[i]>temperatures[stack.peek()]){
                res[stack.peek()]=i-stack.pop();  // 当前温度大于它，出栈并计算天数
            }
            stack.push(i);
        }
        return res;
    }
}
```



模拟：$temperatures = [73,74,75,71,69,72,76,73]$

![image-20240624171707040](assets/image-20240624171707040.png)

使用LinkedList：

<img src="assets/image-20240624171959082.png" alt="image-20240624171959082" style="zoom: 50%;" />

使用Stack:

<img src="assets/image-20240624171930407.png" alt="image-20240624171930407" style="zoom:50%;" />

注意：**<font color=red>LinkedList没加锁，比Stack快，Stack加了锁</font>**（继承自Vector，线程安全）。

<img src="assets/image-20240624171843511.png" alt="image-20240624171843511" style="zoom:50%;" />



## 15. [字符串解码](https://leetcode.cn/problems/decode-string/)（栈的典型应用）

给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: `k[encoded_string]`，表示其中方括号内部的 `encoded_string` 正好重复 `k` 次。注意 `k` 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 `k` ，例如不会出现像 `3a` 或 `2[4]` 的输入。

**示例 1：**

```
输入：s = "3[a]2[bc]"
输出："aaabcbc"
```

**示例 2：**

```
输入：s = "3[a2[c]]"
输出："accaccacc"
```

**示例 3：**

```
输入：s = "2[abc]3[cd]ef"
输出："abcabccdcdcdef"
```

**示例 4：**

```
输入：s = "abc3[cd]xyz"
输出："abccdcdcdxyz"
```

 

**提示：**

- `1 <= s.length <= 30`
- `s` 由小写英文字母、数字和方括号 `'[]'` 组成
- `s` 保证是一个 **有效** 的输入。
- `s` 中所有整数的取值范围为 `[1, 300]` 



细节比较多，麻烦



## 16.[柱状图中最大的矩形](https://leetcode.cn/problems/largest-rectangle-in-histogram/)

给定 *n* 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

求在该柱状图中，能够勾勒出来的矩形的最大面积。

 

**示例 1:**

![img](assets/histogram.jpg)

```
输入：heights = [2,1,5,6,2,3]
输出：10
解释：最大的矩形为图中红色区域，面积为 10
```

**示例 2：**

![img](assets/histogram-1.jpg)

```
输入： heights = [2,4]
输出： 4
```

 

**提示：**

- `1 <= heights.length <=105`
- `0 <= heights[i] <= 104`



暴力枚举引入：

法1：宽固定，求最小高

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int n=heights.length;
        int res=0;
        for(int i=0;i<n;i++){
            int minHeight=heights[i];
            for(int j=i;j>=0;j--){
                minHeight=Math.min(minHeight,heights[j]);
                res=Math.max(minHeight*(i-j+1),res);
            }
        }
        return res;

    }
}
```



法2：高固定，左右外扩求最大宽

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int n=heights.length;
        int res=Integer.MIN_VALUE;
        for(int i=0;i<n;i++){
            // 向左扩散求左边界
            int leftIndex=i-1;
            while(leftIndex>=0&&heights[leftIndex]>=heights[i]){
                leftIndex--;
            }
            // 向右扩散求右边界
            int rightIndex=i+1;
            while(rightIndex<n&&heights[rightIndex]>=heights[i]){
                rightIndex++;
            }
            // 求面积
            res=Math.max(res,(rightIndex-leftIndex-1)*heights[i]);  
        }
        return res;
    }
}
```



上面两种暴力枚举时间复杂度都是$O(n^2)$



下面基于 法2，使用单调栈进行优化到$O(n)$

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int n=heights.length;
        // 固定柱子高度height[i]，以它为中心向左右外扩，下标left[i]+1~right[i]-1的柱子高度都不小于height[i]
        // 面积为height[i]*(right[i]-left[i]-1)
        int[] left=new int[n]; //left[i]：小于柱子高度height[i]的左边界下标  
        int[] right=new int[n]; //right[i]：小于柱子高度height[i]的右边界下标 
        LinkedList<Integer> list=new LinkedList<>();
        for(int i=0;i<n;i++){
            while(!list.isEmpty()&&heights[list.peek()]>=heights[i]){ // 将左边大于等于heights[i]的都弹出
                list.pop();
            }
            left[i]=list.isEmpty()?-1:list.peek(); // 左边界
            list.push(i);
        }

        list.clear();
        for(int i=n-1;i>=0;i--){
            while(!list.isEmpty()&&heights[list.peek()]>=heights[i]){ // 将右边大于等于heights[i]的都弹出
                list.pop();
            }
            right[i]=list.isEmpty()?n:list.peek(); // 右边界
            list.push(i);
        }

        int res=Integer.MIN_VALUE;
        for(int i=0;i<n;i++){
            res=Math.max(res,(right[i]-left[i]-1)*heights[i]);  // 面积
        }
        return res;
    }
}
```



## 17.[数据流的中位数](https://leetcode.cn/problems/find-median-from-data-stream/)

**中位数**是有序整数列表中的中间值。如果列表的大小是偶数，则没有中间值，中位数是两个中间值的平均值。

- 例如 `arr = [2,3,4]` 的中位数是 `3` 。
- 例如 `arr = [2,3]` 的中位数是 `(2 + 3) / 2 = 2.5` 。

实现 MedianFinder 类:

- `MedianFinder() `初始化 `MedianFinder` 对象。
- `void addNum(int num)` 将数据流中的整数 `num` 添加到数据结构中。
- `double findMedian()` 返回到目前为止所有元素的中位数。与实际答案相差 `10-5` 以内的答案将被接受。

**示例 1：**

```
输入
["MedianFinder", "addNum", "addNum", "findMedian", "addNum", "findMedian"]
[[], [1], [2], [], [3], []]
输出
[null, null, null, 1.5, null, 2.0]

解释
MedianFinder medianFinder = new MedianFinder();
medianFinder.addNum(1);    // arr = [1]
medianFinder.addNum(2);    // arr = [1, 2]
medianFinder.findMedian(); // 返回 1.5 ((1 + 2) / 2)
medianFinder.addNum(3);    // arr[1, 2, 3]
medianFinder.findMedian(); // return 2.0
```

**提示:**

- `-105 <= num <= 105`
- 在调用 `findMedian` 之前，数据结构中至少有一个元素
- 最多 `5 * 104` 次调用 `addNum` 和 `findMedian`



思路：

插入排序维持有序可解，但时间复杂度高，此处利用大小对进行优化

创建小顶堆A：存较大的一半，堆顶元素为最小值，假设为a

创建大顶堆B：存较小的一半，堆顶元素为最大值，假设为b



若A B元素数量相等，则中位数为(a+b)/2.0；若A B元素数量不相等，则中位数为a b其中一个，取决于如何插入。总之都可以通过获取堆顶元素计算中位数。

时间复杂度: $O(n log n)$



```java
class MedianFinder {
    PriorityQueue<Integer> A;
    PriorityQueue<Integer> B;
    public MedianFinder() {
        A=new PriorityQueue<>((o1,o2)->o1-o2); // 小顶堆，存较大的一半，堆顶元素为最小值
        B=new PriorityQueue<>((o1,o2)->o2-o1); // 大顶堆，存较小的一半，堆顶元素为最大值
    }
    
    public void addNum(int num) {
        if(A.size()==B.size()){
            A.add(num);
            B.add(A.poll()); // 将A中的最小值放入B中，平衡A B元素个数，使得二者元素个数相差不超过1
        }else{
            B.add(num);
            A.add(B.poll()); // 将B中的最大值放入A中，平衡A B元素个数
        }
          
    }
    
    public double findMedian() {
        return A.size()!=B.size()?B.peek():(A.peek()+B.peek())/2.0;
    }
}
```



但是时间复杂度感觉还是不是很好，官解103ms ，上面的代码122ms， 插入排序527ms，插入排序用二分查找优化328ms



<img src="assets/image-20240624212131825.png" alt="image-20240624212131825" style="zoom:50%;" />



## 18.[分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)

给你一个 **只包含正整数** 的 **非空** 数组 `nums` 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

**示例 1：**

```
输入：nums = [1,5,11,5]
输出：true
解释：数组可以分割成 [1, 5, 5] 和 [11] 。
```

**示例 2：**

```
输入：nums = [1,2,3,5]
输出：false
解释：数组不能分割成两个元素和相等的子集。
```

 

**提示：**

- `1 <= nums.length <= 200`
- `1 <= nums[i] <= 100`



关键：将该问题转化为 ”**<font color=red>是否存在和为数组元素综合一半的组合</font>**“  ，设nums元素总和为sum，则寻找有无方案可使和为target=sum/2。

剪枝：排除下列情况直接返回false

- sum为奇数
- 数组长度小于2
- 数组中最大元素大于target



接下来使用动态规划求数组nums中是否存在组合使得和为target。

定义状态数组 $dp[n][target+1]$， 其中$dp[i][j]$表示”**从下标$0~i$ 是否存在组合使得和为$j$**“

转移方程如下：

- 若$nums[i]>j$: 必不可能选下标$nums[i]$，$dp[i][j]=dp[i-1][j]$
- 若$num[i]\leq j$： $nums[i]$可选可不选，任一满足即可，$dp[i][j]=dp[j-1][j-nums[i]]$  |  $dp[i-1][j]$



初始化：

- 所有都置$false$
- 和为0都满足，$dp[i][0]=true$
- 若$nums[0]<=target$，有$dp[0][nums[0]]=true$

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int n=nums.length;
        int sum=0,maxNum=0;
        for(int i=0;i<n;i++){
            sum +=nums[i];
            maxNum=Math.max(maxNum,nums[i]);
        }

        int target=sum/2;  // 找出是否可由nums中的元素组成目标和target
        if(sum%2!=0||n<2||maxNum>target){ // 总和为奇数 或数组长度小于2 或数组元素最大值大于target 必不可能
            return false;
        }
       
        // 默认false
        boolean[][] dp=new boolean[n][target+1]; // dp[i][j]: 从下标0~i 是否存在组合使得和为j
        for(int i=0;i<n;i++){
            dp[i][0]=true;
        }
        if(nums[0]<=target){
            dp[0][nums[0]]=true;
        }

        for(int i=1;i<n;i++){
            for(int j=1;j<=target;j++){
                if(nums[i]>j){ // 不选nums[i]
                    dp[i][j]=dp[i-1][j];
                }else { //  nums[i]可选可不选
                    dp[i][j]=dp[i-1][j]|dp[i-1][j-nums[i]];
                }
            }
        }
        return dp[n-1][target];
    }
}
```



## 19. [最长有效括号](https://leetcode.cn/problems/longest-valid-parentheses/)

给你一个只包含 `'('` 和 `')'` 的字符串，找出最长有效（格式正确且连续）括号

子串的长度。



**示例 1：**

```
输入：s = "(()"
输出：2
解释：最长有效括号子串是 "()"
```

**示例 2：**

```
输入：s = ")()())"
输出：4
解释：最长有效括号子串是 "()()"
```

**示例 3：**

```
输入：s = ""
输出：0
```

 

**提示：**

- `0 <= s.length <= 3 * 104`
- `s[i]` 为 `'('` 或 `')'`



思路：常规判断有效括号（遍历）的方法也能过，但时间复杂度太高且需要注意细节，下面用动态规划，下图为两种方法的耗时对比：

<img src="assets/image-20240625162932422.png" alt="image-20240625162932422" style="zoom:50%;" />



$dp[i]$ ：表示以下标 $i$字符结尾的最长有效括号的长度

当s.charAt(i)='('时，$dp[i]=0$，合法括号只需考虑s.charAt(i)==')'的情况：

- s.charAt(i-1)=='('$时，$$dp[i]=dp[i-2]+2$
- s.charAt(i-1)==')'时，以$s.charAt(i-1)$结尾的合法括号开始下标为$i-dp[i-1]$，若其前一个字符$s.charAt(i-dp[i-1]-1)$为')'，则$dp[i]=0$；否则$s.charAt(i)$和$s.charAt(i-dp[i-1]-1)$可以组一对括号，同时拼接上以下标$i-dp[i-1]-2$结尾的有效括号，因此$dp[i]=dp[i-1]+2+dp[i-dp[i-1]-2]$



```java
class Solution {
    public int longestValidParentheses(String s) {
        int n=s.length();
        if(n<2){
            return 0;
        }
        int[] dp=new int[n]; // dp[i]: 以下标i结尾的最长有效括号字串长度
        int maxLength=0; // 总体最长有效括号
        for(int i=1;i<n;i++){
            if(s.charAt(i)==')'){
                if(s.charAt(i-1)=='('){
                    dp[i]=(i-2>=0?dp[i-2]:0)+2;
                }else if(i-dp[i-1]-1>=0&&s.charAt(i-dp[i-1]-1)=='('){
                    dp[i]=dp[i-1]+2+(i-dp[i-1]-2>=0?dp[i-dp[i-1]-2]:0);
                }
            }
            maxLength=Math.max(maxLength,dp[i]);
        }
        return maxLength; 
    }
}
```

## 20.[ 编辑距离](https://leetcode.cn/problems/edit-distance/)

给你两个单词 `word1` 和 `word2`， *请返回将 `word1` 转换成 `word2` 所使用的最少操作数* 。

你可以对一个单词进行如下三种操作：

- 插入一个字符
- 删除一个字符
- 替换一个字符

 

**示例 1：**

```
输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```

**示例 2：**

```
输入：word1 = "intention", word2 = "execution"
输出：5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```

 

**提示：**

- `0 <= word1.length, word2.length <= 500`
- `word1` 和 `word2` 由小写英文字母组成



思路:

操作等价：

- A插入一个字符和B删除一个字符等价
- A删除一个字符和B插入一个字符等价
- A修改一个字符和B修改一个字符等价

因此可以简化为如下3种操作：

- A插入一个字符
- B插入一个字符
- A修改一个字符

以A=horse  B=ros为例：

- A插入一个字符：假设horse到ro的编辑距离是a，那么horse到ros的编辑距离最大为a+1
- B中插入一个字符：假设hors到ros的编辑距离是b，那么horse到ros的编辑距离最大为b+1
- A中修改一个字符：假设hors到ro的编辑距离是c，那么horse到ros的编辑距离最大为c+1

详细证明：https://dl.acm.org/doi/pdf/10.1145/321796.321811

设$dp[i][j]$为： $word1[0:i]$到$word2[0:j]$的编辑距离，那么有递推公式：

- $word1.charAt(i)==word2.charAt(j)$:    $dp[i][j]=min(dp[i][j-1]+1,dp[i-1][j]+1,dp[i-1][j-1])$
- $word1.charAt(i)!=word2.charAt(j)$:    $dp[i][j]=min(dp[i][j-1]+1,dp[i-1][j]+1,dp[i-1][j-1]+1)$



```java
class Solution {
    public int minDistance(String word1, String word2) {
        int m=word1.length(),n=word2.length();
        if(m==0||n==0){
            return m==0?n:m;
        }
        int[][] dp=new int[m][n];  //dp[i][j]: word1[0:i]到word2[0:j]的最小编辑距离
        // 填充边界
        for(int i=0;i<Math.max(m,n);i++){ 
            if(i<m){
                dp[i][0]=word1.substring(0,i+1).indexOf(word2.charAt(0))!=-1?i:i+1;
            }
            if(i<n){
                dp[0][i]=word2.substring(0,i+1).indexOf(word1.charAt(0))!=-1?i:i+1;   
            } 
        }
        for(int i=1;i<m;i++){
            for(int j=1;j<n;j++){
                dp[i][j]=word1.charAt(i)==word2.charAt(j)?Math.min(Math.min(dp[i][j-1]+1,dp[i-1][j]+1),dp[i-1][j-1]):Math.min(Math.min(dp[i][j-1]+1,dp[i-1][j]+1),dp[i-1][j-1]+1);    
            }
        }
        return dp[m-1][n-1];
    }
}
```



## 21.[只出现一次的数字](https://leetcode.cn/problems/single-number/)

给你一个 **非空** 整数数组 `nums` ，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

你必须设计并实现线性时间复杂度的算法来解决此问题，且该算法只使用常量额外空间。

 

**示例 1 ：**

```
输入：nums = [2,2,1]
输出：1
```

**示例 2 ：**

```
输入：nums = [4,1,2,1,2]
输出：4
```

**示例 3 ：**

```
输入：nums = [1]
输出：1
```

 

**提示：**

- `1 <= nums.length <= 3 * 104`
- `-3 * 104 <= nums[i] <= 3 * 104`
- 除了某个元素只出现一次以外，其余每个元素均出现两次。



位运算：

1、异或

$0\oplus N=N(展开为二进制)$ 

$N\oplus N=0$

2、设整型数组 $nums$中出现一次的数字为 $x$，出现两次的数字为$a,a,b,b,...$，即$nums=[a,a,b,b,...,x]$,利用性质1有：
$$
a\oplus a\oplus b\oplus b\oplus ... \oplus x=0\oplus 0\oplus ... \oplus x=x
$$

```java
class Solution {
    public int singleNumber(int[] nums) {
        int res=0;
        for(int i:nums){
            res=res^i;
        }
        return res;
    }
}
```

## 22.[寻找重复数](https://leetcode.cn/problems/find-the-duplicate-number/)

给定一个包含 `n + 1` 个整数的数组 `nums` ，其数字都在 `[1, n]` 范围内（包括 `1` 和 `n`），可知至少存在一个重复的整数。

假设 `nums` 只有 **一个重复的整数** ，返回 **这个重复的数** 。

你设计的解决方案必须 **不修改** 数组 `nums` ，时间复杂度 `O(n)`，空间复杂度 `O(1)` 。

 

**示例 1：**

```
输入：nums = [1,3,4,2,2]
输出：2
```

**示例 2：**

```
输入：nums = [3,1,3,4,2]
输出：3
```

**示例 3 :**

```
输入：nums = [3,3,3,3,3]
输出：3
```

 

 

**提示：**

- `1 <= n <= 105`
- `nums.length == n + 1`
- `1 <= nums[i] <= n`
- `nums` 中 **只有一个整数** 出现 **两次或多次** ，其余整数均只出现 **一次**



思路: 限制了数字范围1~n，构建有向图，令$i->nums[i]$，由于$0-n$共n个节点，却有n+1条边，因此必有环。该题转换为找有向链表中相交的节点（交点即重复数字），可用快慢指针解决。

![image-20240515085903347](assets/image-20240515085903347.png)

快指针fast一次走2步，慢指针slow一次走1步，若有环必相遇（模拟操场）。设相遇点为B，慢指针所走长度为***l+a***， 即图中OA-> AB； 快指针为***l+n(a+c)+a***, 即快指针比慢指针多走n个环的长度。又因为速度是慢指针的2倍，故***l+n(a+c)+a=2*(l+a)**，-->    ***l=c+(n-1)(a+c)***，即相遇后再额外使用一个指针 current=head，从O出发，慢指针从B出发，二者步幅相同，必相遇于点A。

```java
class Solution {
    public int findDuplicate(int[] nums) {
        // 0->1->3->2<->4   n个数字有n+1个指向，必有环
        // 0->3->4->2 1->1  2->3 
        // 0->3->3
        if(nums.length==1){
            return nums[0];
        }

        int fast=0,slow=0;
        while(fast<=nums.length-1&&slow<=nums.length){
            slow=nums[slow];
            fast=nums[nums[fast]];
            if(fast==slow){
                break;
            }
        }
        fast=0; 
        while(fast<=nums.length-1&&slow<=nums.length){
            slow=nums[slow];
            fast=nums[fast];
            if(fast==slow){
                return fast;
            }
        }
        
        return -1;
    }
}
```

## 23.[盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。

找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。要求时间复杂度$O(n)$

**说明：**你不能倾斜容器。

 

**示例 1：**

![img](assets/question_11.jpg)

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

**示例 2：**

```
输入：height = [1,1]
输出：1
```

 

**提示：**

- `n == height.length`
- `2 <= n <= 105`
- `0 <= height[i] <= 104`



思路：双指针（本质贪心）

```java
class Solution {
    public int maxArea(int[] height) {
        int n=height.length;
        int left=0,right=n-1; // 左右边界
        int res=0;
        while(left<right){
            res=Math.max(Math.min(height[left],height[right])*(right-left),res);
            if(height[left]<height[right]){
                left++; // 高取决于最小的那根，左边界右移（若右边界左移，高度不会增加，宽度减小，总面积减小）
            }else{
                right--;  // 右边界左移
            }
        }
        return res;
    }
}
```

## 24. [合并区间](https://leetcode.cn/problems/merge-intervals/)

以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [starti, endi]` 。请你合并所有重叠的区间，并返回 *一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间* 。

 

**示例 1：**

```
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

**示例 2：**

```
输入：intervals = [[1,4],[4,5]]
输出：[[1,5]]
解释：区间 [1,4] 和 [4,5] 可被视为重叠区间。
```

 

**提示：**

- `1 <= intervals.length <= 104`
- `intervals[i].length == 2`
- `0 <= starti <= endi <= 104`



先排序再合并

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        Arrays.sort(intervals,(o1,o2)->o1[0]!=o2[0]?o1[0]-o2[0]:o1[1]-o2[1]); // 排序
        int n=intervals.length;
        int[][] res=new int[n][2];
        res[0]=intervals[0];
        int count=0;
        for(int i=1;i<n;i++){
            if(intervals[i][0]<=res[count][1]&&intervals[i][1]>res[count][1]){ // 需要合并
                res[count][1]=intervals[i][1];
            }else if(intervals[i][0]>res[count][1]){  // 不用合并，直接拿
                res[++count]=intervals[i];
            }
        }
        int[][] finalRes=new int[count+1][2];
        System.arraycopy(res,0,finalRes,0,count+1);
        return finalRes;
    }
}
```



## 25. [最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)

 给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` 所有字符的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 。

**注意：**

- 对于 `t` 中重复字符，我们寻找的子字符串中该字符数量必须不少于 `t` 中该字符数量。
- 如果 `s` 中存在这样的子串，我们保证它是唯一的答案。

**示例 1：**

```
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
```

**示例 2:**

```
输入: s = "a", t = "aa"
输出: ""
解释: t 中两个字符 'a' 均应包含在 s 的子串中，
因此没有符合条件的子字符串，返回空字符串。
```

**提示：**

- `1 <= s.length, t.length <= 10^5`
- `s` 和 `t` 由英文字母组成

![image-20240706101046387](assets/image-20240706101046387.png)

**滑动窗口**：两个指针，**一个用于延伸现有窗口的 r 指针，一个用于收缩窗口的 l 指针**。在任意时刻，只有一个指针运动，另一个保持静止。在 s上滑动窗口，首先 r指针右移不断扩张窗口，直到找到满足条件的覆盖字串（窗口内字符串包含 t 全部所需的字符）；然后左指针右移收缩窗口，移动过程中不断判断窗口内的字符串是否为覆盖字串，直到得到最小窗口。

模拟：

- l=0,r=0，窗口内字符串为"A",不是t的覆盖字串，右移 r，直到找到第1个覆盖字串（"ABAAC"）
- 接着收缩窗口：r不动，l指针右移，由于"BAAC"不满足条件，停止，l=1
- 继续向右扩张窗口：l不动，r右移，直到窗口内的字符串满足条件（"BAACBA"）
- 收缩窗口：r不动，l指针右移，会经过最小字符串"CBA"，直到窗口为"BA"停止
- 按上面的思路循环往复：r右移...

判断当前的窗口包含 t 所有字符：用哈希表统计字母出现次数

```java
class Solution
{
    Map<Character, Integer> winMap = new HashMap<Character, Integer>();  //滑动窗口
    Map<Character, Integer> tMap = new HashMap<Character, Integer>();  //t字符串
    public String minWindow(String s, String t)
    {
        //统计t字符串每个字符的个数
        for (int i = 0; i < t.length(); i++)
        {
            if(tMap.containsKey(t.charAt(i)))
                tMap.put(t.charAt(i),tMap.get(t.charAt(i))+1);
            else
                tMap.put(t.charAt(i),1);
        }

        //初始时  l r 置默认值
        int l = 0, r = -1;  //窗口左右边界
        int min_win = Integer.MAX_VALUE, resL = -1, resR = -1;  //最小窗口

        while (r < s.length())
        {
            r++;  //向右拓展
            if (r < s.length() && tMap.containsKey(s.charAt(r)))
                winMap.put(s.charAt(r), winMap.getOrDefault(s.charAt(r), 0) + 1);

            while (valid() && l <= r) //满足条件,窗口收缩,左指针右移
            {
                if (r - l + 1 < min_win)  //更新满足条件的最小窗口
                {
                    min_win = r - l + 1;
                    resL = l;
                    resR = r + 1;
                }
                if (tMap.containsKey(s.charAt(l)))
                    winMap.put(s.charAt(l), winMap.get(s.charAt(l)) - 1);

                l++; //收缩
            }
        }
        return resL == -1 ? "" : s.substring(resL, resR);
    }

    public boolean valid()
    {
        Set<Map.Entry<Character, Integer>> set=tMap.entrySet();
        Iterator<Map.Entry<Character, Integer>> ite=set.iterator();
        while (ite.hasNext())
        {
            Map.Entry<Character, Integer> tem = ite.next();
            if (!winMap.containsKey(tem.getKey())||winMap.get(tem.getKey())<tem.getValue())
                return false;
        }
        return true;
    }
}
```



官方解答：类似于上面这种写法时间复杂度也只能击败13%  (上面写法656ms，官解235ms)，关键是每移动一个都字符都调valid将tMap里的字符从头遍历到尾

![image-20240706084745853](assets/image-20240706084745853.png)



![image-20240706084420839](assets/image-20240706084420839.png)



优化：找到第一个覆盖字串后valid判断滑动窗口内的字符串是否为覆盖字串只需上一个变动的字母

```java
class Solution
{
    Map<Character, Integer> winMap = new HashMap<Character, Integer>();  //滑动窗口内字符统计
    Map<Character, Integer> tMap = new HashMap<Character, Integer>();  //t字符串
    boolean exist=false;
    public String minWindow(String s, String t)
    {
        //统计t字符串每个字符的个数
        for (int i = 0; i < t.length(); i++){
            tMap.put(t.charAt(i),tMap.getOrDefault(t.charAt(i),0)+1); 
        }

        //初始时  l r 置默认值
        int l = 0, r = -1;  //窗口左右边界
        int min_win = Integer.MAX_VALUE, resL = -1, resR = -1;  //最小窗口
        char c='0';
        while (r < s.length()){  // 拓展右边界
            r++;  //向右拓展
            if (r < s.length() && tMap.containsKey(s.charAt(r))){
                winMap.put(s.charAt(r), winMap.getOrDefault(s.charAt(r), 0) + 1);
                
                while (l <= r&&valid(c)) {//找到覆盖字串,收缩窗口左边界，左指针右移
                    exist=true;
                    if (r - l + 1 < min_win) { //更新满足条件的最小窗口
                        min_win = r - l + 1;
                        resL = l;
                        resR = r + 1;
                    }
                    if (tMap.containsKey(s.charAt(l))){
                        winMap.put(s.charAt(l), winMap.get(s.charAt(l)) - 1);
                    }                     
                    l++; //左边界向右收缩
                    c=s.charAt(l-1); // 下一次valid判断时只需判断字符c的情况即可（左边界往右收缩过程中只有它在变动）
                }
               
                if(l>0){
                // s = "ADOBECODEBANC", t = "ABC" 第一次匹配到ADOBEC后，往右收缩左边界l=1,指向D，A字母缺少，因此往右扩张右边界只需要判断A字母的情即可
                    c=s.charAt(l-1);  
                }else{
                    exist=false;
                }   
            }
        }
        return resL == -1 ? "" : s.substring(resL, resR);
    }

    // 判断窗口内的字符串是否为t的覆盖字串
    public boolean valid(char c){
        if(!exist){  // 还没找到第一个满足条件的覆盖字串
            Set<Map.Entry<Character, Integer>> set=tMap.entrySet();
            Iterator<Map.Entry<Character, Integer>> ite=set.iterator();
            while (ite.hasNext()){
                Map.Entry<Character, Integer> tem = ite.next();
                if (!winMap.containsKey(tem.getKey())||winMap.get(tem.getKey())<tem.getValue())
                    return false;
            }
            return true;
        }else{ // 找到了满足条件的覆盖字串，收缩边界过程中只需要判断变动的字符
            return tMap.get(c)==null||(tMap.get(c)!=null&&winMap.get(c)>=tMap.get(c));
        }
    }
}
```

优化后耗时44ms，击败35%，感觉得找其他方法

![image-20240706084248488](assets/image-20240706084248488.png)

其他人的：

```java
class Solution {
    public String minWindow(String s, String t) {
        int[] cnt = new int[128];
        for (int i = 0; i < t.length(); i++) cnt[t.charAt(i)]++;
        int l = 0, r = 0, ansL = 0, ansR = 0, ans = Integer.MAX_VALUE, cntT = t.length();
        while (r < s.length()) {
            if (cnt[s.charAt(r++)]-- > 0) cntT--;
            while (cntT == 0) {
                if (r - l < ans) {
                    ans = r - l;
                    ansL = l;
                    ansR = r;
                }
                if (cnt[s.charAt(l++)]++ == 0) cntT++;
            }
        }
        return ans == Integer.MAX_VALUE ? "" : s.substring(ansL, ansR);
    }
}
```

![image-20240706102347396](assets/image-20240706102347396.png)

## 26. [缺失的第一个正数](https://leetcode.cn/problems/first-missing-positive/)

给你一个未排序的整数数组 `nums` ，请你找出其中没有出现的最小的正整数。

请你实现时间复杂度为 `O(n)` 并且只使用常数级别额外空间的解决方案。

 

**示例 1：**

```
输入：nums = [1,2,0]
输出：3
解释：范围 [1,2] 中的数字都在数组中。
```

**示例 2：**

```
输入：nums = [3,4,-1,1]
输出：2
解释：1 在数组中，但 2 没有。
```

**示例 3：**

```
输入：nums = [7,8,9,11,12]
输出：1
解释：最小的正数 1 没有出现。
```

 

**提示：**

- `1 <= nums.length <= 105`
- `-231 <= nums[i] <= 231 - 1`



思路：使用哈希表空间复杂度O(n)，不满足要求



实际上，对于一个长度为 N 的数组，其中没有出现的最小正整数只能在 [1,N+1] 中。这是因为如果 [1,N] 都出现了，那么答案是 N+1，否则答案是 [1,N] 中没有出现的最小正整数。

我们对数组进行遍历，对于遍历到的数 x，如果它在 [1,N] 的范围内，那么就将数组中的第 x−1 个位置（注意：数组下标从 0 开始）打上「标记」。在遍历结束之后，如果所有的位置都被打上了标记，那么答案是 N+1，否则答案是最小的没有打上标记的位置加 1。

那么如何设计这个「标记」呢？



由于数组中的数没有任何限制，因此这并不是一件容易的事情。但我们可以继续利用上面的提到的性质：由于我们只在意 [1,N] 中的数，因此我们可以先对数组进行遍历，把不在 [1,N] 范围内的数修改成任意一个大于 N 的数（例如 N+1）。这样一来，数组中的所有数就都是正数了，因此我们就可以将「标记」表示为「负号」。算法的流程如下：

- 排除非正整数（因为要将标记打成负整数）：将数组中所有小于等于 0 的数修改为 N+2（只要是0~N之外的正整数就行）；
- 遍历数组中的每一个数 x，它可能已经被打了标记，因此原本对应的数为 ∣x∣，其中 ∣∣ 为绝对值符号。如果 ∣x∣∈[1,N]，那么我们给数组中的第 ∣x∣−1 个位置的数添加一个负号。注意如果它已经有负号，不需要重复添加；
- 遍历完成之后，如果数组中的每一个数都是负数，那么答案是 N+1，否则答案是第一个正数的位置加 1。



```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int n=nums.length;
        for(int i=0;i<n;i++){
            if(nums[i]<=0){
                nums[i]=n+2;  // 负数转正（确保在0~n+1外）
            }
        }
        // 1-n+1  
       // 3 4 6 1
        for(int i=0;i<n;i++){
            int tem=Math.abs(nums[i]);
            if(tem<=n){
                nums[tem-1]=-Math.abs(nums[tem-1]);  // 取反是为了保留被替换的值nums[tem-1],不能随便设一个负数，因为后面的可能被打标记覆盖
            }
        }
        //  3 4  -6 -1
        // -3 4 -6 -1

        for(int i=0;i<n;i++){   
            if(nums[i]>=0){
                return i+1;
            }
        }
        return n+1;
    }
}
```

## 27. [排序链表](https://leetcode.cn/problems/sort-list/)

给你链表的头结点 `head` ，请将其按 **升序** 排列并返回 **排序后的链表** 。要求时间复杂度`O(n log n)` 和常数级空间复杂度

 

**示例 1：**

![img](assets/sort_list_1.jpg)

```
输入：head = [4,2,1,3]
输出：[1,2,3,4]
```

**示例 2：**

![img](assets/sort_list_2.jpg)

```
输入：head = [-1,5,3,4,0]
输出：[-1,0,3,4,5]
```

**示例 3：**

```
输入：head = []
输出：[]
```

 

**提示：**

- 链表中节点的数目在范围 `[0, 5 * 104]` 内
- `-105 <= Node.val <= 105`

 

**思路：**

- 使用快慢指针找到中间节点：同时从头节点出发，慢指针走一步，快指针走两步，当快指针到尾部时满指针在中间。
- 调用自身对前后两部分链表进行排序，然后老套路合并两个有序链表

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        if(head==null||head.next==null){
            return head;
        }
        ListNode slow=head,fast=head,parent=null;
        while(fast!=null&&fast.next!=null){
            parent=slow;
            slow=slow.next;
            fast=fast.next.next;
        }
        ListNode head1=head,head2=slow;
        parent.next=null;
        ListNode sortHead1=sortList(head1),sortHead2=sortList(head2);
        return merge(sortHead1,sortHead2);
    }
    public ListNode merge(ListNode head1,ListNode head2){
        if(head1==null||head2==null){
            return head1==null?head2:head1;
        }
        ListNode current1=head1,current2=head2,current3=null,res=null;
        if(current1.val<current2.val){
            res=new ListNode(current1.val);
            current3=res;
            current1=current1.next;
        }else{
            res=new ListNode(current2.val);
            current3=res;
            current2=current2.next;
        }
        while(current1!=null&&current2!=null){
            if(current1.val<current2.val){
                current3.next=new ListNode(current1.val);
                current3=current3.next;
                current1=current1.next;
            }else{
                current3.next=new ListNode(current2.val);
                current3=current3.next;
                current2=current2.next;
            }
        }
        if(current1!=null){
            current3.next=current1;
        }
        if(current2!=null){
            current3.next=current2;
        }
        return res;
    }
}
```

## 28. **[二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)**

二叉树中的 **路径** 被定义为一条节点序列，序列中每对相邻节点之间都存在一条边。同一个节点在一条路径序列中 **至多出现一次** 。该路径 **至少包含一个** 节点，且不一定经过根节点。

**路径和** 是路径中各节点值的总和。

给你一个二叉树的根节点 `root` ，返回其 **最大路径和** 。

 

**示例 1：**

![img](assets/exx1.jpg)

```
输入：root = [1,2,3]
输出：6
解释：最优路径是 2 -> 1 -> 3 ，路径和为 2 + 1 + 3 = 6
```

**示例 2：**

![img](assets/exx2.jpg)

```
输入：root = [-10,9,20,null,null,15,7]
输出：42
解释：最优路径是 15 -> 20 -> 7 ，路径和为 15 + 20 + 7 = 42
```

 

**提示：**

- 树中节点数目范围是 `[1, 3 * 104]`
- `-1000 <= Node.val <= 1000`



思路：都是遍历所有节点，然后以它为顶点，设当前节点为node, 求出它的左子树单边最大路径和leftMax，右子树单边最大路径和rightMax，则以node为顶点的最大路径和为$root.val+Math.max(0,leftMax)+Math.max(0,rightMax)$。遍历所有节点可找到全局最大。

法一：一遍dfs

```java
class Solution {
    public int maxPathSum(TreeNode root) {
        maxPathSigle(root);
        return res;
    }
    int res=Integer.MIN_VALUE;
    public int maxPathSigle(TreeNode root){ // 单边最大路径和
        if(root.left==null&&root.right==null){
            res=Math.max(res,root.val);
            return root.val;
        }
        int leftMax=root.left==null?0:maxPathSigle(root.left);
        int rightMax=root.right==null?0:maxPathSigle(root.right);
        
        res=Math.max(res,root.val+Math.max(0,leftMax)+Math.max(0,rightMax));
        return root.val+Math.max(Math.max(0,leftMax),Math.max(0,rightMax));
    }
}
```

![image-20240706200341013](assets/image-20240706200341013.png)

法二：先求以node节点开始的单向最大路径和，bfs遍历所有顶点，有重复dfs

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    HashMap<TreeNode,Integer> cache=new HashMap<>();
    public int maxPathSum(TreeNode root) {
        int res=Integer.MIN_VALUE;
        LinkedList<TreeNode> list=new LinkedList<>();
        list.add(root);
        while(!list.isEmpty()){
            TreeNode node=list.remove();
            int leftMax=0;
            if(node.left!=null){
                list.add(node.left);
                leftMax=getMax(node.left);
            }
            int rightMax=0;
            if(node.right!=null){
                list.add(node.right);
                rightMax=getMax(node.right);
            }

            res=Math.max(res,node.val+Math.max(leftMax,0)+Math.max(rightMax,0));
        }

        return res;
    }

    int maxValue=Integer.MIN_VALUE;
    int tem=0;
    public void dfs(TreeNode node){  // 求以node节点开始的单向最大路径和
        if(node==null){
            return;
        }
        tem +=node.val;
        maxValue=Math.max(tem,maxValue);
        if(node.left!=null){
            dfs(node.left);
        }

        if(node.right!=null){
            dfs(node.right);
        }
        tem -=node.val;
    }

    public int getMax(TreeNode node){
        Integer res=cache.get(node);
        if(res==null){
            maxValue=Integer.MIN_VALUE;
            tem=0;
            dfs(node);
            cache.put(node,maxValue);
        }

        return cache.get(node);
    }

}
```

![image-20240706200359977](assets/image-20240706200359977.png)



# 经典面试150

## 1. [单词搜索 II](https://leetcode.cn/problems/word-search-ii/)

给定一个 `m x n` 二维字符网格 `board` 和一个单词（字符串）列表 `words`， *返回所有二维网格上的单词* 。

单词必须按照字母顺序，通过 **相邻的单元格** 内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母在一个单词中不允许被重复使用。

 

**示例 1：**

![img](assets/search1.jpg)

```
输入：board = [["o","a","a","n"],["e","t","a","e"],["i","h","k","r"],["i","f","l","v"]], words = ["oath","pea","eat","rain"]
输出：["eat","oath"]
```

**示例 2：**

![img](assets/search2.jpg)

```
输入：board = [["a","b"],["c","d"]], words = ["abcb"]
输出：[]
```

 

**提示：**

- `m == board.length`
- `n == board[i].length`
- `1 <= m, n <= 12`
- `board[i][j]` 是一个小写英文字母
- `1 <= words.length <= 3 * 104`
- `1 <= words[i].length <= 10`
- `words[i]` 由小写英文字母组成
- `words` 中的所有字符串互不相同



按[单词搜索](https://leetcode.cn/problems/word-search/) dfs逐个判断words中每个字符是否在网格中，铁定超时。

![image-20240709094912477](assets/image-20240709094912477.png)

思路：字典树+dfs

为减少重复搜索：先基于words构建字典树，然后遍历网格，若$board[i][j]$​​为words中字符串的第一个字符，则开始dfs搜索，dfs过程中维护一个临时字符串tem，记录从搜索开始到当前位置经历的路径，只有tem在前缀树中才往下搜素（且不要重复判断tem是否在前缀树中，比如搜了ab在树中，再搜abc，就重复遍历了ab，只要判断新增单元格的字母是否是上一个单元格对应前缀树结点的子结点即可）。官解592ms，差不多。

![image-20240709094939526](assets/image-20240709094939526.png)

```java
class Solution {
    HashMap<Character,HashSet<String>> map=new HashMap<>(); 
    Tree tree=new Tree();
    List<String> res=new ArrayList<>();
    public List<String> findWords(char[][] board, String[] words) {
        int m=board.length,n=board[0].length;
        for(String s:words){
            if(map.get(s.charAt(0))==null){
                HashSet<String> tem=new HashSet<>();
                tem.add(s);
                map.put(s.charAt(0),tem);
            }else{
                map.get(s.charAt(0)).add(s);
            }
            tree.insert(s);  // 以words构建字典树
        }

        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(map.get(board[i][j])!=null){  // 只搜还在待搜列表中的字符串（比如'a'在board中，但以它开头的字符串都已经判断完了，那么就不再搜）
                    current=tree.root;
                    dfs(i,j,board,"");
                }
            }
        }
        return res;
    }

    // o a b n
    // o t a e
    // a h k r
    // a f l v
    Node current=tree.root;
    public void dfs(int i,int j,char[][] board,String s){
        int m=board.length,n=board[0].length;
        if(!valaid(i,j,m,n)||board[i][j]=='1'||map.isEmpty()){
            return;
        }
        char c=board[i][j];  
        board[i][j]='1'; // 已访问标记
        String tem=s+c;
        // tem是words构成字典树中的前缀  && words中还有以tem第一个字母开头的未搜字符串  ->  才往下继续搜
        if(current.children[c-'a']!=null&&map.get(tem.charAt(0))!=null){  // 不要重复搜索tem是否在前缀树中（会有重复，比如搜了ab在树中，再搜abc，就重复遍历了ab，只要判断新增单元格的字母是否是上一个单元格对应前缀树结点的子结点即可）
            Node node=current;
            current=current.children[c-'a'];
            if(current.isEnd&&map.get(tem.charAt(0)).contains(tem)){ 
                res.add(tem);
                // 确定存在就从待搜索列表中移除，避免重复搜索
                map.get(tem.charAt(0)).remove(tem);  
                if(map.get(tem.charAt(0)).isEmpty()){  
                    map.remove(tem.charAt(0));
                }        
            }
            
            int[][] dir={{-1,0},{1,0},{0,-1},{0,1}};
            for(int[] d:dir){
                dfs(i+d[0],j+d[1],board,tem);        
            }
            current=node; 
        }
        board[i][j]=c;
    }

    public boolean valaid(int i,int j,int m,int n){
        return i>=0&&i<m&&j>=0&&j<n;
    }

}

// 字典树
class Tree{
    Node root;  //根节点，作为搜索入口
    public Tree(){
        root=new Node();
    }
    // 插入字符串
    public void insert(String s){ 
        Node current=root;
        for(int i=0;i<s.length();i++){
            if(current.children[s.charAt(i)-'a']==null){
                current.children[s.charAt(i)-'a']=new Node();
            }
            current=current.children[s.charAt(i)-'a'];
        }
        current.isEnd=true;
    }
    
    // 下面这些都用不上，会造成重复搜
    public boolean search(String s){
        Node res=searchPrefix(s);
        return res!=null&&res.isEnd;
    }

    public boolean startWith(String prefix){
        return searchPrefix(prefix)!=null;
    }

    public Node searchPrefix(String prefix){
        Node current=root;
        for(int i=0;i<prefix.length();i++){
            if(current.children[prefix.charAt(i)-'a']==null){
                return null;
            }
            current=current.children[prefix.charAt(i)-'a'];
        }
        return current;
    } 
}


class Node{
    Node[] children;  // 对应26个字母
    boolean isEnd; // 字符串结束标志
    public Node(){
        children=new Node[26];
    }
}
```

