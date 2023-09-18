1. **Primary Key** - Table can have one and only one primary key, this uniquely identifies a record in the table. Generally it's the ID column.
2. **Foreign Key** - Defines relationship between tables, a foreign key generally references the primary key of the other table.
3. **Candidate Key** - Potential primary key in a table.
4. **Composite Key** - Combination of multiple columns in a table.

### Normalisation
https://www.youtube.com/watch?v=U3L4NYNwb6k
1. 1NF - 1st Normal Form 
	1. Every row must have a unique primary key.
	2. There can be no nested tables.
2. 2NF - 2nd Normal Form
	1. All the rules from 1NF.
	2. All columns that are not part of the primary key must only be dependent on the entire primary key.
3. 3NF - 3rd Normal Form
	1. All the rules from 1NF and 2NF.
	2. All the columns not in the primary key are dependent only on the primary key.
4. BCNF - Boyce-Codd Normal Form
	1. All the rules from 1NF, 2NF and 3NF
	2. A column that is a part of the primary key may not depend on the column that is not part of the primary key.
### Joins
https://stackoverflow.com/questions/53949197/isnt-sql-a-left-join-b-just-a
![[Pasted image 20230918090731.png]]
### Indexing 
An index is an in-memory structure that ensures that queries we run on a database are _performant_, that is to say, they run _quickly_. Most database indexes are just [binary trees](https://en.wikipedia.org/wiki/Binary_tree)! The binary tree can be stored in [ram](https://en.wikipedia.org/wiki/Random-access_memory) as well as on [disk](https://en.wikipedia.org/wiki/Computer_data_storage), and it makes it easy to lookup the location of an entire row.
This ensures that the lookup are in `O(log(n)`.

`PRIMARY KEY` columns are indexed by default, ensuring you can look up a row by its `id` very quickly. However, if you have other columns that you want to be able to do quick lookups on, you'll need to _index_ them.

While indexes make specific kinds of lookups much faster, they also add performance overhead - they can slow down a database in other ways. Think about it, if you index every column, you could have hundreds of binary trees in memory! That needlessly bloats the memory usage of your database. It also means that each time you _insert_ a record, that record needs to be added to _many_ trees - slowing down your insert speed.

The rule of thumb is simple:

> Add an index to columns you know you'll be doing frequent lookups on. Leave everything else un-indexed. You can always add indexes later.

#### CREATE INDEX

```sql
CREATE INDEX index_name on table_name (column_name);
```
