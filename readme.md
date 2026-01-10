# git rebase

내 작업 브랜치의 base를 현재 오리진의 최신 브랜치 끝으로 이동시킨다.

## merge와 rebase 비교

Merge: 여러 명이 사용하는 공용 브랜치(main, dev)를 합칠 때 사용하세요. 히스토리가 복잡해도 누가 언제 합쳤는지 기록을 남기는 것이 중요할 때 유리합니다.

Rebase: 개인 작업 브랜치에서 최신 메인 코드의 변경 사항을 가져올 때 사용하세요. 히스토리를 한 줄로 유지하여 리뷰어가 작업 흐름을 파악하기 좋게 만들어줍니다.

## 충돌

Merge: 충돌을 해결하고 commit하면 끝입니다.

Rebase: 내 커밋을 하나하나 순서대로 다시 쌓는 방식입니다. 그래서 만약 내가 3개의 커밋을 만들었다면, 첫 번째 커밋에서 충돌 해결 -> 두 번째 커밋에서 충돌 해결... 이런 식으로 여러 번 충돌을 해결해야 할 수도 있습니다.

# squash merge

모든 커밋 내용의 나열이 아닌 내 커밋들을 하나의 새로운 커밋으로 합쳐서 머지를 한다.

# git fetch

원격 브랜치의 최신 내용을 내 로컬의 임시 공간에 업데이트만 해둔다.(실제 파일에는 영향이 없다.)

## 변경사항 미리보기

합치기 전에 도대체 남들이 무엇을 수정했는지 검토할 수 있습니다.

비교 명령어: git diff main origin/main

이 명령어를 치면 "내 로컬 main"과 "서버의 최신 상태(origin/main)" 사이에 어떤 코드 차이가 있는지 미리 볼 수 있습니다.

커밋 확인: git log main..origin/main

내가 아직 받지 않은 커밋 메시지들만 골라서 확인할 수 있습니다.

## 어떻게 봐야 효과적으로 fetch를 사용하는 거지?

1. 파일 목록만 먼저 보기 (--name-only)
   어떤 파일이 바뀌었는지 이름만 훑는다

```
git diff feature-a origin/branch --name-only
```

2. 특정 파일만 콕 집어서 비교
   목록에서 내가 수정한 파일이 겹치는 것을 발견 했다면 그 파일만 비교

```
git diff feature-a origin/branch -- {파일 명}
```

3. 통계로 상황 파악하기 (--stat)
   어느 파일이 얼마나 많이 바뀌었는지 요약해서 보고 싶을 때

```
git diff feature-a origin/branch --stat
```

4. 커밋 단위로 파악하기 (Log 활용)
   코드 한 줄 한 줄 비교하기 전 어떤 작업들(커밋)이 서버에 추가 되었는지 제목만 읽는 것

```
git log feature-a..origin/branch --oneline --graph
```

# rebase flow

```
git fetch origin/branch # 최신 서버 정보를 로컬의 origin/branch에만 업데이트
git rebase origin/branch # 내 브랜치를 origin/branch 기준으로 리베이스 한다.
```

1단계: Rebase 전 (현 상태)
A 개발자가 dev에 새로운 커밋을 반영해서, B 개발자의 시작점(Base)이 뒤처진 상태입니다.

(A의 작업들)
dev: A1 --- A2 --- A3 (origin/dev)
--------\\
dev-page: B1 --- B2 --- B3 (내 로컬 작업)

2단계: git fetch origin
서버의 최신 데이터만 가져옵니다. 내 파일은 그대로지만, 로컬 저장소 내의 origin/dev 포인터가 최신 지점(A3)을 가리키게 됩니다.

3단계: git rebase origin/dev
내 작업(B1, B2, B3)의 뿌리를 origin/dev의 끝인 A3로 옮깁니다. 이때 커밋들의 해시값이 변하며 새로운 커밋(B1', B2', B3')으로 재생성됩니다.

dev: A1 --- A2 --- A3 (origin/dev)
--------\\
dev-page: B1' --- B2' --- B3' (HEAD)

4단계: PR 승인 후 Squash Merge (GitHub)
dev-page의 모든 커밋(B1', B2', B3')을 하나로 뭉쳐서 dev 브랜치에 하나의 새로운 커밋(B-Total)으로 합칩니다.

dev: A1 --- A2 --- A3 --- B-Total (Merge 완료!)

- git rebase를 했을 때 conflict발생시 origin/branch의 코드로 rebase를 처리해도
  git log를 보면 내가 작업한 커밋 메시지가 남는다.
  ex)
  origin/branch
  text.txt
  line 1: add text

feature-a
text.txt
line 1: add txt local

이 경우 git rebase로 origin/branch로 conflict를 수정 했다면

git log에서
commit: [add local] add txt local
|
commit: [add origin] add text
로 확인이 된다.

이것은 내 커밋의 뿌리를 origin/branch의 최신으로 옮기고 그 뒤로 내 커밋을 다시 기록하는 것이기 때문에 내 커밋 기록이 그대로 남는다.
