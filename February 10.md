## February 10

Lecture

+ SQL commands were a big problem

+ Possible awarding of more days to do the project

+ Translation of aggregations to SQL

+ How to represent constraints in SQL

+ Rule for ISA
  
  + Parent entity / child entity
  
  + Must inherent primary key
  
  + Set of attributes of child is a superset (not strict) of parent
  
  + We only denote the additional attributes
  
  + As a set, the child is a subset of the parent entity set

Overview of problem 1

If we don't know the cardinality, we assume the more general

- We know that the isbn is the primary key

- We have a company which publishes a book
  
  - publishing co.
  
  - Connected to textbook via relationship set
  
  - for every textbook, exactly one textbook that published it
    
    - Key constraint and participation constraint
    
    - Bolded arrow
  
  - What about textbook which hasn't been published yet?
    
    - We assume that the description we have is binding
  
  - Each publisher has to have published at least one textbook
    
    - Participation constraint

- Each textbook has one or more sellers
  
  - ISA relationship
  
  - Must inherent primary key
    
    - What's the primary key of sellers?
    
    - Can't use ISA since they have different primary keys
    
    - What attributes would we have for sellers besides the attributes which represent individuals and companies?
  
  - Sold by relationship
    
    - What is the sold by relationship? The set of triplets -- (a textbook, an individual seller, a company)
      
      What does this mean in the real world?
      
      - In this case, they happen to be selling the same book
      
      - Is there any connection between the individual and the company other than them selling the same textbook?
        
        - No! So a ternary relationship isn't the right choice
    
    - So we look at having two different relationships
      
      - Sold by individual, and sold by company
      
      - However, we can't express the fact that every textbook has at least one seller

- How do we connect companies to corporate phones?
  
  - has phones relationship
  
  - Specs don't say whether there is at least one
  
  - So we leave it without constraint

- We have a single POC of the company
  
  - Connect with contacts person that is its own entity set which ISA individual vs. using a direct relationship to individuals from company
    
    - Direct misses some information
    
    - If we want all the contact persons, the contact person entity set has all of that information
      
      - Is also more expandable because then can add additional attributes
    
    - Having contact person separately and connecting them via isa is a better idea because it provides additional visibility and flexibility
  
  - Every company has exactly one contact person
  
  - Can also add a participation constraint
    
    - Every contact person is a contact person for at least one company (there's no limit in the description)
  
  - When you have some meaningful ISA hierarchy, extend it
  
  - More ISA hierarchies for selling companies and selling individuals to individuals and companies

- Even if each entity has only a single address, it's still a good idea to put it in an entity set -- just use a key/participation constraint

Translating to a table

+ Before translating to tables, let's do a high level design

+ the basic entity sets are translated into a table

+ relationship set sometimes translated into a table sometimes combined with a table to create a new unified table

+ we need to understand which ones will be combined and which ones will be different
  
  + criterion for combining relationship set and entity set:
    
    + look at the constraints -- key constraints
    
    + we we have a key constraint we can combine the table and the corresponding entity set
    
    + textbook may not have more than one publishing company
    
    + Every textbook has exactly one publishing company and we can represent it as an extra attribute for the extended table
    
    + If on the other hand we didn't have the key constraint, we can't represent it as an attribute because we'd have multiple companies having published the same book
    
    + If we didn't have the participation constraint it might not have a publishing company -- can't have the name of the publishing company as a primary key if its null
    
    + Can unify company and contact person since there is also a key constraint
    
    + We can unify the seller relationships / indiv seller / comp seller just by adding the sid
    
    + Contact person is represented by name and email
    
    + CompaniesPlus must inherent the primary key for the contact person
    
    + One table for companies including has contact
      
      + Circle back for use of foreign key to refer to contact person table
    
    + One table for textbook and info of published by
      
      + What are the attributes
      
      + All of the textbook
      
      + Primary key of publishing company which is name
      
      + And descriptive attribute of relationship (publishing year)
    
    + One table for sold by relationship set
      
      + Every tuple in this table must represent an element of the set of the relationship
      
      + Textbook entity and sellers
      
      + sid and isbn are primary key
      
      + If sold by had descriptive attribute we would add it as a field, but it doesn't so we don't
      
      + Relationship cannot exist unless the entities its relating exist as well
      
      + We'll have a foreign key constraint
        
        + isbn references textbook table
        
        + sid references seller table
      
      + What happens if a particular textbook with a particular isbn is removed? What should happen with the isbn, sid combo?
        
        + It should be delete
        
        + Must set cascade delete
        
        + If someone removes a tuple from textbook, the tuple from soldby that references that will be deleted as well
    
    + How do we manage the ISA hierarchy? How do we preserve this relationship?
      
      + Create the table for the top of the hierarchy
      
      + Create a table for the individuals
      
      + CompanyPlus contact person cannot exist without associated individual (it is a subset of individual) and therefore must be associated with a tuple for individual, so we should represent this with a foreign key
  
  + How do we go about key translation
    
    + If we have a relationship with a key constraint (or key + participation) we unify the table to represent both the entity and relationship (all attributes of entity set + primary of other entity + descriptive attributes)
  
  + We created a table for Companyplus
    
    + Add not null constraint for participation constraint
  
  + What goes into this table? The primary keys from entities which are participating in this relationship and the descriptive attributes

+ If you have an ISA hierarchy, create a table for the top, and then a table for every child
  
  + The child gets the primary key in the table and the additional attributes
  
  + Set the foreign key to the parent because we can always find them in the parent and we don't want duplicates
  
  + It could be that the child doesn't have additional attributes
  
  + child has just the primary key in this case

***Assume that you can add SID for the first problem***

```sql
CREATE TABLE CompaniesPlus(
    name VARCHAR2(32) NOT NULL,
    address VARCHAR2(32) NOT NULL,
    cp_name VARCHAR(32) NOT NULL,
    cp_email VARCHAR2(32) NOT NULL,
    PRIMARY KEY (name),
    FOREIGN KEY (cp_name, cp_email) REFERENCES Individuals (name, email) ON DELETE CASCADE
);

CREATE TABLE PublisherPlus(
    isbn INT NOT NULL,
    cat VARCHAR2(32) NOT NULL,
    name VARCHAR2(32) NOT NULL,
    pub_year INT  NOT NULL,
    comp_name
    PRIMARY KEY
);

CREATE TABLE SoldBy(
    isbn INT,
    sid INT,
    PRIMARY KEY (isbn, sid),
    FOREIGN KEY (isbn) REFERENCES Textbook ON DELETE CASCADE,
    FOREIGN KEY (sid) REFERENCES Seller ON DELETE CASCADE
);

CREATE TABLE Individuals(
    name VARCHAR2(32),
    email VARCHAR2(32),
    PRIMARY KEY (name, email)
)
```
