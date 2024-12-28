**Maven** is both a container and configuration for projects. It's a build tool. Build tools help us build apps more easily by:
- downloading dependencies
- running tests
- compiling the app
- packaging source code in an executable

A typical Maven project.
![[mvn-project-configuration.png]]

1. Install Maven
```sh
brew install maven
apt install maven
```

2. Validate Maven project
```sh
mvn validate
```

3. Clean target folder
```sh
mvn clean
```

4. Compile source code
```sh
mvn compile
```

5. Run tests
```sh
mvn test
```

6. Create build artifact
```sh
mvn package
```