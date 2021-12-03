# Leetcode Medium
### 2. Add Two Numbers
#### idea
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        int carry = 0;
        ListNode* ans = new ListNode(0);
        ListNode* tail = ans;
        while(l1 || l2){
            ListNode* temp;
            if(l1 && l2){
                temp = new ListNode((l1->val + l2->val + carry)%10);
                carry = (l1->val + l2->val + carry)/10;
                l1 = l1->next;
                l2 = l2->next;
            }
            else if(l1 && !l2){
                temp = new ListNode((l1->val + carry)%10);
                carry = (l1->val + carry)/10;
                l1 = l1->next;
            }
            else if(!l1 && l2){
                temp = new ListNode((l2->val + carry)%10);
                carry = (l2->val + carry)/10;
                l2 = l2->next;
            }
            tail->next = temp;
            tail = tail->next;
        }
        if(carry){
            ListNode* temp = new ListNode(carry);
            tail->next = temp;
        }
        return ans->next;
    }
};
```
### 3. Longest Substring Without Repeating Characters
#### idea
* 技巧 : sliding window
* 用 1 個 set 與 2 個 pointer。
```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int ans = 0;
        int left = 0;
        int right = 0;
        unordered_set<char> set;
        for(int right=0; right<s.size(); right++){
            while(set.count(s[right])){
                set.erase(s[left]);
                left++;
            }
            set.insert(s[right]);
            ans = max(ans, right-left+1);
        }
        return ans;
    }
};
```
### 11. Container With Most Water
#### idea
```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int i = 0; 
        int j = height.size()-1;
        int ans = 0;
        while(i<j){
            int h = min(height[i], height[j]);
            int w = j-i;
            ans = max(ans, h*w);
            if(height[i]>height[j])
                j--;
            else
                i++;
        }
        return ans;
    }
};
```
### 15. 3Sum
#### idea
* 沒啥技巧，就是有點技術的暴力法，i, left, right 要檢查下一輪的元素是否有重複，如果有重複則跳過，來避免時間過長。
```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int> &nums) {
        vector<vector<int>> ans;
        sort(nums.begin(), nums.end());
        for(int i=0; i<nums.size(); i++){
            int left = i+1;
            int right = nums.size()-1;
            int target = -nums[i];
            while(left<right){
                int sum = nums[left]+nums[right];
                if(sum>target)
                    right--;
                else if(sum<target)
                    left++;
                else{
                    vector<int> trip = {nums[i], nums[left], nums[right]};
                    ans.push_back(trip);
                    while(left<right && nums[left]==trip[1])
                        left++;
                    while(left<right && nums[right]==trip[2])
                        right--;
                }
            }
            while((i+1)<nums.size() && nums[i]==nums[i+1])
                i++;
        }
        return ans;
    }
};
```
### 17. Letter Combinations of a Phone Number
#### idea
* DFS
* Recursive
```c++
class Solution {
public:
    vector<string> mappings = {"abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"}, ans; 
    vector<string> letterCombinations(string digits) {
        if(digits=="")
            return ans;
        dfs(digits, 0, "");
        return ans;
    }
    
    void dfs(string& digits, int i, string combination){
        if(i==digits.size()){
            ans.push_back(combination);
            return;
        }
        for(auto char_ : mappings[digits[i]-'2'])
            dfs(digits, i+1, combination+char_);
    }
};
```
### 19. Remove Nth Node From End of List
#### idea
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        int length = 0;
        struct ListNode* temp = head;
        while(temp){
            length++;
            temp = temp->next;
        }
        if(length==1 && n==1)
            return NULL;
        else if(length==n)
            return head->next;
        length = length-n;
        temp = head;
        while(length>1){
            length--;
            temp = temp->next;
        }
        temp->next = temp->next->next;
        return head;
    }
};
```
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        int length = 0;
        ListNode* temp = head;
        ListNode* ans = new ListNode(0);
        ans->next = head;
        while(temp){
            length++;
            temp = temp->next;
        }
        length-=n;
        temp = ans;
        while(length){
            length--;
            temp = temp->next;
        }
        if(temp->next->next){
            ListNode* prev = temp->next->next;
            temp->next = prev;
        }
        else
            temp->next = NULL;
        return ans->next;
    }
};
```
### 22. Generate Parentheses
#### idea
![](https://i.imgur.com/K3cuZYV.jpg)
```c++
class Solution {
public:
    vector<string> ans;
    vector<string> generateParenthesis(int n) {
        dfs(0, 0, n, "");
        return ans;
    }
    
