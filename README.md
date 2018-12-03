# Data Structure and Algorithm/leetcode/lintcode

This book is about learning data structure and algorithm. 

## Introduction

1. Part I is some brief introduction of basic data structures and algorithm, such as linked lists, stack, queues, trees, sorting.
2. Part II is the analysis and summary of programming problems, and most of the programming problems come from <https://leetcode.com/>, <http://www.lintcode.com/>, <http://www.geeksforgeeks.org/>, <http://hihocoder.com/>, <https://www.topcoder.com/>.
3. Part III is the appendix of resume and other supplements.

每日签到

回复刷题

[系统设计/OOD] 系統設計救星! 一天內手把手教你面試System design 只看楼主

垅头jyt0532收藏2016-11-3 10:18:55

前言：常看到有人問 system design需不需要準備 new grad會不會考system design 我想給地裡的戰友們建議 當然準備面試刷題是基本中的基本 但當你刷到一定程度後 刷題投資報酬率就開始低了 這時我真心建議可以花些時間讀system design 因為你花在這個的前10-20小時投資報酬率非常高！基本的觀念有了以後可以apply到所有的問題 面試之前不管HR跟你說有沒有system design你都不怕 廢話不多說 直接進入主題


我的所有資料來源都是地裡還有以下連結

https://www.evernote.com/shard/s576/sh/7e58b450-1abe-43a8-bf82-fbf07f1db13c/049802174415b418a2e65f75b744ab72

https://github.com/checkcheckzz/system-design-interview

https://www.interviewbit.com/courses/system-design/


這篇文章主要是把所有手上資源照priority順序加上一些自己的整理 用簡單的方式介紹怎麼Ace system design 依照priority順序分成

明天面 下禮拜面 下個月面 跟明年面


=====明天要面system design=====


迷途書僮:誒jyt0532 我明天就要面試了 沒空讀那麼多你給的文章 該怎麼辦？

jyt0532: 很好 今天幸好你遇到我 我用最少的時間讓你知道明天面試的時候大方向怎麼走

Step1: 先問所有requirement, spec 這個系統需要提供什麼功能

Step2: Constrains: 問他我們需要處理多少traffic, 多少data, latency重不重要 A和C選哪個

Step3: 計算需要多少機器 要用什麼storage

Step4: Abstract design: 先畫出大架構！ 每個會出現的component都要畫出來 再看面試官希望你深入講哪個component

Step5: Scale: 讓你的system有fault tolerance, scale成大公司的系統架構

以下内容需要积分高于 188 您已经可以浏览


迷途書僮: 等等step2的A和C是什麼

jyt0532: CAP的A和C 如果你明天要面試你不知道CAP是什麼 那我的建議是你今天早點休息 明天才有精神 地球是很危險的

迷途書僮: 別這樣啦 我幫你加大米拜託給我一個最簡單最好的解釋

jyt0532: 既然你都這麼說了 今天幸好你遇到我 把這個看完你就通透了http://ksat.me/a-plain-english-introduction-to-cap-theorem/

迷途書僮: 喔原來是這個 那這4個step 你這樣講真的很抽象 可不可以舉個例子 我可以apply到所有system design的題目

jyt0532: 光說不練假把戲 我帶你手把手跑一次system design process


來個基本的 假設現在要design一個hash

Step1, 2: 問requirement

多少資料需要進cache? 30TB

expected QPS? 10M

eviction strategy? LRU

Access pattern? Write back(有空的話Write through, write around, write back都要知道什麼意思 利弊)

Latency重要嗎? cache的用途就是降低latency

C or A: A




Step3:

算一下你需要多大的machine多少台

https://gist.github.com/jboner/2841832 這裡面的數字要有點sense

假設我們現在要用72GB RAM 4 core的machine

那總共以儲存data來說 需要30TB/72GB = 420台

這樣的話每台的QPS = 10M/420 = 23000, 即使所有core都用了 每個core要處理6000QPS

代表說 1/6000 = 167us 搭配上面那個link可知道即使是ram sequentially read 1MB要250M 所以我們如果用這個size的machine 會無法負荷

改變主意 假設現在用16GB RAM  4core的machine

30TB/16GB = 1875台, QPS per CPU = 10M/1875/4 = 1400QPS = 700us per queries. 這個數字負擔小多了


看完上面的流程知道我們在幹嘛了吧? 先用data constrain算出要幾台機器 再用traffic constrain算看看這樣的配置合不合理

這樣做完你就知道你的system是需要猛的機器少台一點 還是差一點的機器多台一點


Step4: 畫出大架構

