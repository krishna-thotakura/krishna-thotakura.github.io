## Spring Data JPA Quirk - Access a single column in an entity directly

With Spring Data JPA, you cannot access a single column in an entity directly using Spring's Query creation and property expression API.

Their justification for leaving this out is that we need to deal with entities, and not individual columns. Although i agree with this to some extent, i find it impractical at times.

I want to get distinct values of a column for display in the UI. There is no reason for me to get more than what i need from the database.

Hence, in such situations, I resort to writing the @Query. Note: This is Spring data JPA annotation.

@Query("select e.firstName from Eyes e where e.color = ?1)
List<String> findGreenEyedPeople(String favColor);

Named parameter notation is not so succinct. Hence, i prefer positional parameters. With named parameters it would look like below.

@Query("select e.firstName from Eyes e where e.color = :myColor)
List<String> findGreenEyedPeople(@Param("myColor") String favColor);
