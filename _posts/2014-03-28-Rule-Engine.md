## Simple Rule Engine 

![Rule Engine Class Diagram]({{ site.baseurl }}/images/GeneralRuleEngineClassDiagram.JPG "Simple Rule Engine")


```
public abstract class Rule<T> {
 private String name;
 protected Rule(String name) {
  this.name = name;
 }
 public abstract boolean isConditionTrue(T data);
 public abstract void doAction(T data);
 public String getName() {
  return name;
 }
}

public abstract class CustomRule extends Rule<CustomData> {
}

public interface RuleConfigurer {
 public void configure();
}
@Component
public class CustomRuleConfigurer implements RuleConfigurer {
 @Autowired 
 private Condition conditionChecker;

 @Autowired
 private Action actionHelper;

 public static List<CustomRule> ruleset = new ArrayList<CustomRule>();

 @PostConstruct
 public void configure() {
  CustomRule rule1 = new CustomRule("if order contains 2 twinkies, give 1 twinkie free") {
   @Override
   public boolean isConditionTrue(CustomData data) {
    return conditionChecker.orderContains2Twinkies(data);
   }
   @Override
   public void doAction(CustomData data) {
    actionHelper.giveFreebie(data);
   }
  };
 }
 ruleset.add(rule1);
}  
public class RuleEngine {
   public void processRules(CustomData data) {
     for (CustomRule rule : CustomRuleConfigurer.ruleset) {
  if (rule.isConditionTrue(data)) {
   LOG.debug("Rule:{} condition met. Invoking action..",rule.getName());
   rule.doAction(data);
  }
  }
  }
}
```
Our product needed to capture 50-60 business rules. But these rules do not change except between releases every few months. (It is not a retail e-commerce product). The client has no interest in maintaining the rules. Hence, we chose this simple approach, and it worked well for us.

If you a have special rule, which when true, you need to skip the remaining rules in the Ruleset, then you should throw a new SkipRemainingRulesException() from within the doAction() method of the Rule.
But note that when using Exceptions to control flow of logic, make sure you override fillInStackTrace() method on your custom exception class. This is because whenever an exception object is created, the fillInStackTrace() is called in the constructor of Throwable, and this is a very expensive call. When using exception for controlling flow, we are generally never interested in the stack traces anyway.
```
public class SkipRemainingRulesException extends RuntimeException {
   @Override
   public Throwable fillInStackTrace() {
      return this;
   }
}
```
Another performance tweak to the custom exception class is to use a Singleton instance of it, instead of creating a new instance each time. Note: This recommendation is applicable to situations when you are using Exceptions to control logic flow only(because you are tired of doing boolean checks and chaining multiple booleans, etc).
```
public class SkipRemainingRulesException extends RuntimeException {
   public static final SkipRemainingRulesException instance = new SkipRemainingRulesException();

   private SkipRemainingRulesException() {}

   public static SkipRemainingRulesException getInstance() {
     return instance;
   }

   @Override
   public Throwable fillInStackTrace() {
      return this;
   }
}
```
Advantages
1. Everything is in plain vanilla java code. Easy to debug. (Typos and mistakes made in property files and XML config files are harder to catch.)
2. Simple and fast. No need to incorporate a 3rd party heavy weight engine if it is not really needed.
3. Easy to test. 
4. No learning curve. No need to learn a new script or an entirely new framework if you are not going to use much of it.