以下内容需要积分高于 188 您已经可以浏览


這時候就必須推薦https://www.youtube.com/watch?v=-W9F__D3oY4 這根本太精采

什麼你又沒時間 好啦別說我虧待你 有人把重點整理好給你了http://ninefu.github.io/blog/Harvard_CS75_Notes/

但最後那個畫圖的地方還是要看一下


Step5: 喇賽時間

這時候小system畫完了 如果要scale的話需要什麼東西 不外乎就是load balancer啦 DB就是可能要master-slave或是multi-master 這種東西

至於怎麼fault tolerance呢 常見的處理就是replication 就是一樣的資料存很多地方 假設有P個replication

因為每次寫和讀都寫進/讀出這P個地方非常花時間 那該怎麼辦呢

假設寫的時候 只要有W個replication confirm update我就return to user

假設讀的時候 只要有R個replication給我一個一樣的value, 我就return這個value給user

depends on design的use case(這就是為什麼use case很重要) 你要看read跟write哪一個operation可以承受高一些的latency

如果要求read很快 write可以慢一點沒關係 那就可以設R = 1, W = P, 反之可以設R = P, W = 1

總之 只要R+W > N 那這database就是strong consistent! 如果真的要求高速度的話就必須犧牲consistent 那R+W就會<P(weak consistent)

這些也不是一時半刻講得完的 有需要的話我再補充 打到這邊有點累了




=====下禮拜要面system design=====


從小公司到上億用戶公司的架構演進(非常建議讀)

http://highscalability.com/blog/2016/1/11/a-beginners-guide-to-scaling-to-11-million-users-on-amazons.html

InterviewBit:這是個非常好的互動式網站 他是一步一步漸進式的問你每個你在面試中該問的問題 帶你走過一遍system design interview的process 非常建議這裡面的八題都要寫過

https://www.interviewbit.com/courses/system-design/

Scalable Web Architecture and Distributed Systems

http://www.aosabook.org/en/distsys.html


=====下個月要面=====

把這裡的文章都K過你就比大多數candidate強很多了 除非你想進的事system and data infra那就是另外一段故事了

https://github.com/checkcheckzz/system-design-interview#intro



=====明年面試=====

明年才要面你現在就開始準備的話 基本上你是個非常自律的人 你就定時follow各公司engineer寫的文章就可以了 別忘了面之前一個月再回來看我這篇

https://github.com/checkcheckzz/system-design-interview#blog



其實有工作經驗的都知道 你很常需要去design一個新的project 而釐清use case這些事情是基本 連use case都沒問那面試官根本不會覺得你是個好的工程師 主要考察的是communication and problem solving, 給你一個開放性問題 你怎麼分析step by step, 你如何跟別人討論你的idea, 如何optimize你的system 往這個方向想就覺得其實system design真的沒什麼好怕的 這篇寫了快五個小時 請各位地友不吝賞個大米吧  這篇就不設權限了 希望造福大家 如果大米超過100我再稍微寫一下怎麼design twitter吧




补充内容 (2016-11-7 02:51):
筆誤 example是要打造一個cache不是打造一個hash

补充内容 (2016-11-12 04:37):
下一篇已經寫好了 可是不知道為什麼需要經過審核

补充内容 (2017-11-2 14:34):
更多優質內容可以到我的部落格: https://www.jyt0532.com/2017/03/27/system-design/

推荐paopaojeffrey2016-12-4 03:06:22

简体网站能用简体吗

推荐gerent91962017-3-20 05:30:17

《《《搭配上面那個link可知道即使是ram sequentially read 1MB要250M》》》，这句话根据link里面最后应该是250us吧。。。1M是250us，那对应10M就是2500us，感觉换成后面的16GB RAM 还是不够用啊。。。请问这里怎么回事？求这里详细计算过程

推荐悠然2018-1-21 02:16:49

引用: gerent9196 发表于 2017-3-19 16:30
《《《搭配上面那個link可知道即使是ram sequentially read 1MB要250M》》》，这句话根据link里面最后应该 ...


"假設我們現在要用72GB RAM 4 core的machine 那總共以儲存data來說 需要30TB/72GB = 420台 這樣的話每台的QPS = 10M/420 = 23000, 即使所有core都用了 每個core要處理6000QPS 代表說 1/6000 = 167us 搭配上面那個link可知道即使是ram sequentially read 1MB要250M 所以我們如果用這個size的machine 會無法負荷"

