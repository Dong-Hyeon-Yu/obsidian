
modern static analyzer need analysis heuristics to become practical.
automatically generate heuristics automatically instead manually.

framework: static analyzer + training data(programs) + atomic features

k-context sensitivity --> keep important K

Q. importance를 어떻게 결정? rank로 결정하나?
A. rank로 결정하지 않음. learned heuristic은 안중요한 정보를 포함하고 있기 때문에 그것을 기준으로 결정.