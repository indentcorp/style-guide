# Github

## 저장소 네이밍

```
<service>-<role>-<platform(Optional)>
```

##### service

| service | description | acronym |
| --- | --- | --- |
| vreview | 브이리뷰 | vreview |
| vreviewx | 브이리뷰 X | vreviewx |  
| vreview 2.0 | 브이리뷰 2.0 | vreview-2 |
| openapi | Open API 서비스 | openapi |



##### role

| role | description | acronym |
| --- | --- | --- |
| service | 서비스 | service |
| backend server | 백엔드 서버 | backend |
| web frontend | 웹 프론트엔드 | frontend |
| database | 데이터베이스 | database |
| … |  |  |



##### platform (Optional)

| platform | description | acronym |
| --- | --- | --- |
| springboot | 스프링부트 | springboot |
| python3.x | 파이썬 3.x | py |
| Golang | Go 언어 | go |
| vue.js | vue.js | vue |
| terraform | 테라폼 | terraform |
| tensorflow | 텐서플로우 | tf |
| … |  |  |


## 마이크로서비스 저장소
### 문서화
1. 아키텍쳐 다이어그램을 첨부하여야 한다.
1. 초기 설정에 대한 가이드가 포함되어야 한다.
1. local 환경에 대한 테스트 방식에 대한 가이드가 포함되어야 한다.
1. aws 에서의 개발/테스팅 환경에 대한 가이드가 포함되어야 한다.

## 브랜치
### 원칙
####  No JIRA, No Branch
Jira 이슈가 없이 브랜치 만들기 및 PR을 금지합니다.


- `main`
    - 직접 commit 을 금지합니다. 오직 PR로만 가능합니다.

- `develop`


- `master`

- `feauture/<ISSUE-KEY>-<DESCRIPTION (OPTIONAL)>`

- `hotfix/<ISSUE-KEY>-<DESCRIPTION (OPTIONAL)>`



## 커밋
### 원자적 커밋 (Atomic Commit)
![Commit bad example](assets/commit-bad-example.png){ loading=lazy }

1. 제목과 본문을 빈 행으로 분리한다.
1. 제목 행을 50자로 제한한다.
1. 제목 행 첫 글자는 대문자로 쓴다.
1. 제목 행 끝에 마침표를 넣지 않는다.
1. 제목 행에 명령문을 사용한다.

    > If applied, this commit will 'COMMIT-MESSAGE'

1. 본문을 72자 단위로 개행한다.
1. 어떻게 보다는 무엇과 **왜**를 설명한다.

```
$ git log
commit 42e769bdf4894310333942ffc5a15151222a87be
Author: Kevin Flynn <kevin@flynnsarcade.com>
Date:   Fri Jan 01 00:00:00 1982 -0200

 Derezz the master control program

 MCP turned out to be evil and had become intent on world domination.
 This commit throws Tron's disc into MCP (causing its deresolution)
 and turns it back into a chess game.

```

```
$ git log --oneline
42e769 Derezz the master control program
```

```
$ git shortlog
Kevin Flynn (1):
      Derezz the master control program

Alan Bradley (1):
      Introduce security program "Tron"

Ed Dillinger (3):
      Rename chess program to "MCP"
      Modify chess program
      Upgrade chess program

Walter Gibbs (1):
      Introduce protoype chess program

```


## Pull Request
- Pull Request 의 커밋 관리는 `commit --amend` 와 `rebase` 를 적극 활용합니다.
- Pull Request 가 Merge 된 경우, 해당 브랜치를 삭제하도록 합니다.
- 모든 대화가 해결되어야 Pull Request 를 완료할 수 있습니다.
