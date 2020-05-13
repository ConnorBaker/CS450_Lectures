Today we're finishing the relational algebra and everything that we're doing for homework two.

Working on auto grader for relational algebra and calculus.

We'll have an expression in python invoking relational operators.

# Relational Algebra (Summary)

- Selection $\sigma$

- Projection $\pi$

- Cross-product $\times$
  
  - Pre-condition: they have no common attributes

- Set difference $-$

- Union $\cup$

- Rename $\rho$

- Since each operation returns a relation, operations can be composed (the Algebra is closed)

- Use of temporary relations is recommended

- To use set operators, we must apply them to tables which have identical schemas

- Any beyond these can be expressed using the above operators

# 17

Natural join

$S\bowtie R$

Result schema is similar to the cross-product, but only one copy of each field which appears in both relations

Natural join = cross product + selection on common fields + dropping duplicate fields

Schema of the result is the union of all attributes

Takes only values which agree on common attributes

# 19

Find the sailors who reserved all the red boats

More generally, two types of queries

- Existential
  
  - In order to satisfy the condition set forth by the query you need to find examples

- Built in universal quantification
  
  - All those that satisfy a condition

Simpler question: sailors who reserved all boats

$R$ (reservation)

| sid | bid | day      |
| --- | --- | -------- |
| 22  | 101 | 10/10/96 |
| 22  | 103 | 10/11/96 |
| 58  | 102 | 11/12/96 |
| 22  | 102 | 12/12/96 |

$B$ (boats)

| bid | color |
| --- | ----- |
| 101 | Red   |
| 102 | Green |
| 103 | Red   |

First of all, what are all boats? $\pi_{bid}(R)$

What about sailors that reserved a boat? $\pi_{sid}(R)$

AllBoats = $\pi_{bid}(B)$

ReservedBoats = $\pi_{bid}(R)$

ReservedBy

| sid | bid |
| --- | --- |
| 22  | 101 |
| 22  | 102 |
| 22  | 103 |
| 58  | 102 |

AllCombinations (arbitrarily create all possible reservations)

| sid | bid |
| --- | --- |
| 22  | 101 |
| 22  | 102 |
| 22  | 103 |
| 58  | 101 |
| 58  | 102 |
| 58  | 103 |

What if we have a tuple in AllCombinations but not in ReservedBy? That means that that sid did not reserve that bid.

Since 58 has tuples which appear in AllCombinations but not in ReservedBy, they cannot have reserved all boats.

If we subtract, we get exactly those tuples which did not reserve all boats

DidNotReserveAllBoats = AllCombinations - ReservedBy

ReservedAllBoats = $\pi_{sid}$(ReservedBy) - $\pi_{sid}$(DidNotReserveAllBoats)

---

Professor's way

ReservedPairs = $\pi_{sid,bid}(R)$

AllSailors = $\pi_{sid}(R)$

AllPairs = AllSailors $\times \pi_{bid}(B)$

Now we want to find tuples like the red boats, which appear in AllPairs but not in ReservedPairs

SailorsWhoDidNotReserveAllBoats = $\pi_{sid}$(AllPairs - ReservedPairs)

SailorsWhoDidReserveAllBoats = AllSailors - SailorsWhoDidNotReserveAllBoats

Now how do we modify this such that we get only the sailors that reserved red boats?

We're not interested in all reserved pairs any more

ReservedRedPairs = $(\pi_{bid}\circ\sigma_{color=red})(B)$

AllPairs $\supseteq$ ReservedPairs

# 21

Division

- Not supported as a primitive operator, but useful for expressing queries like:
  
  Find sailors who have reserved all red boats.

- Let $S1$ have 2 fields, $x$ and $y$; $S2$ have only field $y$
  
  - $S1/S2 = \{<x> | (\forall y \in R2)(\exists <x,y> \in R1)\}$
  
  - i.e., $S1/S2$ contains all $x$ tuples (sailors) such that for every $y$ tuple (redboat) in S2, there is an $(x,y)$ tuple in $S1$ (i.e, **x reserved y**).

In general, $x$ and y can be any lists of fields; $y$ is the list of fields in $S2$, and $x\cup y$ is the list of fields of $S1$.

Result of the division has the schema of the set of attributes in the left operand less the attributes of the second

Looking at the primary key and the other attributes of the left hand table -- if the attributes of the right hand table appear as the attributes of the left hand table, then that primary key is in the quotient

## 22

A/B1 all of them have p2 in pno so we take their sno

A/B2 s1 has a p2 and p4, s2 has a p2 but not a p4, s3 has a p2 but not a p4, and s4 has a p2 and a p4. Therefore, we take only s1 and s4.

The second operands schema must be a subset of the first operand

So we're looking at the set of other attributes related to a key in the leftside operand. As long as that set is a superset of the values of the attributes that we're dividing by, we include the primary key related to that set in the quotient.
