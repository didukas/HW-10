# HW-10

class Node:
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None

class DoublyLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None
        self.size = 0

    def insert(self, key, value):
        new_node = Node(key, value)
        if not self.head:
            self.head = self.tail = new_node
        else:
            new_node.prev = self.tail
            self.tail.next = new_node
            self.tail = new_node
        self.size += 1

    def find(self, key):
        current = self.head
        while current:
            if current.key == key:
                return current
            current = current.next
        return None

    def remove(self, node):
        if node == self.head:
            self.head = node.next
        if node == self.tail:
            self.tail = node.prev
        if node.prev:
            node.prev.next = node.next
        if node.next:
            node.next.prev = node.prev
        self.size -= 1

    def get_size(self):
        return self.size

class HashTable:
    def __init__(self, initial_capacity=8):
        self.capacity = initial_capacity
        self.size = 0
        self.table = [DoublyLinkedList() for _ in range(self.capacity)]
        self.A = 0.6180339887  # Golden ratio for multiplication method

    def hash(self, key):
        fractional = key * self.A - int(key * self.A)
        return int(self.capacity * fractional)

    def resize(self, new_capacity):
        new_table = [DoublyLinkedList() for _ in range(new_capacity)]
        for i in range(self.capacity):
            current = self.table[i].head
            while current:
                new_index = self.hash(current.key)
                new_table[new_index].insert(current.key, current.value)
                current = current.next
        self.table = new_table
        self.capacity = new_capacity

    def insert(self, key, value):
        index = self.hash(key)
        existing = self.table[index].find(key)
        if existing:
            existing.value = value
        else:
            self.table[index].insert(key, value)
            self.size += 1
            if self.size >= self.capacity:
                self.resize(self.capacity * 2)

    def get(self, key):
        index = self.hash(key)
        node = self.table[index].find(key)
        return node.value if node else -1  # Return -1 if not found

    def remove(self, key):
        index = self.hash(key)
        node = self.table[index].find(key)
        if node:
            self.table[index].remove(node)
            self.size -= 1
            if self.size <= self.capacity // 4 and self.capacity > 8:
                self.resize(self.capacity // 2)

# Test HashTable implementation
if __name__ == "__main__":
    ht = HashTable()

    # Test insertion
    ht.insert(5, 50)
    ht.insert(15, 150)
    ht.insert(25, 250)

    # Test retrieval
    print("Value for key 15:", ht.get(15))
    print("Value for key 25:", ht.get(25))

    # Test update
    ht.insert(15, 151)
    print("Updated value for key 15:", ht.get(15))

    # Test removal
    ht.remove(25)
    print("Value for removed key 25:", ht.get(25))
