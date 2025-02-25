# 2-3. build.gradle 의존성추가

이미 프로젝트를 생성했는데 다른거도 추가하고 싶다! 하면 어떻게 해야되는가

## Intellij Ultimate를 쓰고있다면…

build.gradle 파일에서 그냥 우클릭을 해주면 의존성을 추가해줄수 있다

![2-3.springboot-1.png](/images/2-3.springboot-1.png)

![2-3.springboot-2.png](/images/2-3.springboot-2.png)

![2-3.springboot-3.png](/images/2-3.springboot-3.png)

## Intellij Community를 쓰고 있다면…

![2-3.springboot-5.png](/images/2-3.springboot-5.png)

![2-3.springboot-6.png](/images/2-3.springboot-6.png)

예… 맥북에서 gradle일때 이런버그가 있다고..(Maven에선 괜찮다함) Artifact검색이 안된다네(극히 일부만됌)

https://youtrack.jetbrains.com/issue/IDEA-237566/Maven-Artifact-Search-doesnt-work-properly

5년이나 지난이슈인데 아직도 해결 안해준듯,,,

## MavenRepository 사이트에 가서 검색하고 직접 추가하기

https://mvnrepository.com/

![2-3.springboot-7.png](/images/2-3.springboot-7.png)

![2-3.springboot-8.png](/images/2-3.springboot-8.png)

이런식으로 나오는거 그냥 복사붙여넣기하면 된다. Intellij는 이런과정을 간소화해준거일뿐

![2-3.springboot-9.png](/images/2-3.springboot-9.png)

이제 의존성추가가 다 끝났으면 Gradle reload를 해주면 설정해준대로 의존성 관련 파일들을 다운받거나 정리한다