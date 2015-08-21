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

*	Name: Bad Horse
*	Problem: 给出所有人的名字（名字不重复），和所有的pair（两个不同的名字组成一个pair），问能否把所有人分成两队，每个队伍里面不包含上述的任意一个pair
*	Link: https://code.google.com/codejam/contest/6234486/dashboard

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

	// 将string映射到数字，方便后续的DFS操作
	int getid(const string & s) {
		if (mp.count(s)) {
			return mp[s];
		} else {
			return mp[s] = top++;
		}
	}

	// 添加双向边
	void addEdge(int a, int b) {
		g[a].push_back(b);
		g[b].push_back(a);
	}

	bool ans;
	vector<int> vis;

	// DFS搜索，一个节点如果标记为1类，则他的children标记为2类
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
			// 如果类标不符，说明找到环路
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
			// 初始化无向图
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
	
---

*	Name: Captain Hammer
*	Problem: 给出初始速度大小和飞行的距离，求初始飞行角度。这里只考虑重力的影响，不受空气摩擦力。
*	Link: https://code.google.com/codejam/contest/6234486/dashboard#s=p1

思路：解对应的物理方程即可，注意越界（int连续相乘可能越界，考虑使用long long）和精度问题（题目要求精确到小数点后7位）。

代码

	#include <iostream>
	#include <fstream>
	#include <algorithm>

	#define PI 3.14159265

	using namespace std;
	
	int main() {
    	// open file
    	ifstream inputFile("B-small-attempt1.in");
    	ofstream outputFile("output");
    	outputFile.precision(8);
    	int caseNum;
    	inputFile >> caseNum;
    	for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
        	int speed, distance;
        	inputFile >> speed >> distance;
        	long long distanceD = (long long)distance;
        	distanceD = distanceD * distanceD;
        	long long speedL = (long long)speed;
        	speedL = speedL * speedL * speedL * speedL * 4;
        	double disTemp = 9.8 * 9.8 * distanceD;
        	double temp = abs(0.25 - disTemp / speedL);
        	temp = 0.5 - sqrt(temp);
        	double angle = 90.0 - acos(sqrt(temp)) * 180.0 / PI;
        	// output result
        	outputFile << "Case #" << caseIndex << ": " << angle << endl;
    	}
    	inputFile.close();
    	outputFile.close();
    	return 0;
	}

---

*	Name: Moist
*	Problem: 给出一个卡片组，要求计算用插入排序时，移动当前卡片的次数（一次排序只算一次）
*	Link: https://code.google.com/codejam/contest/6234486/dashboard#s=p2

思路：这里并不需要实现一个插入排序算法，只需要记录上一个卡片的名字即可。另外，C++中字符串的比较默认是从低位开始，按照字典顺序进行比较

代码

	#include <iostream>
	#include <fstream>
	#include <string>

	using namespace std;

	int main() {
    	// open file
    	ifstream inputFile("C-small-2-attempt0.in");
    	ofstream outputFile("output");

    	string temp;
    	int caseNum;
    	getline(inputFile, temp);
    	caseNum = atoi(temp.c_str());
    	for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
        	int cardNum;
        	getline(inputFile, temp);
        	cardNum = atoi(temp.c_str());
        	int cost = 0;
        	string prevCard = "", curCard = "";
        	for (int i = 1; i <= cardNum; i++) {
            	getline(inputFile, curCard);
            	if (curCard < prevCard) {
                	cost++;
            	}else {
            		// 只记录上一张卡片的名字
                	prevCard = curCard;
            	}
        	}
        	// output result
        	outputFile << "Case #" << caseIndex << ": " << cost << endl;
    	}
    	inputFile.close();
    	outputFile.close();
    	return 0;
	}
	
---

##### Google APAC 2015 University Graduates Test

Round A

*	Name: Super 2048
*	Problem: 根据游戏2048的规则，计算滑动一次之后的结果
*	Link: https://code.google.com/codejam/contest/3214486/dashboard#s=p1

思路：按照滑动的方向逆向计算结果即可，注意合并出来的新结果不能再参与下一次计算

