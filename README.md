# CSE 132A Homework 5: Relational Algebra and Logical Query Optimization


**Database System:** Any relational system (conceptual / logical level only)

**Deliverables:**  
- For each problem (1, 2, 3), submit **three separate `.txt` files**:
  - `problemX_original.txt` – the **initial (naïve) logical plan** in relational algebra for Problem X.
  - `problemX_optimized.txt` – the **optimized logical plan** in relational algebra for Problem X.
  - `problemX_explanation.txt` – a brief **explanation** (2–5 sentences) of which rules you used and why the plan is better for Problem X.
- In total, you will submit **9 files**:
  - Problem 1: `problem1_original.txt`, `problem1_optimized.txt`, `problem1_explanation.txt`
  - Problem 2: `problem2_original.txt`, `problem2_optimized.txt`, `problem2_explanation.txt`
  - Problem 3: `problem3_original.txt`, `problem3_optimized.txt`, `problem3_explanation.txt`
- Submit all files through Gradescope.


**Grading (100 pts total)**

| **Problem** | **Points** | **Key Skills Tested**                            |
| ----------- | ---------- | ------------------------------------------------ |
| 1           | 30         | SQL → RA translation, selections, joins         |
| 2           | 35         | Correlated subqueries, EXISTS → semi-join, RA   |
| 3           | 35         | Join order, predicate pushdown, projection RA   |

Note that this time the autograder will not be the final grade of your submission (as it is much harder to auto-grade compared to previous assignments). Instead, the autograder will only check if the files are present and if they are formatted correctly.

------

## Problem 1: Basic Join Query Optimization [30 pts]

### Schema

We use the following relations:

```sql
Employee(empid, name, dept, salary)
Department(deptid, dname, location)
Project(pid, deptid, budget)
Assignment(empid, pid, hours)
```

### Given SQL Query

```sql
SELECT d.dname, e.name, p.pid
FROM Department d
JOIN Project p      ON d.deptid = p.deptid
JOIN Assignment a   ON p.pid = a.pid
JOIN Employee e     ON e.empid = a.empid
WHERE d.location = 'LA'
  AND e.salary > 80000
  AND p.budget BETWEEN 100000 AND 300000;
```

### Task

1. Write the initial logical plan (straight translation to relational algebra).
2. Apply logical optimizations and produce the final optimized plan tree.
   - Selection pushdown
   - Projection pushdown (keep only needed attributes)
   - Join reorder using commutativity/associativity
   - Join minimization if applicable
3. Explain how you applied each rule, and why the optimized plan is better.

### Deliverables
   - `problem1_original.txt`
   - `problem1_optimized.txt`
   - `problem1_explanation.txt`

------

## Problem 2: Correlated Subquery and Semi-Join Optimization [35 pts]

### Schema

```sql
Student(sid, sname, major, year)
Enrollment(sid, cid, grade)
Course(cid, cname, dept)
Instructor(iid, name, dept)
Teaches(iid, cid, quarter)
```

### Given SQL Query

```sql
SELECT s.sname
FROM Student s
WHERE s.major = 'CS'
  AND EXISTS (
        SELECT 1
        FROM Enrollment e
        JOIN Course c ON e.cid = c.cid
        WHERE e.sid = s.sid
          AND c.dept = 'CS'
    );
```

### Task

1. Write the initial logical plan (with correlated subquery).
2. Apply logical optimizations and produce the final optimized plan tree.
    - Predicate pushdown inside subquery
    - Subquery unnesting → convert `EXISTS` into a semi-join
    - Projection minimization
    - Join reordering
3. Explain how you applied each rule, and why the optimized plan is better.

### Deliverables
   - `problem2_original.txt`
   - `problem2_optimized.txt`
   - `problem2_explanation.txt`

------

## Problem 3: Join Reordering and Predicate Pushdown [35 pts]

### Schema

```sql
Orders(oid, cid, amount, date)
Customer(cid, cname, region)
Product(pid, pname, category)
OrderItem(oid, pid, quantity)
```

### Given SQL Query

```sql
SELECT c.cname, p.pname
FROM Customer c
JOIN Orders o       ON c.cid = o.cid
JOIN OrderItem oi   ON o.oid = oi.oid
JOIN Product p      ON p.pid = oi.pid
WHERE c.region = 'West'
  AND p.category LIKE 'Electronics%'
  AND o.amount > 200;
```

### Task

1. Write the initial logical plan (straight translation to relational algebra).
2. Apply logical optimizations and produce the final optimized plan tree.
   - Push all selections to base tables
   - Push projections to remove unneeded columns
   - Apply join reordering (choose best order based on selectivity)
   - Check for join minimization if any join variable collapses
3. Explain how you applied each rule, and why the optimized plan is better.

### Deliverables
   - `problem3_original.txt`
   - `problem3_optimized.txt`
   - `problem3_explanation.txt`

------

### General Notes and Hints

- You may use **standard relational algebra notation**, for example:
  - Selection: `σ_condition(R)`
  - Projection: `π_attrlist(R)`
  - Join: `R ⋈_condition S`
  - Renaming (if needed): `ρ_newname(R)`
- Clearly label your:
  - **Initial plan**, and
  - **Optimized plan**  
  for each problem.
- Focus on **logical** rather than physical optimization:
  - We are not choosing indexes or access paths.
  - We are only rewriting the algebra tree using equivalence rules.
- When in doubt, write more structure rather than less:
  - It is better to explicitly show multiple selections and projections than to leave them implicit.


