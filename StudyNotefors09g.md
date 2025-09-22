# Google TL system design
SOT: https://space.bilibili.com/15120021/?spm_id_from=333.788.upinfo.detail.click
## design tiktok
### requirement
**Functional**
- upload video
- watch video

**non-functional**
- scalability
- availablity
- video latency
- fault tolance

**assumption**
- 1B DAU
- watch 100/day
- upload 1/day
- avg 1 min video -> 10MB
- length of video 15s - 3min

**Basic for everything**
![Fig tiktok](pic/Screenshot%202025-09-05%20at%209.56.39 PM.png)

**Scale for function**
![Fig tiktok](pic/Screenshot%202025-09-05%20at%209.59.41 PM.png)

**To scalale up**
- more data partition
- more functional partition
- more copy

**Add metaData for video**
![Fig tiktok](pic/Screenshot%202025-09-05%20at%2010.02.53 PM.png)

**Deep dive for metaData**
- SQL vs NonSql
  - Sql
    - ralation, bunnessis is already there
    - need to handle data sharding
  - non sql
    - data sharding is handled already
    - cheap
    - no business searching
- whatever is in use

**Deep dive for storage**
- Blob vs FS
  - FS
    - Pro:
      - hard to lost the files
      - a lot of chunk for data
      - clasic 3 numnber of replica
    - Con:
      - it's slow
      - a lot of space
  - Blob
    - Pro:
      - a lot of small files

### Deep dive for upload

- Simple solution
  - Just upload as one file
    - Pro: simple
    - Con: 
      - risk of upload 1 big file
      - too late to detect the invalid video
        - copy right
      - expose the storage
- Updated solution
  - temp raw space
![Fig tiktok](pic/Screenshot%202025-09-05%20at%2010.16.42 PM.png)

1min -> 10Mb
60s * 30 fps * (720 * 480 * 3 * 8 bytes ) = 1800 m/s

**Upload video alg**
- video compress
  - split video to small chunk
    - 10Mb to 10 * 1Mb
    - syntax, split the video by moving bits
  - in mobile env, the connection is not stable
    - split vide to small chunk helps failure tolance
    - paralle upload

**video processing**
- add a message queue to process the status of the upload

![Fig tiktok](pic/Screenshot%202025-09-05%20at%2010.25.01 PM.png)

- add a worker queue to process the message queue
- Adjust the worker number depends on the api call

![Fig tiktok](pic/Screenshot%202025-09-05%20at%2010.26.08 PM.png)

**video encoding for woker**

- encoding the video for different device
  - the video get uploaded is different fromt the video get played 
  - the video for play changes depends on
    - quality of the connection
    - power of the device

![Fig tiktok](pic/Screenshot%202025-09-05%20at%2010.31.22 PM.png)

**State machine**
Start -> ready -> process -> done
           |         |
           - ---> retry
### Deep dive for upload


**Basic**
1. view api query metadata
2. view api query blob storage

**Hotspot**
- for popular video
  - Add cdn
    - ![Fig tiktok](pic/Screenshot%202025-09-05%20at%2010.37.38 PM.png)
    - More cost
  - Move popular video to CDN
    - ![Fig tiktok](pic/Screenshot%202025-09-05%20at%2010.39.34 PM.png)
    - more cost
**Stream protocal**
- apple's protocal
- different device have different protocal

**Show off**
- Use machine learning to improve what video to view feed
![Fig tiktok](pic/Screenshot%202025-09-05%20at%2010.42.17 PM.png)
- localization 
  - different data center

## design yelp / point of interest

Indexing or retrival problem
- modeling

Caching problem
- for query

Same:
1. store a lot of data
2. return the data quickly
3. High concurrency
4. low requirement for write data

Different:
- core for caching is hit & missing ratio

### requirement
**Functional**
- search nearby location
- watch view shop details
- owner add/update/delete shop

**non-functional**
- low latency
- freshness -daily/hourly
- scalable
- FT

