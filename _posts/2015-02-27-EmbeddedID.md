## Dealing with Embedded Ids
When using Hibernate, Spring data JPA, working with embedded Ids can be tricky.


How do you access the column that is part of the (embedded) primary key ?

Let us say you have a table

Employee with Primary key consisting of combined value of first_name, last_name, gender, dob.

You would have an Id class
```
@Embeddable
public class EmployeeId implements Serializable {
..
}
```
In the Employee entity class,
```
public class Employee implements Serializable {

private EmployeeId id;

@EmbeddedId
@AttributeOverrides( {
         @AtrributeOverride(name="firstName", column=@Column(name="FIRST_NAME", nullable=false, length = 50)),
         @AtrributeOverride(name="lastName", column=@Column(name="LAST_NAME", nullable=false, length = 50)),
         @AtrributeOverride(name="gender", column=@Column(name="GENDER", nullable=false, length = 50)),
         @AtrributeOverride(name="dob", column=@Column(name="DOB", nullable=false, length = 50)) })
public EmployeeId getId() {
     return this.id;
}
```
Note: The properties "firstName", "lastName", etc in the @AttributeOverride are actually referring to the private instance variables of the class EmployeeId, not Employee class.

In your Repository class, you can access gender as shown below.

@Query("select r.Id.gender from Employee r where r.id.firstName = ?1 and r.id.lastName = ?2 ")
public String findGender(String firstname, String lastname, String dob);
What if another table has reference to this table ?
i.e. EmployeeDetails also has the above 4 attributes and other height, weight columns. The 4 attributes in EmployeeDetails(firstname, lastname, gender, dob) form a foreign key referring to Employee table.

In EmployeeDetails entity,

@ManyToOne(fetch=FetchType.LAZY)
@JoinColumns( {
     @JoinColumn(name="FIRST_NAME", referencedColumnName="FIRST_NAME", nullable=false, insertable=false, updatable=false),
     @JoinColumn(name="FIRST_NAME", referencedColumnName="FIRST_NAME", nullable=false, insertable=false, updatable=false),
     @JoinColumn(name="FIRST_NAME", referencedColumnName="FIRST_NAME", nullable=false, insertable=false, updatable=false),
     @JoinColumn(name="FIRST_NAME", referencedColumnName="FIRST_NAME", nullable=false, insertable=false, updatable=false) })
public Employee getEmployee() {
    return this.employee;
}

The key here is to use referencedColumnName. Otherwise, strangely, on the generated queries, you will see joins involving unrelated columns(FIRST_NAME on EMPLOYEE_DETAILS table against LAST_NAME on EMPLOYEE table).

If that is setup correctly, you can use the Repository class on EmployeeDetails without writing any code.

@Repository
public interface EmployeeDetailsRepository extends JpaRepository<EmployeeDetails, EmployeeDetailsId> {
     EmployeeDetails findByEmployee(Employee emp);
}

Dont ask me why someone uses this sort of data model. :-)
