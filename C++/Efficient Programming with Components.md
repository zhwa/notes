#### 1.1 

	std::set and std::unordered_set

[std::set](https://en.cppreference.com/w/cpp/container/set) is completely different with [Python set](\\web). In Python, set is based on hash table, while std::set is based on [red-black tree](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree).

Everywhere the standard library uses the Compare requirements, uniqueness is determined by using the equivalence relation: `!comp(a, b) && !comp(b, a)`. It's not efficient. 

[std::unordered_set]() is based on hash table. There's a [comparison on SO](https://stackoverflow.com/questions/1349734/why-would-anyone-use-set-instead-of-unordered-set):

* Unordered sets have to pay for their O(1) average access time in a few ways:

* set uses less memory than unordered_set to store the same number of elements.
For a small number of elements, lookups in a set might be faster than lookups in an unordered_set.

* Even though many operations are faster in the average case for unordered_set, they are often guaranteed to have better worst case complexities for set (for example insert).

* That set sorts the elements is useful if you want to access them in order.

* You can lexicographically compare different sets with <, <=, > and >=. unordered_sets are not required to support these operations.



-----

```
	#include <iostream>
	#include <set>
	#include <algorithm>
```

