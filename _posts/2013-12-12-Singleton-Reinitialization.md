## Reinitializing Singleton Data when using Spring
If your Spring Singleton Bean contains some data (to reduce fetches from the database) and you want this data refreshed from the database at a later time without refreshing the entire application context, here is how you could do this.
```
@Component 
public class OpenHoursService implements OpenHours {

  private  int[] openHours = new int[7];

  public OpenHoursService() {}

  @PostConstruct
  public void init() {
    fetchFromDb(); 
  }

  /** Call this method when your data change event occurs. i.e. Admin modifies the reference data using an admin screen **/
  public void reinitialize() {
    init();
  } 
} 
```
