***************************************   Details   ******************************************************** 
JDK17-Maven3.9-FetchCode-BuildCode-UnitTest-CheckStyleAnalysis-SonarQubeScanner-SonarQubeServer-QualityGates
************************************************************************************************************

Pipe Line using JDK 17 & Maven 3.9
Stage 1 : Fetch Code from GitHub Link and atom Branch.
Stage 2 : Build code using mvn & -DskipTests to skip tests while building artifacts.
Stage 3 : Unit test the code using mvn test.
Stage 4 : Check Style Analysis using mvn checkstyle.
Stage 5 : Use SonarQube Scanner tool for Code Analysis.
Stage 6 : Use SonarQube Server for Code Analysis result.
Stage 7: Use Quality Gates for custom quality standards 