    void dfs(int left, int right, int n, string s){
        if(s.length()==n*2){
            ans.push_back(s);
            return;
        }
        if(left<n)
            dfs(left+1, right, n, s+"(");
        if(right<left)
            dfs(left, right+1, n, s+")");
    }
};
```
### 24. Swap Nodes in Pairs
#### idea
* Recursive
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if(head==NULL || head->next==NULL)
            return head;
        struct ListNode* temp = head->next;
        head->next = swapPairs(head->next->next);
        temp->next = head;
        return temp;
    }
};
```
### 33. Search in Rotated Sorted Array
#### idea
* 不難，用一般的 binary search 即可。
* 先判斷 left, mid, right 的相對大小，再來決定往左還是往右。
```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size()-1;
        while(left<=right){
            int mid = left+(right-left)/2;
            if(nums[mid]==target)
                return mid;
            if(nums[mid]>=nums[left]){
                if(target>=nums[left] && target<nums[mid])
                    right = mid-1;
                else
                    left = mid+1;
            }
            else{
                if(target>nums[mid] && target<=nums[right])
                    left = mid+1;
                else
                    right = mid-1;
            }
        }
        return -1;
    }
};
```
### 34. Find First and Last Position of Element in Sorted Array
#### original idea
* Binary Search
* 先用二分搜尋找到指定的 target
* 找到後，左右兩邊分頭線性搜尋找到 start 與 end
```c++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size()-1;
        bool find = false;
        int m;
        while(left<=right){
            m = left+(right-left)/2;
            if(target-nums[m]>0)
                left = m+1;
            else if(target-nums[m]<0)
                right = m-1;
            else{
                find = true;
                break;
            }
        }
        int start = m;
        int end = m;
        if(find){
            while(true){
                if(end+1<nums.size() && nums[end+1]==target)
                    end++;
                else if(0<=start-1 && nums[start-1]==target)
                    start--;
                else
                    break;
            }
            return {start, end};
        }
        else
            return {-1, -1};
    }
};
```
```
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> ans = {-1, -1};
        int left = 0;
        int right = nums.size()-1;
        while(left<=right){
            int mid = left+(right-left)/2;
            if(nums[mid]==target){
                left = mid;
                right = mid;
                while(left-1>=0 && nums[left]==nums[left-1])
                    left--;
                while(right+1<=nums.size()-1 && nums[right]==nums[right+1])
                    right++;
                ans = {left, right};
                break;
            }
            else if(nums[mid]<target)
                left = mid+1;
            else
                right = mid-1;
        }
        return ans;
    }
};
```
#### better idea
```c++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        return {find(nums, target, "left"), find(nums, target, "right")};
    }
    
    int find(vector<int>& nums, int target, string mode){
        int left = 0;
        int right = nums.size()-1;
        int position = -1;
        while(left<=right){
            int mid = left+(right-left)/2;
            if(nums[mid]==target){
                position = mid;
                (mode=="left") ? right = mid-1 : left = mid+1;
            }
            else if(nums[mid]<target)
                left = mid+1;
            else
                right = mid-1;
        }
        return position;
    }
};
```
### 36. Valid Sudoku
#### idea
```c++
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        vector<unordered_set<int>> row(9), col(9), block(9);
        for(int i=0; i<9; i++){
            for(int j=0; j<9; j++){
                if(board[i][j]=='.')
                    continue;
                int num = board[i][j]-'0';
                if(row[i].count(num) || col[j].count(num) || block[(i/3)*3+j/3].count(num))
                    return false;
                row[i].insert(num);
                col[j].insert(num);
                block[(i/3)*3+j/3].insert(num);
            }
        }
        return true;
    }
};
```
### 39. Combination Sum
#### idea
* DFS
* Recursive
* 利用不斷的遞迴來找到各種排列組合
```c++
class Solution {
public:
    vector<vector<int>> ans;
    vector<int> subAns;
    void dfs(vector<int>& candidates, int target, int index, int sum){
        if(sum>target)
            return;
        if(sum==target)
            ans.push_back(subAns);
        for(int i=index; i<candidates.size(); i++){
            sum+=candidates[i];
            subAns.push_back(candidates[i]);
            dfs(candidates, target, i, sum);
            sum-=candidates[i];
            subAns.pop_back();
        }
    }
    vector<vector<int>> combinationSum(vector<int>& candidates, int target){
        int sum = 0;
        dfs(candidates, target, 0, sum);
        return ans;
    }
};
```
### 40. Combination Sum II
#### idea
* DFS
* Recursive
* 在每次 pop 時用個變數 nowValue 存起來，進到下一輪時拿來比對，若相同則跳過進到下下一輪。
```c++
class Solution {
public:
    vector<vector<int>> ans;
    vector<int> subAns;
    void dfs(vector<int>& candidates, int target, int index, int sum){
        int nowValue = -1;
        if(sum>target)
            return;
        if(sum==target){
            ans.push_back(subAns);
            return;
        }
        for(int i=index; i<candidates.size(); i++){
            if(nowValue!=candidates[i]){
                sum+=candidates[i];
                subAns.push_back(candidates[i]);
                dfs(candidates, target, i+1, sum);
                sum-=candidates[i];
                nowValue = subAns.back();
                subAns.pop_back();
            }
        }
    }
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        int sum = 0;
        sort(candidates.begin(), candidates.end());
        dfs(candidates, target, 0, sum);
        return ans;
    }
};
```
### 46. Permutations
#### idea
```c++
class Solution {
public:
    vector<vector<int>> ans;
    void dfs(vector<int>& nums, int index){
        if(index==nums.size()-1){
            ans.push_back(nums);
            return;
        }
        for(int i=index; i<nums.size(); i++){
            swap(nums[index], nums[i]);
            dfs(nums, index+1);
            swap(nums[index], nums[i]);
        }
    }
    
    vector<vector<int>> permute(vector<int>& nums) {
        dfs(nums, 0);
        return ans;
    }
};
```
### 47. Permutations II
#### idea
* DFS
* Recursive
* 與 46 題不同的是該題是採用 call by value，所以不需要用到第二次 swap
```c++
class Solution {
public:
    vector<vector<int>> ans;
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        dfs(nums, 0);
        return ans;
    }
    
    void dfs(vector<int> nums, int index){
        if(index==nums.size()-1){
            ans.push_back(nums);
            return;
        }
        
        for(int i=index; i<nums.size(); i++){
            if(index!=i && nums[index]==nums[i])
                continue;
            swap(nums[i], nums[index]);
            dfs(nums, index+1);
        }
    }
};
```
### 48. Rotate Image
#### idea
![](https://i.imgur.com/ctJLULv.png)

```c++
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int circle = matrix.size();
        int depth = circle/2;
        for(int i=0; i<depth; i++){
            int opp = circle-i-1;
            int walk = circle-2*i-1;
            for(int j=0; j<walk; j++){
                int temp = matrix[i][i+j];
                matrix[i][i+j] = matrix[opp-j][i];
                matrix[opp-j][i] = matrix[opp][opp-j];
                matrix[opp][opp-j] = matrix[i+j][opp];
                matrix[i+j][opp] = temp;
            }
        }
    }
};
```
### 62. Unique Paths
#### idea
* DP
```c++
key : 
dp[i][j] = dp[i-1][j] + dp[i][j-1]

first step :
把 DP 的第 0 row 與第 0 col 填 1，因為都只有一種走法
ex: (0, 0) -> (0, 2) 只有一種走法
1 1 1
1 0 0
1 0 0
```
```
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<int> row(n, 0);
        vector<vector<int>> dp(m, row);
        for(int j=0; j<dp[0].size(); j++)
            dp[0][j] = 1;
        for(int i=0; i<dp.size(); i++)
            dp[i][0] = 1;
        for(int i=1; i<dp.size(); i++){
            for(int j=1; j<dp[0].size(); j++){
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
};
```
### 64. Minimum Path Sum
#### idea
* DP
```c++
dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + grid[i][j]
```
```
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        vector<int> row(grid[0].size(), 0);
        vector<vector<int>> dp(grid.size(), row);
        for(int i=0; i<dp.size(); i++){
            for(int j=0; j<dp[0].size(); j++){
                if(i==0 && j==0)
                    dp[i][j] = grid[i][j];
                else if(i==0)
                    dp[i][j] = dp[i][j-1]+grid[i][j];
                else if(j==0)
                    dp[i][j] = dp[i-1][j]+grid[i][j];
                else
                    dp[i][j] = min(dp[i-1][j]+grid[i][j], dp[i][j-1]+grid[i][j]);
            }
        }
        return dp[grid.size()-1][grid[0].size()-1];
    }
};
```
### 75. Sort Colors
#### original idea
* 用 quick sort
```c++
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int left = 0; 
        int right = nums.size()-1;
        quickSort(nums, left, right);
    }
    
    void swap(int *a, int *b){
        int temp = *a;
        *a = *b;
        *b = temp;
    }

    int partition(vector<int>& arr, int left, int right){
        int pivot = arr[right];
        int i = left-1; 
        for(int j=left; j<right; j++){
            if(arr[j]<pivot){
                i++;
                swap(&arr[i], &arr[j]);
            }
        }
        swap(&arr[i+1], &arr[right]);
        return i+1;
    }

    void quickSort(vector<int>& arr, int low, int high){
        if(low<high){
            int pi = partition(arr, low, high);
            quickSort(arr, low, pi-1);
            quickSort(arr, pi+1, high);
        }
    }
};
```
#### better idea
* 用 for loop i 掃過一遍，在 left 之前的都是 0，在 right 之後的都是 2
```c++
class Solution {
public:
    void sortColors(vector<int>& nums) {
        for(int i=0, left=0, right=nums.size()-1; i<=right; i++){ 
            if(nums[i]==0) 
                swap(nums[i], nums[left++]);         
            else if(nums[i] ==2) 
                swap(nums[i--], nums[right--]); 
        }
    }
};
```
### 77. Combinations
#### original idea
* 這個想法應該沒錯但是寫不出來
* 取第 i 個值，接著用遞迴從 i+1 挑 k-1 的值出來
#### Correct idea
* DFS
* Recursive
* 換位思考
* 當 index>n-k 時，代表該 index 值必取
* 這種 Combinations 的題目解法大同小異
![](https://i.imgur.com/z5A9FLV.png)
![](https://i.imgur.com/o0GqEZt.jpg)
```c++
class Solution {
public:
    vector<vector<int>> ans;
    vector<vector<int>> combine(int n, int k) {
        vector<int> temp;
        dfs(n, k, 1, temp);
        return ans;
    }
    void dfs(int n, int k, int index, vector<int> temp){
        if(k==0){
            ans.push_back(temp);
            return;
        }
        if(index<=n-k)
            dfs(n, k, index+1, temp); //(1)
        temp.push_back(index);
        dfs(n, k-1, index+1, temp);   //(2)
    }
};
```
### 78. Subsets
#### idea
* DFS
* Recursive
```c++
class Solution {
public:
    vector<vector<int>> ans;
    void dfs(vector<int>& nums, int start, vector<int> subAns){
        if(start>nums.size()-1)
            return;
        for(int i=start; i<nums.size(); i++){
            subAns.push_back(nums[i]);
            ans.push_back(subAns);
            dfs(nums, i+1, subAns);
            subAns.pop_back();
        }
    }
    vector<vector<int>> subsets(vector<int>& nums) {
        ans.push_back({});
        int start = 0;
        vector<int> subAns;
        dfs(nums, start, subAns);
        return ans;
    }
};
```
### 79. Word Search
#### idea
* DFS
* Recursive
```c++
class Solution {
public:
    bool dfs(vector<vector<char>>& board, string word, int i, int j, int index){
        if(board[i][j]==word[index]){
            if(index==word.length()-1)
                return true;
            char c = board[i][j];
            board[i][j] = '1';
            if(0<=i-1 && board[i-1][j]!='1')
                if(dfs(board, word, i-1, j, index+1))
                    return true;
            if(i+1<board.size() && board[i+1][j]!='1')
                if(dfs(board, word, i+1, j, index+1))
                    return true;
            if(0<=j-1 && board[i][j-1]!='1')
                if(dfs(board, word, i, j-1, index+1))
                    return true;
            if(j+1<board[0].size() && board[i][j+1]!='1')
                if(dfs(board, word, i, j+1, index+1))
                    return true;
            board[i][j] = c;
            return false;
        }   
        return false;
    }
    
    bool exist(vector<vector<char>>& board, string word) {
        for(int i=0; i<board.size(); i++){
            for(int j=0; j<board[0].size(); j++){
                if(dfs(board, word, i, j, 0))
                    return true;
            }
        }
        return false;
    }
};
```
### 90. Subsets II
#### idea
* DFS
* Recursive
```c++
class Solution {
public:
    vector<vector<int>> ans;
    void dfs(vector<int>& nums, int start, vector<int> subAns){
        int temp = -11;
        if(start>nums.size())
            return;
        for(int i=start; i<nums.size(); i++){
            if(nums[i]!=temp){
                subAns.push_back(nums[i]);
                ans.push_back(subAns);
                dfs(nums, i+1, subAns);
                temp = subAns.back();
                subAns.pop_back();
            }
        }
    }
    
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        ans.push_back({});
        vector<int> subAns;
        dfs(nums, 0, subAns);
        return ans;
    }
};
```
### 95. Unique Binary Search Trees II
* Recursive 
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<TreeNode*> generateBST(int start, int end){
        vector<TreeNode*> treeSet;
        if(start>end){
            treeSet.push_back(NULL);
            return treeSet;
        } 
        for(int i=start; i<=end; i++){
            vector<TreeNode*> leftTree = generateBST(start, i-1);
            vector<TreeNode*> rightTree = generateBST(i+1, end);
            for(auto leftNode:leftTree){
                for(auto rightNode:rightTree){
                    TreeNode* node = new TreeNode();
                    node->val = i;
                    node->left = leftNode;
                    node->right = rightNode;
                    treeSet.push_back(node);
                }
            }
        }
        return treeSet;
    }
    
    vector<TreeNode*> generateTrees(int n) {
        vector<TreeNode*> treeSet;
        if(n==0) return treeSet;
        return generateBST(1, n);
    }
};
```
### 109. Convert Sorted List to Binary Search Tree
#### idea
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* build(ListNode* left, ListNode* right){
        if(left==right)
            return NULL;
        if(left->next == right)
            return new TreeNode(left->val);
        struct ListNode* mid = left;
        struct ListNode* fast = left;
        while(fast->next != right){
            mid = mid->next;
            fast = fast->next;
            if(fast->next == right)
                break;
            fast = fast->next;
        }
        struct TreeNode* root = new TreeNode(mid->val);
        root->left = build(left, mid);
        root->right = build(mid->next, right);
        return root;
    }
    TreeNode* sortedListToBST(ListNode* head) {
        return build(head, NULL);
    }
};
```
### 128. Longest Consecutive Sequence
#### idea
* 用 map
* 題目要求 O(n)，所以不能用紅黑數的 map，要使用 hashTable 的 unordered_map
* 針對每個 num 值，看看 map 內有沒有比它小 1 的值，若有的話，則代表該 num 還不是連續子序列的起點，所以用 continue 進到下一輪。
* 若 map 內沒有該 num 小 1 的值了，則該 num 必定是連續子序列的起點，可以計算該連續子序列的長度。 
```c++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        int ans = 1;
        unordered_map<int, int> dic;
        for(auto num:nums)
            dic[num]++;
        for(auto [num, count]:dic){
            if(dic.count(num-1))
                continue;
            int temp = 1;
            while(dic.count(num+temp))
                temp++;
            ans = max(ans, temp);
        }
        return nums.size()==0 ? 0 : ans;
    }
};
```
### 130. Surrounded Regions
#### Wrong idea
* DFS
* 對 'O' 去做DFS，經過的都改為 'X'
```c++
class Solution {
public:
    static bool inBoundary(int x, int y, int row, int col){
        int dirs[8][2] = {{-1, -1}, {0, -1}, {1, -1},
                          {-1, 0},           {1, 0}, 
                          {-1, 1}, {0, 1}, {1, 1}};
        for(auto dir:dirs){
            int newX = x+dir[0];
            int newY = y+dir[1];
            if(newX<0 || row<newX || newY<0 || col<newY){
                return false;
            }
        }
        return true;
    }
    
    void solve(vector<vector<char>>& board) {
        for(int i=0; i<board.size(); i++){
            for(int j=0; j<board[0].size(); j++){
                if(board[i][j]=='O' && inBoundary(i, j, board.size()-1, board[0].size()-1)){
                    board[i][j] = 'X';
                }
            }
        }
    }
};
```
#### Better Idea
* DFS
* Recursive
* 題意只有上下左右 4 個方向，並不是 8 個
* 針對邊界有 0 的去做 DFS，找出所有邊界為 0 的
```c++
class Solution {
public:
    static void dfs(vector<vector<char>>& board, int i, int j){        
        if(i<0 || board.size()<=i || j<0 || board[0].size()<=j)
            return;
        if(board[i][j]!='O')
            return;
        board[i][j] = '1';
        dfs(board, i+1, j);
        dfs(board, i, j+1);
        dfs(board, i-1, j);
        dfs(board, i, j-1);
        return;
    }
    
    void solve(vector<vector<char>>& board) {
        if(board.size()==0 || board[0].size()==0)
            return;
        
        for(int i=0; i<board[0].size(); i++){
            if(board[0][i]=='O')
                dfs(board, 0, i);
            if(board[board.size()-1][i]=='O')
                dfs(board, board.size()-1, i);
        }
        
        for(int i=1; i<board.size()-1; i++){
            if(board[i][0]=='O')
                dfs(board, i, 0);
            if(board[i][board[0].size()-1]=='O')
                dfs(board, i, board[0].size()-1);
        }
        
        for(int i=0; i<board.size(); i++){
            for(int j=0; j<board[0].size(); j++){
                if(board[i][j]!='1')
                    board[i][j] = 'X';
                if(board[i][j]=='1')
                    board[i][j] = 'O';
            }
        }
    }
};
```
### 131. Palindrome Partitioning
#### idea
* DFS
* Recursive
* 範例
    * s = "aabgv"
        1. a abgv
        2. aa bgv
        3. aab gv
        4. aabg v
        5. aabgv
```c++
class Solution {
public:
    vector<vector<string>> ans = {};
    bool isPalindrome(string s){
        int left = 0;
        int right = s.length()-1;
        while(left<right){
            if(s[left]!=s[right])
                return false;
            left++; right--;
        }
        return true;
    }
    void dfs(string& s, vector<string>& subAns){
        if(s.length()==0){
            ans.push_back(subAns);
            return;
        }
        for(int i=0; i<s.length(); i++){
            string left = s.substr(0, i+1);
            string right = s.substr(i+1);
            if(isPalindrome(left)){
                subAns.push_back(left);
                dfs(right, subAns);
                subAns.pop_back();
            }
        }   
    }
    vector<vector<string>> partition(string s) {
        vector<string> subAns = {};
        dfs(s, subAns);
        return ans;
    }
};
```
### 153. Find Minimum in Rotated Sorted Array
#### idea
* Binary Search
* 用 nums[right] - nums[m] 是否大於 0 來判斷要往左還是往右
```c++
class Solution {
public:
    int findMin(vector<int>& nums) {
        int left = 0;
        int right = nums.size()-1;
        int m;
        int ans = INT_MAX;
        while(left<=right){
            m = left+(right-left)/2;
            if(nums[m]<ans) ans = nums[m];
            if(nums[right]-nums[m] < 0)
                left = m+1;
            else
                right = m-1;
        }
        return ans;
    }
};
```
### 198. House Robber
#### idea
* DP
* dp[i] = max(dp[i-1], nums[i]+dp[i-2])
```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        if(nums.size()==1) 
            return nums[0];
        else if(nums.size()==2)
            return max(nums[0], nums[1]);
        else{
            int dp[nums.size()];
            dp[0] = nums[0];
            dp[1] = max(nums[0], nums[1]);
            for(int i=2; i<nums.size(); i++){
                dp[i] = max(dp[i-1], nums[i]+dp[i-2]);
            }
            return dp[nums.size()-1];
        }
    }
};
```
#### other idea
* DP
* dp[i] = max(dp[i-1], dp[i-2]+nums[i-1]);
* dp 內的 index 不能想成一般 array 從 0 開始的 index，要想成偷到第幾間的最大直
```c++
nums
2  1  2  2

 i  0  1  2  3  4
dp  0  2  2  3  4
```
```
class Solution {
public:
    int rob(vector<int>& nums) {
        if(nums.size()==1)
            return nums[0];
        vector<int> dp(nums.size()+1, 0);
        dp[1] = nums[0];
        for(int i=2; i<=nums.size(); i++){
            dp[i] = max(dp[i-1], dp[i-2]+nums[i-1]);
        }
        return dp[nums.size()];
    }
};
```
### 200. Number of Islands
#### Original idea
* DFS
* Recursive
* 用 stack 與 set<pair<int, int>>
* 48 / 48 test cases passed. But too long.
* code 沒錯，但會 Time Limit Exceeded
```c++
class Solution {
public:
    static void DFS(vector<vector<char>> grid, stack<pair<int, int>>* s, set<pair<int, int>>* record){
        pair<int, int> p = (*s).top();
        (*s).pop();
        int dirs[4][2] = {{0, -1}, {1, 0}, {0, 1}, {-1, 0}};
        for(auto dir:dirs){
            int newX = p.first+dir[0];
            int newY = p.second+dir[1];
            if(0<=newX && newX<grid.size() && 0<=newY && newY<grid[0].size() && grid[newX][newY]=='1' && !(*record).count({newX, newY})){
                (*s).push(make_pair(newX, newY));
                (*record).insert(make_pair(newX, newY));
                DFS(grid, s, record);
            }
        }
    }
    
    int numIslands(vector<vector<char>>& grid) {
        if(grid.size()==1 && grid[0].size()==1 && grid[0][0]=='1') 
            return 1;

        set<pair<int, int>> record;
        stack<pair<int, int>> s;
        int ans = 0;
        bool path = false;
        for(int i=0; i<grid.size(); i++){
            for(int j=0; j<grid[0].size(); j++){
                if(grid[i][j]=='1' && !record.count({i, j})){
                    path = true;
                    ans++;
                    s.push(make_pair(i, j));
                    DFS(grid, &s, &record);
                }        
            }
        }
        if(path) 
            return ans;
        else 
            return 0;
    }
};
```
#### Better idea
* DFS
* Recursive
```c++
class Solution {
public:
    static void DFS(vector<vector<char>>& grid, int i, int j){
        if(0>i || i>=grid.size() || 0>j || j>=grid[0].size())
            return;
        if(grid[i][j]=='0' || grid[i][j]=='2')
            return;
        grid[i][j] = '2';
        DFS(grid, i+1, j);
        DFS(grid, i-1, j);
        DFS(grid, i, j+1);
        DFS(grid, i, j-1);
    }
    
    int numIslands(vector<vector<char>>& grid) {
        int ans = 0;
        for(int i=0; i<grid.size(); i++){
            for(int j=0; j<grid[0].size(); j++){
                if(grid[i][j]=='1'){
                    DFS(grid, i, j);
                    ans++;
                }        
            }
        }
        return ans;
    }
};
```
### 213. House Robber II
#### idea
* DP
* dp[i] = max(dp[i-1], nums[i]+dp[i-2])
```
把 array 拆分成下列情形，分別求 dp

          1 2 3 1
          /     \
       1 2 3    2 3 1
```
```c++
class Solution {
public:
    int robber(vector<int>& nums){
        int dp[nums.size()];
        dp[0] = nums[0];
        dp[1] = max(nums[0], nums[1]);
        for(int i=2; i<nums.size(); i++){
            dp[i] = max(dp[i-1], nums[i]+dp[i-2]);
        }
        return dp[nums.size()-1];
    }
    
    int rob(vector<int>& nums) {
        if(nums.size()==1) 
            return nums[0];
        if(nums.size()==2)
            return max(nums[0], nums[1]);
        
        vector<int> v1(nums.begin(), nums.end()-1);
        vector<int> v2(nums.begin()+1, nums.end());
        return max(robber(v1), robber(v2));
    }
};
```
### 215. Kth Largest Element in an Array
#### idea - 1
* 先用 quickSort 排序，之後從頭掃一遍找出第K大的值
```c++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        //k--;
        int index = 0;
        quickSort(nums, 0, nums.size()-1);
        for(int i=nums.size()-1; i>0; i--){
            k--;
            if(k==0) index = i;
        }
        return nums[index];
    }
    
    void swap(int *a, int *b){
        int temp = *a;
        *a = *b;
        *b = temp;
    }

    int partition(vector<int>& arr, int left, int right){
        // cout<<"in"<<endl;
        int pivot = arr[right];
        int i = left-1; 
        for(int j=left; j<right; j++){
            if(arr[j]<=pivot){
                i++;
                swap(&arr[i], &arr[j]);
            }
        }
        swap(&arr[i+1], &arr[right]);
        return i+1;
    }

    void quickSort(vector<int>& arr, int low, int high){
        if(low<high){
            int pi = partition(arr, low, high);
            quickSort(arr, low, pi-1);
            quickSort(arr, pi+1, high);
        }
    }
};
```
#### idea - 2
* 用 priority queue(heap)，max 或是 min 都可以。
```c++
// min-heap 
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        priority_queue<int, vector<int>, greater<int>> pq;
        for (int num : nums) {
            pq.push(num);
            if (pq.size() > k) {
                pq.pop();
            }
        }
        return pq.top();
    }
};
```
### 216. Combination Sum III
#### idea
* DFS
* Recursive
```c++
class Solution {
public:
    vector<vector<int>> ans;
    vector<int> subAns;
    void dfs(int k, int target, int sum, int start){
        if(sum==target && k==0){
            ans.push_back(subAns);   
            return;
        }
        if(sum>target || k<=0)
            return;
        for(int i=start; i<=9; i++){
            sum+=i;
            subAns.push_back(i);
            dfs(k-1, target, sum, i+1);
            sum-=i;
            subAns.pop_back();
        }
    }
    vector<vector<int>> combinationSum3(int k, int target) {
        int sum = 0;
        int start = 1;
        dfs(k, target, sum, start);
        return ans;
    }
};
```
### 230. Kth Smallest Element in a BST
#### idea
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    void inOrder(TreeNode* root, int& k, int& ans){
        if(!root)
            return;
        inOrder(root->left, k, ans);
        k--;
        if(k==0)
            ans = root->val;
        inOrder(root->right, k, ans);
    }
    int kthSmallest(TreeNode* root, int k) {
        int ans = -1;
        inOrder(root, k, ans);
        return ans;
    }
};
```
### 240. Search a 2D Matrix II
#### idea
* ![](https://i.imgur.com/UvuVZCC.png)
* 利用該矩陣的特性(row : 小到大, col : 小到大)，由 matrix[0][-1]開始搜尋
```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int i = 0;
        int j = matrix[0].size()-1;
        while(i<matrix.size() && 0<=j){
            if(matrix[i][j]==target)
                return true;
            target>matrix[i][j] ? i++ : j--;
        }
        return false;
    }
};
```
### 241. Different Ways to Add Parentheses
#### idea
* 用 Recursive 
```c++
class Solution {
public:
    vector<int> diffWaysToCompute(string expression) {
        vector<int> ans;
        for(int i=0; i<expression.size(); i++){
            char temp = expression[i];
            if(temp=='+' || temp=='-' || temp=='*'){
                vector<int> ansLeft = diffWaysToCompute(expression.substr(0, i));
                vector<int> ansRight = diffWaysToCompute(expression.substr(i+1));
                for(auto left:ansLeft){
                    for(auto right:ansRight){
                        if(temp=='+')
                            ans.push_back(left+right);
                        else if(temp=='-')
                            ans.push_back(left-right);
                        else if(temp=='*')
                            ans.push_back(left*right);
                    }
                }
            }
        }
        
        // recursive termination condition 
        if(ans.empty())
            ans.push_back(atoi(expression.c_str()));
        return ans;
    }
};
```
### 260. Single Number III
#### idea
```c++
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        vector<int> ans = {0, 0};
        long sum = 0;
        for(auto num:nums)
            sum^=num;
        sum&=(-sum);
        for(auto num:nums){
            if(sum&num)
                ans[0]^=num;
            else
                ans[1]^=num;
        }
        return ans;
    }
};
```
### 279. Perfect Squares
#### idea
* BFS
```c++
n = 12
step 1 : 代表 1 個完全平方數的組合
step 2 : 代表可重複的 2 個完全平方數的組合
step 3 : 代表可重複的 3 個完全平方數的組合

             step:1 ||   step:2    ||   step:3
