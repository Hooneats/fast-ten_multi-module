# build.gradle

    // module-common -> settings.gradle 파일이 중복일 경우 더 자세한 경로의 우선순위를 갖기에 현재 모듈의 settings.gradle 삭제해야함.
#### implementation project(':module-common') // root project -> settings.gradle 에 선언한 값과 동일해야함.

---

    // ERROR 시 추가 "org.gradle.execution.TaskSelectionException: Task 'prepareKotlinBuildScriptModel' not found in project ':module-api'"
#### tasks.register('prepareKotlinBuildScriptModel'){}

---

    // "mainClassName = 'xxx''" mainClassName 지정해 주는게 아래와 같이 바뀌었다.
### tasks.bootJar {
### enabled = true
###     mainClass.set('dev.be.moduleapi.ModuleApiApplication')
### }

---

### Profile
ex) local, dev, test, prod
환경별로 DB 또는 설정을 다르게 가져가야할 경우 
프로젝트에서는 다음과 같은 파일로 구분한다.
application-{env}.yaml
ex) application-local.yaml , application-dev.yaml

참고 url : https://docs.spring.io/spring-boot/docs/2.1.9.RELEASE/reference/html/boot-features-external-config.html
