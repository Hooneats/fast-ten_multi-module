# build.gradle


    /*
    api가 아닌 implementation로 선언하면
    dev.be.moduleapi.service.DemoService.memberRepository가 JpaRepository의 메소드를 사용하지 못한다.
    */

    /*
    api 키워드를 사용하기 위해선 2가지 중 1가지 방법으로 선언을 해야 한다.
    1. api를 사용하려는 build.gradle의 plugins에 `id 'java-library'` 추가
    2. 상위 모듈(=root)에서 'java-library' 추가
    ex) subprojects {
            apply plugin: 'java-library'
        }
     */

---

    // api 라는 키워드 사용하기 위해서는 "id 'java-library'" 추가 -> api 는 의존성까지 타 모듈에서 사용가능하게 한다.
#### api 'org.springframework.boot:spring-boot-starter-data-jpa'
#### implementation 'mysql:mysql-connector-java'

---

    // bootJar 는 기본값 true , true 인 경우 xxx.jar 파일이 생성된다.
    // 이러한 jar 파일은 파일안에 어플리케이션을 실행하기위한 의존성, 클래스, 리소스들을 포함하고있기에 java -jar 옵션으로 jar 파일을 실행할 수 있다.
    // 그러나 common 모듈은 다른 모듈에서 참조하는 목적이기에 실행가능한 jar 를 만들 필요가 없다.
    // 만약 bootJar 를 true 로 설정하면 main 클래스를 찾게되는데 common 모듈에는 main 파일이없어 에러가 난다.
#### tasks.bootJar {enabled = false}

---

    // jar 옵션도 기본값은 true 인데, jar 옵션이 true 인 경우 xxx-plain.jar 파일이 생성한다.
    // plain.jar 파일은 의존성을 포함하고 있지 않다. 클래스와 리소스만 갖고있기에 plain 의 경우 서버를 실행시킬 수 없다.
    // 그러므로 common 모듈에서는 plain 파일이 필요하지만 plain 이 없는 jar 파일은 필요없다.
#### tasks.jar {enabled = true}

---


    // 그렇다면 어떻게 빌드를 할 수 있을까? 다음 명령어로 빌드하면 된다.
    // ./gradlew clean :module-api:buildNeeded --stacktrace --info --refresh-dependencies -x test
    // clear: 기존 빌드 폴더 날림
    // :module-api:buildNeeded : api 모듈 빌드할 것임
    // --stacktrace : stacktrace 또한 보여달라
    // --info : 로깅 레벨 설정 info 레벨 이상으로 설정하겠다. (debug -> info -> warn -> error)
    // --refresh-dependencies : 의존성을 refresh 한다.
    // -x test : 테스트 코드는 스킵

---


/*
[tasks.bootJar 기본 값 : true]
`bootJar` 옵션을 true로 설정하면 'xxx.jar' 파일이 생성된다.
ex) module-common-0.0.1-SNAPSHOT.jar
이렇게 생성된 'jar' 파일은
그 파일 안에 Application을 실행시키는 데 필요한
[dependencies / classes / resources]을 포함하고 있어
`java -jar` 옵션으로 jar 파일을 실행시킬 수 있다.
그런데 Common Module은
다른 Module에서 참조하는 목적의 Module이므로
실행 가능한 jar 파일을 생성할 필요가 없다.
그러므로 `ModuleCommonApplication.class`는 존재할 필요가 없다.
그러므로 Common Module에서는
bootJar 옵션값을 false로 설정한다.
만약 bootJar 옵션을 true로 주면
Main.class를 찾게 되는데
Common Module에서는 Api Module처럼
ModuleApiApplication.class가 없으므로 다음과 같은 에러가 발생한다.
ex) Caused by: org.gradle.api.InvalidUserDataException: Main class name has not been configured and it could not be resolved
*/

/*
[tasks.jar 기본 값 : true]
`jar` 옵션을 true로 설정하면 'xxx-plain.jar' 파일이 생성된다.
ex) module-common-0.0.1-SNAPSHOT-plain.jar
이렇게 생성된 '-plain.jar' 파일은 'jar' 파일과는 다르게
'dependencies'를 제외한 [classes / resources]만을 포함하고 있어
`java -jar` 옵션으로 jar 파일을 실행시킬 수 없다.
Common Module에서는 Api Module에서 사용할
[classes / resources]만 존재하면 되므로
jar 옵션값을 true로 설정한다.
*/

/*
[참고]
- root project에서 Gradle 빌드 명령어를 실행한다.
  ex) pwd 입력 시 "$HOME/xxx/yyy/Spring-Boot-2.7.1-Multi-Module-Template"
- Api module을 실행시킬 jar 파일이 생성된 경로로 이동한다.
  ex) cd module-api/build/libs/
- 해당 Path로 이동 후 java -jar 명령어를 실행한다.
  ex) java -jar module-api-0.0.1-SNAPSHOT.jar
  [Multi Module에서 Gradle 빌드 명령어 + jar 파일 실행 CLI]
- Gradle 빌드 명령어 :: root project
  -> ./gradlew clean :module-api:buildNeeded --stacktrace --info --refresh-dependencies -x test
- profile 지정 X
  -> java -jar module-api-0.0.1-SNAPSHOT.jar
- profile 지정 O
  -> java -jar -Dspring.profiles.active=local module-api-0.0.1-SNAPSHOT.jar
*/

---

      그렇다면 이렇게 생성한 jar 파일을 어떻게 실행할까?
      -> 먼저 plain 이 아닌 jar 파일이 있는 위치로 이동하자
      cd module-api/build/libs
      ls 명령어로 jar 파일 이름확인
      java -jar module-api-0.0.1-SNAPSHOT.jar
      확인 완료 했다면 다시 루트로 돌아가 clean 실행해 주자 
      cd ../../..
      ./gradlew clean

---

### java -jar -Dspring.profiles.active=local  module-api/build/libs/module-api-0.0.1-SNAPSHOT.jar

실행했는데 다음과 같이 에러가 난다면

### Unable to access jarfile .profiles.active=local

실행 옵션을 쌍따옴표를 묶어 문자열임을 명시해주자

### java -jar "-Dspring.profiles.active=local"  module-api/build/libs/module-api-0.0.1-SNAPSHOT.jar

---
