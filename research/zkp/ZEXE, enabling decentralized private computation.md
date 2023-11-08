#IeeeSP 

### Contributions
- privacy & succinctness (zksnark 사용)
- shared execution environment 
	- extensibility, isolation, inter-process communication
- decentralized private computation (DPC)
	- record units (Zcash programming model) 
	- a record is bound to (private key, payload, predicates)
		- tx updates must satisfy the predicates in the record.
		- ex) zkproof P1 (r1, r2) = 1
		  -> 항상 참이 되는 naive 한 predicate를 만들면 안됨.
		  -> DPC forces to use birth predicate & death predicate as one of the predicates in a record
	- records nano-kernel (RNK)

q. shared execution environment?

q. what are the birth predicate and death predicate in detail?

q. is the all record commitments (muckle tree) is append-only? how exactly does it work? (p12) // what type of data is stored in the record serial number table?

q. (14p) where is the local data for perdicate stored? How can make the different visibility on each field?

q. (20p) why is the execution time long? which tx is executing? which operations?

q. (21p) only the commitment of local data is stored in storage. how do the other people find
the local data of specific tx?



