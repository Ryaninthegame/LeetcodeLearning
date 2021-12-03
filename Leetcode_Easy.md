###### tags: `Interview material`
# Leetcode Easy
### 1. Two Sum
#### idea
* 注意 : 因為是要回傳原本的 index，所以不能排序。
* 用一個 dic 紀錄每一輪 target-nums[i] 的 index
* 每一輪檢查 dic 內是否有 nums[i]，若有，則代表該輪的 nums[i] 與之前 dic 內紀錄的值相加後可以得到 target。
```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> ans;
        map<int, int> dic;
        for(int i=0; i<nums.size(); i++){
            if(dic.count(nums[i])){
                ans.push_back(i);
                ans.push_back(dic[nums[i]]);
                break;
            }
            dic[target-nums[i]] = i;
        }
        return ans;
    }
};
```
### 7. Reverse Integer
#### idea
* 注意 ans>INT_MAX 與 ans<INT_MIN 這兩種情況
```c++
class Solution {
public:
    int reverse(int x) {
        int ans = 0;
        while(x!=0){
            if(ans>INT_MAX/10 || ans<INT_MIN/10)
                return 0;
            ans = (ans*10)+(x%10);
            x/=10;
        }
        return ans;
    }
};
```
### 9. Palindrome Number
#### idea
* 討論區大多數都是轉換成 string 後去做，但我認為此題目的意思應該不是希望這樣做。
```c++
class Solution {
public:
    bool isPalindrome(int x) {
        if(x<0)
            return false;
        int temp = x;
        int reverse = 0;
        while(x!=0 && reverse<INT_MAX/10){
            reverse = reverse*10 + x%10;
            x/=10;
        }
        return temp==reverse;
    }
};
```
### 13. Roman to Integer
#### idea
```c++
class Solution {
public:
    int query(char c){
        int res = 0;
        switch(c){
            case 'I':
                res = 1;
                break;
            case 'V':
                res = 5;
                break;
            case 'X':
                res = 10;
                break;
            case 'L':
                res = 50;
                break;
            case 'C':
                res = 100;
                break;
            case 'D':
                res = 500;
                break;
            case 'M':
                res = 1000;
                break;
        }
        return res;
    }
    
    int romanToInt(string s) {
        int ans = 0;
        for(int i=0; i<s.size(); i++){
            if(i<s.size()-1 && query(s[i])<query(s[i+1]))
                ans-=query(s[i]);
            else
                ans+=query(s[i]);
        }
        return ans;
    }
};
```
### 14. Longest Common Prefix
#### idea
* 注意 : 題目只要求前綴而已，也就是說只要求前面幾個字要相同
* 把 strs 的第一個字串當作對照字串，逐一檢查 strs 內剩下的字串
```c++
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        if(strs.size()==1)
            return strs[0];
        if(strs[0]=="")
            return "";
        string ans = "";
        int i = 0;
        while(i<strs[0].length()){
            char c = strs[0][i];
            for(int j=1; j<strs.size(); j++){
                if(i>strs[j].size() || c!=strs[j][i])
                    return ans;
            }
            ans+=c;
            i++;
        }
        return ans;
    }
};
```
### 20. Valid Parentheses
#### idea 1
* leetcode 環境內若 stack 為空的話，stack.top() 會報錯，但 VScode 不會。
* leetcode 環境內要多增加 stack.size()>0 的條件。
```c++
class Solution {
public:
    bool isValid(string s) {
        stack<int> stack;
        for(int i=0; i<s.size(); i++){
            if(stack.size()>0 && s[i]==')' && stack.top()=='(')
                stack.pop();
            else if(stack.size()>0 && s[i]==']' && stack.top()=='[')
                stack.pop();
            else if(stack.size()>0 && s[i]=='}' && stack.top()=='{')
                stack.pop();
            else
                stack.push(s[i]);
        }
        return stack.empty() ? true : false;
    }
};
```
#### idea 2
* 2021/09/06 第二次寫這題時想出來的
```c++
class Solution {
public:
    bool isValid(string s) {
        stack<int> stack;
        stack.push(s[0]);
        for(int i=1; i<s.size(); i++){
            if(s[i]=='(' || s[i]=='[' || s[i]=='{')
                stack.push(s[i]);
            else{
                if(stack.size()>0){
                    char c = stack.top();
                    if(c=='(' && s[i]==')')
                        stack.pop();
                    else if(c=='[' && s[i]==']')
                        stack.pop();
                    else if(c=='{' && s[i]=='}')
                        stack.pop();
                    else
                        return false;
                }
                else
                    return false;
            }
        }
        return stack.size()==0 ? true : false;
    }
};
```
### 21. Merge Two Sorted Lists
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
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if(l1==NULL && l2==NULL)
            return NULL;
        if(l1==NULL)
            return l2;
        if(l2==NULL)
            return l1;
        
        struct ListNode* ans = NULL;
        struct ListNode* temp = NULL;
        
        if(l1->val<=l2->val){
            temp = l1;
            l1 = l1->next;
        }
        else{
            temp = l2;
            l2 = l2->next;
        }
        ans = temp;
        
        while(l1 && l2){
            if(l1->val<=l2->val){
                temp->next = l1;
                temp = l1;
                l1 = l1->next;
            }
            else{
                temp->next = l2;
                temp = l2;
                l2 = l2->next;
            }
        }
        if(l1==NULL)
            temp->next = l2;
        if(l2==NULL)
            temp->next = l1;
        return ans;
    }    
};
```
### 26. Remove Duplicates from Sorted Array
#### idea
```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if(nums.size()==0)
            return 0;
        int left = 0;
        for(int right=1; right<nums.size(); right++){
            if(nums[left]!=nums[right]){
                left++;
                nums[left] = nums[right];
            }
        }
        return left+1;
    }
};
```
### 27. Remove Element
#### wrong idea
* 下列 case 會報錯
```
[1]
1
```
```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int right = nums.size()-1;
        int left = 0;
        while(left<right){
            if(nums[left]==val){
                nums[left] = nums[right];
                right--;
            }
            else
                left++;
        }
        return right+1;
    }
};
```
#### correct idea
```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int right = nums.size();
        int left = 0;
        while(left<right){
            if(nums[left]==val){
                nums[left] = nums[right-1];
                right--;
            }
            else
                left++;
        }
        return right;
    }
};
```
### 28. Implement strStr()
#### idea
* 偶爾會 Time Limit
```c++
class Solution {
public:
    int strStr(string haystack, string needle) {
        if(needle.size()==0)
            return 0;
        int i = 0;
        int j = 0;
        while(i<haystack.size()){
            if(haystack[i]==needle[j])
                j++;
            else{
                i-=j;
                j = 0;
            }
            if(j==needle.size())
                return i-j+1;
            i++;
        }
        return -1;
    }
};
```
### 53. Maximum Subarray
#### idea
* 第一次寫時的想法
* Dynamic Programming
* dp[i] 代表從 nums[0] ~ nums[i] 中，連續的 nums array 相加的最大值
```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int temp;
        vector<int> dp;
        dp.assign(nums.size(), 0);
        dp[0] = nums[0];

        for(int i=1; i<nums.size(); i++){
            temp = nums[i]+dp[i-1];
            if(nums[i]<temp)
                dp[i] = temp;
            else
                dp[i] = nums[i];
        }
        auto biggest = max_element(begin(dp), end(dp));
        return *biggest;
    }
};
```
#### better idea
* 第二次寫時的想法，自己想的唷
* dp[i] = max{nums[i], dp[i-1]+nums[i]}
```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int dp = 0;
        int ans = INT_MIN;
        for(int i=0; i<nums.size(); i++){
            dp = max(nums[i], dp+nums[i]);
            if(dp>ans)
                ans = dp;
        }
        return ans;
    }
};
```
### 58. Length of Last Word
#### idea
```c++class Solution {
public:
    int lengthOfLastWord(string s) {
        int ans = 0;
        int flag = 0;
        for(int i=s.size()-1; i>=0; i--){
            if(s[i]==' ' && flag==0)
                continue;
            flag = 1;
            if(s[i]!=' ')
                ans++;
            else
                break;
        }
        return ans;
    }
};
```
### 66. Plus One
#### idea
```c++
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        for(int i=digits.size()-1; i>=0; i--){
            if(digits[i]==9)
                digits[i] = 0;
            else{
                digits[i]++;
                return digits;
            }
        }
        digits.push_back(0);
        digits[0] = 1;
        return digits;
    }
};
```
### 67. Add Binary
#### idea
```c++
class Solution {
public:
    string addBinary(string a, string b) {
        string ans = "";
        int carry = 0;
        int i = a.size()-1;
        int j = b.size()-1;
        while(i>=0 || j>=0 || carry){
            if(i>=0 && a[i--]=='1')
                carry++;
            if(j>=0 && b[j--]=='1')
                carry++;
            ans.insert(0, 1, carry%2==1 ? '1' : '0');
            carry /= 2;
        }
        return ans;
    }
};
```
### 69. Sqrt(x)
#### Bad idea
* 此解法會有 signed integer overflow 的問題
```c++
class Solution {
public:
    int mySqrt(int x) {
        int boundary = (x+1)/2;
        int ans = 0;
        for(int i=0; i<=boundary; i++){
            if(i*i==x) 
                ans = i;
            else if((i*i<x) && (i+1)*(i+1)>x) 
                ans = i;
        }
        return ans;
    }
};
```
#### Better idea
* 關鍵 : 由於 int 經過平方相乘後很有可能會 overflow，所以要用 unsigned long 或是 long long
```c++
class Solution {
public:
    int mySqrt(int x) {
        unsigned long ans;
        unsigned long m;
        unsigned long left = 0; 
        unsigned long right = x;  
        while(left<=right){             
            m = (left+right)/2;
            if(m*m==x) 
                return m;     
            else if(m*m<x){             
                left = m+1;        
                ans = m;        
            }
            else 
                right = m-1;       
        }
        return ans; 
    }
};
```
### 70. Climbing Stairs
#### Bad idea
* DP
* Recursive
* Fibonacci
* 該方法想法沒錯，但是會 Time Limit Exceeded
```c++
class Solution {
public:
    int dp(int n){
        if(n==1)
            return 1;
        if(n==2)
            return 2;
        return dp(n-1)+dp(n-2);
    }
    
