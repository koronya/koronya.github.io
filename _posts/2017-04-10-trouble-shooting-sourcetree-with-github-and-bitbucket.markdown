---
layout: post
title:  "SourceTree에서 github, bitbucket이 안될 때 해결법"
date:   2017-04-10 20:20:17 +0900
categories: troubleShooting
tags: [troubleShooting, sourcetree, github, bitbucket]
---


# 들어가며

macOS를 쓰고 있는데  
갑자기 sourcetree 에서 github와 bitbucket에 대한 연결이 끊겼고  
따라서, 로컬이 아닌 원격 메뉴를 누르면  
아무것도 뜨지 않는 현상이 발생하였다.

또한, 이 상황에서 이 remote repository로 push도 안되는 현상이 발생하였다.

따라서, 이를 github와 bitbucket 두개 따로 이슈를 해결해보자.


# github

github의 경우에는 IntelliJ나, bash에서 git command를 직접 입력해서 push는 잘 되고 있었다.  
sourcetree에서만 안되고 있었는데  
이를 해결하기 위해서  
일단, 현재 쓰고 있는 sourcetree 프로그램을 update하자.  
본인은 기존에 sourcetree 2.3.1(69)을 사용하고 있었는데(MAC 버전)  
왜 인지는 모르겠지만, sourcetree 프로그램은 update 확인... 을 눌러도  
update가 되지 않아서, 홈페이지[^2]에 가서 최신 버전을 받아주었다.  
이 글을 쓰는 현재의 최신 버전은 맥 기준 2.4.1(97)  
update를 하고 실행시킨 뒤에  
sourcetree의 설정을 눌러보면(⌘,)  
전에는 없던 계정이라는 탭이 생긴 것을 볼 수 있다.  
이 계정 탭에 자신의 github, bitbucket 계정이 설정되어 있을텐데  
github 계정을 일단 지우고  
다시 github 계정을 추가하면  
인증방식에 전에는 없던 OAuth가 생긴 것을 볼 수 있다.  
프로토콜은 HTTPS로 설정하고, OAuth를 클릭하고  
사용자 이름: 옆에 Connect Account를 누르면

![alt text]({{ site.url }}/images/2017-04-11_koro_1.png)


github의 로그인 창이 뜨고, 각종 정보를 잘 입력하고 나면

![alt text]({{ site.url }}/images/2017-04-11_koro_2.png)


위와 같이 사용자 이름:  
옆에 사용자의 ID가 정상적으로 나오게 된다.

sourcetree에서의 github 오동작은 이렇게 수정 끝!!!



# bitbucket

bitbucket은 위와 같이 하더라도  
된 것 같아보이지만  
실제 원격 저장소를 눌러보면  
GitHub만 제대로 나오고  
bitbucket은 아래와 같은 에러 메시지를 뱉으면서 되질 않는다.

![alt text]({{ site.url }}/images/2017-04-11_koro_3.png)

홈페이지에서 two-step verification 설정어쩌구 저쩌구 하는데  
two-step verification을 설정하면 HTTPS를 못쓰고, SSH만 써야 된다고 해서  
그것을 설정하지는 않았다.  
또한, 홈페이지[^1]에서 보여주는 GIF 설명 이미지를 봐도  
SSH가 아닌 HTTPS로 설정하는 것을 봐서  
bitbucket에서 two-step verification을 설정하지는 않고 진행하였다.  
기존에 HTTPS로 연결해놓은 것들을 일일이 다 바꾸기 성가셔서  
또한, 바꾼다고 된다는 보장도 없고.

![alt text]({{ site.url }}/images/2017-04-11_koro_4.png)

그래서 키체인 접근에 들어가서 찾기에서 bitbucket을 누르면  
여러개가 나오는데 그 중  
아래 그림에서 선택된 SourceTree application에 저장된 bitbucket의 암호를 삭제하고

![alt text]({{ site.url }}/images/2017-04-11_koro_5.png)

다시 SourceTree를 실행해서 SourceTree에 대한 로그인 정보를 다시 입력해주면  
SourceTree의 원격 탭에서도 bitbucket이 잘 나오는 것을 볼 수 있...으면 좋으련만 그렇지는 않고  
대신에 IntelliJ나 bash에서 bitbucket으로 push가 잘 되는 것을 확인할 수 있었다.

정확히 위 작업 때문에 그렇게 된 것인지는 모르겠지만  
여기저기 인터넷 찾으면서 이것저거 하다가 된 것이라  
이렇게 기록으로 남겨둔다.


# Reference

[^1]: <https://blog.sourcetreeapp.com>
[^2]: <https://www.sourcetreeapp.com>
