# Notes

연습하며 정리한 내용.

## 머지 전략

| 전략 | 결과 | 쓸 만한 때 |
| --- | --- | --- |
| **Merge commit** | 브랜치의 커밋이 모두 남고 머지 커밋이 하나 추가됨 | 작업 맥락을 그대로 보존하고 싶을 때 |
| **Squash** | 브랜치의 커밋들이 하나로 합쳐져 main에 올라감 | 커밋이 잘게 쪼개져 지저분할 때 |
| **Rebase** | 머지 커밋 없이 일렬로 붙음 | 히스토리를 직선으로 유지하고 싶을 때 |

Squash와 Rebase는 원래 커밋 해시가 사라지므로, 이미 남이 받아간 브랜치에는 쓰지 않는다.

## gh CLI

```bash
gh pr create --title "..." --body "..."   # 현재 브랜치로 PR 생성
gh pr create --reviewer <user>            # 리뷰어 지정까지 한 번에
gh pr merge <번호> --merge --delete-branch
gh pr list --state merged
gh issue create --title "..." --body "..."
gh issue close <번호> --reason completed
```

`gh api`로 REST를 직접 두드릴 수도 있다. 엔드포인트 앞에 `/`를 붙이면 Git Bash가 경로로 오해해 깨지므로 빼고 쓴다.

```bash
gh api repos/<owner>/<repo> --jq '.description'
gh api -X PATCH repos/<owner>/<repo> -F private=true
```

## 협업자 초대

리포 소유자가 아닌 계정이 PR을 올리고 머지까지 하려면 write 권한이 필요하다.

```bash
gh api -X PUT repos/<owner>/<repo>/collaborators/<user> -f permission=push
gh api user/repository_invitations              # 초대받은 쪽에서 확인
gh api -X PATCH user/repository_invitations/<id>  # 수락
```

## 삽질 기록

- Windows에서 `git push`가 `unable to get local issuer certificate`로 실패 → `git config http.sslBackend schannel`로 Windows 인증서 저장소를 쓰게 하면 해결. 검증을 끄는 게 아니라 CA 출처만 바꾸는 것이라 안전하다.
- PowerShell에서 따옴표로 시작하는 경로를 실행할 땐 호출 연산자가 필요하다: `& "C:\경로\프로그램.exe" 인자`

## 작업 중인 변경을 잠시 치워둘 때

브랜치를 급히 바꿔야 하는데 커밋하기는 애매할 때 `stash`를 쓴다.

```bash
git stash                 # 변경사항을 치워두고 깨끗한 상태로
git stash -u              # untracked 파일까지 포함
git stash list            # 쌓아둔 목록
git stash pop             # 가장 최근 것을 꺼내며 목록에서 제거
git stash apply stash@{1} # 특정 항목을 꺼내되 목록에는 남김
```

`pop`은 충돌이 나면 스태시가 목록에 남으니, 충돌을 해결한 뒤 `git stash drop`으로 직접 지워야 한다.
