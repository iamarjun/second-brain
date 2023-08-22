1. ViewModels are instantiated using `ViewModelProvider`
2. `ViewModelProvider` takes `ViewModelStoreOwner` as a parameter
4. `ViewModelStoreOwner` is an interface which holds `ViewModelStore` ,
	1. 3 implementations of `ViewModelStoreOwner`:
		1. ComponentActivity
		2. Fragment
		3. NavBackStackEntry
5. `ViewModelStore` is  a class which stores a `HashMap<String, ViewModel>`
6. The implementation of `ViewModelStoreOwner#getViewModelStore`