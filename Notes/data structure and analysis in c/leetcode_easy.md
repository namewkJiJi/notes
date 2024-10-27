## 链表

### 21.合并两个有序链表

```c
#include <stdlib.h>
/*
 * Definition for singly-linked list.
 
  typedef struct ListNode {
      int val;
      struct ListNode *next;
  }ListNode;
 */

struct ListNode* mergeTwoLists(struct ListNode* list1,struct ListNode* list2) {
    //创建一个头节点用来存放新的链表
    struct ListNode * headNode = (struct ListNode*)malloc(sizeof(struct ListNode));
    headNode->val = 0;
    headNode->next = NULL;
    struct ListNode* temp = headNode;

    //当两个列表都有值未比较时，将较小的插入新链表
    while(list1 && list2){
        //比较两个节点
        if(list1->val <= list2->val)
        {
            temp->next = list1;
            list1 = list1->next;
        }else{
            temp->next = list2;
            list2 = list2->next;
        }
        temp = temp->next;
    }

    //若list1更长：
    if(list1){
        temp->next = list1;
    }

    if(list2){
        temp->next = list2;
    }

    return headNode->next;

}
```

创建一个头指针并创建一个临时指针 

头指针易于开始插入操作，也易于找到最后形成链表的头（head->next）

temp指针标识插入位置

### 83删除排序链表中的重复元素

遍历链表

比较该指针与该指针的后一个 （与前一个相比较也可行，在开始前将head指针指向第二个node）

```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
struct ListNode* deleteDuplicates(struct ListNode* head) {
    // if(!head){return head;}
    struct ListNode* flag = head;
    //travers the list
    while(flag && flag->next){    //note01
        //compare head and head's sucssusor
        if(flag->val == flag->next->val){ //note02
            //delete head->next
            struct ListNode*temp = flag->next;
            flag->next = flag->next->next;
            free(temp);
        }else{//后移指针
            flag = flag->next;
        }
    }
    
    return head;
}
```

1. 在判断flag->next是否为空之前先判断一下flag是否为空，若flag为空则flag->next没有意义，会报错
2. 比较该节点和该节点的后一个 比较的是val域而不是比较node指针

### 141.环形链表判断

https://leetcode.cn/problems/linked-list-cycle/description/



双指针 --->  快慢指针

若不存在环 则指针一直后移直到null结束

若存在环，则快指针与慢指针一定会相遇 

point ： 在循环内判断快慢指针是否会相遇即可



```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
bool hasCycle(struct ListNode *head) {

    if(!head) return false;

    struct ListNode* fast = head;
    struct ListNode* slow = head;

    while(fast&&fast->next&&slow){//note
        fast = fast->next->next;
        slow = slow->next;
        if(slow == fast && fast && slow )//note
            return true;
     }

    return false;

}
```

note : 注意空指针错误的发生

若不想更改链表，则尽量保持原有的链表不变，包括头节点，故创建局部变量来保持原有链表不变



## 字符串

### 28.找出字符串中第一个匹配项的下标

https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/description/



1. 暴力匹配算法：

   ```c
   int strStr(char* haystack, char* needle) {
       int i = 0,j = 0;
       while(haystack[i] != '\0' && needle[j] != '\0'){
           if(haystack[i] == needle[j]) {
               i++;
               j++;
           }else{
               i = i - j + 1;//在大二上学期的数据结构课中，j从1开始取，故 i= i - j + 2
               j = 0;
           }
       }
   
       if(needle[j]=='\0'){
           return i-j;  //同上，i - j + 1
       }else{
           return -1;
       }
   
   }
   ```

   

​	2. KMP 算法：

```c

```















































