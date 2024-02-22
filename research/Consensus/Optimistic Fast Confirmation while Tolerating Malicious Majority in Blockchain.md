
s&p '23

## Motivation
- $f_{max}$ 의 theoretical value와 practical value 사이의 gap 이 존재함.
- block confirmation latency를 줄이는 것이 목표
- $f_{max}$ 가 작을 때는 optimistic 하게, 클 때는 normal 하게 작동

## Challenge
 - 실제 동작 환경에서는 $f$의 값을 알 수 없음.
	 - optimistic track: generalized byzantine broadcast protocol --> $b\_cert$
	 - normal track: standard byzantine broadcast protocol

## FlintBB
- 모든 노드가 fixed time 동안 OptimisticTrack()을 수행
	- 한 노드가 결과를 리턴하면, 모든 노드는 OptimisticTrack()을 중단
- 어떤 노드도 OptimisticTrack()을 리턴하지 못하면 NormalTrack()을 수행.

q. hotstuff가 전체 커미티에서 하나만 돌아가는 건가? 아니면 노드 별로 병렬적으로 동작?
q. 