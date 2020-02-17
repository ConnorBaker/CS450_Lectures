# Chapter 3

# 3.1

**Relation**

a relation schema and a relation instance

**relation instance**

a table

**relation schema**

defines the column heads for the table

Let $R(f_1:D1, \dots, f_n:Dn)$ be a relation schema, and for each $f_i$, $1\leq i \leq n$ let $Dom_i$ be the set of values associated with the domain $Di$. An instance of $R$ that satisfies the domain constraints in the schema is a set of tuples with $n$ fields:

$$
\{<f_1:d_1,\dots,f_n:d_n> | d_1 \in Dom_1,\dots, d_n\in Dom_n\}
$$

the angle brackets identify the fields of a tuple.

Therefore an instance of $R$ is defined as a set of tuples.

**field**

column or attribute

**domain**

possible values of field

**Instance of relation**

Set of tuples, can be thought of as table

**tuples**

records, same number of fields as the relation schema

**relational database**

a collection of relations with distinct relation names

**relational database schema**

the collection of schemas for the relations in the database

**instance of relational database**

a collection of relation instances, on per relation schema in the database schema

Each relation instance must satisfy the domain constraints in its schema

*In sql, table denotes a relation*

```sql
UPDATE Students S
SET S.age = S.age + 1, S.gpa = S.gpa - 1
WHERE S.sid = 53688
```

## 3.2

DBMS provides integrity constraints

**legal instance**

a database instance which satisfies all the integrity constraints specified on the database schema

A DBMS enforces **integrity constraints** (IC) in that it permits only legal instances to be stored in the database

IC is specified when the DBA defines a database schema and enforced when a database application is run (the DBMS checks for violations and disallows changes to the data that violate the specified ICs)

**key constraints**

a statement that a certain minimal subset of the fields of a relation is a unique identifier for a tuple

**candidate key**

a set of fields that uniquely identifies a tuple according to a key constraint

*abbrv. to just key*

**superkey**

a set of fields that contains a key

*note that every relation is guaranteed to have a key. Since a relation is a set of tuples, the set of all fields is always a superkey*

**primary key**

selected candidate key that the database indexes

```sql
CREATE TABLE Students(
    sid CHAR(20),
    name CHAR(30),
    login CHAR(20),
    sid INTEGER,
    gpa REAL,
    UNIQUE (name, age),
    CONSTRAINT StudentsKey PRIMARY KEY (sid)
)
```

we can tell that (name, age) is a key and that sid is the primary key

**foreign key**

```sql
CREATE TABLE Enrolled(
    studid CHAR(20),
    cid CHAR(20),
    grade CHAR(10),
    PRIMARY KEY (studid, cid),
    FOREIGN KEY (studid) REFERENCES Students
)
```

Suppose we have that in addition to `Students` table above. We want only valid students to enroll in a course. Any value that appears in the `studid` field of an instance of the `Enrolled` relation should also appear in the `sid`field of some tuple in the `Students` relation. The `studid` field of `Enrolled` is called a **foreign key** and refers to `Students`.

The foreign key in the referencing relation (`Enrolled`) must match the primary key of the referenced relation (`Students`); that is, it must have the same number of columns and compatible data types, although the column names can be different.

Every `studid` value in `Enrolled` must also appear in `Students`. Incidentally, the primary key constraint for `Enrolled` states that a student has exactly one grade for each course he or she is enrolled in. If we want to record more than one grade per student per course, we should change the primary key constraint.

Primary key cannot have `null` field, though foreign key can

## 3.3

ICs are specified when a relation is created and enforced when a relation is modified. The impact of domain, `PRIMARY KEY`, and `UNIQUE` constraints is straightforward: if an insert, delete, or update command causes a violation, it is rejected.

SQL provides several different ways to handle foreign key violations.

1. What should we do if an `Enrolled` row is inserted, with a `studid` column value that does not appear in any row of the `Students` table?
   
   In this case, the INSERT command is rejected.

2. What should we do if a `Students` row is deleted?
   
   The options are
   
   - Delete all `Enrolled` rows that refer to the deleted `Students` row
   
   - Disallow the deletion of the `Students` row if an `Enrolled` row refers to it
   
   - Set the `stuid` column to the `sid` of some (existing) default student, for every `Enrolled` row that refers to the deleted `Students` row
   
   - For every `Enrolled` row that refers to it, set the `studid` column to `null`. In our example, this conflicts with the fact that `studid` is part of the primary key and cannot be set to null.

3. What should we do if the primary key value of a `Students` row is updated?
   
   The options here are similar to the previous options.

