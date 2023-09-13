
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

