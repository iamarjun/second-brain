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