**assumption**
- 1B Monthly AU
- 500M DAU
- 200M shops

### Technical

#### Storage
- SQL vs NonSql vs in memory
  - Sql
    - ralation, bunnessis is already there
    - need to handle data sharding
  - non sql
    - data sharding is handled already
    - cheap
    - no business searching
- whatever is in use

**Data model**
- id: int
- owner id: int
- location: double
- address: string
- city: string
- state: string
- contry: string
- zip: long
- description: string: 5kb
- picture/video as url: string
-> 10kb

200M * 10kb = 2TB -> not worthy for in memory

**Basic for everything**
![Fig yelp](pic/Screenshot%202025-09-05%20at%2011.09.53 PM.png)

**Calculation**
- search
  - 500M * 5/day = 2500M/100k sec = 25k QPS
  - peak 100k QPS
  - 20 shards
  - auto scalaing 
- Business owner 
  - 200M * 1 upd - 10ate/month = 200M/(30*100k) = 70 qps
  - no need to worries, can handle on single machine

Conclusion: focus on search

#### Deep dive on search**
- master + slave
- database sharding
  - which id to shard
  - some maintaince cost

API:
![Fig yelp](pic/Screenshot%202025-09-06%20at%204.05.52 AM.png)

**solutions for sql**
There are 2 paras
1. write a query for the whole table 
2. 联合索引？
  - 最左匹配，无法优化
3. geo hash
   1. 2d to 1d -> 字母+数字
   2. stored as data in data base
4. quatar tree
   1. stored as structure in RAM
   2. how to fit into memory
   3. tree structure store info on leaf
   4. what to store
      1. business id
      2. lat/long
      3. short desc
      4. < 50 byte
      5. (200M/100(per node)) * (100*50B) -> 10G
      6. can be store in ram
   5. hard to update the tree
      1. what if the new node is on the boarder
   6. relax the refresh rate
      1. daily or hourly, not much cost increase
   7. ![Fig yelp](pic/Screenshot%202025-09-06%20at%204.22.43 AM.png)
      1. add seed for fault tolance, on serice crash, build the tree from seed
   8. break down the quad tree from global to deployment (west/east/centra us). it's too much by city.
      1. more cost
      2. helps on FT
      3. reduce the size of tree
5. Improve the search with cache
   1. ![Fig yelp](pic/Screenshot%202025-09-06%20at%204.24.59 AM.png)
   2. the more cache is closer to the front end, the better the performance, but the cost is higher

## Design job schduler 

### requirement 
1. internal system
2. mvp? 
   1. scope
3. type of job
   1. 常驻
   2. 临时
   3. can be all type

**functional**
from user point of view
- user submit task (short term scrip or long term task)
- visulazation/monitor
- advanced feature
  - add future task
  - DAG (job dependency, directed acyclie graph)

**non functional**
1. laytency 
   1. submit takes less than 10s
   2. dashboard  fresh per 60s
2. scalablilty
3. relaibility

**job data model**
1. repo (code/config/exec)
2. metadata
   1. id
   2. owner id
   3. binary path
   4. in/output path
   5. created time
   6. status
      1. ready -> waiting -> exec -> success
                      |        |
                      retry-- fail -> abort      
   7. retry_count

**Basic**
![Fig job sch](pic/Screenshot%202025-09-06%20at%205.16.02 AM.png)

**Improved**
- add cache
- add queue
![Fig job sch](pic/Screenshot%202025-09-06%20at%205.17.58 AM.png)

- dashboard qps
  - 10k user * 100 job/user/day * 10 view = 100 qps
  - Peak = 500 qps

- submission qps 
  - 10k user * 100 job/user/day = 10 qps

**Deep dive for database**
- single point faiure
- double master for reliability

No sql vs sql?
- defer decision, not a blocker
- data model -> foreign key
- query pattern -> do you need union table
- consistency, do I need strong consistency or just eventual consistency
- 开闭原则
  - 好的架构会增加未作出决策的数量

