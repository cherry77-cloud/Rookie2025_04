## 一. 排序链表
- 找到链表的中间结点 `head2` 的前一个节点，并断开 `head2` 与其前一个节点的连接。这样我们就把原链表均分成了两段更短的链表。
- **分治**，递归调用 `sortList`，分别排序 `head`（只有前一半）和 `head2`。
- 排序后，我们得到了两个有序链表，那么合并两个有序链表，得到排序后的链表，返回链表头节点

---

```cpp
class Solution {
public:
    ListNode* middleNode(ListNode* head) {
        ListNode* pre = head;
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast && fast->next) {
            pre = slow;
            slow = slow->next;
            fast = fast->next->next;
        }
        pre->next = nullptr;
        return slow;
    }

    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode dummy;
        ListNode* cur = &dummy;
        while (l1 && l2) {
            if (l1->val <= l2->val) {
                cur->next = l1;
                l1 = l1->next;
            } else {
                cur->next = l2;
                l2 = l2->next;
            }
            cur = cur->next;
        } 
        cur->next = l1 == nullptr ? l2 : l1;
        return dummy.next;
    }

    ListNode* sortList(ListNode* head) {
        if (head == nullptr || head->next == nullptr) {
            return head;
        }
        ListNode* head2 = middleNode(head);
        head = sortList(head);
        head2 = sortList(head2);
        return mergeTwoLists(head, head2);
    }
};
```
