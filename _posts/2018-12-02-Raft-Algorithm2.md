---
title: "Raft Algorithm - 2"
layout: post
comments: true
excerpt: "Raft에 대해서 알아본다."
tags:
  - algorithm
date: 2018-12-02
---

Raft에서는 각 control electoins들 마다 두 개의 timeout 세팅이 존재한다.

### election timeout
- follower 가 candidate가 될 때까지 기다리는 시간의 총량을 의미한다.
- 이 시간은 150ms 에서 300ms 의 범위에서 랜덤하게 설정된다.
- election timeout 시간을 초과한 follower는 candidate가 된다. 그리고 자기 자신에게 투표하기 위한 election term을 시작한다.
![raft]({{site.url}}/assets/img/raft4.png){: .img-center width="300px"}
- 그리고다른 노드들에게 투표 요청 메시지를 전송한다.
![raft]({{site.url}}/assets/img/raft5.png){: .img-center width="300px"}
- 아직 투표를 받지 못한 노드들은 candidate 노드에게 투표하고 election timeout을 리셋한다.
- 과반수의 투표를 받으면 candidate 노드는 leader노드로 바뀐다.
- leader는 `Append Entries` 메시지를 follower 들에게 보낸다
- 이 메시지들은 **heartbeat timeout**에 지정된 간격으로 전송된다.
- 각각의 follower들은 받은 메시지에 대해서 응답한다.
이런 election term의 절차는 follower가 `heartbeats`를 받지 않고, candidate가 될때까지 계속한다.

### 두개의 노드가 같은 election timeout을 갖게 될 때
![raft]({{site.url}}/assets/img/raft6.png){: .img-center width="300px"}
- 같은 투표수를 받게 되므로 `election timeout`에 걸려 새로운 candidate 노드가 생긴다.