
problems in learning dense prediction tasks
- per-pixel labeling is too laborious and often too expensive
- tasks are too broad and diverse

building a universal few-shot learner for DP
- existing few-shot learners : domain-specific task 들에 대해서 inductive-bais를 사용하여 예측. --> task마다 따로 설정해야만 한다.
- universal few-shot learner를 위한 조건
	1. unified & task-agnostic function form
	2. flexible & data-efficient adaptation
- analogy-making via Visual Token Matching


Q. failure case가 뭐가 있는지?
A. optical flow 에 대해서 underfit이 생김. 아직은 문제를 정확히 파악을 못함. underfit 에 대해서 overfitting 되어 있음.