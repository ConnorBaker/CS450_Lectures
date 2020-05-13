# 2020-03-04

6. Find students who have taken SOME CS courses

## Relational algebra

$(\pi_{ssn} \circ \sigma_{dcode = 'CS'}) (transcript)$

7. Find students who have taken ALL CS courses

## Relational Calculus

First question is "where do we take the answers from?"

Start with students.

$$
\begin{aligned}
\{ s.ssn | &s \in student \land \\
&(\forall c \in course)( \\
&\qquad c.title = "CS*" \implies (\exists t \in transcript)( \\
&\qquad\qquad c.dcode = t.dcode \land c.cno = t.cno \land s.ssn = t.ssn))\}
\end{aligned}
$$

## Relational Algebra



## SQL

Set $B$ is set of all courses taken by student
Set $A$ is set of all computer science courses
We should have $A \subseteq B$

```sql
SELECT s.ssn
FROM student s
WHERE (SELECT t.dcode t.cno
       FROM transcript t
       WHERE t.ssn = s.ssn)
      CONTAINS
      (SELECT c.dcode c.cno
       FROM course co
       WHERE co.dcode LIKE 'CS')
```

What if we don't have the `CONTAINS` operator?

$A - B = \emptyset$

8. Find students who have taken ONLY CS courses









5..  Find courses of max number of units

## Relational Calculus

What do we start with?

From the table courses.

$$
\begin{aligned}
\{ (c.dcode, c.cno) | &c \in course \land \\
&(\forall c' \in course)( \\
&\qquad c'.units < c.units)\}
\end{aligned}
$$




## Download Tools

- python 3
- zorba (query processing language for JSONiq)
- IDEs which allow handling of JSON