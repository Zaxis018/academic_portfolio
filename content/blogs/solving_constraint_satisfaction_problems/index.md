---
title : Solving constraint satisfaction problems
summary: CSP, CSP Graph , AC3 algorithm  with crossword generation as example
date: 2024-10-25
authors:
    - admin
tags:
    - CSP

---
## Constraint Satisfaction Problem (CSP)

- Defined by a set of variables, set of domains for each variable and set of constraints for each variables
- <b>Variables(X)</b> : <br>
  A finite set of variables `X = {x1,x2,x3...}`
- <b>Domains(D)</b> : <br>
  `D = {D1,D2,D3,...}`
  Where `Di` is set of possible values for variable `xi` . That is `xi ∈ Di`.

- <b>Constraints(C)</b> : <br>
  A set of constraints C={c1,c2,…,cm} where each constraint `cj`​ involves a subset of variables `Xj ​⊆ X` and specifies a relation `Rj`​ that must be satisfied. The relation RjRj​ defines the set of allowable tuples of values for the variables in `Xj​`.

A solution to a CSP is an assignment of values to all variables x1,x2,…,xn such that :
xi ∈ Di​ for all i ∈ {1,2,…,n} and All constraints cj ∈ C are satisfied.

## CSP as a Graph

-  <b>Nodes</b>:
  Each variable in the CSP is represented as a node (or vertex) in the graph.
-  <b>Edges</b>:
    An edge is drawn between two nodes (variables) if there is a direct constraint between them.

- Example: 
```
    Constraints:
      X ≠ Y
      Y < Z
      X > 1

    Graph Representation:
      Nodes: X, Y, Z
      Edges:
        X -- Y (due to the constraint X ≠ Y)
        Y -- Z (due to the constraint Y < Z)
 ```

### Node consistency

- Node consistency is achieved when, for every variable, each value in its domain is consistent with the variable’s unary constraints.
- To make a variable `x` node consistent : from the domain of variable `x`, remove a value `v` that doesn't satisfy the constraint (length in case of crossword)

### Arc Consistency

- `x` is arc consistent with `y` when every value in the domain of `x` has a possible value in the domain of `y` that does not cause a conflict. (A conflict in the context of the crossword puzzle is a square for which two variables disagree on what character value it should take on.)
- To make `x` arc consistent with `y`, remove any value from the domain of `x` that does not have a corresponding possible value in the domain of `y`. The domain of `y` should be left unmodified.

### AC3 Algorithm

Maintains a `queue` of arcs to process.

- To implement AC3, `revise`(make a variable x arc consistent with another variable y) each arc in the queue one at a time. Any time you make a change to a domain, though, you may need to add additional arcs to your queue to ensure that other arcs stay consistent.
- When trying to enforce arc consistency , we remove all values from domain, it is impossible to solve the problem
<br><br>


## Crossword as a CSP


Each variable(word) in a crossword is represented by 4 values:

```
    {
        i : coordinate of square of starting row
        j : coordinate of square of starting column
        length : some number
        direction : down or across
    }
```

#### Domain : Initially the domain for each variable is set of all words in corpus.

#### Unary constraints : length<br>

#### Binary constraints : intersecting/overlapping letter with neighbour words(variables)
<br>

## Code Implemetation
### enforce_node_consistency:
- remove values from domain of a variable if it doesnt satisfy unary constraint (length of variable)

### revise : 
- To make `x` arc consistent with `y`, remove any value from the domain of `x` that does not have a corresponding possible value in the domain of `y`.
- for every value in domain of `X` , check if it has possible values in `Y` , if not remove that value from `X`

### ac3 :
- Maintain queue of arcs
- enforce arc consistency using `revise` function 
- when a domain of a variable is changed to enforce arc consistency, other arcs may be affected. so add other arcs in the queue
```
function AC-3(csp):
  queue = all arcs in csp

  while queue non-empty:
    (X, Y) = DEQUEUE(queue)

    if REVISE(csp, X, Y):
      if size of X.domain == 0:
        return false
      for each Z in X.neighbors - {Y}:
        ENQUEUE(queue, (Z, X))

  return true
```

