## Using Subqueries in QueryDSL to perform Update
When you need to perform an update on a table based on values from another table, in plain SQL, it is straightforward.
```
update tab1
set tab1.col1 = (select tab2.col1 from tab2 .. where ..)
where tab1.col2 ='x'
```

To do the same with QueryDSL 3.1.1+,
```
JPAUpdateClause upd = new JPAUpdateClause(entityManager, qTab1);
upd
.set(qTab1.col1, new JPASubQuery().from(qTab2).where..)
.where(qTab1.col2.eq('x')
.execute();
```
