# CodingPractice
算法题整理

将本人在编程过程中的一些体会，以及网上看到的心得的记录和整理，方便以后自己和团队的参考，提高效率。如果本文对其他开发人员也有所帮助，也欢迎大家给出反馈或者提出改进意见。

*	[Leetcode](#leetcode)
	*	[Paint House](#painthouse)
	*	[Paint House II](#painthouse2)
	*	[Meeting Rooms](#meetingrooms)
	*	[Meeting Rooms II](#meetingrooms2)
	*	[Shortest Word Distance](#shortestworddistance)
	*	[Shortest Word Distance II](#shortestworddistance2)
	*	[Shortest Word Distance III](#shortestworddistance3)
	*	[Find the Celebrity](#findthecelebrity)
	*	[Closest Binary Search Tree Value](#closestbinarysearchtreevalue)
	*	[Closest Binary Search Tree Value II](#closestbinarysearchtreevalue2)
	*	[Palindrome Permutation](#palindromepermutation)
	*	[Palindrome Permutation II](#palindromepermutation2)
	*	[Verify Preorder Sequence in Binary Search Tree](#verifypreordersequenceinbinarysearchtree)
*	[Google Code Jam](#googlecodejam)
	*	[Google APAC 2016 University Graduates Test](#google-apac-2016-university-graduates-test)
		*	[Bad Horse](#badhorse)
		*	[Captain Hammer](#captainhammer)
		*	[Moist](#moist)
		*	[Googol String](#googolstring)
		*	[gCube](#gcube)
		*	[gCampus](#gcampus)
		*	[gSnake](#gsnake)
	*	[Google APAC 2015 University Graduates Test](#google-apac-2015-university-graduates-test)
		*	[Super 2048](#super2048)
		*	[Addition](#addition)
		*	[Parentheses Order](#parenthesesorder)
		*	[Minesweeper](#minesweeper)
		*	[Broken Calculator](#brokencalculator)
		*	[GBus count](#gbuscount)
		*	[Sort a scrambled itinerary](#sortticket)
		*	[Cube IV](#cube-iv)
		*	[Itz Chess](#itzchess)
		*	[Card Game](#cardgame)

<h4 id="leetcode">Leetcode</h4>

<h5 id="painthouse">Paint House</h5>

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

<h5 id="painthouse2">Paint House II</h5>

There are a row of n houses, each house can be painted with one of the k colors. The cost of painting each house with a certain color is different. You have to paint all the houses such that no two adjacent houses have the same color. The cost of painting each house with a certain color is represented by a n x k cost matrix. For example, costs[0][0] is the cost of painting house 0 with color 0; costs[1][2] is the cost of painting house 1 with color 2, and so on... Find the minimum cost to paint all houses. All costs are positive integers. Could you solve it in O(nk) runtime? 

思路：利用动态规划，dp[i]代表当前house用第i个color上色时的最小代价。通过记录上个房间的最小代价和次小代价，来计算当前房间的代价，并依次递推

    dp[i][j] = min(dp[i-1][1], dp[i-1][2], ... , dp[i-1][k]) + cost[i][j];
    // 实际上可以转化为
    dp[i][j] = (minValue or secondMinValue) + cost[i][j];
        
代码

    public int minCost(int n, int k, vector<vector<int>> &cost) {
    // cost[i][j]是第i个房子上色为第j个颜色时的cost
		int m1 = 0, m2 = 0;
		vector<int> dp(k, 0);
		for (int i = 0; i < n; i++) {
			int t1 = m1, t2 = m2;
			m1 = INT_MAX, m2 = INT_MAX;
			for (int j = 0; j < k; j++) {
				dp[j] = (dp[j] != t1 ? t1 : t2) + cost[i][j];
				if (m1 <= dp[j]) m2 = min(dp[j], m2);
				else {
					m2 = m1;
					m1 = dp[j];
				}
			}
		}
	    return m1;
    }

---

<h5 id="meetingrooms">Meeting Rooms</h5>

Given an array of meeting time intervals consisting of start and end times \[[s1,e1],[s2,e2],...] (si < ei), determine if a person could attend all meetings.

思路：按照每个会议开始的时间进行排序，然后依次检查每个会议的开始时间，如果比上一个会议的结束时间早，就说明会议时间有重叠
        
代码

    bool meetingRoom(vector<int> &meeting, int n) {
        vector<Meeting> meetingVector(n);
        for (int i = 0; i < n; i++) {
            meetingVector[i].start = meeting[i+i];
            meetingVector[i].end = meeting[i+i+1];
        }
        sort(meetingVector.begin(), meetingVector.end(), comp);
        for (int i = 1; i <= n; i++) {
            if (meetingVector[i].start < meetingVector[i-1].end)
                return false;
        }
        return true;
    }

    static bool comp(Meeting m1, Meeting m2) {
        if (m1.start < m2.start)
            return true;
        return false;
    }

---

<h5 id="meetingrooms2">Meeting Rooms II</h5>

Given an array of meeting time intervals consisting of start and end times \[[s1,e1],[s2,e2],...] (si < ei), find the minimum number of conference rooms required.

思路：把时间不重叠的会议放到一个房间中，然后统计房间的数目即可
        
代码

    bool canAddtoRoom(Meeting &lastMeeting, Meeting &curMeeting) {
        return curMeeting.start >= lastMeeting.end;
    }

    int meetingRoom(vector<int> &meeting, int n) {
        vector<Meeting> meetingVector(n);
        for (int i = 0; i < n; i++) {
            meetingVector[i].start = meeting[i+i];
            meetingVector[i].end = meeting[i+i+1];
        }
        sort(meetingVector.begin(), meetingVector.end(), comp);
        vector<Meeting> result;
        result.push_back(meetingVector[0]);
        for (int i = 1; i < n; i++) {
            bool needNewRoom = false;
            for (int j = 0; j < result.size(); j++) {
                if (canAddtoRoom(result[j], meetingVector[i])) {
                    result[j] = meetingVector[i];
                    break;
                }
                if (j == result.size()-1 && !needNewRoom)
                    needNewRoom = true;
            }
            if (needNewRoom)
                result.push_back(meetingVector[i]);
        }
        return result.size();
    }

---

<h5 id="shortestworddistance">Shortest Word Distance</h5>

Given a list of words and two words word1 and word2, return the shortest distance between these two words in the list.
For example,
Assume that words = ["practice", "makes", "perfect", "coding", "makes"].
Given word1 = "coding", word2 = "practice", return 3.
Given word1 = "makes", word2 = "coding", return 1.
Note:
You may assume that word1 does not equal to word2, and word1 and word2 are both in the list.

思路：依次遍历数组，记录下word1和Word2的位置即可，时间复杂度O(n)，空间复杂度O(1)
        
代码

	int shortestDistance(vector<string>& words, string word1, string word2) {
        int size = words.size(), index1 = -1, index2 = -1, dist = INT_MAX;
        for (int i = 0; i < size; i++) {
            if (words[i] == word1) index1 = i;
            else if (words[i] == word2) index2 = i;
            if (index1 != -1 && index2 != -1)
                dist = min(dist, abs(index1 - index2));
        }
        return dist;
    }

---

<h5 id="shortestworddistance2">Shortest Word Distance II</h5>

This is a follow up of Shortest Word Distance. The only difference is now you are given the list of words and your method will be called repeatedly many times with different parameters. How would you optimize it?

思路：依次遍历数组，通过hashMap<string, index>记录下每个单词的位置，最后再查询，时间复杂度O(n)，空间复杂度O(n)
        
代码

	unordered_map<string, int> map;

    public WordDistance(vector<string>& words) {
        for (int i = 0; i < words.length; i++)
            map[words[i]] = i;
    }

    public int shortest(string word1, string word2) {
    	int index1 = -1, index2 = -1, dist = INT_MAX;
    	if (map.find(word1) != map.end())
    		index1 = map[word1];
    	if (map.find(word2) != map.end())
    		index2 = map[word2];
		if (index1 != -1 && index2 != -1)
        	dist = min(dist, abs(index1 - index2));
        return dist;
    }

---

<h5 id="shortestworddistance3">Shortest Word Distance III</h5>

Given a list of words and two words word1 and word2, return the shortest distance between these two words in the list.
word1 and word2 may be the same and they represent two individual words in the list.
For example,
Assume that words = ["practice", "makes", "perfect", "coding", "makes"].
Given word1 = "makes", word2 = "coding", return 1. Given word1 = "makes", word2 = "makes", return 3.

思路：依次遍历数组，通过hashMap<string, vector<int>>记录下每个单词的位置，最后再查询，求出最短距离，时间复杂度O(n)，空间复杂度O(n)
        
代码

	int shortestDistance(vector<string>& words, string word1, string word2) {
        int size = words.size(), index1 = -1, index2 = -1, dist = INT_MAX;
        unordered_map<string, vector<int>> map;
        for (int i = 0; i < size; i++) {
			if (map.find(words[i]) == map.end()) {
				vector<int> wordIndex;
				wordIndex.push_back(i);
				map[words[i]] = wordIndex;
			}else
				map[words[i]].push_back(i);
        }
        vector<int> tmp1 = map[word1], tmp2 = map[word2];
        for (int index1 : tmp1)
        	for (int index2 : tmp2)
        		if (index1 != index2)
        			dist = min(dist, abs(index1 - index2));
        return dist;
    }

---

<h5 id="findthecelebrity">Find the Celebrity</h5>

Suppose you are at a party with n people (labeled from 0 to n - 1) and among them, there may exist one celebrity. The definition of a celebrity is that all the other n - 1people know him/her but he/she does not know any of them.

Now you want to find out who the celebrity is or verify that there is not one. The only thing you are allowed to do is to ask questions like: "Hi, A. Do you know B?" to get information of whether A knows B. You need to find out the celebrity (or verify there is not one) by asking as few questions as possible (in the asymptotic sense).

You are given a helper function bool knows(a, b) which tells you whether A knows B. Implement a function int findCelebrity(n), your function should minimize the number of calls to knows.

Note: There will be exactly one celebrity if he/she is in the party. Return the celebrity's label if there is a celebrity in the party. If there is no celebrity, return -1.

思路：如果a认识b，说明a不是celebrity；如果a不认识b，说明b不是celebrity。根据这个原则从两边向中间搜索，最后确认即可
        
代码

	// Forward declaration of the knows API.
	bool knows(int a, int b);

    int findCelebrity(int n) {
        int l = 0, r = n - 1;
        while (l < r) {
            if (knows(l, r)) l++;
            else r--;
        }
        for (int i = 0; i < n; i++) if (i != l) {
            if (knows(l, i) || !knows(i, l)) return -1;
        }
        return l;
    }
    
---

<h5 id="closestbinarysearchtreevalue">Closest Binary Search Tree Value</h5>

Given a non-empty binary search tree and a target value (float), find the value in the BST that is closest to the target.

思路：按照BST的搜索顺序依次向下搜索即可
        
代码

	int closestValue(TreeNode* root, double target) {
		int val = root->val;
		TreeNode *next = val > target ? root->left : root->right;
		if (!next)
			return val;
		int childVal = closestValue(next, target);
		return abs(val - target) > abs(childVal - target) ? childVal : val;
	}

---

<h5 id="closestbinarysearchtreevalue2">Closest Binary Search Tree Value II</h5>

Given a non-empty binary search tree and a target value, find k values in the BST that are closest to the target.

Given target value is a floating point.
You may assume k is always valid, that is: k ≤ total nodes.
You are guaranteed to have only one unique set of k values in the BST that are closest to the target.

思路：由于BST的中序遍历（左中右）的结果是从小到大排序的，逆序的中序遍历（右中左）结果是从大到小排列的，所以创建两个栈，一个栈放中序遍历结果（比target小），一个栈放逆序的中序遍历结果（比target大），然后依次比较两个栈的数值即可找到k个最相近的元素
        
代码

	vector<int> closestValue(TreeNode* root, double target, int k) {
		vector<int> result(k, 0);
		stack<int> s1, s2;
		visitTree(root, target, s1, false);
		visitTree(root, target, s2, true);
		int index = 0;
		while (index < k) {
			if (s1.empty()) {
				result[index] = s2.top();
				s2.pop();
			}
			else if (s2.empty()) {
				result[index] = s1.top();
				s1.pop();
			}
			else{
				int num1 = s1.top();
				int num2 = s2.top();
				if (target - num1 > num2 - target) {
					s2.pop();
					result[index] = num2;
				}
				else {
					s1.pop();
					result[index] = num1;
				}
			}
			index++;
		}
		return result;
	}

	void visitTree(TreeNode* root, double target, stack<int> &s, bool reverse) {
		if (!root) return;
		TreeNode *first = root->left, *second = root->right;
		if (reverse) {
			first = root->right;
			second = root->left;
		}
		visitTree(first, target, s, reverse);
		// stop here
		if (root->val > target && !reverse || root->val <= target && reverse)
			return;
		s.push(root->val);
		visitTree(second, target, s, reverse);
	}

---

<h5 id="palindromepermutation">Palindrome Permutation</h5>

Given a string, determine if a permutation of the string could form a palindrome.

For example,
"code" -> False, "aab" -> True, "carerac" -> True.

思路：记录每个字符出现的次数，如果奇数次的个数小于两次，则说明能够形成回文串。可以通过hashMap记录次数，也可以用256位数组，也可以用bitset
        
代码

	bool canPermutePalindrome(string s) {
		if (s.empty()) return false;
		vector<int> data(256, 0);
		int count = 0;
		for (char c : s) {
			data[c]++;
			// 如果是奇数，加一；偶数，减一。最终结果如果有0或1个奇数，则可以生成回文串
			count += data[c] & 1 ? 1 : -1;
		}
		return count < 2;
	}

---

<h5 id="palindromepermutation2">Palindrome Permutation II</h5>

Given a string s, return all the palindromic permutations (without duplicates) of it. Return an empty list if no palindromic permutation could be form.

For example:
Given s = "aabb", return ["abba", "baab"].
Given s = "abc", return [].

思路：首先判断string能否生成回文串，如果能生成，则通过next_permutation函数生成前一半，翻转之后生成后一半（可能需要添加中间位）
        
代码

	vector<string> generatePalindromes(string s) {
		vector<string> result;
		if (s.empty()) return result;
		unordered_map<char, int> data;
		int count = 0;
		for (char c : s) {
			data[c]++;
			count += data[c] & 1 ? 1 : -1;
		}
		if (count >= 2) return result;
		char mid;
		bool odd = false;
		string half = "";
		for (auto dataPair : data) {
			if (dataPair.second & 1) {
				odd = true;
				mid = dataPair.first;
			}
			half += string(dataPair.second / 2, dataPair.first);
		}
		result.push_back(half);
		while (next_permutation(half.begin(), half.end())) {
			result.push_back(half);
		}
		for (string &temp : result) {
			string tt = temp;
			reverse(tt.begin(), tt.end());
			if (odd)
				temp += mid;
			temp += tt;
		}
		return result;
	}

---

<h5 id="verifypreordersequenceinbinarysearchtree">Verify Preorder Sequence in Binary Search Tree</h5>

Given an array of numbers, verify whether it is the correct preorder traversal sequence of a binary search tree.

You may assume each number in the sequence is unique.

思路：首先还原出中序遍历的结果，即从小到大排列，如果发现有这个排列中有小于前面数据的，就返回false。需要一个stack来存储前序节点，一个vector存储中序遍历结果
        
代码

	bool verifyPreorder(vector<int> &preorder) {
		if (preorder.empty()) return false;
		stack<int> s;
		vector<int> inorder;
		for (int num : preorder) {
			if (!inorder.empty() && num < inorder.back())
				return false;
			while (!s.empty() && num > s.top()) {
				inorder.push_back(s.top());
				s.pop();
			}
			s.push(num);
		}
		return true;
	}

---

<h4 id="googlecodejam">Google Code Jam</h4>

Tips: 
*	Code Jam 里面的题目需要读写操作（输入数据从文件中读取，输出数据写入到文件中），可以提前准备好程序模板，包括#include的常用文件，main函数，IO操作等
*	一些常用的算法可以事先写好，如DFS，BFS等

---

##### Google APAC 2016 University Graduates Test

<h6 id="badhorse">Bad Horse</h6>

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

<h6 id="captainhammer">Captain Hammer</h6>

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

<h6 id="moist">Moist</h6>

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

Round A

<h6 id="googolstring">Googol String</h6>

*	Name: Googol String
*	Problem: 按照规则顺序生成字符串，求第k个字符 SN = SN-1 + "0" + switch(reverse(SN-1)).
*	Link: https://code.google.com/codejam/contest/4284486/dashboard#s=p0

思路：无需把整个字符串求出后再进行搜索，会溢出。需要根据k的位置进行分析，首先看是在第几个string，然后是否在中间的0处，然后递归到上一个string，直到第一个字符为止。

代码

	#include <iostream>
	#include <vector>
	#include <string>
	#include <fstream>
	#include <vector>
	#include <algorithm>
	
	using namespace std;
	
	long long findPos(long long kth, bool reverse) {
		long long index = 1;
		while (index < kth) {
			index = 2 * index + 1;
		}
		//index = (index - 1) / 2;
		if (index == 1) {
			if (reverse)
				return 1;
			else
				return 0;
		}
		long long centerPos = index / 2 + 1;
		if (kth == centerPos) {
			if (reverse)
				return 1;
			else
				return 0;
		}
		return findPos(centerPos * 2 - kth, !reverse);
	}
	
	int main() {
	
		// open file
		ifstream inputFile("A-large.in");
		ofstream outputFile("output");
	
		int caseNum;
		inputFile >> caseNum;
	
		for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
			long long kth;
			inputFile >> kth;
			long long result = findPos(kth, false);
			outputFile << "Case #" << caseIndex << ": " << result << endl;
		}
		inputFile.close();
		outputFile.close();
		return 0;
	}

---

<h6 id="gcube">gCube</h6>

*	Name: gCube
*	Problem: 给出一个多维长方体，求转化为相同体积多维立方体的边长
*	Link: https://code.google.com/codejam/contest/4284486/dashboard#s=p2

思路：只要把题目的多维立方体定义看懂就行，求解体积的时候注意不要溢出，可以先开方再相乘

代码

	#include <iostream>
	#include <vector>
	#include <string>
	#include <fstream>
	#include <vector>
	#include <algorithm>
	
	using namespace std;
	
	int main() {
	
		// open file
		ifstream inputFile("input");
		ofstream outputFile("output");
	
		outputFile.precision(10);
	
		int caseNum;
		inputFile >> caseNum;
	
		for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
			int dimension, question;
			inputFile >> dimension >> question;
			vector<long long> dime(dimension);
			for (int i = 0; i < dimension; i++) {
				inputFile >> dime[i];
			}
			outputFile << "Case #" << caseIndex << ":" << endl;
			for (int i = 0; i < question; i++) {
				int dimeL, dimeR;
				inputFile >> dimeL >> dimeR;
				int curDime = dimeR - dimeL + 1;
				double volume = 1.0;
				for (int j = dimeL; j <= dimeR; j++) {
					volume *= pow(dime[j], 1.0 / curDime);
				}
				outputFile << volume << endl;
			}
		}
		inputFile.close();
		outputFile.close();
		return 0;
	}

---

<h6 id="gcampus">gCampus</h6>

*	Name: gCampus
*	Problem: 给出一个图，包含节点和路径权值，求出无效路径（没有出现在任意两点之间的最短路径上的路径）
*	Link: https://code.google.com/codejam/contest/4284486/dashboard#s=p2

思路：首先构建无向图，然后利用动态规划计算出两点之间的最短路径长度，最后一次判断每条路径是否符合要求即可。Large-test-case需要在release下跑，否则会超时

代码

	#include <iostream>
	#include <vector>
	#include <string>
	#include <fstream>
	#include <algorithm>
	
	using namespace std;
	
	struct edge {
	    int start;
	    int end;
	    long long cost;
	};
	
	int main() {
	
	    // open file
	    ifstream inputFile("C-small-practice.in");
	    ofstream outputFile("output");
	
		int caseNum;
	    inputFile >> caseNum;
	
		for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
			int n, m;
			inputFile >> n >> m;
			vector<edge> edges(m*2);
	        for (int i = 0; i < m; i++) {
	            edge e;
	            inputFile >> e.start >> e.end >> e.cost;
	            edges[i+i] = e;
	            int temp = e.start;
	            e.start = e.end;
	            e.end = temp;
	            edges[i+i+1] = e;
	        }
	        vector<vector<long long>> route(n, vector<long long>(n, 1e10));
	        for (int i = 0; i < n; i++) {
	            route[i][i] = 0;
	        }
	        vector<bool> result(m, false);
	        m *= 2;
	        for (int i = 0; i < m; i++) {
	            int start = edges[i].start;
	            int end = edges[i].end;
	            long long cost = edges[i].cost;
	            route[start][end] = min(cost, route[start][end]);
	        }
	        for (int k = 0; k < n; k++) {
	            for (int i = 0; i < n; i++) {
	                for (int j = 0; j < n; j++) {
	                    route[i][j] = min(route[i][j], route[i][k] + route[k][j]);
	                }
	            }
	        }
	        for (int i = 0; i < n; i++) {
	            for (int j = 0; j < n; j++) {
	                for (int k = 0; k < m; k++) {
	                    int start = edges[k].start;
	                    int end = edges[k].end;
	                    long long cost = edges[k].cost;
	                    if (route[i][j] == route[i][start] + cost + route[end][j]) {
	                        result[k/2] = true;
	                    }
	                }
	            }
	        }
	        outputFile << "Case #" << caseIndex << ":" << endl;
	        m /= 2;
	        for (int i = 0; i < m; i++) {
	            if (!result[i])
	                outputFile << i << endl;
	        }
		}
	    inputFile.close();
	    outputFile.close();
		return 0;
	}

---

<h6 id="gsnake">gSnake</h6>

*	Name: gSnake
*	Problem: 给出贪吃蛇的规则，要求在n步调整方向之后，贪吃蛇的长度
*	Link: https://code.google.com/codejam/contest/4284486/dashboard#s=p3

思路：根据规则模拟贪吃蛇的运动即可。注意检查碰撞时无需把所有贪吃蛇所在的格点都进行比较，只需要比较当前行包含的贪吃蛇所在格点即可。所以对应的数据结构是一个set数组vector<unordered_set<int>>。存储贪吃蛇所在格点时，应使用链表而不是vector，减少元素的复制和移动次数，提高速度。Large-test-case需要在release下跑，否则会超时

代码

	#include <iostream>
	#include <vector>
	#include <string>
	#include <fstream>
	#include <vector>
	#include <unordered_map>
	#include <unordered_set>
	#include <algorithm>
	#include <list>
	
	using namespace std;
	
	struct Grid
	{
		int x;
		int y;
	};
	
	int row, colume;
	
	const static int UP = 1;
	const static int RIGHT = 2;
	const static int DOWN = 3;
	const static int LEFT = 4;
	
	void getNextPos(Grid &startGrid, char action, int &direction) {
		if (action == 'R') {
			direction = direction % 4 + 1;
		}
		else if (action == 'L') {
			direction = direction - 1;
			if (direction == 0)
				direction = 4;
		}
		switch (direction)
		{
		case UP:
			startGrid.x--;
			if (startGrid.x == 0)
				startGrid.x = row;
			break;
		case RIGHT:
			startGrid.y++;
			if (startGrid.y == colume + 1)
				startGrid.y = 1;
			break;
		case DOWN:
			startGrid.x++;
			if (startGrid.x == row + 1)
				startGrid.x = 1;
			break;
		case LEFT:
			startGrid.y--;
			if (startGrid.y == 0)
				startGrid.y = colume;
			break;
		default:
			break;
		}
	}
	
	int main() {
	
		// open file
		ifstream inputFile("D-large-practice.in");
		ofstream outputFile("output");
	
		int caseNum;
		inputFile >> caseNum;
	
		for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
			int turnNum;
			inputFile >> turnNum >> row >> colume;
			int extraStep = max(row, colume);
			int lastStep;
			unordered_map<int, char> turnAction;
			vector<unordered_set<int>> snake(row+1);
			vector<unordered_set<int>> usedFood(row+1);
			list<Grid> p;
			list<Grid>::iterator head, end;
			for (int i = 0; i < turnNum; i++)
			{
				int step;
				char turn;
				inputFile >> step >> turn;
				turnAction.insert(make_pair(step, turn));
				lastStep = step;
			}
	
			int timeStep = 0;
			int direction = RIGHT;
			Grid startGrid, endGrid;
			startGrid.x = 1;
			startGrid.y = 1;
			endGrid.x = 1;
			endGrid.y = 1;
			snake[1].insert(1);
			p.push_back(startGrid);
			head = p.begin();
			end = p.begin();
			int totalSize = 1;
			for (; timeStep <= lastStep + extraStep; timeStep++) {
				// one step further
				if (turnAction.find(timeStep) != turnAction.end()) {
					getNextPos(startGrid, turnAction[timeStep], direction);
				}
				else
				{
					getNextPos(startGrid, NULL, direction);
				}
				// check self bite
				if (snake[startGrid.x].find(startGrid.y) != snake[startGrid.x].end()) {
					if (startGrid.x == endGrid.x && startGrid.y == endGrid.y) {
					
					}
					else 
					{
						outputFile << "Case #" << caseIndex << ": " << totalSize << endl;
						break;
					}
				}
				p.push_back(startGrid);
				head++;
				snake[startGrid.x].insert(startGrid.y);
				// check for food
				if (((startGrid.x + startGrid.y) & 0x1) == 1 && usedFood[startGrid.x].find(startGrid.y) == usedFood[startGrid.x].end()) {
					// grow
					totalSize++;
					usedFood[startGrid.x].insert(startGrid.y);
				}
				else
				{
					// keep going
					end++;
					snake[endGrid.x].erase(endGrid.y);
					endGrid.x = (*end).x;
					endGrid.y = (*end).y;
				}
			}
			if (timeStep > lastStep + extraStep)
			{
				outputFile << "Case #" << caseIndex << ": " << totalSize << endl;
			}
	
			//outputFile << "Case #" << caseIndex << ": " << result + 1 << endl;
		}
		inputFile.close();
		outputFile.close();
		return 0;
	}

---

##### Google APAC 2015 University Graduates Test

Round A

<h6 id="super2048">Super 2048</h6>

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

<h6 id="addition">Addition</h6>

*	Name: Addition
*	Problem: 给出类似 a+b=12, b+c=8 求e+d=?
*	Link: https://code.google.com/codejam/contest/3214486/dashboard#s=p2

思路：按照滑动的方向逆向计算结果即可，注意合并出来的新结果不能再参与下一次计算

代码

---

Round B

<h6 id="parenthesesorder">Parentheses Order</h6>

*	Name: Parentheses Order
*	Problem: 给出括号的对数n，求第K个字母序列的括号组合
*	Link: https://code.google.com/codejam/contest/4214486/dashboard#s=p3

思路：按照滑动的方向逆向计算结果即可，注意合并出来的新结果不能再参与下一次计算

代码

---

<h6 id="newyearseve">New Years Eve</h6>

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

<h6 id="minesweeper">Minesweeper</h6>

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

<h6 id="brokencalculator">Broken Calculator</h6>

*	Name: Broken Calculator
*	Problem: 计算器上面只能选择乘法和等号，以及0-9的部分数字，要求利用这些条件计算出制定数字
*	Link: https://code.google.com/codejam/contest/5214486/dashboard#s=p2

思路：将数字拆成两两相乘的形式（DFS），如a = b * (a/b)，需要确保a%b == 0，不断拆分直到b可以用数字键直接按出来为止。用map存储中间结果，避免重复计算（DP）

代码

	#include <iostream>
	#include <vector>
	#include <string>
	#include <fstream>
	#include <vector>
	#include <unordered_map>
	#include <algorithm>
	
	using namespace std;
	
	vector<bool> numTemplate(10, false);
	unordered_map<int, int> checkMap;
	
	int directClickNum(int target) {
	    int origin = target;
	    int click = 0;
	    if (target == 0) {
	        if (numTemplate[0])
	            click++;
	        else {
	            click = -1;
	        }
	    }
	    while (target >0) {
	        int index = target % 10;
	        if (numTemplate[index])
	            click++;
	        else {
	            click = -1;
	            break;
	        }
	        target /= 10;
	    }
	    checkMap[origin] = click;
	    return click;
	}
	
	int check(int target) {
	    if (checkMap.find(target) != checkMap.end()) {
	        return checkMap[target];
	    }
	    int directClick = directClickNum(target);
	    if (directClick == -1) {
	        int size = sqrt(target) + 1;
	        int minClick = -1;
	        for (int i = 1; i <= size; i++) {
	            if (target % i != 0)
	                continue;
	            int firstClick = check(i);
	            if (firstClick == -1)
	                continue;
	            int secondClick = check(target/i);
	            if (secondClick == -1)
	                continue;
	            int totalClick = firstClick + secondClick + 1;
	            if (minClick == -1)
	                minClick = totalClick;
	            minClick = minClick > totalClick ? totalClick : minClick;
	        }
	        checkMap[target] = minClick;
	        return minClick;
	    }else {
	        checkMap[target] = directClick;
	        return directClick;
	    }
	}
	
	int main() {
	
	    // open file
	    ifstream inputFile("C-large-practice.in");
	    ofstream outputFile("output");
	
		int caseNum;
	    inputFile >> caseNum;
	
		for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
	        for (int i = 0; i < 10; i++) {
	            int tempNum;
	            inputFile >> tempNum;
	            if (tempNum)
	                numTemplate[i] = true;
	            else
	                numTemplate[i] = false;
	        }
	
	        int target;
	        inputFile >> target;
	        checkMap.clear();
	        int result = check(target);
	        if (result == -1)
	            outputFile << "Case #" << caseIndex << ": Impossible" << endl;
	        else
	            outputFile << "Case #" << caseIndex << ": " << result + 1 << endl;
		}
	    inputFile.close();
	    outputFile.close();
		return 0;
	}


---

Round D

<h6 id="gbuscount">GBus count</h6>

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

<h6 id="sortticket">Sort a scrambled itinerary</h6>

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

<h6 id="cube4">Cube IV</h6>

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

---

<h6 id="itzchess">Itz Chess</h6>

*	Name: Itz Chess
*	Problem: 给出一个国际象棋的棋盘和初始的棋子，根据规则计算“吃棋子”的操作数量
*	Link: https://code.google.com/codejam/contest/6214486/dashboard#s=p3

思路：构建国际象棋的规则即可

代码

	#include <iostream>
	#include <vector>
	#include <string>
	#include <fstream>
	#include <vector>
	
	using namespace std;
	
	enum Piece {
	King = 1,
	Queen, Rook, Bishop, Knight, Pawn
	};
	
	int getType(string &type) {
	    if (type == "K")
	        return Piece::King;
	    if (type == "Q")
	        return Piece::Queen;
	    if (type == "R")
	        return Piece::Rook;
	    if (type == "B")
	        return Piece::Bishop;
	    if (type == "N")
	        return Piece::Knight;
	    if (type == "P")
	        return Piece::Pawn;
	    return 0;
	}
	
	int getNumOnDirection(int i, int j, int direction, int scope, vector<vector<int>> &grid) {
	    int xOffset = 0;
	    int yOffset = 0;
	    switch (direction) {
	    case 1 :
	        xOffset = -1;
	        yOffset = 0;
	        break;
	    case 2 :
	        xOffset = -1;
	        yOffset = 1;
	        break;
	    case 3 :
	        xOffset = 0;
	        yOffset = 1;
	        break;
	    case 4 :
	        xOffset = 1;
	        yOffset = 1;
	        break;
	    case 5 :
	        xOffset = 1;
	        yOffset = 0;
	        break;
	    case 6 :
	        xOffset = 1;
	        yOffset = -1;
	        break;
	    case 7 :
	        xOffset = 0;
	        yOffset = -1;
	        break;
	    case 8 :
	        xOffset = -1;
	        yOffset = -1;
	        break;
	    default :
	        break;
	    }
	
	    for (int k = 1; k <= scope; k++) {
	        i += xOffset;
	        j += yOffset;
	        if (i < 0 || i >= 8 || j < 0 || j >= 8)
	            return 0;
	        if (grid[i][j] > 0)
	            return 1;
	    }
	}
	
	int getKnightNum(int i, int j, vector<vector<int>> &grid) {
	    int num = 0;
	    int index1 = i-1, index2 = j+2;
	    if (index1>=0 && index2<8 && grid[index1][index2] > 0)
	        num++;
	    index2 = j-2;
	    if (index1>=0 && index2>=0 && grid[index1][index2] > 0)
	        num++;
	    index1 = i+1;
	    if (index1<8 && index2>=0 && grid[index1][index2] > 0)
	        num++;
	    index2 = j+2;
	    if (index1<8 && index2<8 && grid[index1][index2] > 0)
	        num++;
	    index1 = i-2, index2 = j+1;
	    if (index1>=0 && index2<8 && grid[index1][index2] > 0)
	        num++;
	    index2 = j-1;
	    if (index1>=0 && index2>=0 && grid[index1][index2] > 0)
	        num++;
	    index1 = i+2;
	    if (index1<8 && index2>=0 && grid[index1][index2] > 0)
	        num++;
	    index2 = j+1;
	    if (index1<8 && index2<8 && grid[index1][index2] > 0)
	        num++;
	    return num;
	}
	
	int getKillNum(int i, int j, int curPiece, vector<vector<int>> &grid) {
	    int num = 0;
	    switch (curPiece) {
	    case Piece::King :
	        num += getNumOnDirection(i, j, 1, 1, grid);
	        num += getNumOnDirection(i, j, 2, 1, grid);
	        num += getNumOnDirection(i, j, 3, 1, grid);
	        num += getNumOnDirection(i, j, 4, 1, grid);
	        num += getNumOnDirection(i, j, 5, 1, grid);
	        num += getNumOnDirection(i, j, 6, 1, grid);
	        num += getNumOnDirection(i, j, 7, 1, grid);
	        num += getNumOnDirection(i, j, 8, 1, grid);
	        break;
	    case Piece::Queen :
	        num += getNumOnDirection(i, j, 1, 8, grid);
	        num += getNumOnDirection(i, j, 2, 8, grid);
	        num += getNumOnDirection(i, j, 3, 8, grid);
	        num += getNumOnDirection(i, j, 4, 8, grid);
	        num += getNumOnDirection(i, j, 5, 8, grid);
	        num += getNumOnDirection(i, j, 6, 8, grid);
	        num += getNumOnDirection(i, j, 7, 8, grid);
	        num += getNumOnDirection(i, j, 8, 8, grid);
	        break;
	    case Piece::Rook :
	        num += getNumOnDirection(i, j, 1, 8, grid);
	        num += getNumOnDirection(i, j, 3, 8, grid);
	        num += getNumOnDirection(i, j, 5, 8, grid);
	        num += getNumOnDirection(i, j, 7, 8, grid);
	        break;
	    case Piece::Bishop :
	        num += getNumOnDirection(i, j, 2, 8, grid);
	        num += getNumOnDirection(i, j, 4, 8, grid);
	        num += getNumOnDirection(i, j, 6, 8, grid);
	        num += getNumOnDirection(i, j, 8, 8, grid);
	        break;
	    case Piece::Knight :
	        num += getKnightNum(i, j, grid);
	        break;
	    case Piece::Pawn :
	        num += getNumOnDirection(i, j, 6, 1, grid);
	        num += getNumOnDirection(i, j, 8, 1, grid);
	        break;
	    default :
	        break;
	    }
	    return num;
	}
	
	int main() {
	
	    // open file
	    ifstream inputFile("D-large-practice.in");
	    ofstream outputFile("output");
	
		int caseNum;
	    inputFile >> caseNum;
	
		for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
	        int n;
	        inputFile >> n;
	        vector<vector<int>> grid(8, vector<int>(8, 0));
	        for (int i = 0; i < n; i++) {
	            string tempStr, pos, type;
	            inputFile >> tempStr;
	            pos = tempStr.substr(0, 2);
	            type = tempStr.substr(3);
	            grid[pos[1] - '1']['H' - pos[0]] = getType(type);
	        }
	        int result = 0;
	        for (int i = 0; i < 8; i++) {
	            for (int j = 0; j < 8; j++) {
	                result += getKillNum(i, j, grid[i][j], grid);
	            }
	        }
	        outputFile << "Case #" << caseIndex << ": " << result << endl;
		}
	    inputFile.close();
	    outputFile.close();
		return 0;
	}

---

<h6 id="cardgame">Card Game</h6>

*	Name: Card Game
*	Problem:  给出若干卡片，每个卡片上有数字，要求对满足c2 - c1 = c3 - c2 = k的连续三张卡片进行消除，求最后剩下的最少卡片数量
*	Link: https://code.google.com/codejam/contest/4214486/dashboard#s=p2

思路：利用动态规划，dp[i][j]代表从卡片i到j经过删除后的最少卡片数量。如果保留第一张卡片，就是dp[i][j] = dp[i+1][j]+1；如果删除第一张，就寻找后续卡片中是否有满足条件的其他两张ii和iii，同时这三张卡片的空隙dp[i+1][ii-1] == 0和dp[ii+1][iii-1] == 0，就是dp[i][j] = 0 + dp[iii+1][j]

代码

	#include <iostream>
	#include <vector>
	#include <string>
	#include <fstream>
	#include <vector>
	#include <algorithm>
	
	using namespace std;
	
	int getBestNum(vector<int> &data, int k, vector<vector<int>> &dp, int i, int j) {
		if (j - i < 2) {
			dp[i][j] = j - i + 1;
			return j - i + 1;
		}
		if (dp[i][j] != -1)
			return dp[i][j];
		int result = getBestNum(data, k, dp, i+1, j)+1;
		for (int ii = i + 1; ii < j; ii++) {
			if (data[ii] - data[i] == k) {
				for (int iii = ii + 1; iii <= j; iii++) {
					if (data[iii] - data[ii] == k) {
	
						int gap1 = 0, gap2 = 0;
						if (ii > i + 1)
							gap1 = getBestNum(data, k, dp, i + 1, ii - 1);
						if (iii > ii + 1)
							gap2 = getBestNum(data, k, dp, ii + 1, iii - 1);
	
						if (gap1 == 0 && gap2 == 0) {
							if (iii == j)
								result = 0;
							else
								result = min(result, getBestNum(data, k, dp, iii + 1, j));
						}
					}
				}
			}
		}
		dp[i][j] = result;
		return result;
	}
	
	int main() {
	
		// open file
		ifstream inputFile("C-large-practice.in");
		ofstream outputFile("output");
	
		int caseNum;
		inputFile >> caseNum;
	
		for (int caseIndex = 1; caseIndex <= caseNum; caseIndex++) {
			int num, k;
			inputFile >> num >> k;
			vector<vector<int>> dp(num, vector<int>(num, -1));
			vector<int> data(num, 0);
			for (int i = 0; i < num; i++) {
				inputFile >> data[i];
			}
			int result = getBestNum(data, k, dp, 0, num-1);
			outputFile << "Case #" << caseIndex << ": " << result << endl;
		}
		inputFile.close();
		outputFile.close();
		return 0;
	}

---