我觉得楼主的意思是：
6000 QPS = 6000 query / 1000000us =>1000000 us / 6000 query = 167us/query， 从内存中读取1M要250us，所以实际的内存读取速度（250us）比题目要求的（167us）慢，因而不符合要求。（但比较的前提是每次读取都是1M）

同理，"假設現在用16GB RAM 4core的machine 30TB/16GB = 1875台, QPS per CPU = 10M/1875/4 = 1400QPS = 700us per queries. 這個數字負擔小多了"

1400QPS => 1000000us / 1400query = 700us per queries，所以实际的内存读取速度（250us）比题目要求的（700us）快，因而符合要求。（但比较的前提是每次读取都是1M）

推荐nevermor2016-11-10 01:56:51

先mark，繁体看的疼…

推荐jyt05322016-11-7 02:50:35

筆誤 example是要打造一個cache不是打造一個hash

沙发依水斜阳2016-11-3 14:41:38

我竟然是沙发！感谢楼主！

板凳queenjuliana2016-11-3 18:32:00

支持一下～谢谢分享

地板AC4Fun2016-11-6 21:00:54

写的简直太好了，不过第一个印象笔记的链接好像挂了哦

上一页第1页第2页第3页第4页第5页第6页第7页第8页第9页第10页第11页第12页第13页第14页第15页第16页第17页第18页第19页第20页第21页第1页下一页

tqgynn , 退出

标准版 | 触屏版 | 电脑版 | 客户端

© Comsenz Inc.
Nine notes

Live long and prosper

 Home Archive AboutRSS

Notes for Harvard CS75 Web Development Lecture 9 Scalability by David Malan

Jan 16, 2016 | System Design |  Hits

Lecture 9 video

Web Hosts

Features:

Is IP address blocked in some countries/regions?SFTP vs. FTP. SFTP is secure and all the traffic is encrypted, which is important for user names and passwordsSome hosting companies may offer you some unbelievable features, like unlimited storage spac, at a very low price. It’s very likely that you and another hundreds users are sharing the same machine and contending for resources. This is because sometime people actually don’t need that many resources.Virtual private server. May still share one machine with other users, but you have your own copy of the operating system. Run multiple virtual machine on a physical machine. Only you and the system administrators have access to your filesIf you want more privacy, then probably you have to operate your own servers

AWS EC2

How to scaleVertical Scaling

Get more RAM, processors, disks,…, for one machine, but you will exhaust the financial resources/state of arts technology.

Horizontal Scaling

Plural number of machines, use multiple servers to build the topology.

Load Balancer

Need to distriubte inbound HTTP requests

Return the public IP address of the load balancer, and let the load balancer determin how to actually route data to the backend server (private address).

Implementation

Dedicated servers for gifs, jpegs, images, videos, etc for different host HTTP headerRound robin. Or, the load balancer can be a DNS setup which returns the IP address of server 1 when the first time someone asks for a url, then return the IP address of server 2 when the second time someone asks for the same url, then server 3, server 4,…, eventually wrapping up. Downside: one server may get a really computational heavy user;Based on the load on a serverHave a server specifically for storing sessions. But what if that machine breaks down. Lacks redundacy. Can add RAID (redundant array of independent disks), striping and redundancy

When we type the url in the browser and hit enter, the OS will send a packet to DNS server which will translate host names to IPs and vice versa. If we click a link on a website, there is a cache to store the IP address so the OS doesn’t have to send the same DNS request again. Both OS and browser have a cache. Time to Live (TTL) values associated with an answer from a DNS server, 5 minutes, 1 hour, or 1 day. Global load balancing…

If the backend is PHP based and the session in PHP is broken. And if you were on Server 1, then by chance you are sent to Server 2 by a round-robin, you might have to log in again. Or think about shopping cart.

Stick sessions (when you visit a website multiple times your session is somehow preserve even if there are multiple backend servers)

Cookies:
Can store the address of the server so the next the user visit the website, he goes to the same back-end server. Downside: the private IP of the back-end server may change; the private IP is now visible to the whole world

==>

Store a random number and let the load balancer remember which number belongs to which server

SoftwareELBHAProxyLVSHardwareBarracudaCiscoCitrixF5

PHP Acceleration
php.exe compiles php everytime but throws away the result. Some software can keep the result. Like .py vs. .pyc.

Caching

.html vs. MySQL database/XML (avoid regenerating) more performance vs. more space. But requires a lot of work when want to update/redesign the pageMySQL query cache: query_cache_type: 1memchached: store whatever you want in RAM (garbage collection: expire objects based on when they are put in)Replication:

Master-Slave