代码

	#include <iostream>
	#include <vector>
	#include <string>
	#include <fstream>

	using namespace std;

	void handleHandMove(string direction, vector<vector<int>> &mat, int n) {
    	if (direction == "left") {
        // to left
        	for (int i = 0; i < n; i++) {
            	int lastValue = 0;
            	vector<int> tempResult;
            	for (int j = 0; j < n; j++) {
                	if (j == n-1 && mat[i][j] == 0) {
                    	tempResult.push_back(lastValue);
                    	continue;
                	}
                	if (mat[i][j] == 0) continue;
                	if (lastValue == 0) {
                    	lastValue = mat[i][j];
                    	if (j == n-1)
                        	tempResult.push_back(lastValue);
                    	continue;
                	}
                	int tempValue = lastValue;
                	if (lastValue == mat[i][j]) {// merge
                    	tempValue <<= 1;
                    	j++;
                	}
                	tempResult.push_back(tempValue);
                	lastValue = 0;
                	j--;
            	}
            	int size = tempResult.size();
            	for (int k = 0; k < n; k++) {
                	if (k < size)
                    	mat[i][k] = tempResult[k];
                	else
                    	mat[i][k] = 0;
            	}
        	}
    	}else if (direction == "right") {
        	// to right
			// ... 代码思路和左移相同，就不写了
    	}else if (direction == "up") {
        	// to up
			// ... 代码思路和左移相同，就不写了
    	}else {
        	// to down
			// ... 代码思路和左移相同，就不写了
    	}
	}

	int main() {

    	// open file
    	ifstream inputFile("B-large-practice.in");
    	ofstream outputFile("output");

		int caseNum;
    	inputFile >> caseNum;

		for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
			int n;
			inputFile >> n;
    		vector<vector<int>> mat(n, vector<int>(n, 0));
        	string direction = "";
        	inputFile >> direction;

        	// init matrix
        	for (int i = 0; i < n; i++) {
            	for (int j = 0; j < n; j++) {
                	inputFile >> mat[i][j];
            	}
        	}
        	handleHandMove(direction, mat, n);

        	outputFile << "Case #" << caseIndex << ":" << endl;
        	for (int i = 0; i < n; i++) {
            	for (int j = 0; j < n; j++) {
                	outputFile << mat[i][j] << " ";
            	}
            	outputFile << endl;
        	}

		}
    	inputFile.close();
    	outputFile.close();
		return 0;
	}

---

*	Name: Addition
*	Problem: 给出类似 a+b=12, b+c=8 求e+d=?
*	Link: https://code.google.com/codejam/contest/3214486/dashboard#s=p2

思路：按照滑动的方向逆向计算结果即可，注意合并出来的新结果不能再参与下一次计算

代码

---

Round B

*	Name: Parentheses Order
*	Problem: 给出括号的对数n，求第K个字母序列的括号组合
*	Link: https://code.google.com/codejam/contest/4214486/dashboard#s=p3

思路：按照滑动的方向逆向计算结果即可，注意合并出来的新结果不能再参与下一次计算

代码

---

*	Name: New Years Eve
*	Problem: 根据宴会上的塔形高脚杯排列规则，从顶部向下倒酒，要求计算第n层第k个酒杯内的酒量
*	Link: https://code.google.com/codejam/contest/4214486/dashboard#s=p1

思路：首先找出快速定位某一层酒杯对应下一层酒杯ID的方法（一个酒杯对应下一层的三个酒杯），同时需要一个数组记录酒杯内的剩余酒量（只需两层数组即可，当前层和下一层，来回替换）。如果某一层的酒杯都没有被装满，则无需向下计算。

