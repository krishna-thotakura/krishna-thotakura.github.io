## Validation when using Spring REST and AngularJS

GET/PUT/DELETE requests
Parameters are sent on the URL as PathVariables.

There are 2 ways to validate PathVariables.
Option 1 – Using @Validated and MethodValidationPostProcessor
@Controller
@Validated
public class AccountController {
  public Account searchByNum(@AccountNumber @PathVariable(“accountNumber”) final String   acctNo){
..
  }
}
In your spring config file, ensure MethodValidationPostProcessor bean is instantiated.

Define @AccountNumber
@NotBlank
@Size(min=5, max=7)
@Target({FIELD, METHOD, PARAMETER, ANNOTATION_TYPE, LOCAL_VARIABLE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Constraint(validatedBy={})
@ReportAsSingleViolation
public @interface AccountNumber {
              String message() default “{invalid.account.id}”; 
              Class[] groups() default {};
              Class[] payload() default {};
 }
If validation fails, you will get a MethodConstraintViolationException. From this object, you can extract list of ConstraintViolation(s).
Option 2 – Using Editors
Let us you have a String accountNumber, it makes sense to reuse the validation logic without repetition. 
public class AccountNumber implements CharSequence {
    private String accountNumber;
    public AccountNumber(String accountNumber) {
       this.accountNumber = accountNumber;
   }
…
}


Create an Editor that implements your validation logic.
public class AccountNumberEditor extends PropertyEditorSupport {
 public void setAsText(String text) throws IllegalArgumentException {
             if (StringUtils.containsWhitespace(text) {
                throw new IllegalArgumentException();
              } else {
                setValue(newAccountNumber(text);
             }
     } 

    public String getAsText() { return ((AccountNumber) this.getValue()).toString(); }
}

In your controller,
@InitBinder
public void initBinder(WebDataBinder binder) {
   binder.registerCustomEditor(AccountNumber.class, new AccountNumberEditor());
…
}

Your controller method would then be
@RequestMapping(value=”/account/{accountNumber}”) 
@ResponseBody
public Account searchByNum(@PathVariable(“accountNumber”) AccountNumber acctNo){
..
}

          
POST/PUT requests
Add Validation directly into your DTO classes.

Public class Word {
  .@Length(max=100)
   private String word;
}
Use @Valid
@ResponseBody
public Word addWordOfTheDay(@Valid @RequestBody Word) {
}
If validation errors occur, MethodArgumentNotValidException will be thrown.

Exceptions thrown here by the Controller methods or by the Service layer can still be handled globally by @ControllerAdvice.


when the validation rules defined on the bean properties are not sufficient, write a custom validator by specifying, for example, 
Constraint(validatedBy={AccountValidator.class})


