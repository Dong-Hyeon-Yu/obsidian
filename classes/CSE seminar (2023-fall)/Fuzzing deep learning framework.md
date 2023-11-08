
Challenges for AI framework testing 
- fuzz driver are avilable only for 19 C++ APIs
- Generates inputs in format of bytes arrays, which are hard to muatte to generate valid tensor objects.

### DocTor
1. Document-guided contraints generation'
	- API document -> (normalize) -> frequent subtrees -> construct rules
2. Test inputs generation
	- constraints -> (1) conforming inputs, (2) violating inputs 
3. API execution 
4. Result

### Limitation
1. low accurcy w/o manually annotated API document -> Prompt Construction (using large language models, StarCoder)
2. hard to generate valid test inputs. (numeric intensive constraints)
	- low code coverage in c++ backend because of symbolic execution
	- idea: inferring branch conditions via program synthesis (execution tree)

질문.
Q. document가 잘못된 경우도 고려하고 있는 것인지?
A. 그런 부분도 다 찾아내고 있다. (ex, violating input이 실제로 violate 되지 않거나..)



