# 2020-03-02

1. Find pairs of students (ssn) who are enrolled in the same course

We'll use the enrollment table

## Relational Calculus

$$
\begin{aligned}
\{(ssn1 = e1.ssn, ssn2 = e2.ssn) | &e1 \in enrollment \land \\
&e2 \in enrollment \land \\
&e1.class = e2.class \land \\
&e1.ssn \neq e2.ssn \}
\end{aligned}
$$

## Relational Algebra

$$
R1 = \rho (ssn \to ssn1, enrollment)
$$

$$
R2 = \rho (ssn \to ssn2, enrollment)
$$

$$
(\pi_{ssn1, ssn2} \circ \sigma_{ssn1 \neq ss2})(R1 \bowtie R2)
$$

## SQL

```sql
SELECT ssn1 = e1.ssn, ssn2 = e2.ssn
FROM enrollment e1, enrollment e2
WHERE e1.class = e2.class AND
      e1.ssn != e2.ssn
```

2. Find courses that have more than one prereq

Just project on prereqs

3. Find courses that have more than two prereqs

## Relational Calculus

$$
\begin{aligned}
\{(p1.dcode, p2.cno) | &p1 \in prereq \land \\
&p2 \in prereq \land \\
&p1.dcode = p2.dcode \land \\
&p1.cno = p2.cno \land \\
&\neg(p1.pcode = p2.pcode \land p1.pno = p2.pno)\}
\end{aligned}
$$

## Relational Algebra

$R1 = \rho (pcode -> pcode2, pno -> pno1, prereq)$

$(\pi_{dcode, cno} \circ \sigma_{\neg (pcode = pcode1 \land pno = pno1)}) (prereqs \bowtie R1)$

## SQL

```sql
SELECT p1.dcode, p2.cno
FROM prereq p1, prereq p2
WHERE p1.dcode = p2.dcode AND
      p1.cno = p2.cno AND
      NOT(p1.pcode = p2.pcode AND p1.pno = p2.pno)
```

What if we modified this query so that we had exactly two prereqs?

We can say that it is not true that we can find another prerequisite.

We can for every prereq if the prereq tuple agrees with pcode pno it must disagree with both.

4. Find courses that have two prereqs

5. Find courses with max number of units/credits

6. Find students who have taken SOME CS courses

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

```sql


```

8. Find students who have taken ONLY CS courses
