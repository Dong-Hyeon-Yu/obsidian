
점점 공정 수준이 높아짐에 따라, 원자 수준에서의 정확한 simulation이 필요.
Density Functional Theory 전자 밀도 함수를 직접 활용: O(n^3) --> ML Potential를 이용하여 energy와 force를 근사. O(n)
- descriptor vs GNN
	- descriptor 표현력이 local한 영역에 국한됨.
	- GNN layer가 쌓임에 따라 Receptive field가 증가.
- Invariance vs Equivariance
	- invariance input이 어떤 변환으로 바뀌었을 때 output이 변하지 않음
	- equivariance output이 같은 변환으로 바뀜.

Q. 가속화 관련 연구를 하게 된 이유?
A. 살다보니 그렇게 됐다. 처음은 추천 시스템으로 시작했는데, 베이지안이 너무 느려서 빠르게 하는 방법을 연구를 주로 했다. 삼전에서도 인공신경망 경량화 및 가속화 관련 작업을 하게 되었다.