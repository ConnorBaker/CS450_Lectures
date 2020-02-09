---
title: Module 3
author: [Connor Baker]
date: Compiled on \today\ at \currenttime
subject: The Entity-Relationship and Relational Models
keywords: [GMU, Spring, 2020, CS, 450]
subtitle: Database Concepts
lang: en
titlepage: true
titlepage-color: FFFFFF
titlepage-text-color: 0d47a1
titlepage-rule-color: 0d47a1
titlepage-rule-height: 2
papersize: letter
fontsize: 10pt
listings-disable-line-numbers: true
table-use-row-colors: true
footer-right: \thepage\ of \pageref{LastPage}
header-right: \today
header-includes:
- \setcounter{page}{0} # So that the titlepage is the zeroth page
- \usepackage{datetime}
- \settimeformat{ampmtime}
- \usepackage{lastpage}
- \usepackage{tikz-cd}
- \usetikzlibrary{fit, shapes.geometric}
- \usepackage[normalem]{ulem}
---

## Homework One

Company and individual stores

> A contact person whose company sells a book cannot be selling the same book as an individual seller at the same time (he/she may sell other books as an individual seller).

ER model can't represent all constraints

How do we model status descriptions?

- Entity or attribute
  
  - To be an attribute there must be one description per book -- but we don't know that yet just from what was written
  
  - Each book is required to have exactly one description

- Each textbook is published by a company in a publishing year
  
  - Publishing year is a descriptive attribute of the relationship between the book and the company publishing it (it's associated with the relationship)

- Each textbook has one or more sellers
  
  - Seller entity set (ISA relationship)
    
    - Company/individual
    
    - Though, they track different information
    
    - They don't necessarily inherent attributes/primary key (they must be subset of the parent)
  
  - One or more sellers
    
    - Relationship between sellers of a kind and textbooks
    
    - With ISA, sellers related to textbooks
    
    - Without ISA, how does each seller relate to the textbooks?
      
      - One ternary relationship?
      
      - Two binary relationships?
        
        - Individuals can sell multiples of the same books while corporations cannot (? double check)
    
    - Is there a relationship here? Is there meaning in having a ternary relationship?

- Publishing companies and sellers will be ISA relationships of companies
  
  - Both are subsets of companies

- Could be more than one phone, so it'll be an entity set
  
  - How do we connect it to companies?

- Companies have contact persons
  
  - Cardinality one -- how do we model it?
    
    - Entity set for individuals, connect via relationship to company, restrict cardinality
    
    - If we have individuals appearing in multiple contexts then we don't want to model them as a single attribute on the company
    
    - If we unify stuff, the design is better
    
    - We want to reduce repetition  of information 

- How do we model a phone number?
  
  - How many for an individual?
    
    - One -- restricted relationship with phone numbers entity set
  
  - How many for company?
    
    - $n$ -- restricted relationship with phone numbers entity set

## Relational Model

How do we convert from ER to relational?

Database is a collection of tables

Every table has is a collection of rows (tuples), tuples are a collection of attributes

Def: Relational Schema

A relational schema $R$ is a set of attributes and their associated domains $\{(A_1, D_1), \dots, (A_i, D_i)\}$

Def: Relational Tuple

A tuple $t$ over the schema $R = \{(A_1, D_1), \dots, (A_i, D_i)\}$ is defined as $t : \{A_1, \dots, A_n\} \to \cup_{i=1}^n D_i$ such that $t(A_i) \in D_i, \forall i = \{1, \dots, n\}$.

Ex:

$R = \{(age, \R), (name, String)\}$. Then $t(age) = 7.5$, $t(name) = "smith"$

Def: Relational Instance

A relational instance ($\equiv$ relation, table, stating a particular instance) $r$ over the schema $R$ is a set of relational tuples $t$ over $R$.

Def: Relational Database Schema

A set of relational schemas $\{R_1, \dots, R_n\}$.

Def: Relational Database Instance

A relational database instance ($\equiv$ database, database instance) over the relational database schema $\{R_1, \dots, R_n\}$ is a set of relational database instances $\{r_1, \dots, r_m\}$ over the corresponding schemas $R_1, \dots, R_m$.

Ex:

Student ($R_1$)

| ssn | name  | major |
| --- | ----- | ----- |
| 100 | Smith | CS    |

$R_1 = \{(ssn, \Z^+), (name, STR), (major, STR)\}$

Then $t(ssn) = 100, t(name) = "Smith", t(major), "CS"$

Department ($R_2$)

| did | dname | dchair |
| --- | ----- | ------ |
| 1   | CS    | ?      |

$R_2 = \{(did, \Z^+), (dname, STR), (dchair, STR)\}$

## Different Kinds of Data Models

Different data models have different semantics

Header is "does it matter"

|                | being an element | duplication | order |
| -------------- | ---------------- | ----------- | ----- |
| Set            | y                | n           | n     |
| Multiset (bag) | y                | y           | n     |
| List           | y                | y           | y     |

In SQL, we have multiset semantics.

SQL UNIQUE key.

None of the attributes in the primary key can be null.

With unique, it can be null.

Referential integrity constraint is the same as a foreign key in SQL

Foreign key must always refer to the primary key of the foreign table

Constraints never come from the data -- it's from a priori knowledge/business rules/external knowledge about the data. Integrity constraints formalize this knowledge within the DBMS and allow them to catch mistakes.

Homework is submitting a SQL script with create table commands.

How do we convert from the ER model to the relational model?

Create tables for all entities. Now need to capture the information stored in the relationships. Consider what defines the connected entities (the primary keys). We can do this by taking the union of the primary keys of the entities. Make the components of the relationships new primary key foreign keys which reference the employees. Then we add the descriptive attributes as fields to the table representing the relationship.

Make sure to have ON DELETE set to CASCADES so that it affects the tables that are related.
