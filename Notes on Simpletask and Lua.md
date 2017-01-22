# Introduction

The lua callback functions of `onFilter` and `onGroup` allow, as arguments, the `fields` and `extensions` arguments, generally condensed to `f` and `e` respectively when writing the funtion. For every `f`, a certain number of terms are further defined.

Simpletask help assumes a working knowledge of the lexicon and terminology of Lua, and so, does not go beyond stating that `fields` and `extensions` are tables. This writeup aims to explore what exactly is meant by that and some of the other notations that (I felt) needed further explanation.

# Tables

The [Lua Help Manual](https://www.lua.org/manual/5.3/) defines a table as follows:

> The type _table_ implements associative arrays, that is, arrays that can be indexed not only with numbers, but with any Lua value except **nil** and NaN. (_Not a Number_ is a special value used to represent undefined or unrepresentable numerical results, such as 0/0.) Tables can be _heterogeneous_; that is, they can contain values of all types (except **nil**). Any key with value **nil** is not considered part of the table. Conversely, any key that is not part of a table has an associated value **nil**.
>
> Tables are the sole data-structuring mechanism in Lua; they can be used to represent ordinary arrays, sequences, symbol tables, sets, records, graphs, trees, etc. To represent records, Lua uses the field name as an index. The language supports this representation by providing a.name as syntactic sugar for a["name"].
>
> Like indices, the values of table fields can be of any type. In particular, because functions are first-class values, table fields can contain functions. Thus tables can also carry methods.

That is, unlike MATLAB, where an array `A` can only have numerical indices, in lua, arrays can have any index, string or number. So, for instance, these are all valid assignments:

```lua
A = {}        -- Empty Table

A(1) = 2      -- Value of A at index 1 (number) is 2.
A("1") = 5    -- Value of A at index 1 (string) is 5. (Note the index 1 is different from "1")
A(3) = "t"    -- Value of A at index 3 (number) is string "t".

a = "b"
A("a") = 6    -- Value of A at index "a" (string) is 6.
A(a) = 5      -- Value of A at index a (which has the value "b", hence the index is string "b") is 5.
A("b") = 8    -- Value of A at index "b" (string) is updated from 5 to 8.
```

At the end of this set of statements, A is a table with 5 elements at indices 1, "1", 3, "a" and "b". As shown in the above example, the indices need not be sequential.

The indices of the table are also called *keys* and each key has an associated _value_ (which it is given after an assignment statement like the ones above).

Thus, in table `A`, at the `key` "b", the `value` is 8.

As mentioned earlier,

> Any key with value **nil** is not considered part of the table. Conversely, any key that is not part of a table has an associated value **nil**.

Thus, to delete a value assigned to a key, simply assign it to **nil**. Thus, implementing this code:

```lua
A("a") = nil
```

will reduce the number of elements in table `A` to 4.

Also, if you wish to check whether or not a particular key is associated with a table, check whether or not it is equal to **nil**. For instance,

```lua
if A("b") == nil
```

will not enter the `if` loop.

As also mentioned earlier, from the Lua Help manual,

> a.name is syntactic sugar for a["name"]

What this means is in the code above, if the index/key were a string, a different syntax could be used.

Thus,

```lua
A.a = 5         -- equivalent to A("a") = 5
A.b = 4         -- equivalent to A("b") = 4
A.1 = 3         -- equivalent to A("1") = 3

if A.c == nil   -- equivalent to if A("c") == nil
```

Lastly, also note that the value assigned to a key can be a table too. (We'll see how this is all relevant to Simpletask in the following section.)


# Understanding Simpletask Lua Callback Function Arguments and Parameters

Syntax:
- `onFilter (task, fields, extensions)`, eg. `function onFilter(t, f, e)`
- `onGroup (task, fields, extensions)`, eg. `function onGroup(t, f, e)`

### The `fields` argument

Every task in Simpletask has an associated table which is passed to the `onFilter` or `onGroup` functions as the argument `fields`. Thus, when you define `function onFilter(t, f, e)`, when each task is checked, the reference to this table (associated with the particular task) is assigned to `f`.







https://github.com/Vijayanth-Reddy-K/Simpletask-Modifications/blob/Add-Code/Individual%20Functions%20for%20Filtering%20and%20Grouping.md#understanding-tables-generated-by-simpletask
