# worktree

하나의 저장소를 여러개의 폴더에 동시에 펼쳐놓는 기능
즉 현재 작업 중인 폴더(A)는 그대로 두고, 새로운 폴더(B)에 hotfix 브랜치만 따로 체크아웃해서 동시에 띄워 놓을 수 있다.

## 시나리오
1. 현재 폴더(A): 기능 구현 중
2. 터미널에서 명령어 입력
```bash
# ../hotfix-dir 폴더를 만들고 거기서 hotfix 브랜치를 새로 생성해서 연다
git worktree add ../hotfix-dir -b hotfix
```
3. 새 폴더(B): ../hotfix-dir 로 이동해서 핫픽스 작업 후 커밋/푸시
4. 정리: 핫픽스가 끝나면 해당 폴더를 지우고 워크트리 목록에서 삭제 (필수)
- worktree는 폴더만 지운다고 끝나는게 아니라 git의 관리목록에서도 삭제 해야 한다.
- 하지만 hotfix브랜치는 삭제되지 않고 남는다.
```bash
git worktree remove ../hotfix-dir
```

|구분 | git stash | git worktree |
|작업 방식 | 하던 일을 '잠시 치워둠' | 하던 일 '옆에 새 판을 깔음' |
|동시 작업 | 불가능 (브랜치를 옮겨야 함) | 가능 (창 두 개 띄우고 작업)|
|안전성 | 나중에 다시 꺼낼 때 꼬일 수 있음 | 각자 독립된 폴더이므로 매우 안전 |
|추천 상황 | 1~2분 내의 아주 단순한 수정 | "긴급 Hotfix, 오래 걸리는 코드 리뷰"|

## worktree할때 어느 브랜치 기준으로 따지는가
git worktree add 명령어 사용하면서 뒤에 아무런 기준점을 적지 않으면 현재 내가 있는 브랜치(현재 HEAD)기준으로 새 브랜치가 생성된다.

```bash
# 형식: git worktree add [경로] -b [새브랜치명] [기준브랜치명]
git worktree add ../hotfix-dir -b hotfix main
```
이렇게 기준 브랜치를 넣어주면 내가 다른 브랜치에서 작업 중이었더라도 hotfix는 main 브랜치 기준으로 따지게 된다.


