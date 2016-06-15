---
layout: post
section-type: post
title: Redux Middleware
category: tech
tags: [ 'redux' ]
---

**Writing a Redux Middleware to allow shorthand actions**


Redux requires all actions be plain old javascript objects, for example:

```
store.dispatch({"type": "SET_GOLD", "quantity": "22", "user": "Elizabeth" })
```

Additionally, reducer functions must be pure functions, so any additional functionality,  
such as logging or ajax calls, must come from middleware. Fortunately this is not a difficult process.

To illustrate this we can look at a simple Redux Middleware to allow shorthand actions.  

Many actions are what we can call _straight-through dispatches_.
By which I mean the only attribute of the action is the type. For example:  

```
store.dispatch({"type": "INCREMENT_GOLD"})

store.dispatch({"type": "DECREMENT_GOLD"})
```


the straight-through dispatch is enough information for the reducer to change the state accordingly

Wouldn't it be nice if, for straight-through dispatches, we could call it this way instead?

```
store.dispatch("INCREMENT_GOLD")

store.dispatch("DECREMENT_GOLD")
```


Since the reducer only accepts plain old javascript objects, the transformation must occur in middleware

redux middleware follows this curried syntax:  

```
const myMiddleware  = store => next => action => {     
    //my awesome middleware    
    return next(action);      
};
```


So to make our middleware that accepts shorthand dispatches we have:

```
const allowShorthandDispatches  = store => next => action => {    
    if(typeof action === "string"){  
        action = {  
            type: action  
        }  
    }  
    return next(action);    
};
```


Finally we have to register our middleware with the store, and for that we need the  
applyMiddleware method from Redux.

```
const {createStore, applyMiddleware} = Redux;  


const store = createStore(myApp,  
    applyMiddleware(allowShorthandDispatches));  
```

and that's it! We can now use our shorthand syntax for straight-through dispatches.


