
![[Pasted image 20230616145749.png]]
onAttach
onCreate
onCreateView
onViewCreated
onStart
onResume
onPause
onStop
onDestroyView
onDestroy
onDetach


## Fragment Transaction
1. Add - Adds fragments
2. Remove - Removes fragments
3. Replace - Replaces all fragments and add the particular fragment

#### Activity and Fragment lifecycle correlation when added statically
![[Pasted image 20230913095754.png]]

#### Activity and Fragment lifecycle correlation when added dynamically

![[Pasted image 20230913100048.png]]

## Backstack

1. Backstack is a stack of fragment transactions and not fragments itself.
2. Add, Remove, Replace all are considered as transactions.
3. A transaction is only recorded once we call commit().
4. This is also considered as one transaction.
```
supportFragmentManager.beginTransaction()  
    .add(R.id.container, Fragment1())  
    .add(R.id.container, Fragment2())  
    .add(R.id.container, Fragment3())  
    .commit()
```
5. popBackstack() is used to reverse the transaction.

With backstack - backstack entry count is increased with every transaction
	1. Add - Adds fragments on top of one another.
	2. Remove - Removes the particular fragment.
	3. Replace - Removes all the previous fragment(s) and replaces them with a particular fragment.
	4. Back button doesn't reverse the transaction.

Without backstack - backstack entry count is not increased with every transaction
	1. Add - Adds fragments on top of one another.
	2. Remove - Removes the particular fragment.
	3. Replace - Removes all the previous fragment(s) and replaces them with a particular fragment.
	4. Back button reverses the transaction.