q         : 1, 4, 9 || 2, 5, 10, 8 || 3, 6, 11, 12

squareSet : 1, 4, 9 

```
```
class Solution {
public:
    int numSquares(int n) {
        int step = 1;
        queue<int> q;
        unordered_set<int> visited;
        vector<int> squareSet;
        
        for(int i=0; i*i<=n; i++){
            if(i*i==n)
                return step;
            q.push(i*i);
            visited.insert(i*i);
            squareSet.push_back(i*i);
        }
        
        while(!q.empty()){
            step++;
            int size = q.size();
            for(int i=0; i<size; i++){
                int temp = q.front();
                for(auto square:squareSet){
                    int num = temp+square;
                    if(num==n) 
                        return step;
                    else if(num<n && !visited.count(num)){
                        visited.insert(num);
                        q.push(num);
                    }
                }
                q.pop();
            }
        }
        return -1;
    }
};
```
### 287. Find the Duplicate Number
#### idea
* Floy Cycle Algo
* 不難記，可以背
```c++
class Solution {
public:
    int findDuplicate(vector<int>& nums){
        int fast = nums[0];
        int slow = nums[0];
        while(true){
            slow = nums[slow];
            fast = nums[nums[fast]];
            if(fast==slow)
                break;
        }
        slow = nums[0];
        while(true){
            if(fast==slow)
                break;
            fast = nums[fast];
            slow = nums[slow];
        }
        return fast;
    }
};
```
### 300. Longest Increasing Subsequence
#### idea
* DP
* 用兩層 for 迴圈
    * 第一層把 nums 的第 i 處由左到右掃一遍
    * 第二層把 nums 的第 i-1 處由右到左掃一遍
```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int ans = 1;
        vector<int> dp(nums.size(), 0);
        dp[0] = 1;
        for(int i=1; i<nums.size(); i++){
            for(int j=i-1; j>=0; j--){
                if(nums[j]<nums[i] && dp[i]<dp[j])
                    dp[i] = dp[j];
            }
            dp[i]++;
            ans = max(ans, dp[i]);
        }
        return ans;
    }
};
```
### 322. Coin Change
#### idea
* DP
```c++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        vector<int> dp(amount+1, 0);
        sort(coins.begin(), coins.end());
        for(int i=1; i<dp.size(); i++){
            dp[i] = INT_MAX;
            for(auto coin:coins){
                if(i-coin<0) 
                    break;
                if(dp[i-coin]!=INT_MAX)
                    dp[i] = min(dp[i], dp[i-coin]+1);
            }
        }
        return dp[amount] == INT_MAX ? -1 : dp[amount]; 
    }
};
```
### 328. Odd Even Linked List
#### idea
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* oddEvenList(ListNode* head) {
        if(head==NULL || head->next==NULL)
            return head;
        struct ListNode* odd = head;
        struct ListNode* even = head->next;
        struct ListNode* evenHead = even;
        while(odd->next && even->next){
            if(even->next){
                odd->next = even->next;
                odd = even->next;
            }
            if(odd->next){
                even->next = odd->next;
                even = odd->next;
            }
        }
        if(odd->next==NULL){
            odd->next = evenHead;
            even->next = NULL;
        }
        else if(even->next==NULL){
            odd->next = evenHead;
        }
        return head;
    }
};
```
### 337. House Robber III
#### idea
* 用 unordered_map 來降低時間複雜度
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    unordered_map<TreeNode*, int> map;
    int rob(TreeNode* root) {
        if(!root)
            return 0;
        if(map.count(root))
            return map[root];
        map[root] = max(robRoot(root), notRobRoot(root));
        return map[root];
    }
    int robRoot(TreeNode* root){
        if(!root)
            return 0;
        return root->val + notRobRoot(root->left) + notRobRoot(root->right);
    }
    int notRobRoot(TreeNode* root){
        if(!root)
            return 0;
        return rob(root->left) + rob(root->right); 
    }
};
```
### 343. Integer Break
#### idea
* DP
```
dp[i] 的意思是 i 這個值各種拆分相乘後的最大值
j*(i-j) 為只拆成兩個 j 與 (i-j) 相乘
j*dp[i-j] 為上述先把 (i-j) 拆分後再與 j 相乘
```
```c++
class Solution {
public:
    int integerBreak(int n) {
        vector<int> dp(n+1, 0);
        dp[2] = 1;
        for(int i=3; i<=n; i++){
            for(int j=1; j<i; j++){
                dp[i] = max(dp[i], max(j*(i-j), j*dp[i-j]));
            }
        }
        return dp[n];
    }
};
```
### 347. Top K Frequent Elements
#### idea
* 關鍵是要熟悉 C++ 的 map 與 priority queue 這些 library
* 方法很土炮，就是用 map 統計每個數的出現次數，接著用 load 到 priority queue 中，就可以找出前 k 大常出現的數。
```c++
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        priority_queue<pair<int, int>> q; 
        vector<int> ans;
        map<int, int> dic;
        for(int i=0; i<nums.size(); i++){
            dic[nums[i]]++;
        }
        for(auto iter=dic.begin(); iter!=dic.end(); iter++){
            q.push(make_pair(iter->second, iter->first));
        }
        for(int i=0; i<k; i++){
            ans.push_back(q.top().second);
            q.pop();
        }
        return ans;
    }
};
```
### 371. Sum of Two Integers
#### idea 1
```c++
class Solution {
public:
    int getSum(int a, int b) {
        return log(exp(a)*exp(b));
    }
};
```
#### idea 2 
* 技巧
```c++
class Solution {
public:
    int getSum(int a, int b) {
        while(b){
            int carry = (unsigned int)(a&b)<<1;
            a^=b;
            b = carry;
        }
        return a;
    }
};
```
### 376. Wiggle Subsequence
* DP
* ![](https://i.imgur.com/t8kOcYw.png)
```c++
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        int up = 1;
        int down = 1;
        if(nums.size()==1)
            return 1;
        for(int i=1; i<nums.size(); i++){
            if((nums[i]-nums[i-1])>0)
                up = down+1;
            else if((nums[i]-nums[i-1])<0)
                down = up+1;
        }
        return max(up, down);
    }
};
```
### 378. Kth Smallest Element in a Sorted Matrix
#### idea
* 在矩陣內做 Binary search
```c++
class Solution {
public:
    int kthSmallest(vector<vector<int>>& matrix, int k) {
        int row = matrix.size()-1;
        int col = matrix[0].size()-1;
        int low = matrix[0][0];
        int high = matrix[row][col];
        while(low<high){
            int j = col;
            int count = 0;
            int mid = low+(high-low)/2;
            for(int i=0; i<=row; i++){
                while(0<=j && matrix[i][j]>mid)
                    j--;
                count+=(j+1);
            }
            if(count<k)
                low = mid+1;
            else
                high = mid;
        }
        return low;
    }
};
```
### 406. Queue Reconstruction by Height
#### idea
* people[i][0] 由大到小排序，若值相同，則 people[i][1] 由小到大排序
```
[[7,0],[4,4],[7,1],[5,0],[6,1],[5,2]]
排序後               
[[7,0],[7,1],[6,1],[5,0],[5,2],[4,4]]

