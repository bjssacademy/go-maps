# Maps

 In Go, a map is a built-in data structure that provides an unordered collection of *key-value pairs*. 

 ## Key-value Pairs

Key-value pairs are a **fundamental** concept in computer science and data structures. They represent associations between two pieces of data: a key and a value. The key is used to uniquely identify the value within the collection. Key-value pairs are commonly used in various data structures and algorithms for efficient storage and retrieval of information.

**Key**
- The key is a unique identifier within the collection.
- It is used to retrieve the associated value.
- Keys must be unique within the collection, meaning that no two key-value pairs can have the same key.

**Value**
- The value is the data associated with the key.
- It can be of any data type, including integers, strings, booleans, arrays, maps, or even other key-value pairs.
- Values are accessed or manipulated using their corresponding keys.

Keys can be integers or strings, and the value can be either a simple data type (string, int, bool) or more complex (a struct, object, slice etc).

|  Key |  Value | 
|---|---|
| name  | "Elmer"  | 
| 1  | "ABC123"  | 
| Character  | {name: "Elmer Fudd", age: 42}  |  

### Examples
- In a dictionary, words are the *keys*, and their definitions are the *values*.
- In a database, primary keys are used to uniquely identify records, and the remaining data fields constitute the values.
- In a programming language, variables can be thought of as keys, and their assigned values are the corresponding values.

### Usage
- For Data Retrieval: Keys are used to retrieve associated values from the collection.
- For Data Storage: Key-value pairs provide a convenient way to organize and store data (and allow for *relationships* in tables).
- For Indexing: In databases and search algorithms, keys are often used to create indexes for faster lookup operations.
- For Configuration: Key-value pairs are commonly used in configuration files to represent settings and their corresponding values (YAML or JSON for example).

## Maps 

Each key in a map *must* be unique, and the keys and values can be of any type that supports equality comparison. Maps are also known as *hash tables* or *dictionaries* in other programming languages.

There are three ways to declare a map in Go:

