***************************************   Details   ******************************************************** 
JDK17-Maven3.9.6-FetchCode-BuildCode-UnitTest-CheckStyleAnalysis-SonarQubeScanner-SonarQubeServer-QualityGates-BuildDockerImage-UploadDockerImage-CleanupDockerImages
************************************************************************************************************

Pipeline using JDK 17 & Maven 3.9.6

Stage 1 : Fetch code from GitHub Repository using the `docker` branch.
           Repository URL: https://github.com/hkhcoder/vprofile-project.git

Stage 2 : Build the application using `mvn install -DskipTests` to generate build artifacts (WAR file) while skipping tests.
           Archive artifacts from the build directory: `**/target/*.war`

Stage 3 : Execute unit tests using `mvn test` to ensure code correctness.

Stage 4 : Perform Checkstyle Analysis using `mvn checkstyle:checkstyle` to enforce code styling standards.

Stage 5 : Perform Static Code Analysis using SonarQube Scanner (version 6.2).
           SonarQube properties configured:
           - sonar.projectKey = vprofile
           - sonar.projectName = vprofile
           - sonar.projectVersion = 1.0
           - sonar.sources = src/
           - sonar.java.binaries = target/test-classes/com/visualpathit/account/controllerTest/
           - sonar.junit.reportsPath = target/surefire-reports/
           - sonar.jacoco.reportsPath = target/jacoco.exec
           - sonar.java.checkstyle.reportPaths = target/checkstyle-result.xml

Stage 6 : Send analysis results to SonarQube Server (`sonarserver` environment).

Stage 7 : Enforce Quality Gates to validate code quality against defined thresholds.
           - Pipeline will abort if quality gates are not passed within 1 hour.

Stage 8 : Build Docker Image from `./Docker-files/app/multistage/` directory.
           - Image Tag: `<ECR-URL>/vprofile-app-img:$BUILD_NUMBER`

Stage 9 : Upload Docker Image to Amazon ECR.
           - Registry: 349334771881.dkr.ecr.us-east-1.amazonaws.com
           - Image Tags: `$BUILD_NUMBER`, `latest`

Stage 10 : Cleanup local Docker images to free space.
            Command: `docker rmi -f $(docker images -a -q)`
