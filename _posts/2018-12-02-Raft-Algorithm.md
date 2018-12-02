---
title: "Raft Algorithm - 1"
layout: post
comments: true
excerpt: "합의 알고리즘에 대해서 알아본다."
tags:
  - algorithm
date: 2018-12-02
---

분산 시스템을 구축할 때, 모든 노드가 독립적으로 돌아가는 시스템이 아니라면 공유된 상태를 합의하기 위한 방법이 필요하다. 이런 분산 환경에서 상태를 공유하는 알고리즘을 `합의 알고리즘(consensus algorithm)`이라고 한다.

Raft 알고리즘은 이러한 합의 알고리즘 중 하나이다.

## `Leader Election`

- **쿠버네티스에서 3개의 노드가 있다고 가정한다.(노드는 각각 `Follower`, `Candidate`, `Leader`의 역할을 맡을 수 있다.)** 

![raft]({{site.url}}/assets/img/raft1.png){: .img-center width="300px"}

> 이 중에 1개는 `Follower` 상태이다. 또 하나는 `Candidate` 상태, 마지막 하나는 `Leader`이다. 모든 노드는 `Follwer` 상태로 시작된다.

- **만약 follwer 들이 leader를 듣지 못햇다면, 이들은 candidate가 될 수 있다.**

- **cardidate는 다른 노드들에게 투표를 요청한다. 그려면 요청받은 노드들은 투표를 하게된다.**

- **과반수 이상의 노드들에게 투표를 받게된다면 cardidate는 Leader 노드로 변한다.**


<br>

## `Log Replication`

- **모든 시스템 변화에 관한 내용은 leader를 거치게 된다.**

- **변화들은 노드 로그에 기록된다.(아직 node's value가 업데이트 되지 않았기 때문에, 로그 엔트리는 커밋되지 않은 상태다.)**

![raft]({{site.url}}/assets/img/raft2.png){: .img-center width="300px"}

- **leader 노드가 follower 노드들에게 첫번째 복사본을 커밋한다.**

![raft]({{site.url}}/assets/img/raft3.png){: .img-center width="300px"}

- **leader 노드는 과반수의 노드들이 복사된 엔트리를 작성할 때가지 기다린다.**

- **엔트리가 이제 leader 노드에 커밋되고 노드의 상태는 "5"이다.**

- **leader노드는 follwer 노드들에게 엔트리가 커밋되었다는 신호를 보낸다.**

- **클러스터는 시스템 상태가 "5"라는 것을 합의했다.**