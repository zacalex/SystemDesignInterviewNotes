
# Meta Prep

一点要clarify, 先问问题，注意分析解法，时间复杂度，以及如果有别的解法也说明trade off


## PA
- AI: gen AI
- privacy and integrity: 
- developer internal tool:
- metaverse: AR
- discovery 
- monetization: ads
- ins

## Schdule
First-round coding questions: 
- 2 medium in 35 min
- video 4-5 rounds: 2 more coding,
- 2 design, 
- 1 conversation  BQ

## Coding
- 162: find peak element 
- 1249: Minimum Remove to Make Valid Parentheses
- 347: top K frequent elements
- 1004: max consequent ones flip at most k 0
- 528: random pick with weight
- 50: pow(x, n)
- 295: find median
- 199: right side view of binary tree
- 314: vertical order of binary tree
- 127: word ladder
- 938: range sum of BST
- 1778: shortest path in the hidden grid, 走地图题
- 215: kth largest element in the array
- 408: valid word abbreviation 
- 680: Valid Palindrome after delete at most 1 character
- 560: total number of subarray sum equals to k, hashmap with count (all positive / has negative, all possible solutions)
- 140: word break ( time complexity 分析？)
- 1031: Maximum Sum of Two Non-Overlapping Subarrays 
- 22: generate ()
- 39: combination sum
- 129: sum root to leaf 
- 934: shortest bridge 
- 1570: dot product of sparse vector
  - 考虑多  solution, array for for loop, hash map, two pointer, two pointer with binary search