    int climbStairs(int n) {
        return dp(n);
    }
};
```
#### Better idea
* DP
* Recursive
* Fibonacci
``` 
    dp_2
     | dp_1 
     |  |
     |  |
n    1  2  3  4  5
-------------------------------------------
ans  1  2  3  5  8
-------------------------------------------
```
```c++
class Solution {
public:
    int climbStairs(int n) {
        int ans = 0;
        int dp_2 = 1;
        int dp_1 = 2;
        if(n==1)
            return dp_2;
        else if(n==2)
            return dp_1;
        else{
            for(int i=3; i<=n; i++){
                ans = dp_2+dp_1;
                dp_2 = dp_1;
                dp_1 = ans;
            }   
            return ans;
        }
    }
};
```
### 83. Remove Duplicates from Sorted List
#### idea 1
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
    ListNode* deleteDuplicates(ListNode* head) {
        if(head==NULL)
            return head;
        struct ListNode* ans = head;
        struct ListNode* temp = ans;
        while(head->next){
            if(head->val == head->next->val)
                head = head->next;
            else{
                head = head->next;
                temp->next = head;
                temp = head;
            }
        }
        if(temp!=head)
            temp->next = NULL;
        return ans;
    }
};
```
#### idea 2
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
    ListNode* deleteDuplicates(ListNode* head) {
        if(!head)
            return NULL;
        ListNode* slow = head;
        ListNode* fast = head;
        while(fast){
            if(fast->next && slow->val == fast->next->val)
                fast = fast->next;
            else{
                slow->next = fast->next;
                slow = fast->next;
                fast = slow;
            }
        }
        return head;
    }
};
```
### 88. Merge Sorted Array
#### idea 
* ![](https://i.imgur.com/5jtqHQr.png)
```c++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int k = m+n-1;
        m-=1; n-=1;
        while(m>=0 && n>=0){
            if(nums2[n]>nums1[m]){
                nums1[k]=nums2[n];
                k--; n--;
            }
            else{
                nums1[k]=nums1[m];
                k--; m--; 
            }
        }
        while(n>=0)
            nums1[k--]=nums2[n--];
    }
};
```
### 94. Binary Tree Inorder Traversal
#### idea
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
    vector<int> ans;
    void LDR(TreeNode* root){
        if(!root)
            return;
        LDR(root->left);
        ans.push_back(root->val);
        LDR(root->right);
    }
    vector<int> inorderTraversal(TreeNode* root) {
        LDR(root);
        return ans;
    }
};
```
### 101. Symmetric Tree
#### idea 1
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
    bool isEqual(TreeNode* leftTree, TreeNode* rightTree){
        if(!leftTree || !rightTree)
            return leftTree==rightTree;
        if(leftTree->val==rightTree->val)
            return isEqual(leftTree->left, rightTree->right) && isEqual(leftTree->right, rightTree->left);
        else
            return false;
    }
    bool isSymmetric(TreeNode* root) {
        return isEqual(root->left, root->right);
    }
};
```
#### idea 2
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
    bool isEqual(TreeNode* left, TreeNode* right){
        if(!left && !right)
            return true;
        else if(left && right)
            return left->val == right->val && isEqual(left->left, right->right) && isEqual(left->right, right->left);
        else
            return false;
    }
    
    bool isSymmetric(TreeNode* root) {
        return isEqual(root->left, root->right);
    }
};
```
### 104. Maximum Depth of Binary Tree
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
    int maxDepth(TreeNode* root) {
        if(root==NULL)
            return 0;
        return max(maxDepth(root->left), maxDepth(root->right))+1;
    }
};
```
### 108. Convert Sorted Array to Binary Search Tree
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
    TreeNode* build(vector<int>& nums, int left, int right){
        if(left>right)
            return NULL;
        int medium = left+(right-left)/2;
        struct TreeNode* root = new TreeNode(nums[medium]);;
        root->left = build(nums, left, medium-1);
        root->right = build(nums, medium+1, right);
        return root;
    }
    
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return build(nums, 0, nums.size()-1);
    }
};
```
### 110. Balanced Binary Tree
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
    int getHeight(TreeNode* root){
        if(root==NULL)
            return 0;
        return max(getHeight(root->left), getHeight(root->right))+1;
    }
    bool isBalanced(TreeNode* root) {
        if(root==NULL)
            return true;
        if(abs(getHeight(root->left)-getHeight(root->right))>1)
            return false;
        return isBalanced(root->left) && isBalanced(root->right);
    }
};
```
### 111. Minimum Depth of Binary Tree
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
    int find(TreeNode* root, int depth){
        if(!root->left && !root->right)
            return depth;
        depth++;
        int leftDepth = INT_MAX;
        int rightDepth = INT_MAX;
        if(root->left)
            leftDepth = find(root->left, depth);
        if(root->right)
            rightDepth = find(root->right, depth);
        return min(leftDepth, rightDepth);
    }
    
    int minDepth(TreeNode* root) {
        if(!root)
            return 0;
        return find(root, 1);
    }
};
```
### 112. Path Sum
#### original idea
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
    bool judge(TreeNode* root, int targetSum){
        if(!root)
            return false;
        if(!root->left && !root->right){
            if(targetSum-root->val==0)
                return true;
            else
                return false;
        }
        return judge(root->left, targetSum-root->val) || judge(root->right, targetSum-root->val);
    }
    
    bool hasPathSum(TreeNode* root, int targetSum) {
        if(!root)
            return false;
        if(root->left || root->right){
            if(targetSum-root->val==0)
                return false;
        }
        return judge(root, targetSum);
    }
};
```
#### clean code 
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
    bool find(TreeNode* root, int target, int current){
        if(!root)
            return false;
        current+=root->val;
        if(!root->left && !root->right && target==current)
            return true;
        return find(root->left, target, current) || find(root->right, target, current);
    }
    
    bool hasPathSum(TreeNode* root, int targetSum) {
        return find(root, targetSum, 0);
    }
};
```
### 118. Pascal's Triangle
#### idea
```c++
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> ans;
        for(int i=0; i<numRows; i++){
            vector<int> row(i+1, 1);
            for(int j=1; j<i; j++)
                row[j] = ans[i-1][j-1] + ans[i-1][j];
            ans.push_back(row);
        }
        return ans;
    }
};
```
### 121. Best Time to Buy and Sell Stock
#### idea
* greedy
* 算出第 i 天和第 i+1 天的價差
    * 若是正的代表賺錢，若有比 i-1 天前得到的 profit 大，則更新 profit
    * 若是負的代表賠錢，當累積賠到 0 元以下時，代表不能第 i 天買進。
