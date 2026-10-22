# WDM-Ex.No-4--IMPLEMENTATION-OF-GSP-ALGORITHM-
**Aim:** 
 To create a python program for implementing GSP Algorithm. 
**Program:** 
**gsp2.py** 
import os 
import codecs 
import itertools 
import operator 
 
class GSP: 
 '''GSP algorithm implementation''' 
 def __init__(self, min_sup, path): 
  self.min_sup = min_sup 
  self.path = path 
  self.unique_words, self.word_list, self.data = self.parse_data() 
  self.items = {} 
  self.num_users = len(self.data) 
 
 def parse_data(self): 
  files = os.listdir(self.path) 
  files = [self.path + file for file in files] 
  unique_words = {} # contains unique words in the data files 
  unique_counter = 0 # ID number assigned to each unique word 
  seqs = [] 
  word_list = [] 
 
  for filename in files: 
   # read user data 

   lines = [] 
   f = codecs.open(filename, encoding='utf-8') 
   for line in f: 
    lines.append(line.encode('utf-8')) 
   ans = {} # contains final time series dictionary 
   seq = {} 
   seq['file'] = filename 
   s1 = [] 
   s2 = [] 
   # for each line in data file 
   for X in lines: 
    Y = X.strip().split(';') 
    time = Y[0].split(' ')[1] 
    # extract time info 
    x = [] 
 
    # extract useful info at a given time stamp 
    for i,data in enumerate(Y): 
     if i > 0:  
      word = data.split('#')[1] 
 
      # check if word is unique or not 
      # if not, assign a new ID number 
      if word not in unique_words: 
       unique_counter = unique_counter + 1 
       unique_words[word] = unique_counter 
       word_list.append(word) 
      x.append(unique_words[word]) 
    # if x is not empty 
    if x: 
     ans[time] = x 

     s1.extend(x) 
     s2.append(tuple(x)) 
   seq['data'] = ans 
   seq['seq_individual'] = s1 
   seq['seq_combined'] = s2 
   seqs.append(seq) 
 
  return unique_words, word_list, seqs 
 
 def is_subseq(self, x, y): 
  it = iter(y) 
  return all(c in it for c in x) 
 
 
 def find_support(self, item, flag): 
  count = 0 
  if flag == 1: 
   for i in range(self.num_users): 
    if self.is_subseq(item, self.data[i]['seq_individual']): 
     count += 1 
  else: 
   for i in range(self.num_users): 
    if item in self.data[i]['seq_combined']: 
     count += 1 
  return count 
 
 
 def get_support_items(self, level): 
  # Step 1: Find items that meet min. threshold requirement 
  # print self.num_users 
  for word in self.unique_words: 

   l = [self.unique_words[word]] 
   sup = self.find_support(l, 1) 
   if sup >= self.min_sup: 
    self.items[tuple(l)] = sup 
 
  # If we need only 1-grams, we print those here and exit. 
  if level == 1: 
   sorted_patterns = sorted(self.items.items(), key=operator.itemgetter(1), reverse=True) 
   for t in sorted_patterns: 
    p = [] 
    for i in t[0]: 
     print(self.word_list[i - 1],) 
    print t[1] 
   return 
 
  # We now generate permutations of size 2. 
  keys = [x[0] for x in self.items.keys()] 
  perms = itertools.permutations(keys, 2) 
  self.perms = list(perms) 
 
  self.items = {} 
 
  # find support for tuples of size 2 
  for p in self.perms: 
   sup = self.find_support(p, 2) 
   if sup >= self.min_sup: 
    self.items[(p, )] = sup 
 
  for k in keys: 
   self.perms.append((k, k))  
 

  # find support for permutations of size 2 
  for p in self.perms: 
   sup = self.find_support(list(p), 1) 
   if sup >= self.min_sup: 
    self.items[p] = sup 
 
  # sort patterns in descending order 
  sorted_patterns = sorted(self.items.items(), key=operator.itemgetter(1), reverse=True) 
 
  # print patterns 
  for t in sorted_patterns: 
   p = [] 
   if type(t[0][0]) is int: 
    for i in t[0]: 
     print self.word_list[i - 1], 
   else: 
    print '(', 
    for i in t[0][0]: 
     print self.word_list[i - 1] + ',', 
    print ')', 
   print t[1] 
 
  return 
    
 
**main2.py** 
```from gspx import gsp2 
threshold = 3 # minimum support 
level = 3 # number of levels for GSP 
g = gsp2.GSP(threshold, 'C:\\Users\\Welcome\\AppData\\Local\\Programs\\Python\\Python311\\Data_GSP\\') 
g.get_support_items(level) 
Dataset: 
1.txt 
2017-09-11 13:18:28;topos#Chateaulin;chronos#XXe siecle 
2017-09-11 13:19:26;topos#Guiler-sur-Goyen 
2017-09-11 13:20:28;thema#Chapelle;chronos#XVIe siecle 
2017-09-11 13:21:26;topos#Ruca 
2017-09-11 13:22:29;thema#Chapiteau;topos#Saint-Caradec-Trégomel;chronos#XVIIIe siecle 
2017-09-11 13:23:28;chronos#XVe siecle 
2017-09-11 13:24:29;thema#Retable;topos#Saint-Thelo;chronos#XVIIe siecle 
2017-09-11 13:26:27;topos#Ruca 
2017-09-11 13:27:27;topos#Nevez 
2017-09-11 13:28:30;thema#Vitrail;topos#Saint-Nicolas-du-Pelem;chronos#XVe siecle 
2017-09-11 13:29:30;topos#Saint-Brieuc;chronos#XIXe siecle 
2017-09-11 13:31:45;thema#Clocher;topos#Melrand 
2017-09-11 13:32:27;topos#Ploufragan 
2017-09-11 13:29:57;topos#Plouezoch 
2017-09-11 13:30:20;thema#Tableau;topos#Landévennec;chronos#XVIIe siecle 
2017-09-11 13:30:57;thema#Chapelle 
2017-09-11 13:31:59;thema#Chateau;topos#Saint-Martin-des-Champs;chronos#XVIIIe siecle 
2017-09-11 13:32:59;topos#Le Conquet;chronos#XVe siecle 
2017-09-11 13:33:58;thema#Autel;topos#Plumergat 
2017-09-11 13:34:59;thema#Croix;chronos#XVIIe siecle 
2017-09-11 13:35:57;topos#Les Fougerets 
2017-09-11 13:37:59;thema#Presbytere;chronos#XVe siecle 
2017-09-11 13:38:12;thema#Habitations religieuses;topos#Morbihan;chronos#Renaissance 
43
2017-09-11 13:39:01;chronos#XIXe siecle 
2017-09-11 13:39:39;thema#Couvent;topos#Loperhet;chronos#1602 
2017-09-11 13:40:59;thema#Banniere;topos#Loc-Brevalaire 
2017-09-11 13:41:36;thema#Jardin;topos#Brest;chronos#XVIIIe siecle 
2017-09-11 13:42:01;thema#Quai;topos#Sibiril;chronos#XXe siecle 
2017-09-11 13:43:01;chronos#XIIIe siecle 
2017-09-11 13:44:00;chronos#XVIe siècle

```
Output: 
 
 <img width="1091" height="579" alt="image" src="https://github.com/user-attachments/assets/d9a0be35-7a80-491c-b38f-19512b6b320a" />

 
 
 
 
 
 
 
Result: 
Thus, the python program for GSP Algorithm has been implemented successfully. 