In the following table, when a `Students` row is deleted, all `Enrolled` rows that refer to it are to be deleted as well, but when the `sid` column of a `Students` row is modified, this update is to be rejected if an `Enrolled` row refers to the modified `Students` row

```sql
CREATE TABLE Enrolled(
    studid CHAR(20),
    cid CHAR(20),
    grade CHAR(10),
    PRIMARY KEY (studid, cid),
    FOREIGN KEY (studid) REFERENCES Students
                ON DELETE CASCADE
                ON UPDATE NO ACTION
)
```

The `NO ACTION` option is the default option, which means that the action is to be rejected. We could omit the `ON UPDATE` clause in our example and the behavior would not change.

The `CASCADE` keyword says that if a `Students` row is deleted, all `Enrolled` rows that refer to it are to be deleted as well.

If a `Students` row is deleted, we can switch the enrollment to a default student by using `ON DELETE SET DEFAULT`. The default student is specified as part of the definition of the `sid` field in `Enrolled`; if we wanted to set a default value for `studid`, for example, we could have done `studid CHAR(20) DEFAULT '53666'`.

null is supported in `DEFAULT` clause.

program that runs against a database is called a transaction

consider the following tables

```sql
CREATE TABLE Students(
    sid CHAR(20),
    name CHAR(20),
    login CHAR(20),
    age INTEGER,
    honors  CHAR(20) NOT NULL,
    gpa REAL,
    PRIMARY KEY (sid),
    FOREIGN KEY (honors) REFERENCES Courses (cid)
)
```

```sql
CREATE TABLE Courses(
    cid CHAR(20),
    cname CHAR(20),
    credits INTEGER,
    grader CHAR(20) NOT NULL,
    PRIMARY KEY (cid),
    FOREIGN KEY (grader) REFERENCES Students (sid)
)
```

When a students tuple is inserted, a check is made to see if the honors course is in the courses relation, and whenever a courses tuple is inserted, a check is made to see that the grade is in the students relation. How are we to insert the very first course or student tuple? One cannot be inserted without the other.

*The only way to accomplish this insertion is to defer the constraint checking that would normally be carried out at the end of an INSERT statement.*

SQL allows a constraint to be in `DEFERRED` or `IMMEDIATE` mode.

```sql
SET CONSTRAINT ConstraintFoo DEFERRED
```

A constraint in deferred mode is checked at commit time. In our example, the foreign key constraints on Boats and Sailors can both be declared to be in deferred mode. We can then insert a boat with a nonexistent sailor as the captain (**temporarily making the database inconsistent**), insert the sailor (restoring consistency), then commit and check that both constraints are satisfied.

## 3.4

**relational database query**

a query is a question about the data, and the answer consists of a new relation containing the result

```sql
SELECT S.name, E.cid
FROM Students S, Enrolled E
WHERE S.sid = E.studid AND E.grade = 'A'
```

## 3.5

### 3.5.2 Relationship Sets (without Constraints) to Tables

```sql
CREATE TABLE Employees(
    ssn CHAR(11),
    name CHAR(30),
    lot INTEGER,
    PRIMARY KEY (ssn)
)
```

To represent a relationship we must be able to identify each participating entity and give values to the descriptive attributes of the relationship.

Thus the attributes of the relation include

- The primary key attributes of each participating entity set, as foreign key fields

- The descriptive attributes of the relationship set

The set of non-descriptive attributes is a superkey for the relation. If there are no key constraints this set of attributes is a candidate key.

![](/Users/connorbaker/Packages/CS450_Lectures/Chapter%203/WorksIn2.png)

This relationship is captured by

```sql
CREATE TABLE WorksIn2(
    ssn CHAR(11),
    did INTEGER,
    address CHAR(20),
    since DATE,
    PRIMARY KEY (ssn, did, address),
    FOREIGN KEY (ssn) REFERENCES Employees,
    FOREIGN KEY (address) REFERENCES Locations,
    FOREIGN KEY (did) REFERENCES Departments
)
```

Note that `address`, `did`, and `ssn` fields cannot take on null values.

![](/Users/connorbaker/Packages/CS450_Lectures/Chapter%203/ReportsTo.png)

This relationship is captured by

```sql
CREATE TABLE ReportsTo(
    supervisor_ssn CHAR(11),
    subordinate_ssn CHAR(11),
    PRIMARY KEY (supervisor_ssn, subordinate_ssn),
    FOREIGN KEY (supervisor_ssn) REFERENCES Employees (ssn),
    FOREIGN KEY (subordinate_ssn) REFERENCES Employees (ssn)
)
```

Observe that we need to explicitly name the referenced field of Employees because the field name differs from the name(s) of the referring field(s).

