# CodingInterviewinRealLife

Netflix is the biggest video streaming platform in the world, offering movies, seasons, biographies, reality shows, and more. Their video repository is growing significantly. So the engineering team at Netflix keeps trying to find better ways to display content to their consumers.

The scenario and the problems discussed in this chapter also relate to any content displaying functionality and how we can improve it.

# Statement 
Let’s pretend you’re a developer on the Netflix engineering team. You are working on improving the user experience in finding content to watch. This involves the improvement of the search as well as recommendation functionality.

# Features 
We will need to introduce the following features to implement the improvements discussed above:

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
## Related Topics: Hash table
## Similar Questions: Valid Anagram (easy), Grouped Shifted Strings(medium)

 - Feature # 2: Enable the user to view the top-rated movies worldwide, given that we have movie rankings available separately for different geographic regions.

- Feature # 3: As part of a demographic study, we are interested in the median age of our viewers. We want to implement a functionality whereby the median age can be updated efficiently whenever a new user signs up for Netflix.

- Feature # 4: For efficiently distributing content to different geographic regions and for program recommendation to viewers, we want to determine titles that are gaining or losing popularity scores.

- Feature # 5: For the client application, we want to implement a cache with a replacement strategy that replaces the least recently watched title.

- Feature # 6: As an alternative to the above, we want to implement a strategy of replacing the least frequently watched titles in the cache.

- Feature # 7: During a user session, a user often “shops” around for a program to watch. During this session, we want to let them move back and forth in the history of programs they’ve just browsed. As a developer, you can smell a stack, right? But, we also want the user to be able to directly jump to the top-ranked program from the one’s they’ve browsed.

- Feature # 8: As you beta tested feature #7, a user complained that the next and previous functionality isn’t working correctly. Using their session history, we want to check if our implementation is correct or indeed buggy.