ans queue
[7 0]
[7 0][7 1]
[7 0][6 1][7 1]
[5 0][7 0][6 1][7 1]
[5 0][7 0][5 2][6 1][7 1]
[5 0][7 0][5 2][6 1][4 4][7 1]

```
```c++
bool compare(vector<int>& a, vector<int>& b){
    if(a[0]!=b[0]) return a[0]>b[0];
    else return a[1]<b[1];
}
class Solution {
public:
    vector<vector<int>> reconstructQueue(vector<vector<int>>& people) {
        sort(people.begin(), people.end(), compare);
        vector<vector<int>> ans;
        for(auto x:people){
            ans.insert(ans.begin()+x[1], x);
        }
        return ans;
    }
};
```
### 413. Arithmetic Slices
#### idea
* DP
```c++
class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& nums) {
        int ans = 0;
        vector<int> dp(nums.size(), 0);
        for(int i=2; i<nums.size(); i++){
            if(nums[i]-nums[i-1]==nums[i-1]-nums[i-2]){
                dp[i] = dp[i-1]+1;
                ans+=dp[i];
            }
        }
        return ans;
    }
};
```
### 416. Partition Equal Subset Sum
#### idea
* DP
* 若能夠切分成兩個 subset，則兩個 subset 的元素總和會相同。
* 該題 dp[i] 的意思是有沒有辦法從 nums 裡頭組合出來數字 i。
* 若 dp 最後一個的值是 true，則代表可以切分成兩個元素總和相同的 subset。
```c++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = 0;
        for(auto num:nums)
            sum+=num;
        if(sum%2!=0)
            return false;
        sum /= 2;
        vector<bool> dp(sum+1, false);
        dp[0] = true;
        for(auto num:nums){
            for(int i=sum; i>=num; i--){
                dp[i] = dp[i] || dp[i-num];
            }
        }
        return dp[sum];
    }
};
```
### 417. Pacific Atlantic Water Flow
#### idea
* DFS
* Recursive
* 針對 pacific 與 atlantic 做 DFS，返回有交錯重疊的部分

![](https://i.imgur.com/RZnQucO.png)
![](https://i.imgur.com/MlviFLJ.png)
![](https://i.imgur.com/Cfa85TL.png)

```c++
class Solution {
public:
    int row, col;
    vector<vector<bool>> pacific, atlantic;
    vector<vector<int>> ans;
    vector<vector<int>> pacificAtlantic(vector<vector<int>>& heights) {
        if(!size(heights)) 
            return ans;
        row = heights.size(), col = heights[0].size();
        atlantic = pacific = vector<vector<bool>>(row, vector<bool>(col, false));
        for(int i=0; i<row; i++){
            dfs(heights, pacific, i, 0);
            dfs(heights, atlantic, i, col-1);
        }
        for(int i=0; i<col; i++){ 
            dfs(heights, pacific, 0, i);
            dfs(heights, atlantic, row-1, i);   
        }
        return ans;
    }
    
