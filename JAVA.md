# Java

<!-- toc -->

## Which Java, Version

```
$ which java
/usr/bin/java

$ java --version
java 15.0.1 2020-10-20
Java(TM) SE Runtime Environment (build 15.0.1+9-18)
Java HotSpot(TM) 64-Bit Server VM (build 15.0.1+9-18, mixed mode, sharing)
```

## Enable Auto Reload Spring Boot (IntelliJ)

[10-steps-to-enabling-auto-reload-for-spring-boot-in-intellij](https://faun.pub/10-steps-to-enabling-auto-reload-for-spring-boot-in-intellij-230326413b68)

## Spring Boot

### To create a command line runner application

1. Spring Boot Application class should extend CommandLineRunner

2. Override the `run` method in the Application class

```java
@SpringBootApplication
public class JavaDemoApplication implements CommandLineRunner {

	public static void main(String[] args) {
		SpringApplication.run(JavaDemoApplication.class, args);
	}

	@Override
	public void run(String... args) {
		System.out.println("Hello world");
	}
}
```
