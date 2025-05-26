***************************************   Details   ******************************************************** 
JDK17-Maven3.9.6-FetchCode-BuildCode-UnitTest-CheckStyleAnalysis-SonarQubeScanner-SonarQubeServer-QualityGates-BuildDockerImage-UploadDockerImage-CleanupDockerImages
************************************************************************************************************

Pipeline using JDK 17 & Maven 3.9.6

Stage 1 : Fetch code from GitHub Repository using the `atom` branch.

Stage 2 : Build the application to generate build artifacts (WAR file) while skipping tests.

Stage 3 : Execute unit tests using `mvn test`.

Stage 4 : Perform Checkstyle Analysis.

Stage 5 : Perform Static Code Analysis using SonarQube Scanner (version 6.2).

Stage 6 : Send analysis results to SonarQube Server.

Stage 7 : Enforce Quality Gates to validate code quality against defined thresholds.
           - Pipeline will abort if quality gates are not passed within 1 hour.

Stage 8 : Build Docker Image.

Stage 9 : Upload Docker Image to Amazon ECR.

Stage 10 : Cleanup local Docker images to free space.