    void dfs(vector<vector<int>>& grid, vector<vector<bool>>& visited, int i, int j){
        if(visited[i][j])
            return;
        
        visited[i][j] = true;
        if(pacific[i][j] && atlantic[i][j])
            ans.push_back(vector<int>{i, j}); 
        
        if(i+1<row && grid[i+1][j]>=grid[i][j]) 
            dfs(grid, visited, i+1, j); 
        if(i-1>=0 && grid[i-1][j]>=grid[i][j]) 
            dfs(grid, visited, i-1, j);
        if(j+1<col && grid[i][j+1]>=grid[i][j]) 
            dfs(grid, visited, i, j+1); 
        if(j-1>=0 && grid[i][j-1]>=grid[i][j]) 
            dfs(grid, visited, i, j-1);
    }
};
```
### 435. Non-overlapping Intervals
#### idea
* greedy，先對每個 intervals[i][1] 進行排序(由小到大)
* 注意，是非重疊的區間，不是非重疊的連續區間。
```c++
bool compare(vector<int>& a, vector<int>& b){
    return a[1]<b[1];
}

class Solution {
public:
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        sort(intervals.begin(), intervals.end(), compare);
        int ans = 1;
        int right = intervals[0][1];
        for(int i=1; i<intervals.size(); i++){
            if(right>intervals[i][0]) continue;
            ans++;
            right = intervals[i][1];
        }
        return intervals.size()-ans;
    }
};
```
### 437. Path Sum III
#### idea
* 兩個 Recursive，一個找包含 root 的，另一個找不包含 root 的。
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int pathWithoutRoot(TreeNode* root, int target, int current){
        if(!root)
            return 0;
        current+=root->val;
        if(current==target)
            return 1+pathWithoutRoot(root->left, target, current)+pathWithoutRoot(root->right, target, current);
        else
            return pathWithoutRoot(root->left, target, current)+pathWithoutRoot(root->right, target, current);
    }
    
    int pathSum(TreeNode* root, int targetSum) {
        if(!root)
            return 0;
        return pathWithoutRoot(root, targetSum, 0)+pathSum(root->left, targetSum)+pathSum(root->right, targetSum);
    }
};
```
### 451. Sort Characters By Frequency
#### idea
* 用 map 把裡頭所有的字統計過一遍，接著用 prioriy queue 排序後印出。
```c++
class Solution {
public:
    string frequencySort(string s) {
        string ans = "";
        priority_queue<pair<int, char>> q;
        map<char, int> dic;
        for(int i=0; i<s.length(); i++){
            dic[s[i]]++;
        }
        for(auto iter=dic.begin(); iter!=dic.end(); iter++){
            q.push(make_pair(iter->second, iter->first));
        }
        while(!q.empty()){
            for(int i=0; i<q.top().first; i++){
                char b = q.top().second;
                ans+=b;
            }
            q.pop();
        }
        return ans;
    }
};
```
```c++
// 比較簡潔的寫法，但概念一樣
string frequencySort(string s) {
    unordered_map<char,int> freq;           
    priority_queue<pair<int,char>> maxheap; 
    for(char c: s)
        freq[c]++;
    for(auto it: freq)
        maxheap.push({it.second,it.first}); 
    s="";   
    while(!maxheap.empty()){
        s+=string(maxheap.top().first,maxheap.top().second); 
        maxheap.pop();
    }
    return s;
}
```
### 452. Minimum Number of Arrows to Burst Balloons
#### idea
* greedy
```c++
class Solution {
public:
    int findMinArrowShots(vector<vector<int>>& points) {
        sort(points.begin(), points.end());
        int ans = 1;
        int left = points[0][0];
        int right = points[0][1];
        for(auto i:points){
            if(left<=i[0] && i[0]<=right){
                left = i[0];
                if(i[1]<right) right = i[1];
            }
            else{
                left = i[0];
                right = i[1];
                ans++;
            }
        }
        return ans;
    }
};
```
### 462. Minimum Moves to Equal Array Elements II
#### idea
```c++
class Solution {
public:
    int minMoves2(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        int move = 0;
        int median = nums[nums.size()/2];
        for(auto num : nums)
            move+=abs(num-median);
        return move;
    }
};
```
### 503. Next Greater Element II
#### idea
```c++
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        vector<int> ans(nums.size(), 0);
        stack<int> s;
        for(int i=nums.size()-1; i>=0; i--)
            s.push(nums[i]);
        for(int i=nums.size()-1; i>=0; i--){
            while(s.size()>0 && nums[i]>=s.top())
                s.pop();
            if(s.empty())
                ans[i] = -1;
            else
                ans[i] = s.top();
            s.push(nums[i]);
        }
    }
};
```
### 513. Find Bottom Left Tree Value
#### idea
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int findBottomLeftValue(TreeNode* root) {
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            root = q.front();
            q.pop();
            if(root->right)
                q.push(root->right);
            if(root->left)
                q.push(root->left);
        }
        return root->val;
    }
};
```
### 524. Longest Word in Dictionary through Deleting
#### original idea
* 把dic內的詞全部都在s內檢查過一遍(brute method)，時間複雜度可能為O(N**3)
#### better idea
* warning : 
    ```
    * 以下例子 output 為 "a" 不是因為 "a" 為 dict 的第一個字母，是因為 a 排在 s 比較前面的位置
    * 可以用 ASCII 來比較 dic 中哪個在 s 內排比較前面(C++中對string做>, <運算就是以 ASCII 來算)
    Input: s = "abpcplea", dictionary = ["a","b","c"]
    Output: "a"
    
    Input: s = "abce", dictionary = ["abe","abc"]
    correct Output: "abc"
    ```
```c++
class Solution {
public:
    string findLongestWord(string s, vector<string>& dictionary) {
        string ans = "";
        for(auto candidateStr:dictionary){
            if(isSubstring(s, candidateStr)){
                if(candidateStr.size()>ans.size() || (candidateStr.size()==ans.size() && candidateStr<ans))
                    ans = candidateStr;
            }
        }
        return ans;
    }
    
