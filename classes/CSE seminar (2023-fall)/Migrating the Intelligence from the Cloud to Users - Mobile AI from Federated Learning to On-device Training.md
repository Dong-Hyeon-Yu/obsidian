언제 컨텐츠에 몰입하는지를 구분하는 시스템을 제안.
- 심장 박동수 변화가 어떻게 변화하는지 (얼굴의 혈류량 변화를 감지, Vitamon)
- 개인화된 데이터를 가지고 개인화 모델을 만드는 것이 더 좋음.
  --> 모바일 기기에서 모델 학습이 필요

모바일 환경의 cpu/gpu로도 충분히 inference를 할 수 있지만,
training 과정에서는 요구되는 메모리 양이 많아서 힘들다.
훈련 과정에서의 메모리 사용량을 최적화하는 기존 연구의 가정들
1. powerful GPU --> low-power GPU
2. host-side memory --> unified memory
3. scheduling --> heterogeneous and unpredictable environment

Sage: memory-efficient on-device training
- 메모리 사용량을 20배 줄임.
- reducing graph complexity 그래디언트 사이즈 줄임
- optimizing graph evaluation algorithm 미니배치를 나눠서 작업.

AttFL: federated learning + attention learning

FedHM: heterogeneous 한 모바일 기기에서 다 다른 모델을 사용할 때, FL 돌리기.

Q. cpu가 재계산해야하는 부분이 많은데, 계산 시간의 오버헤드가 어떻게 되는지?
A. bare 모델을 모바일에서 돌리는 것은 너무 오래걸린다. general한 모델을 가지고 개인화된 모델을 만드는 것을 가정한다. 이런 경우에는 feasible 한 시간 내에 훈련이 가능하게 된다.


Q. byzantine behavior에 어떻게 대응?
Q. epoch를 어떻게 정의? epoch에 따른 baseline model 이 계속 update 될텐데, epoch가 synchronized 되지 않아도 괜찮나? 