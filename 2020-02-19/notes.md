## Relational Calculus

**Def**

A tuple in a relational calculus expression is of the form 

$\{e(t_1,\dots,t_n) | \land_{i=1}^{n} t_i\in R_i \land \varphi(t_1,\dots,t_n)\}$

where

$e(t_1,\dots, t_n)$ is of the form $t_i, ()1\leq i \leq n)$ or $t_i.A_j, \dots, t_{i_n}.A_{j_m}$

and $\varphi (t_1,\dots,t_n)$ is of the form defined by the following recursive definition

1. $t_i.A\ \texttt{op}\ t_j.B$ and $t_i.A\ \texttt{op constant}$ (operations)

2. If $\varphi_1$ and $\varphi_2$ are atomic formula, so are $\varphi_1\land \varphi_2$, $\varphi_1\lor \varphi_2$, $\varphi_1\implies \varphi_2$, $\neg\varphi_1$ (logical connectives)

3. If $\varphi(t)$ is an allowed formula, so are $(\forall t\in R)(\varphi(t))$ and $(\exists t\in R)(\varphi(t))$(quantifiers) ($\forall$ expression is true if all meet criteria, $\exists$ is true is there is at least one which meets criteria)

**Ex**

$\sigma_{\varphi(t)} (R) \equiv \{t | t\in R \land \varphi(t)\}$

**Ex**

$\pi_{A_1,\dots,A_m} \equiv \{(t.A_1,\dots,t.A_m) | t\in R \}$ (we need to construct the tuple we want to return on the fly)

where $A_1,\dots,A_m$ are a subset of the attributes of $R$

**Ex**

| $R_1$            | $R_2$            |
| ---------------- | ---------------- |
| $A_1,\dots, A_m$ | $B_1,\dots, B_n$ |

$R_1\times R_2 \equiv \{(t.A_1,\dots,t.A_m,t.B_1,\dots,t.B_n) | t_1\in R_1 \land t_2\in R_2\}$

**Ex**

| $R_1$                        | $R_2$                        |
| ---------------------------- | ---------------------------- |
| $A_1,\dots,A_i,B_1\dots,B_n$ | $B_1,\dots,B_n,C_1\dots,C_j$ |

Common attributes are the $B$s

$R_1\bowtie R_2 \equiv \{(t_1.A_1, \dots, t_1.A_i, t_1.B_1, \dots, t_1.B_n, t_2.C_1, \dots, t_2.C_j) | t_1\in R_1\land t_2\in R_2 \land_{i=1}^n t_1.B_i=t_2.B_i \}$

**Ex**

$R_1 \cup R_2 \equiv \{t | t\in R_1 \lor t\in R_2\}$

**Ex**

$R_1 \cap R_2 \equiv \{t | t\in R_1 \land t\in R_2\}$

**Ex**

$R_1 - R_2 \equiv \{t | t\in R_1 \land \neg (t\in R_2)\}$

or

$R_1 - R_2 \equiv \{t | t\in R_1 \land t\not\in R_2\}$

## Homework

Student named John who is enrolled in CS450

This is an existential query.

Must find examples.

We want to do a natural join between class, enrollment, and student

$(\pi_{ssn}\circ \sigma_{name='John'\land dcode='CS' \land cno=450})((class \bowtie enrollment) \bowtie student)$

What if he gave us the database? What would we need to find?

The tuple in the student table with name john

The tuple in enrollment with ssn same as john

Tuple in enrollment with class course id same as course id of name = cs450 in class table

Match ssn enrollment with John's

tuples: $e$ for enrollment, $s$ for student, $c$ for class

What are the matching conditions between these three tuples?

$$
\begin{aligned}
\{ (s.ssn) | &s\in student \land \\
& e \in enrollment \land \\
& c \in class \land \\
& e.class = c.class \land \\
& e.ssn = s.ssn \land \\
& s.name = 'John' \land \\
& c.dcode = 'CS' \land \\
& c.cno = 450 \}
\end{aligned}
$$

Finding all the prerequisites for courses (query C)

$T1$

$dcode, cno$ is a prerequisite for a course for our student, $ssn$

| $ssn$ | $dcode$ | $cno$ |
| ----- | ------- | ----- |
| 17    | 'CS'    | 330   |
|       |         |       |
|       |         |       |

$T2$

student, $ssn$, had taken $dcode, cno$

| $ssn$ | $dcode$ | $cno$ |
| ----- | ------- | ----- |
|       |         |       |
|       |         |       |
|       |         |       |

If we had these two tables, how would we find students who have satisfied all prerequisites?

What about the complement -- those that have not satisfied all prerequisites?

What if we have a tuple in $T1$ that does not appear in $T2$? They haven't taken that course.

Then we find students that have not satisfied all prerequisites by taking the set difference $\pi_{ssn}(T1-T2)$.

Take all students, $\pi_{ssn}(Students) - \pi_{ssn}(T1-T2)$

Query D is very similar

How do we do these things in the calculus?

Students who did satisfy all prerequisites for all courses they're enrolled in

Students come from student table

$A\subseteq B$. Set $B$ is set of all courses a student has taken. Set $A$ is set of all prerequisite courses that the student is currently enrolled in.

The containment of these is defined with existential logic.

$(\forall a)(a\in A \implies a \in B)$

$$
\begin{aligned}
\{ (s.ssn) | &s\in student \land \\
& (\forall co \in course)(A(co) \implies B(co) \text{course satisfies A must satisfy B}\\)
\}
\end{aligned}
$$

Must define those functions $A(co)$ et al.

$A(co) = $