代码

	#include <iostream>
	#include <vector>
	#include <string>
	#include <fstream>

	using namespace std;
	
	int getInnerLevel(int number) {
	    int level = 1;
	    int tempV = level;
	    while (true) {
	        if (number - tempV < 1 && number - tempV > -level )
	            return level;
	        level++;
	        tempV += level;
	    }
	    return level;
	}
	
	bool checkCanNextState(int level, vector<vector<double>> &storage, int numberSize) {
	    for (int k = 1; k <= numberSize; k++) {
	        if (storage[level][k] > 250)
	            return true;
	    }
	    return false;
	}
	
	int main() {
	
	    // open file
	    ifstream inputFile("B-large-practice.in");
	    ofstream outputFile("output");
	
	    outputFile.precision(10);
	
		int caseNum;
	    inputFile >> caseNum;
	
		for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
			int n, level, num;
			inputFile >> n >> level >> num;
	        int lastLevelNum = 0, i = 1;
	        double wine = n * 750;
	        while (i <= level) {
	            lastLevelNum += i;
	            i++;
	        }
	        vector<vector<double>> storage(2, vector<double>(lastLevelNum + 1, 0));
	        storage[0][1] = wine;
	        int currentStorageLevel = 0 , lastStorageLevel = 0;
	
	        for (int j = 1; j <= level; j++) {
	            if (j > 1) {
	                for (int k = 1; k <= lastLevelNum; k++) {
	                    // get wine from lastLevel and pour to current level
	                    if (storage[lastStorageLevel][k] > 0) {
	                        double pourWine = storage[lastStorageLevel][k] / 3;
	                        int innerLevel = getInnerLevel(k);
	                        storage[currentStorageLevel][k] += pourWine;
	                        storage[currentStorageLevel][k + innerLevel] += pourWine;
	                        storage[currentStorageLevel][k + innerLevel + 1] += pourWine;
	                    }
	                }
	                for (int k = 1; k <= lastLevelNum; k++) {
	                    // clear lastLevel
	                    storage[lastStorageLevel][k] = 0.0000000;
	                }
	            }
	            if (j < level && !checkCanNextState(currentStorageLevel, storage, lastLevelNum)) {
	                outputFile << "Case #" << caseIndex << ": 0.0000000" << endl;
	                break;
	            }
	            if (j == level) {
	                outputFile << "Case #" << caseIndex << ": "
		                    << (storage[currentStorageLevel][num] > 250 ? 250.0000000 : storage[currentStorageLevel][num]) << endl;
	                break;
	            }
	            for (int k = 1; k <= lastLevelNum; k++) {
	                storage[currentStorageLevel][k] = storage[currentStorageLevel][k] > 250 ?
	                    storage[currentStorageLevel][k] - 250.0000000 : 0.0000000;
	            }
	            lastStorageLevel = currentStorageLevel;
	            currentStorageLevel = 1 - currentStorageLevel;
	        }
	        //outputFile << "Case #" << caseIndex << ":" << endl;
		}
	    inputFile.close();
	    outputFile.close();
		return 0;
	}

---

Round C

*	Name: Minesweeper
*	Problem: 和扫雷游戏的规则一致，在已知棋盘内容的情况下，计算最少的获胜点击次数
*	Link: https://code.google.com/codejam/contest/5214486/dashboard#s=p0

思路：首先读入棋盘，然后统计带数字格子的个数，注意此处可能涉及到递归（两个数字为0的格子相邻），最后计算剩余的可点击次数即可。