- 608: sql
- 76: minimum window substring
- 71: simplify the path 
- 217: contains duplicate, at least one element appears twice, 用set
- 1209: remove all adjacent letters in string, 只删k个，不多删, stack里放char + freq
- 525: contiguous array, longest subarray with equal 0 and 1, 遇到0就是-1， 1就是1， 用map，计算sum, 遇到相同的就计算，sum只记第一次的index 
- 426: convert tree to doubly linked list
- 56: merge interval, 注意加最后一个
- 384: shuffle the array
- 1060: missing element in the array, 
- 249: group shifted string, abc - bcd
- 1047: remove all adjacent string
- 207: course schedule
- 346: moving average from data stream
- 227: basic calculator, deque, 遇到符号就计算然后入栈, 最后弹栈相加 → O(1) space, follow up是口述一下还有➖➗怎么办，follow up 2是口述一下还有nested括号群怎么办。
- 16: 3 sum closest
- 88: merge sorted array
- 658: find k closest element, find index, then do k
- 8: string to integer
- 1123 = 865: Smallest Subtree with all the Deepest Nodes
- 973: k closest point to origin 
- 543: diameter of a tree
- 116: populate next right pointer in each node
- 79: word search
- 21: merge two sorted list
- 236: lca
- 1216: valid palindrome by removing at most k, dp 
- 437: path sum iii
- 827: making a larger land
- 1762: buildings with ocean view
- 146. LRU Cache
- count and say
- next permutation 
- 339: Nested List Weight Sum
- Diagonal Traverse
- 987：vertical order of binary traversal tree
- 43: multiply string
- Balance a Binary Search Tree - 写一下
- Sudoku Solver
- 1650. Lowest Common Ancestor of a Binary Tree III
- 14: Longest Common Prefix
- merge k sorted, 多种方法
- 125: valid palindrome
- Random Pick Index
- Maximum Swap
- Expression Add Operators
- 443: string compression
- 138: Copy List with Random Pointer
- 1091： shortest path 输出path
- 721: Accounts Merge
- 219： contains duplicate, 两个相同的字母位置差小于k
- 输入为一个字符串，要求根据frequency从高到低重新排序。e.g.  "mammamia" -> "mmmmaaai". 如果出现次数相同，按字母表顺序排列。
- intersection of K lists of intervals
- 23: 23. Merge k Sorted Lists
- 986: Interval List Intersections
- 两个数组，一个表示起飞的价格，一个表示回来的价格。时间随意，求最低价格的机票和。EXAMPLE : [10 7 8 3 10]  [2 4 3 10 10]  选  第二天走 第三天回 价格最低。 7+3 = 10
https://leetcode.com/discuss/interview-question/4288566/E4-meta-phone-screen-qs/
- Search in Rotated Sorted Array
- Continuous Subarray Sum that is a multiple of k
- 480: Sliding Window Median
onsite:
- Custom Sort String
- 721: Accounts Merge
- Longest Substring with At Most K Distinct Characters
- Partition Array Into Three Parts With Equal Sum
- Populating Next Right Pointers in Each Node
- Valid Palindrome III
- Number of Ways to Stay in the Same Place After Some Steps
- Unique Paths II (找到一条path, 分析时空复杂度, 如果dfs搜索的时候维护一个visited保证每个点只走一遍，O(mn)
- Shortest Distance from All Buildings
- Convert Sorted List to Binary Search Tree
- Valid Number
- Diagonal Traverse II
-  Swapping Nodes in a Linked List
有点像leetcode interval intersection list 但是找的是union
- Add Strings
- Closest Leaf in a Binary Tree
- Daily Temperatures 找个点属于最多的intervals
- Minimum Time to Collect All Apples in a Tree
最大公约数
- Remove Invalid Parentheses
- Non-overlapping Intervals
- Find All Anagrams in a String
- Insert into a Sorted Circular Linked List
- All Nodes Distance K in Binary Tree
  - array大小是未知的，你可以理解为一个流式数据，不走完所有数据都不知道数据流有多大。在这种情况下，从里面取K个数，要求取这K个数符合等概率采样。然后就用蓄水池抽样，先装前K个数，后边来一个，假设是第 j 个数，该数会以 k/j的概率去替换预装的那K个数，每个数被替换的几率相等。最终计算下来所有数字被选中的概率相等，都为 k/n，一次采样时间复杂度为O(n)


## System Design: 
- 1Point3Acres Team脸书系统设计集合贴|facebook面经|一亩三分地海外面经版​
- reduce number of writes: batch write until reaching a limit, but will cause db have stale data, can be two stage, get from db, query service to add current count, then sort, then return 
- reduce number of requests/ read: cache, CDN(on the response to our /search endpoint, we can add cache-control headers which tell our CDN when and for how long to cache a result)
reduce storage: on a regular base, find the ones which is less hit, move them to cheaper storage like S3
- LB: round robin or based on the metric such as cpu, memory, connections
设计一个社交平台的评论系统，需要最大程度的实现实时
这个HTTP Resource Downloader就是，面试官会提供一个最基础的downloader，然后要设计一个wrapper，用在Ins之类的app里面。提供诸如retry, callback, priority之类的params，然后根据这些params来设计backend。比如：
sync vs async下载
Resumable Download -> download progress / error notification
如何进行priority下载
如何cache常见下载内容
Multi Threads Download
ad click aggregator 
live comment

- SD 2： 设计leetcode
  - followup问了container/vm/thread经典问题
- SD: 设计周赛/leetcode contest/hackerrank
  - 比赛系统 加一个全局用户分数排序
- Top k song for each user
- Trending hashtags/ 热搜， 
  - 想知道不同时间长度的热搜。如果突然爆发了热点怎么处理。
- 爬虫(有一些限定条件，比如说一万台机器，尽可能让每台机器的load evenly distributed。这道题做了非常多的back of envelope calculation，要计算 different instance types handling different types of work)(我的design大概是这样：page downloader, page parser, url distributer, DB, message buffer and cache。每个component的workload type都完全不一样，cpu intensive/memory intensive/IO intensive/etc, 根据不同的workload来决定instance type再来决定每个component有多少机器)
- System Design 2: Hacker version Web crawler
爬虫
- 搞一个Insta shop的bid system
  - 要在ins的基础上设计个拍卖系统，做出来后追问我如何解决sniping的问题（我的方案是检查到sniping就延迟拍卖结束的时间)