### 3.5.3 Translating Relationship Sets with Key Constraints

If a relationship set involves $n$ entity sets and some $m$ of them are linked via arrows in the ER diagram, the key for any one of these $m$ entity sets constitutes a key for the relation to which the relationship is mapped. Hence we have $m$ candidate keys, and one of these should be designated as the primary key.

![](/Users/connorbaker/Packages/CS450_Lectures/Chapter%203/Manages.png)

Since each department has at most one manager, no two tuples can have the same `did` value but differ on the `ssn` value. A consequence of this observation is that `did` is itself a key for `Manages`; indeed the set `did, ssn` is not a key (because it is not minimal). The manages relation can be defined as

```sql
CREATE TABLE Manages(
    ssn CHAR(11),
    did INTEGER,
    since DATE,
    PRIMARY KEY (did),
    FOREIGN KEY (ssn) REFERENCES Employees,
    FOREIGN KEY (did) REFERENCES Departments
)
```

A second approach to translating a relationship set with key constraints is often superior because it avoids creating a distinct table for the relationship set.

The idea is to include the information about he relationship set in the table corresponding to the entity set with the key, taking advantage of the key constraint. In the `Manages` example, because a department has at most one manager, we can add the key fields of the `Employees` tuple denoting the manager and the `since` attribute to the `Departments` table.

This approach eliminates the  need for a separate `Manages` relation and queries asking for a department's manager can be answered without combining information from two relations. The only drawback to this approach is that space could be wasted if several departments have no managers. In this case, the added fields would have to be filled with null values. The first translation )using a separate table for `Manages`) avoids this inefficiency, but some important queries require us to combine information from two relations, which can be a slow operation.

The following captures the second approach

```sql
CREATE TABLE DeptMgr(
    did INTEGER,
    dname CHAR(20),
    budget REAL,
    ssn CHAR(11),
    since DATE,
    PRIMARY KEY (did),
    FOREIGN KEY (ssn) REFERENCES Employees
)
```

In general, if a relationship set involves $n$ entity sets and some $m$ of them are linked via arrows in the ER diagram, the relation corresponding to any one of the $m$ sets can be augmented to capture the relationship.

### 3.5.4 Translating Relationship Sets with Participation Constraints

Consider

![](/Users/connorbaker/Packages/CS450_Lectures/Chapter%203/ManagesAndWorksIn.png)

Every department has to have a manager because of the participation constraint, and at most one manager, due to the key constraint.

```sql
CREATE TABLE DeptMgr(
    did INTEGER,
    dname CHAR(20),
    budget REAL,
    ssn CHAR(11) NOT NULL,
    since DATE,
    PRIMARY KEY (did),
    FOREIGN KEY (ssn) REFERENCES Employees
                ON DELETE NO ACTION
)
```

It also captures the participation constraint that every department must have a manager: because ssn cannot take null values, each tuple of DeptMgr identifies a tuple in Employees (who is the manager).

The NO ACTION (which is the default) ensure that an Employees tuple cannot be deleted while it is pointed to by a DeptMgr tuple. If we wish to delete such an Employee tuple, we must first change the DeptMgr tuple to have a new employee act as manager.

The constraint that every department must have a manager cannot be captured using the first translation approach discussed in Section 3.5.3. (Look at the definition of Manages and think about what effect it would have if we added NOT NULL constraints to the ssn and did fields. Hint: the constraint would prevent the firing of a manager, but does not ensure that a manager is initially appointed for each department!)

This situation is a strong argument for the second model, especially when the entity set with the key constraint also has a total participation constraint.

We can also capture participation constraints using key and foreign key constraints in one other special situation: a relationship set  in which all participating entity sets have key constraints and total participation. The best translation approach in this case is to map all the entities as well as the relationship into a single table.

### 3.5.5 Translating Weak Entity Sets

Always participates in a one-to-many binary relationship and has a key constraint and total participation. The second translation approach is ideal in this case, but we must take into account that the weak entity has only a partial key. Also, when an owner entity is deleted, we want all owned weak entities to be deleted.

![](/Users/connorbaker/Packages/CS450_Lectures/Chapter%203/WeakEntity.png)

A Dependents entity can be identified only if we take the key of the owning Employee entity set and the pname of the Dependents entity, and the Dependents entity must be deleted if the owning Employees entity is deleted.

```sql
CREATE TABLE DepPolicy(
    pname CHAR(20),
    age INTEGER,
    cost REAL,
    ssn CHAR(11),
    PRIMARY KEY (pname, ssn),
    FOREIGN KEY (ssn) REFERENCES Employees
                ON DELETE CASCADE
)
```

