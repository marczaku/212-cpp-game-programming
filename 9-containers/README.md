# 9 Containers

## Array
- `std::array<int, 10>`

## List
- `std::vector<int>`

## Double-Ended Queue
- `std::deque<int>`

## Double-Linked List
- `std::list<int>`

## Stack
- `std::stack`

## Queue
- `std::queue<int>`

## Priority Queue (Heap)
- `std::priority_queue<int>`

## Bitfield
- `std::bitset<4>`

## SortedList 
- unique elements: `std::set<int>`
- non-unique elements: `std::multiset<int>`

## HashSet
- unique: `std::unordered_set<T>`
- non-unique: `std::unordered_multiset<T>`

## Dictionary
- unique keys: `std::map<const char*, Player>`
- non-unique keys: `std::multimap<const char*, Player>`

## Graph
- `boost::adjacency_list<>`
- `boost::adjacency_matrix<>`
- `boost::edge_list<>`

## Property Tree
Pretty cool for creating e.g. JSON Document Trees
- `boost::property_tree::ptree`

## Initializer List
Allows your custom types to be initialized with initializer lists
- `std::initializer_list`