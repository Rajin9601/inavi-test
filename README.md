# inavi-test

해당 repo 는 iNavi SDK 와 ViewBinding 을 같이 사용했을 때, 생기는 버그 재현용 repo 입니다.

## 재현 환경

openjdk version 11

## 재현 방법 

```sh
./gradlew app:assembleDebug
```

에러 로그

```
> Task :app:compileDebugKotlin FAILED
e: Supertypes of the following classes cannot be resolved. Please make sure you have the required dependencies in the classpath:
    class com.inaviair.sdkEngine.TWMapConstraintLayout, unresolved supertypes: com.inaviair.sdkEngine.ITWGuideComponent
```

만약 해당 커맨드를 [remove-viewBinding](https://github.com/Rajin9601/inavi-test/tree/remove-viewBinding) 브랜치에서 실행하면 정상적으로 컴파일이 된다.
해당 브랜치와 main branch 와의 차이점은 [ViewBinding 객체를 사용했는지 여부](https://github.com/Rajin9601/inavi-test/commit/c7ea70a84702e704b61310410da05ddd0f85a67e)이다.

## 추측

Android Studio 로 TWMapConstraintLayout.class 를 따라가면 kotlin 상으론 `implements ITWGuideComponent` 라고 뜨지만
Decompile to java 를 통해 보면 `implements i` 라고 뜬다.
아마 Metadata annotation 상으론 ITWGuideComponent 라고 뜨는데 jar 파일상으론 `i` 로 난독화된것 같다.
이렇기 때문에 ViewBinding 이 헷갈린게 아닐까 생각한다.

관련 자료 : [Shrinking Kotlin libraries and applications using Kotlin reflection with R8 | by Morten Krogh-Jespersen | Android Developers | Medium](https://medium.com/androiddevelopers/shrinking-kotlin-libraries-and-applications-using-kotlin-reflection-with-r8-6fe0a0e2d115)
