### 2. IndexedForEach
Sometimes you need index in a for each
```swift
ForEach(Array(zip(data.indices, data)), id: \.0) { idx, item in
    content(idx, item)
}
```

### 1. selectable List

## Selectable list
SwiftuI's built in List selection functionality doesn't allow for much customization. Here is a way to do it manually:

1. Create a state that is an optional int
2. Create a list where id is the int. `List(Array(items.indecies), id: \.self)`
3. Create items: `{ i in let item = items[i] }`
3. Set the state on tap of the list item
4. Change the selection state by doing: `let isSelected = selectedIndex == i`
5. Change selected state in the item: `let row = Row(isSelected: isSelected)`

```swift
// code coming soon
```