```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int ans = 0;
        int accu = 0;
        for(int i=0; i<prices.size()-1; i++){
            accu += prices[i+1]-prices[i];
            if(accu>ans)
                ans = accu;
            else if(accu<0) 
                accu = 0;
        }
        return ans;
    }
};
```
### 122. Best Time to Buy and Sell Stock II
#### idea
```
Here the way to tackle this problem is to visualize it.
If I were to buy the data at every small value and sell it at the immediate biggest value i can get the answer.
Let me demonstrate:
 				       7
 					\     5    6 
 					 \    /\  /\
 					  \  /  \/  4 --------->[7,1,5,3,6,4]
 					   \/	 3		  
                                          1
So if we were to buy at every valley and sell at evry peak we can acheive the goal given in this q.
      buy -> 1,3,4
      sell-> 5,6
      but as 4 doesnt have a pair we discard it
      profit = (sum of peaks) - (sum of valleys)
                11 - 4 = 7
```
```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int profit = 0;
        int i = 0;
        while(i<prices.size()){
            while(i<prices.size()-1 && prices[i]>prices[i+1]) i++;
            int valley = prices[i];
            while(i<prices.size()-1 && prices[i]<prices[i+1]) i++;
            int peak = prices[i];
            profit += (peak-valley);
            i++;
        }
        return profit;
    }
};
```
### 125. Valid Palindrome
#### idea
* 要孰悉 C++ 的函示庫
    * 「isalnum」可以拿來檢查字符是否為空白
    * 「tolower」可以把所有的英文字符轉成小寫字符
```c++
class Solution {
public:
    bool isPalindrome(string s) {
        int left = 0;
        int right = s.size()-1;
        while(left<right){
            if(!isalnum(s[left]))
                left++;
            else if(!isalnum(s[right]))
                right--;
            else if(tolower(s[left])!=tolower(s[right]))
                return false;
            else{
                left++;
                right--;
            }
        }
        return true;
    }
};
```
### 136. Single Number
#### idea
* 用 XOR
```c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ans = 0;
        for(int i=0; i<nums.size(); i++)
            ans ^= nums[i];
        return ans;
    }
};
```
### 141. Linked List Cycle
#### idea 
* 一個指標一個走1格, 另外一個指標一次走2格，在這樣的走法下跑無限多次, 若有loop, 兩個指標一定會遇到
```c++
class Solution {
public:
    bool hasCycle(ListNode* head) {
        struct ListNode* fast = head;
        while(head){
            head = head->next;
            if(fast->next && fast->next->next) 
                fast = fast->next->next; 
            else 
                return false;
            if(head==fast) 
                return true;
        }
        return false;
    }
};
```
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if(!head)
            return false;
        ListNode* slow = head;
        ListNode* fast = head;
        while(fast->next && fast->next->next){
            slow = slow->next;
            fast = fast->next->next;
            if(slow==fast)
                return true;
        }
        return false;
    }
};
```
### 144. Binary Tree Preorder Traversal
#### idea
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
    vector<int> ans;
    void DLR(TreeNode* root){
        if(!root)
            return;
        ans.push_back(root->val);
        DLR(root->left);
        DLR(root->right);
    }
    vector<int> preorderTraversal(TreeNode* root) {
        DLR(root);
        return ans;
    }
};
```
### 145. Binary Tree Postorder Traversal
#### idea
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
    vector<int> ans;
    void LRD(TreeNode* root){
        if(!root)
            return;
        LRD(root->left);
        LRD(root->right);
        ans.push_back(root->val);
    }
    vector<int> postorderTraversal(TreeNode* root) {
        LRD(root);
        return ans;
    }
};
```
### 155. Min Stack
#### idea 1
* 只用一個 vector 實作出來
* Runtime: 200 ms, faster than 5.10% of C++ online submissions for Min Stack.
* Memory Usage: 16.2 MB, less than 91.87% of C++ online submissions for Min Stack.
```c++
class MinStack {
public:
    /** initialize your data structure here. */
    vector<int> s;
    MinStack() {
        
    }
    
    void push(int val) {
        s.push_back(val);
    }
    
    void pop() {
        s.pop_back();
    }
    
    int top() {
        int val = s.back();
        return val;
    }
    
