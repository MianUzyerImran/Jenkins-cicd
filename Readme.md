***************************************   Details   ******************************************************** 
JDK17-Maven3.9.6-FetchCode-BuildCode-UnitTest-CheckStyleAnalysis-SonarQubeScanner-SonarQubeServer-QualityGates-UploadArtifact-SlackNotification
************************************************************************************************************

Pipeline using JDK 17 & Maven 3.9.6

Stage 1 : Fetch code from GitHub Repository using the `atom` branch.

Stage 2 : Build the application to generate build artifacts (WAR file) while skipping tests.

Stage 3 : Execute unit tests using `mvn test`.

Stage 4 : Perform Checkstyle Analysis to ensure code formatting and style guidelines.

Stage 5 : Perform Static Code Analysis using SonarQube Scanner.

Stage 6 : Send analysis results to SonarQube Server.

Stage 7 : Enforce Quality Gates to validate code quality against defined thresholds.
           - Pipeline will abort if quality gates are not passed within 2 minutes.

Stage 8 : Upload WAR artifact to Nexus Repository using `nexusArtifactUploader` (a plugin).

Post Actions:
✔️ On Success  : Send Slack notification to `#jenkins-cicd` channel with green status.
❌ On Failure  : Send Slack notification to `#jenkins-cicd` channel with red status.