**Deep dive for decoupule**

- message queue 削峰填谷
  - queue might crash
  - no data in queue, can we use one data system
  - message queue as data store
    - kafeka data base
  - data base as message queue
    - higher accecptance
    - google spanner
- fire and forget
  - not safe
  - worker might be dead
- informer - worker
  - check the status of worker 
  - manages the worker
  - rpc
- model
  - pull
  - push
  - hybrid
    - side kick to monitor the work and report the status to informer
    - Add cost the worker
      - might not have enough resource
      - reduce the frequence or time
- save resource
  - on runtime
    - move the vm to container
      - container expose more stuff
    - lightern the vm
  - IO 密集
  - machine 密集
  - 混合部署
    - both IO and machine 密集
    - reallocate resource
  - recycle resource
    - worker might request more resource
    - long term vs temp task
    - cpu is 可压缩资源

**more feature**

- Cron service
  - priority queue to manages the task
  - timed task
  - tasks have prioirty
- DAG service
  - manages the depencency
  - might be able to move to informer
    - might make informer too complicated
    - if split, the system is more extendable


![Fig job sch](pic/Screenshot%202025-09-06%20at%207.35.17 AM.png)


## desigh file system

### requirements
#### functional 
- upload/download files
- sync files across devices
- handle conflicts
#### non functional
- low latency -> 20s
- availibility >> consistency
- reliablity
- scalablilty
  - large file

### basic design
![Fig file system](pic/Screenshot%202025-09-06%20at%207.43.21 AM.png)

**metadata**
- file id
- owner id
- media type
- created timestamp
- modified timestamp
- permission
- access url
- status
- version 
**metadata storage**

sql vs no sql

- dau: 100M user -> dau 20%
- QPS = 20M * 10 file/day * 200M request / 100k sec = 2k qps
- peak qps: 10k
- both sql and nosql can handle
- no need for strong consistency, just eventual consistency, so use nosql

### deep dive

**reduce upload path**
- split file to 1Mb
  - chunk structure
    - chunk_id
    - file_id
    - status
    - url
    - md5
    - version
    - create timestamp
    - ...
- compress the file
  - txt can reduce a lot space
  - not too much for audio and video
- support paralla upload
- pre-signature 
  - verify the file
  - upload the file from client to blob directly
  - save the upload from file management tot blob storage
- introduce alg to verify file consistency
- don't trust the client info

![Fig file system](pic/Screenshot%202025-09-06%20at%207.59.10 AM.png)

**update**
- fully sync vs delta sync
  - as chunk is used, just use delta sync
  - append
  - update chunk  
- delete
  - mark file with new status delete but don't delete right away
    - delete it later
- Pull the version data then sync if delta
  - pull is data consuming
  - long pulling, more expensive
  - web socket
    - doesn't work for old browser
- push model
  - sse
  - save time 
  - use http
  - adaptbility is even lower

**conflict**
offline update
- use chunk version
- keep both 
- first writer win
  - use version number which is on top of timestamp
- merge

**revert**
Add log sequcnec

- full sync to avoid missing info
- increase complexity
- moneytization 

![Fig file system](pic/Screenshot%202025-09-06%20at%2012.33.25 PM.png)

## ads event aggregator

large number of event
- 50M ad_id
- 1B click event = 1B / 100k sec = 10k/sec
- CTS  = # click / impression 
- 1:100
- 100B impression = 1M qps
###  requirement

**functional**
- aggregate metrics of given ad_id in last k minus
- top k ad_id in last m min
- filitering 
- 2yr history data

**non functional**
- scalalbilty 
- hight thouguput
- high qps
- data correctness
- FT
- latency < 15s

### basic

#### event structure

- ad_id
- timestamp
- event_type = {click, imp, ...}
- ip
- user_id
- country
- ...

10B <...< 100B
request = 0.1k

0.1kB * 100ksec = 1M qps
peak 3M qps

