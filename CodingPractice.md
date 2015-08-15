# CodingPractice
算法题整理

将本人在编程过程中的一些体会，以及网上看到的心得的记录和整理，方便以后自己和团队的参考，提高效率。如果本文对其他开发人员也有所帮助，也欢迎大家给出反馈或者提出改进意见。

#### Leetcode

Paint House

There are a row of houses, each house can be painted with three colors red, blue and green. The cost of painting each house with a certain color is different. You have to paint all the houses such that no two adjacent houses have the same color. You have to paint the houses with minimum cost. How would you do it? Note: Painting house-1 with red costs different from painting house-2 with red. The costs are different for each house and each color.

思路：利用动态规划，dp[i][color]代表当前第i个house上色为color时的最小代价

        dp[i][red] = min(dp[i-1][blue], dp[i-1][green]) + cost[i][red];
        dp[i][green] = min(dp[i-1][blue], dp[i-1][red]) + cost[i][green];
        dp[i][blue] = min(dp[i-1][red], dp[i-1][green]) + cost[i][blue];
        
代码

        public int minCost(int n, int[][] cost) {
        // cost[i][j]是第i个房子上色为第j个颜色时的cost
	        int m = cost.length;
	        int[][] f = new int[n+1][m];
	        for(int i=1; i<=n; i++) {
		        f[i][0] = Math.min(f[i-1][1], f[i-1][2]) + cost[i-1][0];
		        f[i][1] = Math.min(f[i-1][0], f[i-1][2]) + cost[i-1][1];
		        f[i][2] = Math.min(f[i-1][0], f[i-1][1]) + cost[i-1][2];
	        }
	        int min = Math.min(Math.min(f[n][0], f[n][1]), f[n][2]);
	        return min;
        }
