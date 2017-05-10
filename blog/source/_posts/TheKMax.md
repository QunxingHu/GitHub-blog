---
title: 寻找第K大数的方法小结
date: 2017-2-23 12:11:30 PM
categories:
- 术业专攻
tags: 
- 算法
---
在 leet code oj 上， 刷到 `Third Maximum Number` 这类问题，总结记录下解决此类问题的方法。
<!-- more -->
## 题目描述
**Given a non-empty array of integers, return the third maximum number in this array. If it does not exist, return the maximum number. The time complexity must be in O(n).**
- Example 1:
	Input: [3, 2, 1]
	Output: 1
    Explanation: The third maximum is 1.

- Example 2:
	Input: [1, 2]
	Output: 2
	Explanation: The third maximum does not exist, so the maximum (2) is returned instead.

- Example 3:
	Input: [2, 2, 3, 1]
	Output: 1
	Explanation: Note that the third maximum here means the third maximum distinct number.
Both numbers with value 2 are both considered as second maximum.

简而言之， 就是在一个包含N个数的数组中，找出第3个最大值，时间复杂度请控制在O(n) 范围内。如果不存在第3个最大值，请返回数组的最大值。
该题不具备通用性，指定只需要第3大的数，可以通过只需维护三个最大数，遍历数组与之比较，从而就可以得到结果，时间复杂度为 O(3n)，但是如果是需要第k大的数，就需要维护k个最大数，最坏情况下，k=n,复杂度为 O(n^2).
如下解法，即为维护三个最大数的解法：
```
class Solution {
public:
    int thirdMax(vector<int>& nums) {
        long long mx, sec, thr;
        mx = sec = thr = LONG_MIN;
        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] == mx || nums[i] == sec || nums[i] <= thr) {
                continue;
            }
            if (nums[i] > mx) {
                thr = sec;
                sec = mx;
                mx = nums[i];
            } else if (nums[i] > sec) {
                thr = sec;
                sec = nums[i];
            } else if (nums[i] > thr) {
                thr = nums[i];
            }
        }
        return thr == LONG_MIN ? mx : thr;
    }
};
```

## 解题思路
1. 使用排序算法，排序后，取出第k个数，最优复杂度 O（nlogn+k）
2. 使用选择排序，或者冒泡排序， K次选择后，时间复杂度为 O（KN）
3. 利用快速排序的思想，从数组S中随机找出一个元素X，把数组分为两部分Sa和Sb。Sa中的元素大于等于X，Sb中元素小于X。这时有两种情况：
   - Sa中元素的个数小于k，则Sb中的第k-|Sa|个元素即为第k大数；
   - Sa中元素的个数大于等于k，则返回Sa中的第k大数。时间复杂度近似为O(n)
4. 二分[Smin,Smax]查找结果X，统计X在数组中出现，且整个数组中比X大的数目为k-1的数即为第k大数。时间复杂度平均情况为O(nlogn)
5. 用O(4n)的方法对原数组建最大堆，然后pop出k次即可。时间复杂度为O(4n + klogn)
6. 维护一个k大小的最小堆，对于数组中的每一个元素判断与堆顶的大小，若堆顶较大，则不管，否则，弹出堆顶，将当前值插入到堆中。时间复杂度O(nlogk)
7. 利用hash保存数组中元素Si出现的次数，利用计数排序的思想，线性从大到小扫描过程中，前面有k-1个数则为第k大数，平均情况下时间复杂度O(n)
8. 利用STL的SET有序属性，对数组建立set,复杂度 O(nlogn)

## 解决代码
1. 解法2对应的代码
	```
	class Solution {
	public:
		int thirdMax(vector<int>& nums) {
			int n = nums.size();
			int maxIndex = 0;
			int maxValue = nums[0];

			if(n==1)
				return nums[0];
			else if(n==2)
				return nums[0]>nums[1]?nums[0]:nums[1];
			//选择排序，但是无法确定重复数有多少，所以不能确定排多少次，故全排
			for(int i=0; i<n-1;i++){
				maxIndex = i;
				maxValue = nums[i];
				for(int j=i+1; j<n ;j++){
					if(nums[j] > maxValue){
						maxValue = nums[j];
						maxIndex = j;
					}
				}
				int temp = nums[i];
				nums[i] = maxValue;
				nums[maxIndex] = temp;
			}
			// 返回第K个数，跳过重复值
			int count = 1;
			for(int i=0; i<n-1; i++){
				if(nums[i] == nums[i+1])
					continue;
				else{
					count++;
					if(count == 3)
						return nums[i+1];
				}
			}
			return nums[0];
		}
	};
	```

2. 解法3对应的代码
	```
	#include <iostream>  
	using namespace std;

	const int N = 8;   
	const int K = 4;

	int partition(int  a[] ,int low , int high)   
	{  
		int i = low - 1;  
		int j = low;  

		while(j < high){  
		 if(a[j] >=  a[high]){  
		   swap( a[i+1] , a[j]);
		   i++;
		 }
		 j++;
		}
		//最后处理a[high]
		swap(a[i+1] , a[high]);
		return i + 1;
	}  

	int findk(int  a[] , int low , int high , int k)  
	{  
	   if(low < high){  
			int q = partition(a , low , high) ;  
			int len = q - low + 1 ; //表示第几个位置    

			if(len == k)  
				return q ; //返回第k个位置   
			else if(len < k)   
				return findk(a , q + 1 , high , k - len) ;     
			else  
				return findk(a , low , q - 1, k ) ;  
		}  
	}  

	int main()  
	{  
		int a[N] = {5 ,2 ,66 ,23, 11 ,1 ,4 ,55} ;  
		findk(a , 0 , N - 1 , K) ;    

		for(int i = 0 ; i < K ; i++)  
			cout<<a[i]<<endl ;  
		system("pause") ;    
		return 0 ;      
	} 
	```

3. 解法8对应的代码
	```
	class Solution {
	public:
		// 建立 set，顺序从大至小， set 的大小只需要维护 k 个size即可  
		int kMax(vector<int>& nums, int k){
			set<int> topN;
			for(auto num: nums){
				topN.insert(num);
				//如果超出k个，剔除掉当前的第k大数
				if(topN.size()>k)
					topN.erase(topN.begin());
			}
			//如果不存在第k大数，直接返回最大值
			return (topN.size() >= k) ? *(topN.begin()): *(topN.rbegin()); 
		}
		int thirdMax(vector<int>& nums) {
			return kMax(nums,3);
		}
	};
	```
**注意：** 数组中可能存在重复值，重复值需要进行过滤。