### assignment_complete : 
- An `assignment` is a dictionary where the keys are `Variable` objects and the values are strings representing the words those variables will take on.
- An `assignment` is complete if every crossword variable is assigned to a value (regardless of what that value is).

### consistent :
- An `assignment` is consistent if it satisfies all of the constraints of the problem: that is to say, all values are distinct, every value is the correct length, and there are no conflicts between neighboring variables.

### order_domain_values :
 - returns a list of all of the values in the domain of `var`, ordered according to the `least-constraining values` heuristic.
- if assigning var to a particular value results in eliminating `n` possible choices for neighboring variables, you should order your results in ascending order of `n`.
In other words, we should choose a value for a given variable that keeps more options for other variables.
- any variable present in assignment already has a value, and therefore shouldn’t be counted when computing the number of values ruled out for neighboring unassigned variables.
- Arbitrary choice of unassigned variable may also produce correct  crossword.
### select_unassigned_variable :
- returns single `Variable` in the crossword puzzle not yet assigned , By using `Minimum remaining value` heuristic and then `degree` heuristic.

- <b>Minimum remaining value</b>: Select a unassigned `Variable` such that the variable has fewest values in its domain.
This is done to quickly eliminate a branch of possible options.
<br>
<br>This may sound counterintuitive to `order_domain_values` which tried to select a least constraining value But we are talking about different things here. The `order_domain_values` tries to select a `value` for a specific `Variable` to keep  the other variables with more choices, while `Minimum remaining value` selects a `Variable`(not `Value`) that prunes the unnecessary  solution space quickly.
- <b>Degree</b> : 
If there is tie between variables by having same number of values in the domains, select the variable that has most neighbours. This is done because , by choosing such variable, it is likely to rule out more choices for other variables. <br>
If it is still tie, choose arbitrary variable.

- Arbitrary choice of unassigned variable will also be correct for generating crossword, this is just for optimization
### backtrack :
- Given a partial `assignment` dictionary , returns a complete satisfactory `assignment` if possible.
- Interleaving search with inference  (as by maintaining arc consistency every time you make a new assignment) makes it more efficient

backtrack starts with a assignment and recursively calls itself by passing the updated `assignment` disctionary with newly added assignment.
```
function BACKTRACK(assignment, csp):
  if assignment complete: 
    return assignment

  var = SELECT-UNASSIGNED-VAR(assignment, csp)

  for value in DOMAIN-VALUES(var, assignment, csp):
    if value consistent with assignment:
      add {var = value} to assignment
      result = BACKTRACK(assignment, csp)
      if result ≠ failure: 
        return result
      remove {var = value} from assignment  

  return failure
```
-  `remove {var = value} from assignment`, is where backtracking actually occurs as we return to previous state of assignment.



## Optimimzed Backtracing Search
- When we assign a value to a variable , we can check arc consistency on other nodes(variable) to quickly find out values other nodes could take. this inference procedure makes the algorithm more efficient as we do not need to solely assign values in the search(backtrack) process, but also can infer other values.
- whenever we assign a value to a variable we check if other inferences can be made. those inferences are added to the assignment.<br>
`inferences = INFERENCE(assignment, csp)`
<br>
`add inferences to assignment`


- If later , that choice of value results in failure, we remove not only the value but also the inferences made from that value in the assignment<br>
`remove {var = value} and inferences from assignment`
```
function BACKTRACK(assignment, csp):
  if assignment complete: 
    return assignment

  var = SELECT-UNASSIGNED-VAR(assignment, csp)

  for value in DOMAIN-VALUES(var, assignment, csp):
    if value consistent with assignment:
      add {var = value} to assignment
      inferences = INFERENCE(assignment, csp)
      if inferences ≠ failure: 
        add inferences to assignment
        result = BACKTRACK(assignment, csp)
        if result ≠ failure: 
          return result
      remove {var = value} and inferences from assignment

  return failure
```