    int getMin() {
        int minValue = INT_MAX;
        for(int i=0; i<s.size(); i++){
            if(s[i]<minValue)
                minValue = s[i];
        }
        return minValue;
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(val);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->getMin();
 */
```
#### idea 2
* 用兩個 stack 實作出來
* Runtime: 20 ms, faster than 81.22% of C++ online submissions for Min Stack.
* Memory Usage: 16.4 MB, less than 68.78% of C++ online submissions for Min Stack.
```c++
class MinStack {
public:
    /** initialize your data structure here. */
    stack<int> s1;
    stack<int> s2;
    MinStack() {
        
    }
    
    void push(int val) {
        if(s2.empty() || s2.top()>=val)
            s2.push(val);
        s1.push(val);
    }
    
    void pop() {
        if(s1.top()==s2.top())
            s2.pop();
        s1.pop();
    }
    
    int top() {
        return s1.top();
    }
    
    int getMin() {
        return s2.top();
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(val);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->getMin();
 */
```
### 160. Intersection of Two Linked Lists
#### idea
* 用兩個指標分別指向 headA 與 headB，每次走一格，走到底則返回至起始點並且繼續走，若有交會必定會找到
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* getIntersectionNode(ListNode* headA, ListNode* headB) {
        struct ListNode* p1 = headA;
        struct ListNode* p2 = headB;
        while(p1!=NULL && p2!=NULL && p1!=p2){
            p1 = p1->next;
            p2 = p2->next;
            if(p1==p2)
                return p1;
            if(p1==NULL)
                p1 = headA;
            if(p2==NULL)
                p2 = headB;
        }
        return p1;
    }
};
```
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* p1 = headA;
        ListNode* p2 = headB;
        while(true){
            if(p1==p2)
                return p1;
            if(p1->next)
                p1 = p1->next;
            else
                p1 = headA;
            if(p2->next)
                p2 = p2->next;
            else
                p2 = headB;
            if(p1==headA && p2==headB)
                break;
        }
        return NULL;
    }
};
```
### 167. Two Sum II - Input array is sorted
#### idea 1
1. i 指向 array[0], j 指向 array[length-1]
2. 判斷 array[i]+array[j] 加起來是否等於 target number 
    *  array[i]+array[j]==target number : return
    *  array[i]+array[j]>target number : j--
    *  array[i]+array[j]<target number : i++
```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        vector<int> ans;
        int i = 0, j = numbers.size()-1;
        while(i<j){
            int temp = numbers[i]+numbers[j];
            if(temp==target){
                ans.push_back(i+1);
                ans.push_back(j+1);
            }
            if(temp>target) j--;
            else i++;
        }
        return ans;
    }
};
Time : O(N)
```
#### idea 2
1. 用 for loop i 掃過 array
    * (i+1)~(array.size-1) 作 binary search 找值
2. warning
    * index 要考慮以下情況
        1. array : [2, 4, 6], target : 8
        2. array : [2, 4, 6], target : 6
        3. array : [2, 4, 6], target : 10
```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        for(int i=0; i<numbers.size(); i++){
            int left = i+1;
            int right = numbers.size()-1;
            while(left<right){
                int mid = left+(right-left)/2;
                if(numbers[mid]==target-numbers[i]) return {i+1, mid+1};
                else if(numbers[mid]>target-numbers[i]){
                    right = mid;
                }
                else{
                    left = mid+1;
                }
            }
            if(numbers[left]==target-numbers[i]) return {i+1, left+1};
        }
        return {};
    }
};
Time : O(NlogN)
```
### 168. Excel Sheet Column Title
#### idea
```c++
class Solution {
public:
    string convertToTitle(int n) {
        string map = "ZABCDEFGHIJKLMNOPQRSTUVWXY";
        string ans = "";
        while(n>0){
            ans = map[(n%26)]+ans;
            if(map[n%26]=='Z')
                n--;
            n/=26;
        }
        return ans;
    }
};
```
### 169. Majority Element
#### Brute Idea
```C++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int length = nums.size()/2;
        int ans = 0;
        map<int, int> dic;
        for(auto num:nums)
            dic[num]++;
        for(auto iter=dic.begin(); iter!=dic.end(); iter++){
            if(iter->second>length)
               ans = iter->first; 
        }
        return ans;
    }
};
```
#### Better Idea
* Boyer–Moore majority vote algorithm
```C++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int ans = nums[0];
        int count = 0;
        for(auto num:nums){
            ans = (count==0) ? num : ans;
            count = (ans==num) ? count+1 : count-1;
        }
        return ans;
    }
};
```
```c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int ans = nums[0];
        int count = 1;
        for(int i=1; i<nums.size(); i++){
            if(nums[i]==ans)
                count++;
            else
                count--;
            if(count==0){
                count = 1;
                ans = nums[i];
            }
        }
        return ans;
    }
};
```
### 171. Excel Sheet Column Number
#### idea
* 要孰悉 C++ 的 ascii 碼
```
A : 65
B : 66
.
.
.
Z : 90
```
```c++
class Solution {
public:
    int titleToNumber(string columnTitle) {
        int ans = 0;
        int carry = columnTitle.size()-1;
        for(int i=0; i<columnTitle.size(); i++){
            ans+=(columnTitle[i]-64)*pow(26, carry);
            carry--;
        }
        return ans;
    }
};
```
### 172. Factorial Trailing Zeroes
#### idea
* 階層的尾數為 0，一定是 1 個 2 與 1 個 5 相乘得到的。以此類推，尾數兩個 0，則一定是 2 個 2 與 2 個 5 相乘得到的。
* 只針對 5 來做計算，因為階層的特性，5!一定包含 2。
```
 5! : 5*4*3*2*1             -> 1個5與3個2 (把4拆開來)
10! : 10*9*8*7*6*5*4*3*2*1  -> 2個5與好幾個2
```
```c++
class Solution {
public:
    int trailingZeroes(int n) {
        int ans = 0;
        while(n){
            ans+=n/5;
            n/=5;
        }
        return ans;
    }
};
```
### 190. Reverse Bits
#### wrong idea
```c++
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        uint32_t ans = 0;
        while(n!=0){
            ans<<=1;
            uint32_t temp = n&1;
            ans|=temp;
            n>>=1;
        }
        return ans;
    }
};
```
#### correct idea
```c++
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        uint32_t ans = 0;
        for(int i=0; i<32; i++){
            ans<<=1;
            ans+=n%2;
            // ans|=(n&1);
            n>>=1;
        }
        return ans;
    }
};
```
### 191. Number of 1 Bits
#### idea 
```c++
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int ans = 0;
        while(n){
            ans+=(n&1);
            n>>=1;
        }
        return ans;
    }
};
```
```c++
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int ans = 0;
        for(int i=0; i<32; i++){
            if(n&1)
                ans++;
            n>>=1;
        }
        return ans;
    }
};
```
### 202. Happy Number
#### idea
* 背
* 89 與 1 會陷入無窮迴圈，所以需要額外判斷。
```c++
class Solution {
public:
    int getNum(int n){
        int sum = 0;
        while(n>0){
            sum+=pow(n%10, 2);
            n/=10;
        }
        return sum;
    }
    
    bool isHappy(int n) {
        while(1){
            if(n==89)
                return false;
            if(n==1)
                return true;
            n = getNum(n);
        }
    }
};
```
### 204. Count Primes
#### idea
* ![](https://i.imgur.com/zXjtDRt.gif)
* 2, 3, 5, 7 的倍數掃過一遍後，剩下來的就是質數
```C++
class Solution {
public:
    int countPrimes(int n) {
        int ans = 0;
        vector<int> record(n+1, false);
        for(int i=2; i<n; i++){
            if(record[i])
                continue;
            ans++;
            for(int j=2; i*j<n; j++)
                record[i*j] = true;
        }
        return ans;
    }
};
```
### 205. Isomorphic Strings
#### idea
```C++
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        unordered_map<char, char> dics;
        unordered_map<char, char> dict;
        for(int i=0; i<s.size(); i++){
            dics[s[i]] = t[i];
            dict[t[i]] = s[i];
        }
        for(int i=0; i<s.size(); i++){
            if(dics[s[i]] != t[i])
                return false;
            if(dict[t[i]] != s[i])
                return false;
        }
        return true;
    }
};
```
### 206. Reverse Linked List
#### idea
```C++
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
    ListNode* reverseList(ListNode* head) {
        if(head==NULL)
            return NULL;
        struct ListNode* fast = head;
        struct ListNode* mid = NULL;
        struct ListNode* slow = NULL;
        while(fast->next){
            mid = fast;
            fast = fast->next;
            mid->next = slow;
            slow = mid;
        }
        fast->next = mid;
        return fast;
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
    ListNode* reverseList(ListNode* head) {
        if(!head)
            return NULL;
        ListNode* fast = head;
        ListNode* slow = NULL;
        while(head->next){
            fast = head->next;
            head->next = slow;
            slow = head;
            head = fast;
        }
        head->next = slow;
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
    ListNode* reverseList(ListNode* head) {
        if(!head)
            return head;
        if(!head->next)
            return head;
        ListNode* temp = head->next;
        ListNode* fast = temp->next;
        head->next = NULL;
        while(temp){
            temp->next = head;
            head = temp;
            temp = fast;
            if(temp)
                fast = fast->next;
        }
        return head;
    }
};
```
### 217. Contains Duplicate
#### original idea
* Runtime: 48 ms, faster than 18.26% of C++ online submissions for Contains Duplicate.
* Memory Usage: 21.1 MB, less than 21.73% of C++ online submissions for Contains Duplicate.
```C++
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        map<int, int> dic;
        for(int i=0; i<nums.size(); i++){
            if(dic.count(nums[i]))
                return true;
            dic[nums[i]]++;
        }
        return false;
    }
};
```
#### better idea
* Runtime: 16 ms, faster than 96.71% of C++ online submissions for Contains Duplicate.
* Memory Usage: 15.6 MB, less than 73.35% of C++ online submissions for Contains Duplicate.
```C++
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        for(int i=0; i<nums.size()-1; i++){
            if(nums[i]==nums[i+1])
                return true;
        }
        return false;
    }
};
```
### 225. Implement Stack using Queues
#### idea
* 只要用一個 queue 就可以實作出 stack
```C++
class MyStack {
public:
    queue<int> q;
    /** Initialize your data structure here. */
    MyStack() {
        
    }
    
