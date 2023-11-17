
# Removing Items from JavaScript Arrays

*Published on July 06, 2012*

## "Using" the `delete` operator

When you delete an array element using the `delete` operator, the array length is not affected. For example if you delete `arr[2]`, `arr[3]` is still `arr[3]` and `arr[2]` is `undefined`.

```language-javascript
arr = ['a', 'b', 'c', 'd']
// ["a", "b", "c", "d"]
delete arr[2]
// true
arr
// ["a", "b", undefined, "d"]
```

## Using the `splice` method

One technique for removing content is to use the `splice` method to rebuild the array (adding new elements while removing old elements).

```language-javascript
arr = ['a', 'b', 'c', 'd']
// ["a", "b", "c", "d"]
arr.splice(2, 1)
// ["c"]
arr
// ["a", "b", "d"]
```

## Extending the Array prototype

Another technique John Resig came up with extends the native prototype for the Array constructor by adding a `remove` method.

```language-javascript
Array.prototype.remove = function(from, to) {
  var rest = this.slice((to || from) + 1 || this.length);
  this.length = from < 0 ? this.length + from : from;
  return this.push.apply(this, rest);
};

// Remove the second item from the array
arr = ['a', 'b', 'c', 'd']
// arr = ['a', 'b', 'c', 'd']
arr.remove(2);
// 3
arr
// ["a", "b", "d"]

// Remove the second-to-last item from the array
arr = ['a', 'b', 'c', 'd']
// arr = ['a', 'b', 'c', 'd']
arr.remove(-2);
// 3
arr
// ["a", "b", "d"]

// Remove the second and third items from the array
arr = ['a', 'b', 'c', 'd']
// arr = ['a', 'b', 'c', 'd']
arr.remove(1,2);
// 2
arr
// ["a", "d"]

// Remove the last and second-to-last items from the array
arr = ['a', 'b', 'c', 'd']
// arr = ['a', 'b', 'c', 'd']
arr.remove(-2,-1);
// 2
arr
// ["a", "b"]
```

## jQuery's `grep` method

And finally, a jQuery technique that uses the `grep` method and removes an array item based on it's string value instead of index.

```language-javascript
arr = ['a', 'b', 'c', 'd']
// ["a", "b", "c", "d"]
arr = $.grep(arr, function(value) { return value != 'c'; });
// ["a", "b", "d"]
```
