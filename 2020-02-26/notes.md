1. Find ssn of all students who are enrolled in the same class

Do we need to have a student table? No, we just want the ssn

Cartesian product of enrollment with itself, make sure that the ssn is unique, and if the class is the same, add it

$R1 = \rho(ssn\to ssn1, enrollment)$

$R2 = \rho(ssn\to ssn2, enrollment)$

$\pi_{ssn1, ssn2}(R1 \bowtie R2)$

RC

$\{ (ssn1 = e1.ssn, ssn2 = e2.ssn) | e1 \in enrollment \land e2 \in enrollment \land e1.class = e2.class \land e1.ssn \neq e2.ssn \}$

```sql
SELECT (ssn1 = e1.ssn, ssn2 = e2.ssn)
FROM enrollment e1, enrollment e2
WHERE e1.class = e2.class AND
```

Rename the fields so that they don't clash

1. Find courses that have at least one prereq
   
   $\pi_{dcode, cno} (prereq)$
   
   $\{(p.dcode, p.cno) | p \in prereq \}$
   
   ```sql
   SELECT p.dcode, p.cno
   FROM prereqs p
   ```

2. Find courses that have at least two prereq

$R1 = \rho ((pcode \to pcode 1, pno \to pno1), prereq)$

$(\pi_{dcode, cno} \circ \sigma_{\neg (pcode = pcode 1 \land pno = pno1)})(R1 \bowtie prereq)$





$\{  (p1.dcode, p1.cno)|  p1\in prereq \land p2 \in prereq \land p1.dcode = p2.dcode \land p1.cno = p2.cno \land \neg (p1.pcode = p2.pcode \land p1.pno = p2.pno)\}$

***Almost correct! But we have two free variables -- p1 and p2. The truth value should depend just on all free variables, so that we can compute truth or falsity from it. There should be only one free variable since our tuple only uses one variable. If you have a free variable which does not appear in the answer, it's wrong.***

$\{ (p1.dcode, p1.cno)| p1\in prereq \land (\exists p2 \in prereq)( \land p1.dcode = p2.dcode \land p1.cno = p2.cno \land \neg (p1.pcode = p2.pcode \land p1.pno = p2.pno))\}$

We can use existential quantification to introduce the second variable.

Or we could have used p2.cno instead of p1.cno (thus making the answer depend on all free variables).

```sql
SELECT (p1.dcode, p1.cno)
FROM prereq p1, prereq p2
WHERE p1.dcode = p2.dcode AND
      p1.cno = p2.cno AND
      NOT (p1.pcode = p2.pcode AND p1.pno = p2.pno)
```



1. Find courses that have exactly two prereqs

2. Find courses with the maximum number of units

3. Find students who have taken (must be in transcripts) some CS courses

4. Find students who have taken (must be in transcripts) ALL courses

$$
\{ s.ssn | \\
\text{A is set of all CS courses} \\
\text{B is set of all CS courses taken by s.ssn, our student} \\
\text{If B is superset of A, then our student took all CS courses} \\
\}
$$

1. Find students who have taken only CS courses