    /** Push element x onto stack. */
    void push(int x) {
        int size = q.size();
        q.push(x);
        for(int i=0; i<size; i++){
            q.push(q.front());
            q.pop();
        }
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int temp = q.front();
        q.pop();
        return temp;
    }
    
    /** Get the top element. */
    int top() {
        return q.front();
    }
    
    /** Returns whether the stack is empty. */
    bool empty() {
        return q.empty();
    }
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
```
### 226. Invert Binary Tree
#### idea
```C++
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
    TreeNode* invertTree(TreeNode* root) {
        if(root==NULL)
            return NULL;
        struct TreeNode* temp = root->left;
        root->left = root->right;
        root->right = temp;
        invertTree(root->left);
        invertTree(root->right);
        return root;
    }
};
```
### 231. Power of Two
#### idea
* 技巧
```
1 0 0 0 0 -> 16
0 1 1 1 1 -> 15
--------- and
0 0 0 0 0 -> 0
```
```
class Solution {
public:
    bool isPowerOfTwo(int n) {
        return n>0 ? !(n&(n-1)) : false;
    }
};
```
### 232. Implement Queue using Stacks
#### idea
* 用兩個 stack(first in last out) 實作出 queue(first in first out)。
* 插入時，s1 的值先通通丟入 s2，之後再把新插入的值放進 s2，最後把 s2 裡的值丟回 s1，如此一來 s1 可以確保是 first in first out。
```C++
class MyQueue {
public:
    stack<int> s1;
    stack<int> s2;
    /** Initialize your data structure here. */
    MyQueue() {
        
    }
    
    /** Push element x to the back of queue. */
    void push(int x) {
        while(!s1.empty()){
            s2.push(s1.top());
            s1.pop();
        }
        s2.push(x);
        while(!s2.empty()){
            s1.push(s2.top());
            s2.pop();
        }
    }
    
    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        int temp = s1.top();
        s1.pop();
        return temp;
    }
    
    /** Get the front element. */
    int peek() {
        return s1.top();
    }
    
    /** Returns whether the queue is empty. */
    bool empty() {
        return s1.empty();
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue* obj = new MyQueue();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->peek();
 * bool param_4 = obj->empty();
 */
```
### 234. Palindrome Linked List
#### idea
```C++
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
    bool isPalindrome(ListNode* head) {
        struct ListNode* temp = head;
        stack<int> s;
        while(temp){
            s.push(temp->val);
            temp = temp->next;
        }
        while(!s.empty()){
            if(s.top()==head->val){
                s.pop();
                head = head->next;
            }
            else
                return false;
        }
        return true;
    }
};
```
#### better idea
* 先找到中間，接著把後半部反轉
```C++
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
    bool isPalindrome(ListNode* head) {
        struct ListNode* fast = head;
        struct ListNode* slow = head;
        struct ListNode* prev;
        struct ListNode* temp;
        
        // 找到中間點
        while(fast && fast->next){
            slow = slow->next;
            fast = fast->next->next;
        }
        prev = slow;
        temp = slow;
        slow = slow->next;
        prev->next = NULL;
        
        // 後半部反轉(右半邊)
        while(slow){
            temp = slow;
            slow = slow->next;
            temp->next = prev;
            prev = temp;
        }
        
        // 前半部與後半部做比對
        fast = head;
        while(prev){
            if(prev->val!=fast->val)
                return false;
            fast = fast->next;
            prev = prev->next;
        }
        return true;
    }
};
```
#### better better idea
* 想法與上個相同，只是可讀性更好，且更簡短
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
    bool isPalindrome(ListNode* head) {
        ListNode* fast = head;
        ListNode* slow = head;
        ListNode* prev = NULL;
        ListNode* temp;
        while(fast && fast->next){
            fast = fast->next->next;
            temp = slow->next;
            slow->next = prev;
            prev = slow;
            slow = temp;
        }
        if(fast)
            slow = slow->next;
        while(slow){
            if(slow->val != prev->val)
                return false;
            slow = slow->next;
            prev = prev->next;
        }
        return true;
    }
};
```
### 235. Lowest Common Ancestor of a Binary Search Tree
#### idea
* Recursive
* BST
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root)
            return NULL;
        if((root->val == p->val) || (root->val == q->val))
            return root;
        if((root->val < max(p->val, q->val)) && (root->val > min(p->val, q->val)))
            return root;
        else if(root->val > max(p->val, q->val))
            return lowestCommonAncestor(root->left, p, q);
        else
            return lowestCommonAncestor(root->right, p, q);
    }
};
```
### 236. Lowest Common Ancestor of a Binary Tree
#### idea
* Recursive
* 注意本題非 BST，僅是 Binary Tree 而已
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root)
            return NULL;
        if(root==p || root==q)
            return root;
        struct TreeNode* left = lowestCommonAncestor(root->left, p, q);
        struct TreeNode* right = lowestCommonAncestor(root->right, p, q);
        if(left && right)
            return root;
        return left==NULL ? right : left;
    }
};
```
### 237. Delete Node in a Linked List
#### idea
* 垃圾題目
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    void deleteNode(ListNode* node) {
        node->val = node->next->val;
        node->next = node->next->next;
    }
};
```
### 238. Product of Array Except Self
#### idea
* trace 過一遍就知道怎麼運作的惹
```C++
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int temp = 1;
        vector<int> ans(nums.size(), 1);
        for(int i=1; i<nums.size(); i++)
            ans[i] = ans[i-1]*nums[i-1];
        for(int i=nums.size()-1; i>0; i--){
            temp*=nums[i];
            ans[i-1]*=temp;
        }
        return ans;
    }
};
```
### 242. Valid Anagram
#### idea
* 題目的意思是 s 與 t 這兩個字串裡字元頻率要相同。
* 用 hashTable 
```C++
class Solution {
public:
    bool isAnagram(string s, string t) {
        unordered_map<char, int> dic;
        for(auto c:s)
            dic[c]++;
        for(auto c:t)
            dic[c]--;
        for(auto [c, count]:dic){
            if(count!=0)
                return false;
        }
        return true;
    }
};
```
```c++
class Solution {
public:
    bool isAnagram(string s, string t) {
        unordered_map<char, int> dic;
        for(auto c:s)
            dic[c]++;
        for(auto c:t){
            if(!dic.count(c))
                return false;
            else{
                dic[c]--;
                if(dic[c]==0)
                    dic.erase(c);
            }
        }
        return dic.empty();
    }
};
```
### 257. Binary Tree Paths
#### idea
* DFS
* Recursive
```C++
class Solution {
public:
    vector<string> ans;
    void dfs(TreeNode* node, string path){
        if(node==NULL)
            return;
        path = path+to_string(node->val);
        if(node->left==NULL && node->right==NULL){
            ans.push_back(path);
            return;
        }
        dfs(node->left, path+"->");
        dfs(node->right, path+"->");
    }
    
