
ZEXE with universal setup

DPC with universal setup w/o sacrificing transaction process speed

obstacles of universal SNARK
- pairing checks over polynomial commitment schemes
- multi-scalar multiplications (compared with non-universal SNARKS) / higher circuit complexity
- polynomial evaluations on non-native field
- Fiat-Shamir transform (require SNARK-friendly hash functions)

1.  lightweight verifier circuit from accumulation scheme
	- IVC incrementally verifiable computation
	- predicate를 감추기 위함..?
	- lightweight하다는 특성은 왜..?
2. Instance Merging
	- m death predicate (input) and n birth predicates (output)
	- each pair of death, birth 를 하나의 single larger predicate 로 합침.
	- k-mergeable SNARK scheme
3. proof batching
	- 