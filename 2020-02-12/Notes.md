## 2020-02-12

Module 4

# 1

We talked about how to design a database using ER and then the relational model and then how to translate it to a SQL database

Now we're moving to discuss data manipualtion/querying 

# 2

Relational algebra is one of the languages we'll use

query lang allow us to ask questions of the database and get answers

they re not designed to be a full programming language

wihtout the extensions of sql they are not turing complete

allows us to ask queries in a very compact way

# 3

three lang

rel alg

rel calc

sql

sql is defacto standard

understanding algebra is key to understanding SQL and query processing

sql is a declarative language

specify at a high level what you want

algebra is a functional language

relational algebra defines a number of operators which operates on tables

we can take a table and apply a select operator

can take two tables and join them

operands of every operator will be table

result will also be a table

with an algebra the operator must be closed on that set that the operator is defined on

Why is rel algebra an algebra? we have the set of all relational tables that one can define and the operators operate on table(s) and produce table

All query languages have this property

why is rel algebra important when compared to sql? rel sql contains rel algebra as a subset

secondly, though not discussed mcuh in thsi class, sql qeueries are compiled into an expression similar to rel aglebra

optimization deals with restructuring that generated expression

need to be able to take an english question and convert to sql

# 4

Algebra preliminaries

operands are relational instances

recall the pure relational algebra

Relational Model

How do we convert from ER to relational?

Database is a collection of tables

Every table has is a collection of rows (tuples), tuples are a collection of attributes

Def: Relational Schema

A relational schema R is a set of attributes and their associated domains $\{(A_1, D_1), \dots, (A_i, D_i)\}$

Def: Relational Tuple

A tuple $t$ over the schema $R = \{(A_1, D_1), \dots, (A_i, D_i)\}$ is defined as $t : \{A_1, \dots, A_n\} \to \cup_{i=1}^n D_i$ such that $t(A_i) \in D_i, \forall i = \{1, \dots, n\}$.

Ex:

$R = \{(age, \R), (name, String)\}$. Then $t(age) = 7.5, t(name) = "smith"$

Def: Relational Instance

A relational instance ($\equiv$ relation, table, stating a particular instance) r over the schema R is a set of relational tuples t over R.

Def: Relational Database Schema

A set of relational schemas $\{R_1, \dots, R_n\}$.

Def: Relational Database Instance

A relational database instance (\equiv database, database instance) over the relational database schema \{R_1, \dots, R_n\} is a set of relational database instances \{r_1, \dots, r_m\} over the corresponding schemas R_1, \dots, R_m.

Ex:

Student (R_1)

| ssn | name  | major |
| --- | ----- | ----- |
| 100 | Smith | CS    |

R_1 = \{(ssn, \Z^+), (name, STR), (major, STR)\}

Then t(ssn) = 100, t(name) = "Smith", t(major), "CS"

Department (R_2)

| did | dname | dchair |
| --- | ----- | ------ |
| 1   | CS    | ?      |

R_2 = \{(did, \Z^+), (dname, STR), (dchair, STR)\}

# 7

Projection ($\pi$)

deletes attributes that are not in the projection list

remaining columns are those that are specified by it

four tuples on the the table, only two in the result -- why? because the result is a set and we keep only unique attributes

# 8

selection operator ($\sigma$)

select rows that satisfy the selection condition (some boolean statement)

no duplicates again

schema of result identical to schema of (only) input relation

in sql in the select statement you indicate which attributes you want to select, which is different than $\sigma$ since we select whole tuples

# 9

We can combine the operators

$\pi_{sname,rating}(\sigma_{rating>8}(S2))$

this is why it's important that we have an algebra we want to be able to compose

# 10

who reserved boat 101? we need data from disparate tables

# 11

result schema has one field per field of S1 and R1 with field names inherited

can use the ($\rho$) operator to rename and then take the cartesian product ($\times$)

$CP = \rho(sid\to sid1, S1)\times \rho(sid\to sid2, R1)$

changes the schema of the table, but corresponds to the existing ones

after rename there are no common attributes

note that this is a cartesian product, not a sql like `JOIN`

we can do a boolean and for the two selections to create just a single selection

$(\pi_{sname}\circ\sigma_{sid1=sid2\land bid=101})(CP)$

# 13

From slide 10

Would like to figure out sailor with highest rating

We can create intermediate results

He rephrases the question since we're not getting it -- how do we get the sailors who do not have the highest rating?

Compute two tables

One with just sid and rating

$R1 = \pi_{sid,rating}(S1)$

| sid | rating |
| --- | ------ |
| 22  | 7      |
| 32  | 8      |
| 58  | 10     |

Create another table that takes all possible combinations

$R2 = \pi_{sid}(R1) \times \pi_{rating}(R1)$

| sid | rating |
| --- | ------ |
| 22  | 7      |
| 22  | 8      |
| 22  | 10     |
| 32  | 7      |
| 32  | 8      |
| 32  | 10     |
| 58  | 7      |
| 58  | 8      |
| 58  | 10     |

Consider the tuple $(31, 8)$ from $R2$. Is from $R1$, so must have rating 8.

What does the tuple $(31,10)$ from $R2$ mean? Not that they are the highest rating (since not in $R1$), but that somebody has the highest rating of 10.

sidsWithNotHighest

Simplify a bit, consider the following table ($R = \rho((rating\to actual\ rating), S1)\times \rho((rating \to random\ rating), \pi_{rating}(S1))$)

| sid | actual rating | random rating |
| --- | ------------- | ------------- |
| 22  | 7             | 7             |
| 22  | 7             | 8             |
| 22  | 7             | 10            |
| 32  | 8             | 7             |
| 32  | 8             | 8             |
| 32  | 8             | 10            |
| 58  | 10            | 7             |
| 58  | 10            | 8             |
| 58  | 10            | 10            |

from this, what can we tell about sid 32? He doesn't have the highest rating

If there is a rating in the third column which is higher than his rating, he does not have the highest

$NotHighestRating = \sigma_{actual < random}(R)$

| sid | actual rating | random rating |
| --- | ------------- | ------------- |
| 22  | 7             | 8             |
| 22  | 7             | 10            |
| 32  | 8             | 10            |

$AllSid = \pi_{sid}(S1)$

At this point he introduces the set difference ($-$).

$sidsWithHighest = AllSid - \pi_{sid}(NotHighestRating)$

# 14

Set difference ($-$), set union ($\cup$), set intersection ($\cap$).

**Must have the same schema. Otherwise they are not well-defined.**

# 17

Natural join ($\bowtie$)

No requirement that schema be disjoint

Cross product + selection on common fields (must agree on all common attributes ot match them) and dropping duplicate fields

Performance wise, we want to do projection and selection as early as possible
