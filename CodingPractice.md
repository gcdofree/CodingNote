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

---

#### Google Code Jam

Tips: 
*	Code Jam 里面的题目需要读写操作（输入数据从文件中读取，输出数据写入到文件中），可以提前准备好程序模板，包括#include的常用文件，main函数，IO操作等
*	一些常用的算法可以事先写好，如DFS，BFS等

---

##### Google APAC 2016 University Graduates Test

Practice Round

Name: Bad Horse
Problem: 给出所有人的名字（名字不重复），和所有的pair（两个不同的名字组成一个pair），问能否把所有人分成两队，每个队伍里面不包含上述的任意一个pair
Link: https://code.google.com/codejam/contest/6234486/dashboard

思路：转化为图，每个人为一个节点，每个pair的两个节点之间有边相连（注意转化为双向的边）。实际上就是找图中是否存在环路，如果存在，就不可能按要求划分为两个队。判断环路的方法可以通过DFS进行。

代码

		#include <iostream>
		#include <vector>
		#include <string>
		#include <fstream>
		#include <map>

		using namespace std;

		map<string, int> mp;
		int top;
		vector<int> g[1000];

		int getid(const string & s) {
			if (mp.count(s)) {
				return mp[s];
			} else {
				return mp[s] = top++;
			}
		}

		void addEdge(int a, int b) {
			g[a].push_back(b);
			g[b].push_back(a);
		}

		bool ans;
		vector<int> vis;

		void dfs(int s, int color) {
			if (vis[s] == 0) {
				vis[s] = color;
				for (int i = 0; i < (int)g[s].size(); i++) {
					int j = g[s][i];
					dfs(j, 3-color);
					if (!ans) {
						return;
					}
				}
			} else if (vis[s] != color) {
				ans = false;
			}
		}

		int main() {

    		// open file
    		ifstream inputFile("A-small-2-attempt0.in");
    		ofstream outputFile("output");

			int caseNum;
    		inputFile >> caseNum;

			for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
				int n;
				inputFile >> n;
				mp.clear();
				top = 0;
				for (int i = 0; i < 1000; i++) {
					g[i].clear();
				}
				string s, t;
				for (int i = 0; i < n; i++) {
					inputFile >> s >> t;
					addEdge(getid(s), getid(t));
				}
				ans = true;
				vis.clear();
				vis.resize(top);
				for (int i = 0; i < top; i++) {
					if (vis[i] == 0) {
						dfs(i, 1);
					}
				}
				if (ans) {
            		outputFile << "Case #" << caseIndex << ": Yes" << endl;
				} else {
            		outputFile << "Case #" << caseIndex << ": No" << endl;
				}
			}
    		inputFile.close();
    		outputFile.close();
			return 0;
		}