    bool isSubstring(string mainStr, string candidate){
        int mainStrLen = 0; int candidateLen = 0;
        while(mainStrLen<mainStr.size() && candidateLen<candidate.size()){
            if(mainStr[mainStrLen]==candidate[candidateLen]) candidateLen++;
            mainStrLen++;
        }
        return candidate.size()==candidateLen;
    }
};
```
### 540. Single Element in a Sorted Array
#### idea
* Binary search
* 因為元素都是成雙出現，只有一個元素不是成雙，所以用 (rightIndex - midIndex)%2 來判斷要往左走還是往右走
* 注意 : m-1 有可能會超出 array 的範圍
    * [1 ,2 ,2, 3, 3]
```c++
class Solution {
public:
    int singleNonDuplicate(vector<int>& nums) {
        int left = 0;
        int right = nums.size()-1;
        int m, index, ans = -1;
        if(nums.size()==1) return nums[0];
        while(left<right){
            m = left+(right-left)/2;
            if(nums[m]==nums[m+1]){
                index = (right-(m+1))%2;
                if(index==0)
                    right = m-1;
                else
                    left = m+2;
            }
            else if(nums[m-1]==nums[m]){
                index = (m-1-left)%2;
                if(index==0)
                    left = m+1;
                else
                    right = m-2;
            }
            else
                return nums[m];
        }
        return nums[left];
    }
};
```
### 547. Number of Provinces
#### idea
* DFS
* Recursive
```c++
class Solution {
public:
    static void DFS(vector<vector<int>>& isConnected, int i, int j){
        for(int k=j; k<isConnected[0].size(); k++){
            if(isConnected[i][k]==1){
                isConnected[k][k] = 0;
                isConnected[i][k] = 0;
                isConnected[k][i] = 0;
                DFS(isConnected, k, 0);
            }
        }
    }
    