Master: the main database that you write/read data to/from.
Slave: anytime a query is executed on the
master that same query is copied down to one or more slaves and they do the exact same thing

Advantages:

If the master is down, promote one of the slaves and do some configuration. (redundacy)If there are a lot queries, you could just load balance across database serversFor read heavy websites, any select can go to all four databases, while any insert/update/delete has to go to server master

Mastter-Master

you could write to either server one or two and if you happen to write to server1 that query gets replicated on server2 and vice versa so now you could keep it simple

Load balancing + Replication

active + active pair of load balancers
active + passive pair of load balancers, passive promote itself when receives no more packets from the active one.
and send packets to each other

Partitioning
A-M cluster and O-Z cluster

High Availability
One load balancer, two master replicating each other

Summary1
2
3
4
5
6
7
8
Internet ==(TCP 443 80)==> 
two load balancers ==(TCP 80)==> 
web servers ==x==> 
two load balancers ==x (TCP 3306)==> 
two <==X==> master databases 


Fire wall on switch ports

Share

System DesignWeb

3D Array Size326.Power of Three

© Nine notes. Powered by Hexo. Theme by Cho.
Algorithms


https://www.hackerrank.com/domains

https://oj.leetcode.com/problemset/algorithms/ LeetCode至少要刷三遍，付费部分的题建议花点钱看一下，舍不得孩子套不着狼

http://lintcode.com/

http://www.ninechapter.com/solutions/

http://www.geeksforgeeks.org/about/interview-corner/

TopCoder Algorithm Tutorial: http://help.topcoder.com/data-science/competing-in-algorithm-challenges/algorithm-tutorials/ 其中几何算法的教程要仔细看，Google特爱出几何题

CC150: Cracking the Code Interview: http://www.valleytalk.org/wp-content/uploads/2012/10/CrackCode.pdf 


OO Design

http://blog.csdn.net/longyulu/article/details/9159589


System Design


Here are some articles about system design related topics.

How to Rock a Systems Design Interview

System Interview

Scalability for Dummies

Scalable Web Architecture and Distributed Systems

Numbers Everyone Should Know

Scalable System Design Patterns

Introduction to Architecting Systems for Scale

Transactions Across Datacenters

A Plain English Introduction to CAP Theorem

The CAP FAQ

Paxos Made Simple

Consistent Hashing

NOSQL Patterns

Scalability, Availability & Stability Patterns

Hot Questions and Reference:


There are some good references for each question. The references here are slides and articles.


Design a CDN network

Reference:

Globally Distributed Content Delivery.


Design a Google document system

Reference:

google-mobwrite

Differential Synchronization.


Design a random ID generation system

Reference:

Announcing Snowflake

snowflake.


Design a key-value database

Reference:

Introduction to Redis.


Design the Facebook news seed function

Reference:

What are best practices for building something like a News Feed?

What are the scaling issues to keep in mind while developing a social network feed?

Activity Feeds Architecture


Design the Facebook timeline function

Reference:

Building Timeline

Facebook Timeline.


Design a function to return the top k requests during past time interval

Reference:

Efficient Computation of Frequent and Top-k Elements in Data Streams

An Optimal Strategy for Monitoring Top-k Queries in Streaming Windows


Design an online multiplayer card game

Reference:

How to Create an Asynchronous Multiplayer Game

How to Create an Asynchronous Multiplayer Game Part 2: Saving the Game State to Online Database

How to Create an Asynchronous Multiplayer Game Part 3: Loading Games from the Database

How to Create an Asynchronous Multiplayer Game Part 4: Matchmaking

Real Time Multiplayer in HTML5


Design a graph search function

Reference:

Building out the infrastructure for Graph Search

Indexing and ranking in Graph Search

The natural language interface of Graph Search and Erlang at Facebook.


Design a picture sharing system

Reference:

Flickr Architecture

Instagram Architecture.


Design a search engine

Reference:

How would you implement Google Search?

Implementing Search Engines


Design a recommendition system

Reference:

Hulu’s Recommendation System

Recommender Systems


Design a tinyurl system

Reference:

System Design for Big Data-tinyurl

URL Shortener API.


Design a garbage collection system

Reference:

Baby's First Garbage Collector.


Design a scalable web crawling system

Reference:

Design and Implementation of a High-Performance Distributed Web Crawler


Design the Facebook chat function

Reference:

Erlang at Facebook

Facebook Chat


Design a trending topic system

Reference:

Implementing Real-Time Trending Topics With a Distributed Rolling Count Algorithm in Storm

Early detection of Twitter trends explained


Design a cache system

Reference:

Introduction to Memcached.