    vector<string> binaryTreePaths(TreeNode* root) {
        dfs(root, "");
        return ans;
    }
};
```
### 268. Missing Number
#### idea 1
* 用底乘高的方式去解
```C++
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int sum = (nums.size()+1)*nums.size()/2;
        for(int i=0; i<nums.size(); i++)
            sum -= nums[i];
        return sum;
    }
};
```
#### idea 2
* 用 XOR 的方式去解
```C++
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int temp1 = 0;
        int temp2 = 0;
        for(int i=0; i<nums.size(); i++){
            temp1^=nums[i];
            temp2^=(i+1);
        }
        return temp1^temp2;
    }
};
```
```c++
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int temp = 0;
        for(int i=0; i<nums.size(); i++)
            temp^=(i^nums[i]);
        return temp^nums.size();
    }
};
```
### 278. First Bad Version
#### idea
* Binary Search
* 注意以下case
    * Input: n = 3, bad = 2
```C++
class Solution {
public:
    int firstBadVersion(int n) {
        int left = 1;
        int right = n;
        int m;
        while(left<right){
            m = left+(right-left)/2;
            if(!isBadVersion(m)){
                left = m+1;
            }
            else{
                right = m-1;
            }
        }
        return left;
    }
};
```
### 283. Move Zeroes
#### idea
* j 指到的一定會是 0 
```C++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        for(int i=0, j=0; i<nums.size(); i++){
            if(nums[i]){
                swap(nums[i], nums[j]);
                j++;
            }
        }
    }
};
```
### 326. Power of Three
#### idea
```C++
class Solution {
public:
    bool isPowerOfThree(int n) {
        if(n==2)
            return false;
        for(int i=0; pow(3, i)<=n; i++){
            if(pow(3, i)==n)
                return true;
        }
        return false;
    }
};
```
### 338. Counting Bits
#### original idea
* It's correct but too slow
* Runtime: 12 ms, faster than 10.78% of C++ online submissions for Counting Bits.
* Memory Usage: 8.7 MB, less than 18.88% of C++ online submissions for Counting Bits.
```c++
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int> ans = {0};
        for(int i=1; i<=n; i++){
            int bit = log2(i)+1;
            int num = pow(2, bit)-1;
            int temp = i&num;
            int count = 0;
            while(temp){
                if(temp%2==1)
                    count++;
                temp = temp>>1;
            }
            ans.push_back(count);
        }
        return ans;
    }
};
```
#### better idea
* Runtime: 4 ms, faster than 89.11% of C++ online submissions for Counting Bits.
* Memory Usage: 7.9 MB, less than 44.15% of C++ online submissions for Counting Bits.
* 從數論角度出發，先判斷是奇數還是偶數
* 由以下的規律來看
    * 偶數 : num 中 1 的數量會跟 num/2 一樣，因為實際上只是 bit 往左移動 1 格而已。
    * 奇數 : num 中 1 的數量為 num-1 中 1 的數量再加 1。
    ```
     2 ->   10
     3 ->   11
     4 ->  100
     5 ->  101
     6 ->  110
     7 ->  111
     8 -> 1000
     9 -> 1001
    10 -> 1010
    ```
```c++
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int> ans(n+1, 0);
        for(int i=1; i<=n; i++){
            if(i%2==0)
                ans[i] = ans[i/2];
            else
                ans[i] = ans[i-1]+1;
        }
        return ans;
    }
};
```
### 342. Power of Four
#### idea
* 技巧
* (n&n-1)==0 確保為 2 的次方
* 0xAAAAAAAA 為 10101010‭10101010101010101010101010101010‬，所以 (n&0xAAAAAAAA)==0 確保 1 bit 皆在奇數位 
```c++
class Solution {
public:
    bool isPowerOfFour(int n) {
        return n>0 && (n&n-1)==0 && (n&0xAAAAAAAA)==0;
    }
};
```
### 344. Reverse String
#### idea
```c++
class Solution {
public:
    void reverseString(vector<char>& s) {
        int i = 0;
        int j = s.size()-1;
        while(i<j){
            swap(s[i], s[j]);
            i++;
            j--;
        }
    }
};
```
### 345. Reverse Vowels of a String
#### idea
* 先建 dic, dic['vowels']=1
* i 由頭開始往後掃，j 由後往頭掃，遇到 vowel 停下並 swap
```C++
class Solution {
public:
    string reverseVowels(string s) {
        int dic[256] = {0};
        dic['a'] = 1; dic['A'] = 1;
        dic['e'] = 1; dic['E'] = 1;
        dic['i'] = 1; dic['I'] = 1;
        dic['o'] = 1; dic['O'] = 1;
        dic['u'] = 1; dic['U'] = 1;
        int i = 0; int j = s.length()-1;
        while(i<j){
            while(i<j && dic[s[i]]==0) i++;
            while(i<j && dic[s[j]]==0) j--;
            swap(s[i], s[j]);
            i++; j--;
        }
        return s;
    }
};
```
### 350. Intersection of Two Arrays II
#### idea
* 依題目意思來看，不一定要連續，只要有重複的即可。
```c++
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        vector<int> ans;
        unordered_map<int, int> dic;
        for(auto num:nums1)
            dic[num]++;
        for(auto num:nums2){
            if(dic[num]>0){
                dic[num]--;
                ans.push_back(num);
            }
        }
        return ans;
    }
};
```
### 367. Valid Perfect Square
#### idea
```C++
class Solution {
public:
    bool isPerfectSquare(int num) {
        for(long i=1; i*i<=num; i++){
            if(i*i==num)
                return true;
        }
        return false;
    }
};
```
### 387. First Unique Character in a String
#### idea
```c++
class Solution {
public:
    int firstUniqChar(string s) {
        int ans = -1;
        unordered_map<char, int> dic;
        for(auto c:s)
            dic[c]++;
        for(int i=0; i<s.size(); i++){
            if(dic[s[i]]==1){
                ans = i;
                break;
            }
        }
        return ans;
    }
};
```
### 392. Is Subsequence
#### idea
* 沒啥難度，用兩個變數分別指向兩個 string 即可。
```C++
class Solution {
public:
    bool isSubsequence(string s, string t) {
        int balance = 0;
        int i = 0; 
        int j = 0;
        while(i<s.length() && j<t.length()){
            if(s[i]==t[j]){
                balance++;
                i++;
            }
            j++;
        }
        return balance == s.length() ? 1 : 0;
    }
};
```
### 404. Sum of Left Leaves
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
    void find(TreeNode* root){
        if(!root)
            return;
        if(root->left && !root->left->left && !root->left->right)
            ans+=root->left->val;
        find(root->left);
        find(root->right);
    }
    int sumOfLeftLeaves(TreeNode* root) {
        if(!root)
            return ans;
        find(root);
        return ans;
    }
};
```
### 405. Convert a Number to Hexadecimal
#### idea
```c++
class Solution {
public:
    string toHex(int num) {
        if(num==0)
            return "0";
        string ans = "";
        string map = "0123456789abcdef";
        unsigned int n = num;
        while(n>0){
            ans = map[n%16]+ans;
            n = n>>4;
        }
        return ans;
    }
};
```
### 409. Longest Palindrome
#### wrong idea
```c++
class Solution {
public:
    int longestPalindrome(string s) {
        int ans = 0;
        int odd = 0;
        map<char, int> dic;
        for(auto c:s)
            dic[c]++;
        for(auto [c, count]:dic){
            if(count%2==0)
                ans+=count;
            else{
                if(count==1){
                    ans++;
                }
                else{
                    ans+=count-1;    
                }
            }
        }
        return ans;
    }
};
```
#### better idea
* 注意以下 case
    * ccc
    * ccddee
