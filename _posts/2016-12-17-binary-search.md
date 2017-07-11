Binary search implementation in swift<!--more--> 

### Example:
```swift
func binarySearch<T: Comparable>(_ a: [T], key: T, range: Range<Int>) -> Int? {
    if range.lowerBound >= range.upperBound {
        return nil// If we get here, then the search key is not present in the array.
    }else{
        let midIndex = range.lowerBound + (range.upperBound - range.lowerBound) / 2// Calculate where to split the array.
        if(a[midIndex] > key){// Is the search key in the left half?
            return binarySearch(a, key: key, range: range.lowerBound ..< midIndex)
        }else if(a[midIndex] < key ){// Is the search key in the right half?
            return binarySearch(a, key: key, range: midIndex + 1 ..< range.upperBound)
        }else {// If we get here, then we've found the search key!
            return midIndex
        }
    }
}
let numbers = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67]
binarySearch(numbers, key: 43, range: 0 ..< numbers.count)//output: 13
```

### Resource:
Lots of examples and links to fuzzy search:  
https://www.weheartswift.com/binary-search-applications/

Really good explanation for binary searching for tree data structures:   
https://www.raywenderlich.com/139821/swift-algorithm-club-swift-binary-search-tree-data-structure

Many binary search proposals in swift:   
http://stackoverflow.com/questions/31904396/swift-binary-search-for-standard-array

Concise swift implementation of binary search:  
https://github.com/raywenderlich/swift-algorithm-club/tree/master/Binary%20Search