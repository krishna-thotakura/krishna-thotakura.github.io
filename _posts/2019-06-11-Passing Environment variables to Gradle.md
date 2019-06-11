To pass environment variables in Gradle

`./gradlew bootRun --args='--spring.profiles.active=dev --management.server.port=8083'`

Prior to Gradle 4.9, you would have to export the variables, or use gradle plugins, but now this is much easier with Gradle 4.9+.