    int findCircleNum(vector<vector<int>>& isConnected) {
        int ans = 0;
        for(int i=0; i<isConnected.size(); i++){
            if(isConnected[i][i]==1){
                isConnected[i][i] = 0;
                ans++;
                DFS(isConnected, i, 0);
            }
        }
        return ans;
    }
};
```
### 565. Array Nesting
#### idea
* 用 DFS。
* 用一個 visit 向量紀錄有沒有拜訪過。
```c++
class Solution {
public:
    int arrayNesting(vector<int>& nums) {
        int ans = 0;
        vector<int> visit(nums.size(), 0);
        for(int i=0; i<nums.size(); i++){
            int count = 1;
            if(!visit[i]){
                int current = nums[nums[i]];
                visit[current] = 1;
                while(current!=nums[i]){
                    visit[current] = 1;
                    current = nums[current];
                    count++;
                }
            }
            ans = max(count, ans);
        }
        return ans;
    }
};
```
### 633. Sum of Square Numbers
#### idea
* i = 0 , j = sqrt(inputNumber)
    * $i^2$+$j^2$==target : return true
    * $i^2$+$j^2$ > target : j--
    * $i^2$+$j^2$ < target : i++
* warning
    * $1^2+1^2 = 2$
```c++
class Solution {
public:
    bool judgeSquareSum(int c) {
        int i = 0;
        int j = sqrt(c);

        while(i<=j){ 
            if((pow(i, 2)+pow(j, 2))==c) return true;
            else if(pow(i, 2)+pow(j, 2)>c) j--;
            else i++;
        }
        return false;
    }
};
```
### 646. Maximum Length of Pair Chain
#### idea
* DP
* 先針對 pair[i][0] 進行排序由大到小的排序
```c++
class Solution {
public:
    int findLongestChain(vector<vector<int>>& pairs) {
        int ans = 1;
        vector<int> dp(pairs.size(), 1);
        sort(pairs.begin(), pairs.end());
        for(int i=1; i<pairs.size(); i++){
            for(int j=0; j<i; j++){
                if(pairs[j][1]<pairs[i][0])
                    dp[i] = dp[j]+1;
            }
            ans = max(ans, dp[i]);
        }
        return ans;
    }
};
```
### 647. Palindromic Substrings
#### idea
* 定義兩個指針分別為 left 與 right，由以下兩個 case 找出
    1. left 與 right 皆從 i 出發，left 往左找，right 往右找。
    2. left 從 i 出發，right 從 i+1 出發，left 往左找，right 往右找。
```c++
class Solution {
public:
    int countSubstrings(string s) {
        int ans = 0;
        if(s.size()==0)
            return 0;
        for(int i=0; i<s.size(); i++){
            find(s, i, i, ans);
            find(s, i, i+1, ans);
        }
        return ans;
    }
    