代码

	#include <iostream>
	#include <vector>
	#include <string>
	#include <fstream>
	
	using namespace std;
	
	int isSurroundHaveMine(vector<vector<int>> &grid, int i, int j, int n) {
	    int iIndex = i-1, jIndex = j-1;
	    if (iIndex >= 0 && jIndex >= 0 && grid[iIndex][jIndex] == -1)
	        return 2;
		
		// 搜索周围的8个格子，重复代码就不写了...
	
	    return 0;
	}
	
	void makeSurroundClick(vector<vector<int>> &grid, int i, int j, int n) {
	    int iIndex = i-1, jIndex = j-1, temp;
	    if (iIndex >= 0 && jIndex >= 0) {
	        temp = grid[iIndex][jIndex];
	        grid[iIndex][jIndex] = 1;
	        if (temp == 0)
	            makeSurroundClick(grid, iIndex, jIndex, n);
	    }
	
		// 搜索周围的8个格子，重复代码就不写了...
	}
	
	int main() {
	
	    // open file
	    ifstream inputFile("A-large-practice.in");
	    ofstream outputFile("output");
	
		int caseNum;
	    inputFile >> caseNum;
	
		for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
			int n;
			inputFile >> n;
	
	        vector<vector<int>> grid(n, vector<int>(n, 0));
	
	        int mineNum = 0;
	
	        for(int i = 0; i < n; i++) {
	            for(int j = 0; j < n; j++) {
	                char t;
	                inputFile >> t;
	                if (t == '*') {
	                    grid[i][j] = -1;
	                    mineNum++;
	                }
	            }
	        }
	
	        for(int i = 0; i < n; i++) {
	            for(int j = 0; j < n; j++) {
	                if (grid[i][j] == 0) {
	                    grid[i][j] = isSurroundHaveMine(grid, i, j, n);
	                }
	            }
	        }
	
	        int clickZeroNum = 0;
	        for(int i = 0; i < n; i++) {
	            for(int j = 0; j < n; j++) {
	                if (grid[i][j] == 0) {
	                    makeSurroundClick(grid, i, j, n);
	                    clickZeroNum++;
	                }
	            }
	        }
	
	        for(int i = 0; i < n; i++) {
	            for(int j = 0; j < n; j++) {
	                if (grid[i][j] == 2) {
	                    clickZeroNum++;
	                }
	            }
	        }
	
	        outputFile << "Case #" << caseIndex << ": " << clickZeroNum << endl;
		}
	    inputFile.close();
	    outputFile.close();
		return 0;
	}

---

Round D

*	Name: GBus count
*	Problem: 给出每个bus的路线（途经站点），求过某个站点bus的数量
*	Link: https://code.google.com/codejam/contest/6214486/dashboard#s=p1

思路：建立数组，统计每个站点bus经过的数量，只需遍历一次，后续直接查询即可。

代码

	#include <iostream>
	#include <vector>
	#include <string>
	#include <fstream>
	
	using namespace std;
	
	int main() {
	
	    // open file
	    ifstream inputFile("B-large-practice.in");
	    ofstream outputFile("output");

		int caseNum;
	    inputFile >> caseNum;
	
		for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
			int pairNum;
			inputFile >> pairNum;
	        vector<int> countData(5001, 0);
	        for (int i = 0; i < pairNum; i++) {
	            int index1, index2;
	            inputFile >> index1 >> index2;
	            for (int j = index1; j <= index2; j++) {
	                countData[j]++;
	            }
	        }
	
	        outputFile << "Case #" << caseIndex << ":";
	        int testNum;
	        inputFile >> testNum;
	        for (int i = 0; i < testNum; i++) {
	            int index;
	            inputFile >> index;
	            outputFile << " " << countData[index];
	        }
	        outputFile << endl;
		}
	    inputFile.close();
	    outputFile.close();
		return 0;
	}

---

*	Name: Sort a scrambled itinerary
*	Problem: 给出打乱顺序的飞机票（起点和终点），要求重新排序后输出正确的飞行顺序
*	Link: https://code.google.com/codejam/contest/6214486/dashboard#s=p2

思路：实际上就是把每个机场看做是一个node，包含prev，next，val三个属性。首先生成所有node，然后找到最开始的那个机场，一直输出到最后的机场即可。需要用一个hashMap来存储机场<airportName, airportNode>