```c++
class Solution {
public:
    int longestPalindrome(string s) {
        int ans = 0;
        int odd = 0;
        unordered_map<char, int> dic;
        for(auto c:s){
            dic[c]++;
            if(dic[c]%2==0){
                ans+=dic[c];
                dic[c] = 0;
            }
        }
        for(auto [c, count]:dic){
            if(count==1){
                ans++;
                break;
            }
        }
        return ans;
    }
};
```
### 412. Fizz Buzz
#### idea
```c++
class Solution {
public:
    vector<string> fizzBuzz(int n) {
        vector<string> ans;
        for(int i=1; i<=n; i++){
            if(i%3==0 && i%5==0)
                ans.push_back("FizzBuzz");
            else if(i%3==0)
                ans.push_back("Fizz");
            else if(i%5==0)
                ans.push_back("Buzz");
            else
                ans.push_back(to_string(i));
        }
        return ans;
    }
};
```
### 415. Add Strings
#### idea
```c++
class Solution {
public:
    string addStrings(string a, string b) {
        string ans = "";
        int carry = 0;
        int i = a.size()-1;
        int j = b.size()-1;
        while(i>=0 || j>=0 || carry){
            int num1 = i>=0 ? a[i]-'0' : 0;
            int num2 = j>=0 ? b[j]-'0' : 0;
            int sum = num1+num2+carry;
            carry = sum/10;
            ans+='0'+(sum%10);
            i--; j--;
        }
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```
### 455. Assign Cookies
#### original idea
* 分別用 priority queue(max) 與 priority queue(min) 來存放
#### better idea
* greedy
```c++
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());
        int length_g = g.size();
        int length_s = s.size();
        int i = 0; 
        int j = 0;
        int ans = 0;
        while(i<length_g && j<length_s){
            if(s[j]>=g[i]){
                ans++;
                i++;
            }
            j++;
        }
        return ans;
    }
};
```
### 461. Hamming Distance
#### idea
```c++
class Solution {
public:
    int hammingDistance(int x, int y) {
        int ans = 0;
        int z = x^y;
        while(z){
            if(z&1)
                ans++;
            z = z>>1;
        }
        return ans;
    }
};
```
```c++
class Solution {
public:
    int hammingDistance(int x, int y) {
        int ans = 0;
        x^=y;
        while(x){
            ans+=(x&1);
            x>>=1;
        }
        return ans;
    }
};
```
### 476. Number Complement
#### original idea
* tips : log2(num)+1 可以得到 num 共有幾個 bit
```c++
class Solution {
public:
    int findComplement(int num) {
        int count = log2(abs(num))+1;
        int temp = pow(2, count)-1;
        return (num^temp);
    }
};
```
#### better idea
```c++
class Solution {
public:
    int findComplement(int num) {
        int copy = num;
        int i = 0;
        while(copy!=0){
            copy>>=1;
            num^=(1<<i);
            i+=1;
        }
        return num;
    }
};
```
#### wrong idea
* 2 會報錯
```c++
class Solution {
public:
    int findComplement(int num) {
        int ans = 0;
        while(num){
            ans<<=1;
            ans|=((num&1)^1);
            num>>=1;
        }
        return ans;
    }
};
```
### 501. Find Mode in Binary Search Tree
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
    unordered_map<int, int> map;
    void insert(TreeNode* root){
        if(!root)
            return;
        map[root->val]++;
        insert(root->left);
        insert(root->right);
    }
    vector<int> findMode(TreeNode* root) {
        vector<int> ans;
        insert(root);
        int base = INT_MIN;
        for(auto iter=map.begin(); iter!=map.end(); iter++){
            if(iter->second > base)
                base = iter->second;
        }
        for(auto iter=map.begin(); iter!=map.end(); iter++){
            if(iter->second==base)
                ans.push_back(iter->first);
        }
        return ans;
    }
};
```
### 504. Base 7
#### idea
```c++
class Solution {
public:
    string convertToBase7(int num) {
        if(num==0)
            return "0";
        int temp = num;
        num = abs(num);
        string ans = "";
        while(num!=0){
            int mod = num%7;
            num = num/7;
            char c = char(mod+48);
            ans+=c;
        }
        reverse(ans.begin(), ans.end());
        return temp>0 ? ans : "-"+ans;
    }
};
```
### 530. Minimum Absolute Difference in BST
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
    vector<int> map;
    void LDR(TreeNode* root){
        if(!root)
            return;
        LDR(root->left);
        map.push_back(root->val);
        LDR(root->right);
    }
    int getMinimumDifference(TreeNode* root) {
        LDR(root);
        int ans = INT_MAX;
        for(int i=0; i<map.size()-1; i++){
            if(map[i+1]-map[i] < ans)
                ans = map[i+1]-map[i];
        }
        return ans;
    }
};
```
### 543. Diameter of Binary Tree
#### idea
* 找出左子樹與右子樹最長的路徑(節點數)，最後加上父節點，就是最長之節點路徑。
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
    int findMax(TreeNode* root, int& nodes){
        if(root==NULL)
            return 0;
        int left = findMax(root->left, nodes);
        int right = findMax(root->right, nodes);
        nodes = max(nodes, left+right+1);
        return max(left, right)+1;
    }
    
    int diameterOfBinaryTree(TreeNode* root) {
        int nodes = 0;
        int foo = findMax(root, nodes);
        return nodes-1;
    }
};
```
### 566. Reshape the Matrix
#### original idea
* Runtime: 20 ms, faster than 45.52% of C++ online submissions for Reshape the Matrix.
* Memory Usage: 11 MB, less than 25.92% of C++ online submissions for Reshape the Matrix.
```c++
class Solution {
public:
    vector<vector<int>> matrixReshape(vector<vector<int>>& mat, int r, int c) {
        if(mat.size()*mat[0].size() != r*c)
            return mat;
        vector<int> row(c, 0);
        vector<vector<int>> ans(r, row);
        queue<int> temp;
        for(int i=0; i<mat.size(); i++){
            for(int j=0; j<mat[0].size(); j++){
                temp.push(mat[i][j]);
            }
        }
        for(int i=0; i<ans.size(); i++){
            for(int j=0; j<ans[0].size(); j++){
                int value = temp.front();
                temp.pop();
                ans[i][j] = value;
            }
        }
        return ans;
    }
};
```
#### better idea
* Runtime: 12 ms, faster than 45.52% of C++ online submissions for Reshape the Matrix.
* Memory Usage: 10.7 MB, less than 73.58% of C++ online submissions for Reshape the Matrix.
```c++
class Solution {
public:
    vector<vector<int>> matrixReshape(vector<vector<int>>& mat, int r, int c) {
        if(mat.size()*mat[0].size() != r*c)
            return mat;
        vector<int> row(c, 0);
        vector<vector<int>> ans(r, row);
        for(int i=0; i<mat.size()*mat[0].size(); i++)
            ans[i/c][i%c] = mat[i/mat[0].size()][i%mat[0].size()];
        return ans;
    }
};
```
### 572. Subtree of Another Tree
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
    bool isEqual(TreeNode* root, TreeNode* subRoot){
        if(!root || !subRoot)
            return root==subRoot;
        return root->val==subRoot->val && isEqual(root->left, subRoot->left) && isEqual(root->right, subRoot->right); 
    }
    
    bool isSubtree(TreeNode* root, TreeNode* subRoot) {
        return isEqual(root, subRoot) || (root->left && isSubtree(root->left, subRoot)) || (root->right && isSubtree(root->right, subRoot));  
    }
};
```
### 594. Longest Harmonious Subsequence
#### idea
```c++
class Solution {
public:
    int findLHS(vector<int>& nums) {
        int ans = 0;
        unordered_map<int, int> dic;
        for(auto num : nums)
            dic[num]++;
        for(auto [num, count] : dic){
            if(dic.find(num+1)!=dic.end())
                ans = max(ans, count+dic[num+1]);
        }
        return ans;
    }
};
```
### 605. Can Place Flowers
#### idea
* greedy
* 小技巧 : 在前後兩端先加入 0 
* 注意 : 是找到不相鄰的種花方式，使得可以種 n 朵花，不是要找在不相鄰的情況下，最多可以種多少花
```c++
class Solution {
public:
    bool canPlaceFlowers(vector<int>& flowerbed, int n) {
        int i = 0;
        int ans = 0;
        
        // boundary case
        if(n==0) return true;
        if(flowerbed.size()==0) return false;
        if(flowerbed.size()==1) return flowerbed[0]==0;
        
        flowerbed.push_back(0);
        flowerbed.insert(flowerbed.begin(), 0);
        
        while(i<flowerbed.size()-2 && (ans<n)){
            if(flowerbed[i]==0 && flowerbed[i+1]==0 && flowerbed[i+2]==0){
                ans++;
                flowerbed[i+1] = 1;
            }
            i++;
        }
        return n==ans;
    }
};
```
### 617. Merge Two Binary Trees
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
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        if(root1 && root2)
            root1->val = root1->val+root2->val;
        else if(root1==NULL)
            return root2;
        else if(root2==NULL)
            return root1;
        root1->left = mergeTrees(root1->left, root2->left);
        root1->right = mergeTrees(root1->right, root2->right);
        return root1;
    }
};
```
### 628. Maximum Product of Three Numbers
#### idea
```c++
class Solution {
public:
    int maximumProduct(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        int n = nums.size();
        return max(nums[n-1]*nums[n-2]*nums[n-3], nums[0]*nums[1]*nums[n-1]);
    }
};
```
### 637. Average of Levels in Binary Tree
#### idea
* BFS
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
    vector<double> averageOfLevels(TreeNode* root) {
        queue<TreeNode*> q;
        vector<double> ans;
        q.push(root);
        while(!q.empty()){
            int size = q.size();
            double row = 0;
            for(int i=0; i<size; i++){
                struct TreeNode* current = q.front();
                q.pop();
                row+=current->val;
                if(current->left)
                    q.push(current->left);
                if(current->right)
                    q.push(current->right);
            }
            ans.push_back(row/size);
        }
        return ans;
    }
};
```
### 645. Set Mismatch
#### idea
* 有技巧的，背起來。
```c++
class Solution {
public:
    vector<int> findErrorNums(vector<int>& nums) {
        int n = nums.size();
        vector<int> ans;
        vector<bool> visit(n+1, false);
        int sum = n*(n+1)/2;
        for(auto num:nums){
            sum-=num;
            if(visit[num])
                ans.push_back(num);
            visit[num] = true;
        }
        ans.push_back(ans[0]+sum);
        return ans;
    }
};
```
### 653. Two Sum IV - Input is a BST
#### idea
* 用 vector 或是 map 紀錄
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
    vector<int> map;
    void LDR(TreeNode* root){
        if(!root)
            return;
        LDR(root->left);
        map.push_back(root->val);
        LDR(root->right);
    }
    
    bool findTarget(TreeNode* root, int k) {
        LDR(root);
        int left = 0;
        int right = map.size()-1;
        while(left<right){
            int current = map[left]+map[right];
            if(current<k)
                left++;
            else if(current>k)
                right--;
            else
                return true;
        }
        return false;
    }
};
```
### 680. Valid Palindrome II
#### wrong idea
1. 用 dic 統計所有字母出現次數
2. i 由左往又掃, j 由右往左掃, 若 dic[s[i]] 或是 dic[s[j]] 為奇數且是 dic 內的為2個奇數, 則是 true
#### correct idea
```c++
// correct solution
class Solution {
public:
    bool validPalindrome(string s) {
        int i = 0;
        int j = s.length()-1;
        while(i<j){
            if(s[i]!=s[j]){
                return isPali(s, i+1, j) || isPali(s, i, j-1);
            }
            i++; j--;
        }
        return true;
    }
    bool isPali(string s, int i, int j){
        while(i<j){
            if(s[i]==s[j]){
                i++; j--;
            }
            else{
                return false;
            }
        }
        return true;
    }
};
```
### 693. Binary Number with Alternating Bits
#### idea
* x 先存目前 n 最右邊的 bit，之後 n 往右移，y 存目前 n 最右邊的 bit，若 x==y，則代表有連續兩個 bit 相同。
```c++
class Solution {
public:
    bool hasAlternatingBits(int n) {
        int x = n&1;
        while(n){
            n = n>>1;
            int y = n&1;
            if(x==y)
                return false;
            x = y;
        }
        return true;
    }
};
```
### 696. Count Binary Substrings
#### idea
* 演算法，可理解的。
* 先告兩個變數為 prev 與 curr
    * prev 為 i 之前的連續數列(假設為 0)長度
    * curr 為 i 目前的連續數列(假設為 1)長度
```c++
class Solution {
public:
    int countBinarySubstrings(string s) {
        int ans = 0;
        int prev = 0;
        int curr = 1;
        for(int i=1; i<s.size(); i++){
            if(s[i]==s[i-1])
                curr++;
            else{
                prev = curr;
                curr = 1;
            }
            if(curr<=prev)
                ans++;
        }
        return ans;
    }
};
```
### 744. Find Smallest Letter Greater Than Target
#### idea
```c++
class Solution {
public:
    char nextGreatestLetter(vector<char>& letters, char target) {
        char ans = 'a';
        int left = 0;
        int right = letters.size()-1;
        while(left<=right){
            int m = left+(right-left)/2;
            if(letters[m]>target){
                ans = letters[m];
                right = m-1;
            }    
            else if(letters[m]<=target)
                left = m+1;
        }
        if(ans>target)
            return ans;
        else
            return letters[0];
    }
};
```
### 766. Toeplitz Matrix
#### idea
* 技巧
* 假設題目為下列 A 矩陣，則找尋範圍只要針對 B 矩陣(也就是 A 矩陣的左上半部)所包含得元素即可
```
A
1 2 3 4
5 1 2 3
9 5 1 2

B
1 2 3
5 1 2
```
```c++
class Solution {
public:
    bool isToeplitzMatrix(vector<vector<int>>& matrix) {
        for(int i=0; i<matrix.size()-1; i++){
            for(int j=0; j<matrix[0].size()-1; j++){
                if(matrix[i][j]!=matrix[i+1][j+1])
                    return false;
            }
        }
        return true;
    }
};
```