    void find(string s, int left, int right, int& ans){
        while(left>=0 && right<s.size() && s[left]==s[right]){
            left--;
            right++;
            ans++;
        }
    }
};
```
### 665. Non-decreasing Array
#### idea
* 如圖
    ![](https://i.imgur.com/DGXsXSP.png)
* 注意 : 題目是說調整元素，並不是刪除元素
```c++
class Solution {
public:
    bool checkPossibility(vector<int>& nums) {
        int count = 0;
        for(int i=1; i<nums.size(); i++){
            if(nums[i-1]<=nums[i]) 
                continue;
            if(i-2>=0 && nums[i-2]>nums[i])
                nums[i] = nums[i-1];
            else
                nums[i-1] = nums[i];
            count++;
        }
        return count<=1;
    }
};
```
### 669. Trim a Binary Search Tree
#### idea
* Recursive
* 注意是 BST，所以左邊值比 root 小，右邊值比 root 大。
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* trimBST(TreeNode* root, int low, int high) {
        if(!root)
            return root;
        if(root->val < low)
            return trimBST(root->right, low, high);
        if(root->val > high)
            return trimBST(root->left, low, high);
        root->left = trimBST(root->left, low, high);
        root->right = trimBST(root->right, low, high);
        return root;
    }
};
```
### 687. Longest Univalue Path
#### idea
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int ans = 0;
    int longestUnivaluePath(TreeNode* root) {
        findPath(root, INT_MIN);
        return ans;
    }
    int findPath(TreeNode* root, int parentVal){
        if(!root)
            return 0;
        int left = findPath(root->left, root->val);
        int right = findPath(root->right, root->val);
        ans = max(ans, left+right);
        if(root->val == parentVal)
            return max(left, right)+1;
        return 0;
    }
};
```
### 695. Max Area of Island
#### Original idea
* DFS
* Recursive
* 有用 stack 與 set<pair<int, int>>
* 偶爾會 Time Limit Exceeded
* 注意以下 case
    * [[0]]
    * [[1]]
    * [[0], [1]]
```c++
class Solution {
public:
    static int DFS(vector<vector<int>> grid, stack<pair<int, int>>* s, set<pair<int, int>>* record, int area){
    pair<int, int> p = (*s).top();
    (*s).pop();
    int dirs[4][2] = {{0, -1}, {1, 0}, {0, 1}, {-1, 0}};
    for(auto dir:dirs){
        int newX = p.first+dir[0];
        int newY = p.second+dir[1];
        if(0<=newX && newX<grid.size() && 0<=newY && newY<grid[0].size() && grid[newX][newY]==1 && !(*record).count({newX, newY})){
            area++;
            (*s).push(make_pair(newX, newY));
            (*record).insert(make_pair(newX, newY));
            area = DFS(grid, s, record, area);
        }
    }
    return area;
}
    
    
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        if(grid.size()==1 && grid[0].size()==1 && grid[0][0]==1) 
            return 1; 
        set<pair<int, int>> record;
        stack<pair<int, int>> s;
        int ans = 1;
        bool path = false;
        for(int i=0; i<grid.size(); i++){
            for(int j=0; j<grid[0].size(); j++){
                if(grid[i][j]==1 && !record.count({i, j})){
                    path = true;
                    s.push(make_pair(i, j));
                    int area = DFS(grid, &s, &record, 0);
                    if(area>ans) 
                        ans = area;
                }        
            }
        }
        if(path) 
            return ans;
        else 
            return 0;
    }
};
```
#### Better Idea
* DFS
* Recursive
```c++
class Solution {
public:
    static int DFS(vector<vector<int>>& grid, int i, int j){
        if(i<0 || grid.size()<=i || j<0 || grid[0].size()<=j || !grid[i][j]) 
            return 0;
        grid[i][j] = 0;
        return 1+DFS(grid, i+1, j)+DFS(grid, i-1, j)+DFS(grid, i, j+1)+DFS(grid, i, j-1);
    }
    
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        int ans = 0;
        for(int i=0; i<grid.size(); i++){
            for(int j=0; j<grid[0].size(); j++){
                if(grid[i][j]){
                    ans = max(ans, DFS(grid, i, j));
                }
            }
        }
        return ans;
    }
};
```
### 739. Daily Temperatures
#### idea
```c++
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        vector<int> ans(temperatures.size(), 0);
        stack<pair<int, int>> s;
        for(int i=0; i<temperatures.size(); i++){
            while(s.size()>0 && s.top().second<temperatures[i]){
                ans[s.top().first] = i-s.top().first;
                s.pop();
            }
            s.push(make_pair(i, temperatures[i]));
        }
        return ans;
    }
};
```
### 769. Max Chunks To Make Sorted
#### idea
* 題目的意思是切割成好幾個子陣列後，先各自排序，再合併起來，合併後的陣列是由0 ~ n-1 排序。求切割的最大子陣列數。
* 排序後的陣列裡的每個數值剛好會等於該 index，由該想法出發即可。
```c++
class Solution {
public:
    int maxChunksToSorted(vector<int>& arr) {
        int ans = 0;
        int sum = 0;
        int index = 0;
        for(int i=0; i<arr.size(); i++){
            sum+=arr[i];
            index+=i;
            if(sum==index)
                ans++;
        }
        return ans;
    }
};
```
### 1091. Shortest Path in Binary Matrix
#### idea
* BFS
* 走過每個 cell，把 0 更改為步數，則最後 grid[row][col] 是最短路徑
* 注意 : grid = [[0]]
```c++
class Solution {
public:
    int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
        int row = grid.size();
        int col = grid[0].size();
        
        if(row==0 || col==0) 
            return -1;
        if(grid[0][0]!=0 || grid[row-1][col-1]!=0) 
            return -1;
        
        row--; col--;
        int dirs[8][2] = {{1,1}, {0,1}, {1,0}, {0,-1}, {-1,0}, {-1, -1}, {1, -1}, {-1, 1}};
        queue<pair<int, int>> q;
        q.push(make_pair(0, 0));
        
        grid[0][0] = 1;
        while(!q.empty()){
            auto curr = q.front();
            int x = curr.first, y = curr.second;
            if(x==row && y==col) 
                return grid[x][y];
            for(auto dir:dirs){
                int nextX = x+dir[0];
                int nextY = y+dir[1];
                if(0<=nextX && nextX<=row && 0<=nextY && nextY<=col && grid[nextX][nextY]==0){
                    grid[nextX][nextY] = grid[x][y]+1;
                    q.push(make_pair(nextX, nextY));
                }
            }
            q.pop();
        }
        return -1;
    }
};
```
### 1143. Longest Common Subsequence
#### idea
* DP
```c++
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int m = text1.length();
        int n = text2.length();
        vector<int> row(m+1, 0);
        vector<vector<int>> dp(n+1, row); 
        for(int i=1; i<=n; i++){ 
            for(int j=1; j<=m; j++){ 
                if(text2[i-1]==text1[j-1])
                    dp[i][j] = dp[i-1][j-1]+1;
                else
                    dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
            }
        }
        return dp[n][m];
    }
};
```

## Hard
### 127. Word Ladder
#### Trouble idea
* BFS
* 想法與 279 相同，自認解法沒錯，但會 Time Limit Exceeded
```c++
class Solution {
public:
    static bool compare(string a, string b){
        int diff = 0;
        for(int i=0; i<a.size(); i++){
            if(a[i]!=b[i])
                diff++;
        }
        if(diff==1) 
            return true;
        else
            return false;
    }
    
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        int step = 1;
        unordered_set<string> visited;
        queue<string> q;
        q.push(beginWord);

        while(!q.empty()){
            step++;
            int size = q.size();
            for(int i=0; i<size; i++){
                string temp = q.front();
                for(auto word:wordList){
                    if(compare(temp, word) && !visited.count(word)){
                        if(word==endWord)
                            return step;
                        q.push(word);
                        visited.insert(word);
                    }
                }
                q.pop();
            }
        }
        return 0;
    }
};
```

#### Better idea
* BFS
```
vector<string> wordList = {"hot", "dot", "dog", "lot", "log", "cog"};

unordered_set<string> dict(wordList.begin(), wordList.end());
{"cog", "log", "lot", "dog", "dot", "hot"}
```
```
dict.find(str)!=dict.end()
等於是用迭代的方式在 dict 內找是否有無 str
```
```c++
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        int step = 1;
        unordered_set<string> dict(wordList.begin(), wordList.end());
        queue<string> q;
        q.push(beginWord);
        
        while(!q.empty()){
            int size = q.size();
            for(int i=0; i<size; i++){
                string str = q.front();
                q.pop();
                dict.erase(str);
                if(str==endWord)
                    return step;
                for(int j=0; j<str.size(); j++){
                    char c = str[j];
                    for(int k=0; k<26; k++){
                        str[j] = 'a'+k;
                        if(dict.find(str)!=dict.end()){
                            q.push(str);
                        }
                        str[j] = c;
                    }
                }
            }
            step++;
        }
        return 0;
    }
};
```

