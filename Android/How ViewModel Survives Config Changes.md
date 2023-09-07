1. ViewModels are instantiated using `ViewModelProvider`
2. `ViewModelProvider` takes `ViewModelStoreOwner` as a parameter
4. `ViewModelStoreOwner` is an interface which holds `ViewModelStore` ,
	1. 3 implementations of `ViewModelStoreOwner`:
		1. ComponentActivity
		2. Fragment
		3. NavBackStackEntry
5. `ViewModelStore` is  a class which stores a `HashMap<String, ViewModel>`
6. The implementation of `ViewModelStoreOwner#getViewModelStore`
```
@NonNull
@Override
public ViewModelStore getViewModelStore() {
    if (getApplication() == null) {
        throw new IllegalStateException("Your activity is not yet attached to the "
                + "Application instance. You can't request ViewModel before onCreate call.");
    }
    ensureViewModelStore();
    return mViewModelStore;
}
```

7. It's ensured that `mViewModelStore` is not null 
```
void ensureViewModelStore() {
    if (mViewModelStore == null) {
        NonConfigurationInstances nc =
                (NonConfigurationInstances) getLastNonConfigurationInstance();
        if (nc != null) {
            // Restore the ViewModelStore from NonConfigurationInstances
            mViewModelStore = nc.viewModelStore;
        }
        if (mViewModelStore == null) {
            mViewModelStore = new ViewModelStore();
        }
    }
}
```

8. `getLastNonConfigurationInstance` returns an instance of `NonConfigurationInstance` static class defined in the `ComponentActivity` which stores the viewModelStore.
```
static final class NonConfigurationInstances {
    Object custom;
    ViewModelStore viewModelStore;
}
``` 

```
public final Object onRetainNonConfigurationInstance() {
    // Maintain backward compatibility.
    Object custom = onRetainCustomNonConfigurationInstance();

    ViewModelStore viewModelStore = mViewModelStore;
    if (viewModelStore == null) {
        // No one called getViewModelStore(), so see if there was an existing
        // ViewModelStore from our last NonConfigurationInstance
        NonConfigurationInstances nc =
                (NonConfigurationInstances) getLastNonConfigurationInstance();
        if (nc != null) {
            viewModelStore = nc.viewModelStore;
        }
    }

    if (viewModelStore == null && custom == null) {
        return null;
    }

    NonConfigurationInstances nci = new NonConfigurationInstances();
    nci.custom = custom;
    nci.viewModelStore = viewModelStore;
    return nci;
}
```
10. `onRetainNonConfigurationInstance` method is called by the  `Activity#retainNonConfigurationInstances` method.
```
    NonConfigurationInstances retainNonConfigurationInstances() {
        Object activity = onRetainNonConfigurationInstance();
        ...

        NonConfigurationInstances nci = new NonConfigurationInstances();
        nci.activity = activity;
        nci.children = children;
        nci.fragments = fragments;
        nci.loaders = loaders;
        if (mVoiceInteractor != null) {
            mVoiceInteractor.retainInstance();
            nci.voiceInteractor = mVoiceInteractor;
        }
        return nci;
    }
```
11. Activity class also has one static inner class `NonConfigurationInstances` 
```
    static final class NonConfigurationInstances {
        Object activity;
        HashMap<String, Object> children;
        FragmentManagerNonConfig fragments;
        ArrayMap<String, LoaderManager> loaders;
        VoiceInteractor voiceInteractor;
    }
```
12. `retainNonConfigrurationInstances` method is called by `ActivityThread#performDestroyActivity`
	1. `ActivityThread` - This is a `singleton` class that manages the execution of the main thread in an application process, scheduling and executing activities, broadcasts, and other operations on it as the activity manager requests.
13. The `Activity#NonConfigurationInstances` object is stored in `ActivityClientRecord` class, this class is a data holder class for the real Activity instance.
14. `ActivityClientRecord` is kept in `ActivityThread` as a `ArrayMap<IBinder, ActivityClientRecord>`
15. So in short the flow is 
	1. ComponentActivity#NonConfigurationInstances 
	2. Activity#NonConfigurationInstances
	3. ActivityClientRecord
	4. ActivityThread
16. When an activity is restored, the `Activity#onAttach` method is called which has a `Activity#NonConfigurationInstances` as a parameter, this is retrieved from `ActivityClientRecord`, the `mLastNonConfigurationInstance` has a member called `activity` which is an instance of `ComponentActivity#NonConfigurationInstances` which finally contains our viewModeStore.

```
getLifecycle().addObserver(new LifecycleEventObserver() {
    @Override
    public void onStateChanged(@NonNull LifecycleOwner source,
        @NonNull Lifecycle.Event event) {
        if (event == Lifecycle.Event.ON_DESTROY) {
            ...
            // And clear the ViewModelStore
            if (!isChangingConfigurations()) {
                getViewModelStore().clear();
            }
        }
    }
});
```
