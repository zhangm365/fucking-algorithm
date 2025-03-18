
```c++
class Node {
public:
    int _key, _val;
    Node* prev;
    Node* next;
    
    Node(int key, int val) {
        _key = key;
        _val = val;
        prev = nullptr;
        next = nullptr;
    } 
};

// 双链表
class DoubleLinkedList {
private:
    Node* head; // 头指针
    Node* tail; // 尾指针
    int size;

public:
    DoubleLinkedList() {
        head = new Node(0, 0); // 头节点
        tail = new Node(0, 0); // 尾节点
        head->next = tail;
        tail->prev = head;
        size = 0;
    }

    // 在链表头部增加一个节点
    void addFirst(Node* node) {

        if(node == nullptr) {
            return;
        }
        node->prev = head;
        node->next = head->next;
        head->next->prev = node;
        head->next = node;
        size++;

    }
    // 删除尾节点
    Node* removeLast() {
        if(head->next == tail) {
            return nullptr;
        }

        Node* del = tail->prev;
        remove(del);
        
        return del;
    }

    void remove(Node* node) { // 删除某个节点
        node->prev->next = node->next;
        node->next->prev = node->prev;
        node->next = nullptr;
        node->prev = nullptr;
        size--;
    }

    int getSize() const {
        return size;
    }
};

class LRUCache {
public:
    LRUCache(int capacity) {
        cap = capacity;
    }
    
    int get(int key) {
        if(umap.find(key) != umap.end()) {
            makeRecently(key);
            return umap[key]->_val;
        }

        return -1;
        
    }
    
    void put(int key, int value) {
        if(umap.find(key) != umap.end()) { // 已存在，更新 value
            deleteKey(key);
            addRecently(key, value);
            return;

        } else { // 插入新节点
            if(cache.getSize() >= cap) { // 先删除尾节点
                removeLeastRecently();
            }
            addRecently(key, value);
        }

    }
    // 将某个 key 重新提升为最近使用节点
    void makeRecently(int key) {

        if(umap.find(key) == umap.end()) {
           return;
        }

        Node* cur = umap[key];
        // 先删除
        cache.remove(cur);
        // 重新插入
        cache.addFirst(cur);

    };

    // 增加一个节点
    void addRecently(int key, int value) {
        Node* cur = new Node(key, value);
        cache.addFirst(cur);
        umap[key] = cur;
    }

    // 删除最近未被使用的
    void removeLeastRecently() {
        Node* cur = cache.removeLast();
        int delKey = cur->_key;
        umap.erase(delKey);
    }

    // 删除一个 key
    void deleteKey(int key) {
        Node* cur = umap[key];
        cache.remove(cur);
        umap.erase(key);
    }

private:
    int cap;   // 最大容量
    unordered_map<int, Node*> umap;  // 映射 <k, Node*>
    DoubleLinkedList cache;
};

```