storage = 100B * o.1kb * 100k sec = 10 Tb
1M = 300Tb

### structure

![Fig ads event aggregate](pic/Screenshot%202025-09-06%20at%201.43.45 PM.png)

**Storage**
1. RDB -> non, too much data
2. noSql
   1. cassandra? 
   2. 3M/15K qps/sec = 200node
   3. hotspot
3. in memory kv store (redis)
   1. 100k-200k/sec = 15-30 node
   2. ram
      1. might lost, so need storage
4. message queue
   1. more latency
   2. buffer
   3. kaffka
   4. hotspot

**real time processing**
- batch processing
  - map reduce
- mini batch
  - sprak streaming
- streaming
  - flint
    - snapshot
  - high cost
  - low latency
  - input might be faster than processing 
- add checkpoint for ft
  - add resource
  - 50k event/sec, 3M/50k = 60 mode
- aggregate window by min
  - flash interval -> 10s to compete with 15s latency

#### data storage

**aggregated data**
- structure
  - ad_events
    - id
    - aggregated_minitus
    - click count
    - dim_key
    - meta_data
    - size -> 50k
  - 50M * 2 yr * 365 * 24 * 60 * 50k = 3TB
**How to read**
- OLAP
  - cold/hot
  - 120G for 1yr, save on ram or ssd or cache
  - prebuilt
    - by ad_id
    - by geo loc
- correctness
  - reconcile

![Fig file system](pic/Screenshot%202025-09-06%20at%203.05.00 PM.png)
  - kappa ??

Checing on through put & latency


## design chat app
- client + server side message deliver

### requirement

#### functional
- 1v1 message 
  - support offine message
  - online delivery
- goup chat ( < 200)
- present status

#### non functional
- scalableilty 
  - 1B
- low latency < 500ms
- high consistence
  - order
- FT$$

### basic

![Fig chat app](pic/Screenshot%202025-09-08%20at%206.40.39 PM.png)


## design chatGPT

### requirements
- throughtput
- latency

### basic
![Fig gpt](pic/Screenshot%202025-09-21%20at%204.14.33 PM.png)

- an inference service is the core component responsible for running a trained machine learning model to generate predictions or responses. It's where the "thinking" happens
- KV cache

#### work flow
1. input promote
1. tokenization 
1. embedding
1. transformer x N
   1. feed forward
   2. multi-head attentation
      1. self attentation
         1. QKV: query, key, value
         2. token by token
         3. auto regression
         4. Q * K * V calculaton -> new token
         5. https://medium.com/@joaolages/kv-caching-explained-276520203249
2. sampling
3. detokenization
4. output

### Calculation
Hardware A100 80GB ram

**llama2 - 7Billion**
- (4096 dim, 32 layer, 4096 context)
- model weight = 7B * 2 bytes = 14GB size
- kv cache / token = 2 * layer * dim * 2 bytes = 2 * 32 * 4096 * 2 = 512 kb/token
- total kv cache = 512 * 4098 = 2 GB
(- 80 - 14) / 2 = 33 sessoin


**llama3 8B**
- (32, 4096, 32 head 8 kv, 4096)
- model = 16GB
- kv cache - 128 kb/token
- total = 1gb
- 64 session

**For bigger model**




### deep dive
#### batching
   1. native batching
   2. continuous baching
      1. chunked prefill
   3. PD disagg
1. prefill: write cache
   1. time to first token
2. decode: read cache, append new

![Fig gpt](pic/Screenshot%202025-09-21%20at%204.54.10 PM.png)
- decouple prefill and decode node

3. reduce kv Cache
   1. quqatization
      1. FP16-FP8
   2. paged attentation
   3. prefix sharding
   4. compression
4. networking
   1. IB
   2. ROCEv2
5. streaming pipeline
  
#### parallelism
1. tensor parall
2. expert parall
   1. hot expert
3. context parall
4. pipleline parall
   1. for training