Observe that the primary key is `<pname, ssn>` since Dependents is a weak entity. The ON DELETE CASCADE ensure our retention policy is as desired.

### 3.5.6 Translating Class Hierarchies

Two approaches to handling ISA hierarchies.

![](/Users/connorbaker/Packages/CS450_Lectures/Chapter%203/ISA.png)

1. We can map each of the entity sets Employees, Hourly_Emps, and Contract_Emps to a distinct relation. The Employees relation is created as in section 2.2 We discuss Hourly_Emps here; Contract_Emps is handled similarly. The relation for Hourly_Emps includes the hourly_wages and hours_worked attributes of Hourly_Emps. It also contains the key attributes of the superclass which serve as the primary key for Hourly_Emps as well as a foreign key referencing the superclass. For each Hourly_Emps entity, the value fo the name and lot attributes are stored in the corresponding row of the superclass. Note that if the superclass tuple is deleted, the delete must be cascaded to Hourly_Emps.

2. Alternatively, we can create just two relations, corresponding to Hourly_Emps and Contract_Emps. The relation for Hourly_Emps includes all the attributes of Hourly_Emps as well as all the attributes of Employees.

The first approach is general and always applicable. However, queries in which we want to examine, say, hourly employees, may require us to combine Hourly_Emps (or Contract_Emps, as the case may be) with Employees to retrieve name and lot.

The second approach is not applicable if Employee is not covered by its subclasses. Also, if there is overlap between the two subclasses, the name and lot values are stored twice.

In general, covering and overlap constraints can be expressed in SQL only by using assertions.

### 3.5.7 Translating ER Diagrams with Aggregation

![](/Users/connorbaker/Packages/CS450_Lectures/Chapter%203/Aggregation.png)

The Employees, Projects, and Departments entity sets and the Sponsors relationship set are mapped as described in previous sections. For the Monitors relationship set, we create a relation with the following attributes: the key attributes of Employees (ssn) the key attributes of Sponsors (did, pid), and the descriptive attributes of Monitors (until). This translation is essentially the standard mapping for a relationship set, as described in Section 3.5.2.

There is a special case in which this translation can be refined by dropping the Sponsors relation. Consider the Sponsors relation. It has attributes pid, did, and since, and in general we need it (in addition to Monitors) for two reasons:

1. We have to record the descriptive attributes (since) of the Sponsors relationship

2. Not every sponsorship has a monitor, and thus some (pid, did) pairs in the Sponsors relation may not appear in the Monitors relation.

However, if Sponsors has no descriptive attributes and has total participation in Monitors, every possible instance of the Sponsors relation can be obtained from the (pid, did) columns of Monitors; Sponsors can be dropped.

### 3.5.8 ER to Relational: Additional Examples

![](/Users/connorbaker/Packages/CS450_Lectures/Chapter%203/Policies.png)

```sql
CREATE TABLE Policies(
    policiyid INTEGER,
    cost REAL,
    ssn CHAR(11) NOT NULL,
    PRIMARY KEY (policyid),
    FOREIGN KEY (ssn) REFERENCES Employees
                ON DELETE CASCADE
)
```

```sql
CREATE TABLE Dependents(
    pname CHAR(20),
    age INTEGER,
    policyid INTEGER,
    PRIMARY KEY (pname, policyid),
    FOREIGN KEY (policyid) REFERENCES Policies
                ON DELETE CASCADE
)
```

Notice how the deletion of an employee leads to the deletion of all policies owned by the employee and all dependents who are beneficiaries of those policies. Further, each dependent is required to have a covering policy -- because policyid is part of the primary key of Dependents, there is an implicit NOT NULL constraint. This model accurately reflects the participation constraints in the ER diagram and the intended actions when an employee entity is deleted.

In general, there could be a chain of identifying relationships for weak entity sets. For example, we assumed that policyid uniquely identifies a policy. Suppose that policyid distinguishes only the policies owned by a given employee; that is, policyid is only a partial key and Policies should be modeled as a weak entity set. This new assumption about policyid does not cause much to change in the preceding discussion. In fact, the only changes are that the primary key of Policies becomes (policyid, ssn), and as a consequence, the definition of Dependents changes -- a field called ssn is added and becomes part of both the primary key of Dependents and the foreign key referencing Policies.

```sql
CREATE TABLE Dependents(
    pname CHAR(20),
    ssn CHAR(11),
    age INTEGER,
    policyid INTEGER NOT NULL,
    PRIMARY KEY (pname, policyid, ssn),
    FOREIGN KEY (policyid, ssn) REFERENCES Policies
                ON DELETE CASCADE    
)
```

## 3.6
