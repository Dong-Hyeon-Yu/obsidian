#IeeeSP 

### Contributions
- privacy & succinctness (zksnark 사용)
- shared execution environment 
	- extensibility, isolation, inter-process communication
- decentralized private computation (DPC)
	- record units (Zcash programming model) 
	- a record is bound to (private key, payload, predicates)
		- tx updates must satisfy the predicates in the record.
		- ex) zkproof P1 (record1, record2) = 1
		  -> 항상 참이 되는 naive 한 predicate를 만들면 안됨.
		  -> DPC forces to use birth predicate & death predicate as one of the predicates in a record
	- records nano-kernel (RNK)

q. shared execution environment?

q. utxo 사용하는 거 아닌가? foriegn value?

q. is the all record commitments (muckle tree) is append-only? how exactly does it work? (p12) // what type of data is stored in the record serial number table?

q. (20p) why is the execution time long? which tx is executing? which operations?




