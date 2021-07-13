# Netflix

Netflix is the biggest video streaming platform in the world, offering movies, seasons, biographies, reality shows, and more. Their video repository is growing significantly. So the engineering team at Netflix keeps trying to find better ways to display content to their consumers.

The scenario and the problems discussed in this chapter also relate to any content displaying functionality and how we can improve it.

## Statement 
Let’s pretend you’re a developer on the Netflix engineering team. You are working on improving the user experience in finding content to watch. This involves the improvement of the search as well as recommendation functionality.

## Features 
We will need to introduce the following features to implement the improvements discussed above:

### Feature #1 Group Similar Titles
 - Feature # 1: We want to enable users to see relevant search results despite minor typos.
    - First, we need to figure out a way to individually group all the character combinations of each title. Suppose the content library contains the following titles: "duel", "dule", "speed", "spede", "deul", "cars". How would you efficiently implement a functionality so that if a user misspells speed as spede, they are shown the correct title?

We want to split the list of titles into sets of words so that all words in a set are anagrams. In the above list, there are three sets: {"duel", "dule", "deul"}, {"speed", "spede"}, and {"cars"}. Search results should comprise all members of the set that the search string is found in. We should pre-compute these sets instead of forming them when the user searches a title.

Let’s see how we might implement this functionality:
One solution:

```python
def group_titles(strs):
    res = {}
    for s in strs:
        count = [0] * 26
        for c in s:
            index = ord(c) - ord('a')
            count[index] += 1
        key = tuple(count)
        if key in res:
            res[key].append(s)
        else:
            res[key] = [s]
    return res.values()

# Driver code

titles = ["duel","dule","speed","spede","deul","cars"]
gt = list(group_titles(titles))
query = "spede" 

# Searching for all titles
for g in gt:
    if query in g:
        print(g)
```
Easier solution: 

1. We create a dictionary and for each word in the input array, we add a key to the dictionary if the sorted version of the word doesn't already exist in the list of keys. The key then becomes the sorted version of the word, and the value for the key is an array that stores each anagram of the key. i.e. for every next word that is an anagram, we would sort the word, find the key that is equal to the sorted form, and add the original word to the list of values for the key.
```python
def groupAnagrams(strs):
        hash_map = {}
        for word in strs:
            sortedWord = ''.join(sorted(word))
            if sortedWord not in hash_map: 
                hash_map[sortedWord] = [word] #(sortedKey = Key and word = Value)
                #print(hash_map)
            else: #(if there is word like the current word then just append new value to key)
                hash_map[sortedWord].append(word)
        print(hash_map)

titles = ["duel","dule","speed","spede","deul","cars"]
groupAnagrams(titles)
```
2. Return the values of the Hash Map, since each value will be an individual set.
 ```python
  final = []
        for value in hash_map.values():
            final.append(value)
        return final
```
  ``` python
def groupAnagrams(strs):
        hash_map = {}
        for word in strs:
            sortedWord = ''.join(sorted(word)) #(sort duel --> delu and dule--> delu)
            print(sortedWord)
            if sortedWord not in hash_map: (#if there is not word like the the current word then add a new item into dictionary)
                hash_map[sortedWord] = [word]#(sortedKey = Key and word = Value)
                print(hash_map)
            else: #(if there is word like the current word then just append new value to key)
                hash_map[sortedWord].append(word)
        final = []
        for value in hash_map.values():
            final.append(value)
        return final
    
titles = ["duel","dule","speed","spede","deul","cars"]
gt = list(groupAnagrams(titles))
query = "spede" 
for g in gt:
    if query in g:
        print(g)
        
delu
{'delu': ['duel']}
delu
deeps
{'delu': ['duel', 'dule'], 'deeps': ['speed']}
deeps
delu
acrs
{'delu': ['duel', 'dule', 'deul'], 'deeps': ['speed', 'spede'], 'acrs': ['cars']}

Answer:['speed', 'spede']
  ```
### Related Topics: Hash table
### Similar Questions: Valid Anagram (easy), Grouped Shifted Strings(medium)


------------------
### Feature #2: Fetch Top Movies
 - Feature # 2: Enable the user to view the top-rated movies worldwide, given that we have movie rankings available separately for different geographic regions.

We need to build a criterion so the top movies from multiple countries will combine into a single list of top-rated movies. In order to scale, the content search is performed in a distributed fashion. Search results for each country are produced in separate lists. Each member of a given list is ranked by popularity, with 1 being most popular and popularity decreasing as the rank number increases.

```
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
Explanation: The linked-lists are:
[
  1->4->5,
  1->3->4,
  2->6
]
merging them into one sorted list:
1->1->2->3->4->4->5->6
```

