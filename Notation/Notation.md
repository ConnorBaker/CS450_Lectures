+ Arrow
  
  + Indicates "key constraint"
  
  + Arrow indicates it appears it at most one relationship
  
  + Ex: Department $\to$ Manages -- Employees
    
    + Each department is managed by at most one employee

+ Thick Line
  
  + Indicates "participation constraint"
  
  + "Total"
    
    + Every element of the domain is related to *at least* one element of the codomain
  
  + "Partial"
    
    + Not total

![](/Users/connorbaker/Packages/CS450_Lectures/Notation/1.png)

+ Weak entities cannot be uniquely identified by their attributes
  
  + They must be identified via a relation
  
  + Both the weak entity set and the identifying relationship are bolded
  
  + The partial key of the weak entity set is underlined with a broken line

+ ISA
  
  + Primary key must also be primary key for all sub-entities
  
  + Down is called specialization, up is called generalization
  
  + Overlap constraint
    
    + Can two subclasses contain the same entity? Can Joe be an hourly employee and a contract employee?
  
  + Covering constraint
    
    + Do the subclasses collectively include all entities of the superclass? Can we have an employee that is *not* an hourly or contract employee?

+ Aggregation
  
  + Relationship sets participating in relationship sets