代码

	#include <iostream>
	#include <string>
	#include <fstream>
	#include <unordered_map>
	
	using namespace std;
	
	struct Node {
	    Node *prev;
	    Node *next;
	    string val;
	    Node(string str) : prev(NULL), next(NULL), val(str){};
	};
	
	int main() {
	
	    // open file
	    ifstream inputFile("C-large-practice.in");
	    ofstream outputFile("output");
	
		int caseNum;
	    inputFile >> caseNum;
	
		for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
	        Node *root = NULL;
	        unordered_map<string, Node *> airportMap;
			int pairNum;
			inputFile >> pairNum;
	        for (int i = 0; i < pairNum; i++) {
	            string val1, val2;
	            inputFile >> val1 >> val2;
	            Node *temp1, *temp2;
	            if (airportMap.find(val1) != airportMap.end()) {
	                temp1 = airportMap[val1];
	            }else {
	                temp1 = new Node(val1);
	                airportMap[val1] = temp1;
	            }
	            if (airportMap.find(val2) != airportMap.end()) {
	                temp2 = airportMap[val2];
	            }else {
	                temp2 = new Node(val2);
	                airportMap[val2] = temp2;
	            }
	            temp1->next = temp2;
	            temp2->prev = temp1;
	            if (!root)
	                root = temp1;
	        }
	        // find start node
	        while (root->prev) {
	            root = root->prev;
	        }
	        outputFile << "Case #" << caseIndex << ":";
	        while (root->next) {
	            outputFile << " " << root->val << "-" << root->next->val;
	            root = root->next;
	        }
	        outputFile << endl;
		}
	    inputFile.close();
	    outputFile.close();
		return 0;
	}

---

*	Name: Cube IV
*	Problem: 在一个方阵中，每个房间都有数字标号，离开房间时必须移动到比当前数字大1的房间中，求移动的最长路径
*	Link: https://code.google.com/codejam/contest/6214486/dashboard#s=p0

思路：首先构造矩阵，然后按照规则进行DFS即可。注意DFS的方向仅限于上下左右四个方向，不是周围的八个方向

代码

	#include <iostream>
	#include <vector>
	#include <string>
	#include <fstream>
	#include <vector>
	
	using namespace std;
	
	void setLongestPath(int start, int pathLength, vector<int> &result) {
	    if (start < result[pathLength]) {
	        result[pathLength] = start;
	    }
	}
	
	void checkPathDFS(vector<vector<int>> &room, int n, int i, int j, int start, int pathLength, vector<int> &result) {
	    int currentNum = room[i][j];
	    int largerNum = currentNum + 1;
	    int totalLength = n*n;
	    if (currentNum == totalLength) {
	        setLongestPath(start, pathLength, result);
	        return;
	    }
	
	    int index1 = i-1, index2 = j;
	    if (index1 >= 0 && room[index1][index2] == largerNum) {
	        checkPathDFS(room, n, index1, index2, start, pathLength+1, result);
	    }
	
	    index1 = i, index2 = j-1;
	    if (index2 >= 0 && room[index1][index2] == largerNum) {
	        checkPathDFS(room, n, index1, index2, start, pathLength+1, result);
	    }
	
	    index1 = i, index2 = j+1;
	    if (index2 < n && room[index1][index2] == largerNum) {
	        checkPathDFS(room, n, index1, index2, start, pathLength+1, result);
	    }
	
	    index1 = i+1, index2 = j;
	    if (index1 < n && room[index1][index2] == largerNum) {
	        checkPathDFS(room, n, index1, index2, start, pathLength+1, result);
	    }
	
	    setLongestPath(start, pathLength, result);
	}
	
	int main() {
	
	    // open file
	    ifstream inputFile("A-large-practice.in");
	    ofstream outputFile("output");
	
		int caseNum;
	    inputFile >> caseNum;
	
		for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
	        int n;
	        inputFile >> n;
	        vector<vector<int>> room(n, vector<int>(n, 0));
	        int totalLength = n*n;
	        vector<int> result(totalLength+1, totalLength + 1);
	        for (int i = 0; i < n; i++) {
	            for (int j = 0; j < n; j++) {
	                inputFile >> room[i][j];
	            }
	        }
	
	        for (int i = 0; i < n; i++) {
	            for (int j = 0; j < n; j++) {
	                int pathLength = 1;
	                checkPathDFS(room, n, i, j, room[i][j], pathLength, result);
	            }
	        }
	
	        for (int i = totalLength; i > 0; i--) {
	            if (result[i] < totalLength + 1) {
	                outputFile << "Case #" << caseIndex << ": " << result[i] << " " << i << endl;
	                break;
	            }
	        }
		}
	    inputFile.close();
	    outputFile.close();
		return 0;
	}
