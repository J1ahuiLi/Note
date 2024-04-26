### 二分查找（Binary Search）
```C++
//[left, right]
int binarySearch(int target, vector<int> record){
  int left = 0;
  int right = record.size() - 1;
  while(left <= right){
    int mid = left + ((right - left) / 2);
    if(target > record[mid]){
      left = mid + 1;
    }
    else if(target < record[mid]){
      right = mid - 1;
    }
    else {
      return mid;
    }
  }
}

//[left, right)
int binarySearch(int target, vector<int> record){
  int left = 0;
  int right = record.size() - 1;
  while(left < right){
    int mid = left + ((right - left) / 2);
    if(target > record[mid]){
      left = mid + 1;
    }
    else if(target < record[mid]){
      right = mid;
    }
    else {
      return mid;
    }
  }
}
```
### 双指针（Double Pointer）
1. 一头一尾  
   初始化一头一尾的指针，然后对序列进行操作（交换，比较等），再向序列的中间靠拢。
2. 两个头（非链表）
  ```C++
  //给定一个数组nums和一个值val，原地移除所有等于val的元素，并返回长度。
  //1. 初始化一个快指针fast，一个慢指针slow。
  //2. fast：每次移动一步
  //3. slow：fast不指向val时才移动。
  def removeElement(self, nums: List[int], val: int) -> int:
    int slow = 0;
    for(int fast = 0; fast < nums.size(); fast++){
      if(nums[fast] != val){
        nums[slow] = nums[fast];
        slow++;
      }
    }
  ```
3. 两个头（链表）  
   走法一：一个指针每次移动一步，另一个指针每次移动两步。  
   走法二：一个指针先移动n步，然后两个指针同时以每次一步的速度移动。（滑动窗口）
4. 碰撞指针  
   一般是排好序的数组或链表
### 贪心算法（greedy algorithm）
局部最优，从而达到全局最优。
