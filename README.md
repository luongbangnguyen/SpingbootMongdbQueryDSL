# SpingbootMongdbQueryDSL
Project using spring boot and query dsl
Some time you want custom reposity mongodb of spring framework, queryDsl is a choice perfectly 
reference http://www.querydsl.com this project use gradle and springboot
int file build.gradle
```
buildscript {
    ext {
        springBootVersion = '1.2.4.RELEASE'
    }
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}") 
        classpath("io.spring.gradle:dependency-management-plugin:0.5.1.RELEASE")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'spring-boot' 
apply plugin: 'io.spring.dependency-management' 

jar {
    baseName = 'demo'
    version = '0.0.1-SNAPSHOT'
}
sourceCompatibility = 1.8
targetCompatibility = 1.8

repositories {
    mavenCentral()
}

sourceSets {
     generated
}

sourceSets.generated.java.srcDirs = ['src/main/generated/java']

configurations {
     querydslapt
}

dependencies {
    compile("org.springframework.boot:spring-boot-starter-data-mongodb")
    compile("org.springframework.boot:spring-boot-starter-web")
    compile("org.springframework.boot:spring-boot-starter-actuator")
    compile('org.apache.commons:commons-lang3:3.4')
    compile('org.apache.commons:commons-collections4:4.0')
    compile('com.mysema.querydsl:querydsl-apt:3.6.4')
    compile('com.mysema.querydsl:querydsl-mongodb:3.6.4')
    testCompile("org.springframework.boot:spring-boot-starter-test") 
}

task generateQueryDSL(type: JavaCompile, group: 'build', description: 'Generates the QueryDSL query types') {
         source = sourceSets.main.java
         classpath = configurations.compile + configurations.querydslapt
         options.compilerArgs = [
                "-proc:only",
                "-processor", "org.springframework.data.mongodb.repository.support.MongoAnnotationProcessor"
         ]
         destinationDir = sourceSets.generated.java.srcDirs.iterator().next()
}
compileJava{
	dependsOn generateQueryDSL
	source generateQueryDSL.destinationDir
}

task wrapper(type: Wrapper) {
    gradleVersion = '2.3'
}

```

to custom repository you should extend class QueryDslPredicateExecutor<T>
```
interface CustomerRepository extends MongoRepository<Customer, String>,QueryDslPredicateExecutor<Customer>
```

to using QueryDsl example:
```
	BooleanBuilder builder = new BooleanBuilder();
	if (StringUtils.isNotBlank(fistname)) {
		builder.and(QCustomer.customer.firstName.eq(fistname));
	}
	
	if (StringUtils.isNoneBlank(lastname)) {
		builder.and(QCustomer.customer.lastName.eq(lastname));
	}
	return customerRepository.findAll(builder, pageable);
```
