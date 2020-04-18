# msingi
Radix tree implementation in Golang

# Mutable Radix
Provides the `radix` package that implements a [radix tree](http://en.wikipedia.org/wiki/Radix_tree).
The package only provides a single `Tree` implementation, optimized for sparse nodes.

As a radix tree, it provides the following:
 * O(k) operations. In many cases, this can be faster than a hash table since
   the hash function is an O(k) operation, and hash tables have very poor cache locality.
 * Minimum / Maximum value lookups
 * Ordered iteration
 
 Example
 =======
 
 Below is a simple example of usage
 
 ```go
 // Create a tree
 r := radix.New()
 r.Insert("foo", 1)
 r.Insert("bar", 2)
 r.Insert("foobar", 2)
 
 // Find the longest prefix match
 m, _, _ := r.LongestPrefix("foozip")
 if m != "foo" {
     panic("should be foo")
 }
 ```
 
 # Immutable Radix
Provides the `iradix` package that implements an immutable [radix tree](http://en.wikipedia.org/wiki/Radix_tree).
The package only provides a single `Tree` implementation, optimized for sparse nodes.

As a radix tree, it provides the following:
 * O(k) operations. In many cases, this can be faster than a hash table since
   the hash function is an O(k) operation, and hash tables have very poor cache locality.
 * Minimum / Maximum value lookups
 * Ordered iteration

A tree supports using a transaction to batch multiple updates (insert, delete)
in a more efficient manner than performing each operation one at a time.

Example
=======

Below is a simple example of usage

```go
// Create a tree
r := iradix.New()
r, _, _ = r.Insert([]byte("foo"), 1)
r, _, _ = r.Insert([]byte("bar"), 2)
r, _, _ = r.Insert([]byte("foobar"), 2)

// Find the longest prefix match
m, _, _ := r.Root().LongestPrefix([]byte("foozip"))
if string(m) != "foo" {
    panic("should be foo")
}
```

Here is an example of performing a range scan of the keys.

```go
// Create a tree
r := iradix.New()
r, _, _ = r.Insert([]byte("001"), 1)
r, _, _ = r.Insert([]byte("002"), 2)
r, _, _ = r.Insert([]byte("005"), 5)
r, _, _ = r.Insert([]byte("010"), 10)
r, _, _ = r.Insert([]byte("100"), 10)

// Range scan over the keys that sort lexicographically between [003, 050)
it := r.Root().Iterator()
it.SeekLowerBound([]byte("003"))
for key, _, ok := it.Next(); ok; key, _, ok = it.Next() {
  if key >= "050" {
      break
  }
  fmt.Println(key)
}
// Output:
//  005
//  010
```