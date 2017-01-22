# Introduction

The lua callback functions of `onFilter` and `onGroup` allow, as arguments, the `fields` and `extensions` arguments, generally condensed to `f` and `e` respectively when calling the funtion. For every `f`, a certain number of terms are further defined.

Simpletask help assumes a working knowledge of the lexicon and terminology of Lua, and so, does not go beyond stating that `fields` and `extensions` are tables. This writeup aims to explore what exactly is meant by that and some of the other notations that (I felt) needed further explanation.

# Tables

The [Lua Help Manual](https://www.lua.org/manual/5.3/) defines a table as follows:

> The type _table_ implements associative arrays, that is, arrays that can be indexed not only with numbers, but with any Lua value except **nil** and NaN. (_Not a Number_ is a special value used to represent undefined or unrepresentable numerical results, such as 0/0.) Tables can be _heterogeneous_; that is, they can contain values of all types (except **nil**). Any key with value **nil** is not considered part of the table. Conversely, any key that is not part of a table has an associated value **nil**.
>
> Tables are the sole data-structuring mechanism in Lua; they can be used to represent ordinary arrays, sequences, symbol tables, sets, records, graphs, trees, etc. To represent records, Lua uses the field name as an index. The language supports this representation by providing a.name as syntactic sugar for a["name"].
>
> Like indices, the values of table fields can be of any type. In particular, because functions are first-class values, table fields can contain functions. Thus tables can also carry methods.

That is, unlike MATLAB, where an array can only have numerical indices, in lua, tables can have any index - string or number. A table can be thought of as a combination of a structure and a cell array from Matlab. So, for instance, these are all valid assignments:

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

Thus, to delete a value assigned to a key, simply assign it to `nil`. Thus, implementing this code:

```lua
A("a") = nil
```

will reduce the number of elements in table `A` to 4.

Also, if you wish to check whether or not a particular key is associated with a table, check whether or not it is equal to `nil`. For instance,

```lua
if A("b") == nil then
    -- code
end
```

will not enter the `if` loop and run the code block.

As also mentioned earlier, from the Lua Help manual,

> a.name is syntactic sugar for a["name"]

What this means is in the code above, if the index/key were a string, a different syntax could be used.

Thus,

```lua
A.a = 5         -- equivalent to A("a") = 5
A.b = 4         -- equivalent to A("b") = 4
A.1 = 3         -- equivalent to A("1") = 3

if A.c == nil then   -- equivalent to if A("c") == nil
```

Thus, unlike object oriented languages, `M.n` **_does not_** represent a variable or method `n` of object `M`.

Lastly, also note that nested tables are possible, that is, the value assigned to a key can be a table too. (We'll see how this is all relevant to Simpletask in the following section.)


# Understanding Simpletask Lua Callback Function Arguments and Parameters

Syntax:
- `onFilter (task, fields, extensions)`, eg. `function onFilter(t, f, e)`
- `onGroup (task, fields, extensions)`, eg. `function onGroup(t, f, e)`

### The `fields` argument

Every task in Simpletask has an associated table which is passed to the `onFilter` or `onGroup` functions as the argument `fields`. Thus, when you define `function onFilter(t, f, e)`, when each task is checked, the reference to this table (associated with the particular task) is assigned to `f`.

As the [Simpletask Lua Configuration Help File](https://github.com/mpcjanssen/simpletask-android/blob/master/src/main/assets/script.en.md#onfilter-task-fields-extensions---boolean) states, the parameter `fields` has further parameters such as `completed`, `completiondate`, `createdate`, `due`, etc.

What is not explicitly mentioned is that all of these parameters are *keys* for the table referenced now by `f` (for brevity, henceforth, this will be called table `f`). The values corresponding to these keys could be assigned their respective types, or be `nil`. Thus, if no threshold date has been set for a particular task, for example, the table field value addressed by the key `"thresholddate"` will be `nil`.

All the keys of table `f` barring `"lists"` and `"tags"` have single values. Thus, if you wish to check or return the values, you use:

```lua
return f.completed

-- or

if f.due == nil then
```

(Note that as described in the preceeding section, instead of `f.due`, etc., you could also use `f["due"]`, etc. without affecting the outcome of the function.)

In the case of keys `"lists"` and `"tags"`, the values are tables. Taking `lists`, for instance, `f.lists` returns a table where the keys are the lists of the task. And the values are `true`.

That is, for a task: `Test task @Test @@Understand`, `f.lists` is a table where:

```lua
f.lists["Test"] = true          -- The first "@" is supressed.
f.lists["@Understand"] = true
```

The first list can be acceessed even by `f.lists.Test`. But because the second list starts with a "@", `f.lists.@Understanding` throws an error, while the explicit key declaration `f.lists["@Understand"]` still works. And if a task does not belong to the "@Test" list, `f.lists.Test` will have a value `false`.

Similarly for tags. Thus, you can filter for tasks of a particular list (or tag) by using:

```lua
return f.lists["List_Name"]

-- or

if f.tags["Tag_Name"] == false then
```

This is why, in the help page, for a **tag** "@errands" (i.e. the task will have a term `+@errands` in it), the following example is given:

```lua
-- Show all tasks with the @errands tag:

function onFilter(t,f,e)
   return f.tags["@errands"]
end
```

The `extensions` argument (`e` in the function calls) is also a table with some custom Simpletask specific terms (such as due and threshold date strings, and recurrence behaviour) as values, and corresponding custom keys referencing these values. If, instead of checking for the existence of individual tags or lists, you wish to list or concatenate all the lists or tags, the code for doing so can be found [here](https://github.com/Vijayanth-Reddy-K/Simpletask-Modifications/blob/Add-Code/Individual%20Functions%20for%20Filtering%20and%20Grouping.md#understanding-tables-generated-by-simpletask).

Note that if you wish to filter by lists or tags and also perform some additional functions, you can combine lua scripts with the in-built filters. (For more details, see [Tips](Tips.md))

# Appendix

Finally, as an aside, one last additional bit of code (which I do not use, but if needed, here it is). Based on the discussion above and in the [Individual Functions for Filtering and Grouping](Individual Functions for Filtering and Grouping.md) file, if you wish to access the string due or threshold dates (for example, as 2016-12-22 and not as seconds, which is what `f.due` or `f.threshold` return), there are two ways to do so. (Substutute `threshold` instead of `due` for threshold date in the code below.)

```lua
-- Return due date as string. Implemented here in the onGroup function. Can easily be modified for onFilter as well.

function onGroup(t, f, e)
    return e.due
end

-- OR

function onGroup(t, f, e)
    return os.date("%Y-%m-%d", f.due)
end
```

(The second method can be used to return creation date as well, if desired. Also, the date (and time) can be formatted as needed with the second method. More details about formatting the time string can be found [here](https://www.lua.org/pil/22.1.html).)
