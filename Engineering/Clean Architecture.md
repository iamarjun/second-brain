3 Layers:
1. Data Layer 
	1. Database, Remote API, Persistence, etc.
	2. Mappers to map database entities/DTOs to model classes.

2. Domain Layer 
	1. Intermediate layer, contains business logic in the form of usecases 
	2. Model classes mapped out of db entities/DTOs.
	
3. Presentation Layer
	1. UI Layer
	2. Contains Activity, Fragment, Composables.
	3. ViewModel - Call usecases and map it to state

Why use cases:
1. Prevents ViewModel from getting too big/bloated.
2. Follow Single Responsibility.


# Multi Module

 Pros:
 1. Clear Separation.
 2. Faster build times - gradle will only build affected/changed module.
 3. Support for instant apps and dynamic feature module
 4. Reusable.

Cons:
1. Initial setup is a pain.
2. Wrong setup can backfire build times.


## Good Modularisation Strategies

1. Layer based modularisation.
2. Feature based modularisation. 
3. Layer-Feature based modularisation.