- ig auction/ instagram 竞价商品（用户可以view current bid price 然就可以bid with higher price。 
  - 主要问了如果bid at same price 怎么解决conflict。以及怎么sca‍‍‌‌‌‌‌‌‍‍‍‌‌‍‌‌‌‍le一个很hot的auction）
- facebook news app / SD: design Ins
  - 不需要管create post flow，只需要讲newsfeed怎么generate的
  - 大体说了如何fan out on write，存在newsfeed cache里面
  - 主要围绕ranking问了很多问题，不是ml那种ranking。assume不是按照时间顺序rank，而是有个blackbox service能做ranking，问ranking应该在哪一步做，哪个service做，newsfeed cache里面存什么，是存rank好的newsfeed还是在request的时候再rank。
  - 以及pagination怎么做，newsfeed里rank会根据新来的feed变化，该怎么处理，如何解决duplication的问题。
- SD: ig newsfeed, 
  - 追问了怎么处理new post来通知用户的情况 / 设计news feed API
  - facebook 評論系統(?), 有點忘記細節了
    - user1 看到某個post1, user1 write comment
    - user2 看到post2, write comment, 同時要通知user1
    - 题目要求是 对于一个fackbook post
      - user a, user b 在看post
      - user c发了一个新的comment
      - 怎么把这个comment发给user a/b a,b 不需要手动刷新app

- 一个fan out题目的变种算是
楼主的想法是
viewer 的client 会定期发送当前用户正在看的postid 到server 然后把这个entity 写进cache
comment server 查询cache, 获知所有正在看post 的user id
对于 hot post (同时有很多人在看) 用pubsub system aysnc 通知
对于 cold post 用push method 实时推送
前面都还好 就是最后面试官说有一个地方是不work的 当时没看出来 面试结束10秒钟想到
应该是 reading post status cache 的 eviction policy 没讨论
写进去的 user id/post id 应该设置time to live 来保证这个cache真的在反应用户在看这个post
（不知道面试官是不是想要这个 但是至少楼主觉得 当用 cache的时候 应该确保讨论 eviction policy）
- SD: design zoom
- 系统: Ticketmaster
- System Design 1: Online chess gaming platform + leaderboard
  - 需要返回当前整个游戏的top N player、当前用户的前后N个player
  - 返回当前用户朋友的top N player、当前用户朋友的前后N个player）
  - 多用户同时在在线play会有时时的排行榜，且要有game service来判断输赢计算比分，主要是可以在对方允许的情况下撤销上一步，问这部分怎么实现，要怎么记录
- Design a Netflix： 
  - 主要部分是 High Level workflow 和 bottleneck 还有 resilience, 以及 potential cost saving，并没有很detail的implementation.
- design Spotify
- design dropbox. 全程drive对话，自己说哪里是bottleneck，如何解决，面试官的问题都回答上了，后期dive in主要着重于how to chunk large file in detail, get updated file list 的时候如何提高performance，get updated file list的时候有什么edge case吗，如何解决。
- 设计关键词搜索服务
- SD: youtube (no recommendation)
- design post privacy setting 设计post的隐私设置
- 设计手机测试实验室。要求大概是submit的code change 会出发对应的test cases， 然后test cases 需要在手机集群上跑。需要support adhoc 的 test cases 和 daily schedule 的 integration test cases。
price tracker，看的是油管system design fight club的视频
- 设计hotel booking system。hotel得confirm预定，得pull availability等等 proximity
  - 偏重如何搜索范围内的目标
  - 如何用KD-tree、R-tree来搜索
  - 以及如何减少延迟和提升可用性的优化
  - 另外讨论了不同数据库在不同情况下的使用与取舍
  - metrics monitoring
    - 面的时候比较注重考察metric collecting的那部分
    - metrics是engagement metric like tapping， 然后问了 fetching/querying (showing in UI dash-board)