```go
// Using make function
m := make(map[string]int)

// Using map literal syntax
m := map[string]int{}

// Using map literal syntax with direct assignment
m := map[string]int{"a": 1, "b": 2, "c": 3}

````

### Adding and Accessing Elements in a Map

You can add elements to a map by assigning a value to a key:

```go
m["d"] = 4
```
You can access the value associated with a key using square brackets `[]` syntax:

```go
value := m["b"] // value is 2
```
> The value is 2 when we lookup the key in the map that is "b". 

### Deleting From a Map

You can delete an element from a map using the delete function:

```go
delete(m, "c")
```

The above code deletes from the map `m` the kay-value pair where the key is "c". This is why we need to have unique keys!

### Iterating over a Map

You can iterate over a map using a `for` loop:

```go
for key, value := range m {
    fmt.Println("Key:", key, "Value:", value)
}
```

### Checking a Key Exists

```go
value, exists := m["e"]
if exists {
    fmt.Println("Value found:", value)
} else {
    fmt.Println("Key not found")
}
```

You may have noticed that when we get a value from a map using a key that exists, there's only one return type, but when we want to check for existence there are two return types. Why is this?

```go
value := m["b"]
```

When you use the single-value assignment syntax, Go returns the value associated with the key "b" in the map m. If the key "b" exists in the map, value will be assigned the corresponding value. If the key "b" does not exist in the map, value will be assigned the zero value of its type (e.g., 0 for integers, "" for strings, nil for pointers, etc.). This syntax is straightforward and often used when you're confident that the key exists in the map.

```go
value, exists := m["e"]
```

When you use the two-value assignment syntax, Go returns two values: the value associated with the key "e" in the map m, and a boolean indicating whether the key exists in the map (true if it does, false otherwise). This syntax allows you to check whether a key exists in the map before attempting to access its value. If the key "e" exists in the map, value will be assigned the corresponding value, and exists will be true. If the key "e" does not exist in the map, value will be assigned the zero value of its type, and exists will be false.

## Sorting a Map by Key or Value
Maps are *unordered collections* in Go so there is no provision for sorting a map using the sort package. 

From Go 1.12, you can print the map and it’ll be sorted by keys:

```go
func main() {
	m := map[string]int{
		"sally": 45,
		"john":  5,
		"marcy": 15,
		"duff":  10,
		"tom":   20,
	}

	fmt.Println(m) // map[duff:10 john:5 marcy:15 sally:45 tom:20]
}
```

If you intend to do more than just printing the map or if you want to sort by value instead, then you'll need to do some work yourself. You can iterate over maps in Go but, unlike slices, the order of iteration is not guaranteed. 

The idiomatic way to iterate over a map in Go is by using the `for..range` loop construct. Instead of receiving index/value pairs as with slices, you’ll get key/value pairs with maps. The iteration order is intentionally randomised when you use this technique.

```go
func main() {
	m := map[string]int{
		"a": 1,
		"b": 2,
		"c": 3,
	}

	for key, value := range m {
		fmt.Println(key, "->", value)
	}
}
```
```
output
b -> 2
c -> 3
a -> 1
```

To guarantee a specific iteration order, you need to create some additional data structures and use a method from the sort package to control the order of iteration.

```go
func main() {
	m := map[string]int{
		"a": 1,
		"b": 2,
		"c": 3,
	}

	// Create a struct for each map key-value pair
	// Make sure the types match accordingly
	type KeyValue struct {
		Key   string
		Value int
	}

	// create an empty slice of key-value pairs
	s := make([]KeyValue, 0, len(m))
	// append all map keys-value pairs to the slice
	for k, v := range m {
		s = append(s, KeyValue{k, v})
	}
}
```

A `KeyValue` struct is used to hold the values for each map key-value pair. This struct is placed in a slice whose initial capacity is set to the length of the map in question. Now that we have a slice of `KeyValue` structs, we can use the `SortStable()` method from the sort package to sort the slice in any way we please. Once the slice is sorted accordingly, you can iterate over it to get the desired order.

Here’s an example that sorts the slice of structs by value so that the slice is ordered in such a way that the greater numbers come before the lesser ones:

```go
func main() {
	m := map[string]int{
		"a": 1,
		"b": 2,
		"c": 3,
	}

	// Create a struct for each map key-value pair
	// Make sure the types match accordingly
	type KeyValue struct {
		Key   string
		Value int
	}

	// create an empty slice of key-value pairs
	s := make([]KeyValue, 0, len(m))
	// append all map keys-value pairs to the slice
	for k, v := range m {
		s = append(s, KeyValue{k, v})
	}

	// sort the slice of key-value pairs by value in descending order
	sort.SliceStable(s, func(i, j int) bool {
		return s[i].Value > s[j].Value
	})

	// iterate over the slice to get the desired order
	for _, v := range s {
		fmt.Println(v.Key, "->", v.Value)
	}
}
```
```
output
c -> 3
b -> 2
a -> 1
```

If you want to sort the map by key, you only need to change the function argument to `SliceStable()` so it sorts by the struct’s Key instead of Value.

## Tasks

### Part 1

Create a map that has a GUID for a key, and holds a struct of type Person for the value. There should be 5 people stored in the map, with unique DateOfBirth years.

The Person struct should contain the following fields:
- Name (string)
- DateOfBirth (time.Time)
- Email (string)

Read more about the built-in package `time` and how Go uses dates and times: https://www.digitalocean.com/community/tutorials/how-to-use-dates-and-times-in-go

You can use this package to create GUIDs: https://github.com/google/uuid

Here is an example of how to use it:

```go
package main

import (
    "fmt"
    "strings"
    "github.com/google/uuid"
)

func main() {
    uuidWithHyphen := uuid.New()
    fmt.Println(uuidWithHyphen)
    uuid := strings.Replace(uuidWithHyphen.String(), "-", "", -1)
    fmt.Println(uuid)
}
```

### Part 2

Sort the map and return the oldest person's current age.