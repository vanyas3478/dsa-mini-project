# dsa-mini-project
#include <iostream>
#include <unordered_map>
using namespace std;

// Node structure
class Node {
public:
    int key, value;
    Node* prev;
    Node* next;
    Node(int k, int v) {
        key = k;
        value = v;
        prev = next = NULL;
    }
};

class LRUCache {
private:
    int capacity;
    unordered_map<int, Node*> cache;
    Node *head, *tail;

public:
    LRUCache(int cap) {
        capacity = cap;
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head->next = tail;
        tail->prev = head;
    }

    void addNode(Node* node) {
        node->next = head->next;
        node->prev = head;
        head->next->prev = node;
        head->next = node;
    }

    void removeNode(Node* node) {
        Node* prevNode = node->prev;
        Node* nextNode = node->next;
        prevNode->next = nextNode;
        nextNode->prev = prevNode;
    }

    int get(int key) {
        if (cache.find(key) != cache.end()) {
            Node* node = cache[key];
            removeNode(node);
            addNode(node);
            return node->value;
        }
        return -1;
    }

    void put(int key, int value) {
        if (cache.find(key) != cache.end()) {
            Node* node = cache[key];
            removeNode(node);
            cache.erase(key);
        }
        if (cache.size() == capacity) {
            Node* lru = tail->prev;
            removeNode(lru);
            cache.erase(lru->key);
        }
        Node* newNode = new Node(key, value);
        addNode(newNode);
        cache[key] = newNode;
    }
};

int main() {
    LRUCache lru(2);
    lru.put(1, 10);
    lru.put(2, 20);
    cout << lru.get(1) << endl;   // 10
    lru.put(3, 30);               // removes key 2
    cout << lru.get(2) << endl;   // -1
    return 0;
}

