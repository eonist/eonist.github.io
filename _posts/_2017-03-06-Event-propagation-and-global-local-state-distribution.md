Explains how one could do **"upstream-event-propagation"** and **"downstream-state-distribution"**:<!--more--> 
Explains how one could do **"upstream-event-propagation"** and **"downstream-state-distribution"**:

<img width="680" alt="img" src="https://rawgit.com/stylekit/img/master/event_and_state_diagram.svg">

**In Element we don't distribute state**. State is derived by asking up hierarchy about the entire "hierarchy-state" and then calculating final state for the descendants.  We still have to distribute the state call to call the render() recursively to each descendant. But we don't pass an externalState down hierarchy. "The-passing-of-externalState-down-hierarchy-example" described in the diagram above is to enable state change in AppKit since AppKit doesn't do the whole CSS thing that Element does and can't look up hierarchy to derive its design.

In element we would rather do:
```css
/*All Buttons that descend from window will now use the white fill and grey line when window is set to inactive*/
Window :inactive Button{
   fill:white;
   line:grey;
}
```

And thats all the code thats needed to add an inactive mode for all Buttons in Window. 

If we need more granularity: We do: 

```css
Window :inactive TitleBar Button{
   ...
}
```

Now only buttons inside**TitleBar** inside **Window** gets this inactive look. 
And other buttons render in a default style.


How is all this related to MOSBY? 🤔

There needs to be a simple way to communicate up and down stream between Presenters. I presume there can be multiple levels of presenters in MOSBY? One at app level and one a TitleBar level. These need to be able to get event calls from deep down in the UI hierarchy. And then react accordingly. 


Still not convinced about traveling up and down hierarchy?

Well, When RX broadcasts its state change to all listeners it's the same amount of calls as a recursive call on hierarchy. The difference being that RX attaches every point it traveled through to get to its destination. Which is a good idea actually because then you can assert on its inverse hierarchy. If the RX event came from App then do this. If RX event came from window then do that. etc. 🤔

I guess this starts to look more and more like RX actually. Personal RX enlightenment 💥 

What if we just Distributed an ExternalState that collects its path from its origin until its farthest descendant. inverse hierarchy. Similar to how Event works but in reverse and also storing all path "checkpoints". 

Just an idea. that would give us power to do something RX like: 

```swift
let state + ExternalState.path.reduce{$0 == TitleBarPresenter}.wheaterState+ ExternalState.path.reduce{$0 == AppPresenter}.locationState + internalState
render()
```

And then there is reduction to limit the scope the event affects. Not all UI needs every state change at the app level etc. 

Hmm. I think I need to do more research into **RX and related concepts**. No need to re-invent the wheel if it already exists. I will do some examples that combine RX with the MOSBY presenter regime tomorrow  😁 

Ah. I think I get it now. We use RX for UI communication. and the presenter as just a layer that attach it self to the RX eventBus. but then why do view need a ref to presenter. Because RX only travels down stream?  