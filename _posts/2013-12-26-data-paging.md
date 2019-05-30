## Pitfall of data paging. Use row limits instead ?

There are a million rows that need to be processed, and you want to process a few hundred rows at a time. You may think a paging solution will be simple and elegant. But note there is a caveat.. 

Paging - It allows you to read a block of rows, and then the next, and then the next, with no repeats. This looks nice and easy. See PagingAndSortingRepository at http://docs.spring.io/spring-data/jpa/docs/1.0.0.M1/reference/html/ 
But the caveat here is that if the underlying data changes while you are processing, then this solution wont work.
For example, you have a 10,000 rows that are in "NEED PROCESSING" status. You query the database based on this status, and have 20 pages with 500 rows each.
If some other process accesses the same table and changes the status of one of these rows, then your paging solution becomes invalid.
Also, if in the course of your processing, you plan to change the status of the rows from "NEED PROCESSING" to PROCESSED", then again you will have invalid pages.

In such a scenario, you may want to think about other simpler row limit implementations as shown below.

1. Plain Vanilla JPA Implementation
```
                  Query query = entityManager.createQuery("select app from Appointment app where app.status = 'NEED PROCESSING'");
                  query.setFirstResult(0);
                  query.setMaxResults(limitRowCount);
                  List<Appointment> apptRqsts = (List<Appointment>)query.getResultList();
                  return apptRqsts;
 ```

2. QueryDSL + JPA
```                  JPAQuery query = new JPAQuery(entityManager);
                  QApptRqst qApptRqst = new QApptRqst("qApptRqst");
                  List<ApptRqst> resultList = query.from(qApptRqst)                                                                   .where(qApptRqst.apptRqstStatusRef.apptRqstStatusCd.eq("NEED PROCESSING"))
                                    .limit(limitRowCount)
                                    .list(qApptRqst);
```