Since our task involves multiple lists, you should divide the problem into multiple tasks, starting with the problem of combining two lists at a time. Then, you should combine the result of those first two lists with the third list, and so on, until the very last one is reached.

Let’s discuss how we will implement this process:

1. Consider the first list as the result, and store it in a variable.

2.Traverse the list of lists, starting from the second list, and combine it with the list we stored as a result. The result should get stored in the same variable.

3. When combining the two lists, like l1 and l2, maintain a prev pointer that points to a dummy node.

4. If the value for list l1 is less than or equal to the value for list l2, connect the previous node to l1 and increment l1. Otherwise, do the same but for list l2.

5. Keep repeating the above step until one list points to a null value.

6. Connect the non-null list to the merged one and return.


![image]()

![image]()

``` python
**class LinkedListNode:
  def __init__(self, data):
    self.data = data
    self.next = None
    self.prev = None
    self.arbitrary = None
    
import random

def insert_at_head(head, data):
  newNode = LinkedListNode(data)
  newNode.next = head
  return newNode

def insert_at_tail(head, node):
  if head is None:
    return node

  temp = head;

  while temp.next != None:
    temp = temp.next

  temp.next = node;
  return head

def create_random_list(length):
  list_head = None
  for i in range(0, length):
    list_head = insert_at_head(list_head, random.randrange(1, 100))
  return list_head

def create_linked_list(lst):
  list_head = None
  for x in reversed(lst):
    list_head = insert_at_head(list_head, x)
  return list_head

def display(head):
  temp = head
  while temp:
    print(str(temp.data),end="")
    temp = temp.next
    if temp != None:
      print(", ", end="")

def to_list(head):
  lst = []
  temp = head
  while temp:
    lst.append(temp.data)
    temp = temp.next
  return lst

def is_equal(list1, list2):
  if list1 is list2:
    return True

  while list1 != None and list2 != None:
    if list1.data != list2.data:
      return False
    list1 = list1.next
    list2 = list2.next

  return list1 == list2**
###############################
from LinkedList import *
def merge2_country(l1, l2): # helper function
    dummy = LinkedListNode(-1)

    prev = dummy
    while l1 and l2:
        if l1.data <= l2.data:
            prev.next = l1
            l1 = l1.next
        else:
            prev.next = l2
            l2 = l2.next            
        prev = prev.next

    if l1 is not None:
        prev.next = l1
    else:
        prev.next = l2

    return dummy.next

def mergeK_county(lists): # Main function
    
    if len(lists) > 0:
        res = lists[0]
        for i in range(1, len(lists)):
            res = merge2_country(res, lists[i])
        return res
    return
```

``` python
# https://leetcode.com/problems/merge-k-sorted-lists/discuss/10919/Python-easy-to-understand-divide-and-conquer-solution.
 def mergeKLists(self, lists):
        if not lists:
            return None
        if len(lists) == 1:
            return lists[0]
        mid = len(lists) // 2
        l, r = self.mergeKLists(lists[:mid]), self.mergeKLists(lists[mid:])
        return self.merge(l, r)
    
    def merge(self, l, r):
        dummy = p = ListNode()
        while l and r:
            if l.val < r.val:
                p.next = l
                l = l.next
            else:
                p.next = r
                r = r.next
            p = p.next
        p.next = l or r
        return dummy.next
    
    def merge1(self, l, r):
        if not l or not r:
            return l or r
        if l.val< r.val:
            l.next = self.merge(l.next, r)
            return l
        r.next = self.merge(l, r.next)
        return r
```


We’ll be given n lists that are all sorted in ascending order of popularity rank. We have to combine these lists into a single list that will be sorted by rank in ascending order, meaning from best to worst.

- Feature # 3: As part of a demographic study, we are interested in the median age of our viewers. We want to implement a functionality whereby the median age can be updated efficiently whenever a new user signs up for Netflix.

- Feature # 4: For efficiently distributing content to different geographic regions and for program recommendation to viewers, we want to determine titles that are gaining or losing popularity scores.

- Feature # 5: For the client application, we want to implement a cache with a replacement strategy that replaces the least recently watched title.

- Feature # 6: As an alternative to the above, we want to implement a strategy of replacing the least frequently watched titles in the cache.

- Feature # 7: During a user session, a user often “shops” around for a program to watch. During this session, we want to let them move back and forth in the history of programs they’ve just browsed. As a developer, you can smell a stack, right? But, we also want the user to be able to directly jump to the top-ranked program from the one’s they’ve browsed.

- Feature # 8: As you beta tested feature #7, a user complained that the next and previous functionality isn’t working correctly. Using their session history, we want to check if our implementation is correct or indeed buggy.

