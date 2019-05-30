## Returning Java object resultset when using Hibernate Native Query
I will assume you are using Spring Data JPA along with Hibernate. 
```
public class EmployeeRepositoryImpl implements EmployeeRepositoryCustom {

     @PersistenceContext
     private EntityManager entityManager;

     private static final String EMP_NATIVE_QUERY = "select emp.name, bio.hairColor, phy.height from Employee emp inner join Biodata bio on emp.id = bio.empid inner join Physical phy on emp.id = phy.empid where emp.id=:employeeId";


     public List<EmpDetail> retrieveEmpDetails(String employeeId) {

        List<EmpDetail> empDetails = entityManager.createNativeQuery(EMP_NATIVE_QUERY, "EmpDetailResultSet")
                               .setParameter("employeeId", employeeId)
                               .getResultList();
          return empDetails;
      }
}
```
EmpDetail.java : 
```
@SqlResultSetMapping(name="EmpDetailResultSet",
                entities = {
                     @EntityResult(entityClass=EmpDetail.class,
                             fields = { 
                                @FieldResult(name="empName", column = "EMP_NAME"),
                                @FieldResult(name="hairColor", column = "EMP_HAIR_COLOR"),
                                @FieldResult(name="height", column = "EMP_PHY_HEIGHT") }) })
                              
// Still marked with @Entity although not really associated with an underlying data table directly.
@Entity
public class EmpDetail {
   
     // Need this - should be unique in the resultset returned by the native query
     @Id
     private String empName;
     private String hairColor;
     private String height;

    //  .. getters and setters for the properties above.
}
```
The repository implementation should be part of your jpa:repositories scan and the entity class should be part of the packagesToScan when wiring up the persistenceManager.
