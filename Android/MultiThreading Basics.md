1. Main thread shouldn't be blocked for more than 5 sec. This will lead to ANR - Application Not Responding.
2. Main thread consists of -
	1. Message Queue
	2. Looper - Event Loop
	3. Handler - Responsible for putting tasks (Runnable) into the Message Queue. should have a reference to the Message Queue.
	5. Runnable - Tasks which the Message Queue executes.