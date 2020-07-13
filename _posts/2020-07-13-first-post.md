---
title: "git ignore에 대한 고찰"
date: 2017-10-20 08:26:28 -0400
categories: GIT
---
Git의 핵심 목적
버전관리
백업
협업
 

1. 버전관리
파일이 변경되었을때 변경사항들을 버전으로 만들어서 관리하는것.


버전관리 사이클
 

init

현재 디렉토리를 git을 통해 버전관리를 요청한다.
버전이 저장되는 저장소(repository)를 생성하는 명령어
git init
status

git을 통해 관리하고 있는 디렉토리의 상태를 묻는 명령어
git status
add

working-directory의 수정사항을 버전으로 만들기 위해 staging-area로 올리는 명령어
// [파일이름]을 staging-area 올린다.
git add [파일이름]

// 모든 수정된 file을 staging-area에 올린다.
git add .
commit

staging-area에 올라와 있는 수정사항을 저장소(repository)로 보내서 버전으로 만드는 명령어
git commit

// -m : 커맨드라인에서 직접 commit 메시지 적기
git commit -m "commit message"

// -am : add & commit 메시지 적기
// -am 설정은 Untracked 파일에는 사용할 수 없기 때문에 최초 1번의 add 명령어로 
// staging-area에 올려서 tracked 상태가 되어야 한다.
// 추적되지 않은 파일이 추적되는 것을 막을수 있다.
git commit -am "commit message"

// --ammend : 마지막 commit 메시지 수정하기
git commit --ammend
log

commit 내역을 확인하기 위한 명령어
git log

// -p : commit 상세 내용 보기
git log -p

// --oneline : commit 내역 한줄로 보기
git log --oneline

// --all : 모든 branch의 로그 보기
git log --all

// --graph : 그래프 형식으로 로그 보기
git log --graph
diff

difference의 약자로 수정된 working-directory의 수정된 차이점을 보여주는 명령어
마지막 commit 버전과 working-directory의 차이점을 통해서 commit하기 전에 최종적으로 검토할 수 있다.
git diff
checkout

commit 버전을 만든 시점으로 돌아가는 명령어
// commit ID값 입력
git checkout b322b7a993d1a98ac132319667509097e7674cd6

// 해당하는 branch로 이동
git checkout [branch 이름]

자동으로 생성되는 commit ID값
reset

commit ID값을 통해 해당하는 버전으로 리셋하는 명령어(선택한 버전이 되겠다.)
선택된 버전 이후의 commit 버전은 모두 사라진다.
git reset --hard 3a1489b8f528bd3b4dd7593a61156b005a5aab5e
 

staging-area에 올라오기 전 working-directory에 존재하는 변경사항을 마지막 버전으로 되돌리는 명령어
git reset --hard
revert

선택한 commit 버전의 전 버전으로 되돌리는 명렁어(선택한 버전의 이전 버전으로 되돌리겠다.)
reset의 경우 선택한 버전이 되서 그 이후의 커밋 버전을 지우지만 revert는 선택한 버전의 이전 버전을 복사해서 새로운 커밋 버전으로 생성하는 개념이다.
git revert 28a3d81b47044ca203affd2082c929b235f44cb2

3개의 커밋 내역
git log를 통해 확인한 commit 내역이다. 위 내역에서 커밋 메시지 "R3"를 revert 한다면 해당 커밋의 이전의 커밋으로, 즉 커밋 메시지 "Message 3"의 상태로 되돌아 간다. revert는 코드의 상태가 되돌아가지만 커밋 메시지 "R3"에 대한 내역은 사라지지 않고 새롭게 커밋을 생성하는 개념이다.

 

만약 커밋 메시지 "Message 2" 로 가기 위해서는 바로 revert Message 3를 해야할 것 같지만, 역순으로 작업을 해야한다. 즉, revert R3 를 하고 revert Message 3를 해야 Message 2로 돌아갈 수 있는 것이다.

 

2. Branch & Conflict
branch

브랜치 목록을 확인하는 명령어
기본은 master 브랜치 위에서 작업을 하게된다.
// branch 목록 보기
git branch

// branch 생성
git branch [생성할 branch 이름]
checkout

해당 브랜치로 이동하는 명령어
// HEAD 브랜치 이동
git checkout [branch 이름]

// commit 값으로 이동(detacted)
git checkout [commit ID값]
 

 

저장소를 생성하면 기본적으로 HEAD가 만들어지고 기본적으로 master branch를 가리킨다. checkout은 HEAD의 값을 바꾸는 기능이다.  만약 checkout 명령어를 통해 브랜치가 아닌 commit ID값을 입력하면 브랜치로부터 떨어져있다는 detacted되 있다고 한다.

merge

브랜치를 병합하기 위한 명령어
병합을 하기 위해서 우선 master branch 상태가 되어야 한다.
// 병합하기전 master branch 이여야 한다.
git checkout master

git merge [병합할 branch 이름]
충돌(conflict)

같은 라인에서 각각의 수정이 있는 파일을 병합(merge)하였을 경우 발생하는 충돌.

충돌 발생 로그
 


merge 이후 충동이 일어난 파일의 내부
'<<<<<' 에서 '=====' 사이의 내용은 HEAD, 즉 현재 브랜치의 내용을 나타내고,

'=====' 에서 '>>>>>' 사이의 내용은 w3 브랜치의 내용을 나타낸다.

git은 같은 라인에서 브런치 각각의 수정을 자동으로 병합할 수 없으므로 사용자에게 보기를 주고 선택하도록 하는 것이다.

 

3. backup
remote

지역저장소(Local Repository)의 파일을 원격저장소(Remote Repository)와 연결하는 명령어
// origin은 원격저장소 별칭으로 origin을 기본으로 한다.
git remote origin [원격저장소에 생성한 주소 URL]

// 연결된 원격 저장소 확인
git remote -v
clone

원격저장소(Remote Repository)의 파일을 복제해서 지역지역소(Local Repository)를 만드는 명령어
원격저장소의 이름을 딴 폴더가 생성되고 폴더 안에서 프로젝트가 git을 통해 관리된다.
git clone [원격저장소에 생성한 주소 URL]
push

지역저장소(Local Repository)에 존재하는 commit된 파일들을 원격저장소(Remote Repository)에 밀어넣는 명령어
git push
pull

원격저장소(Remote Repository)의 파일을 지역저장소(Local Repository)에 당겨오는 명령어
git pull
 

 

4. collaboration
fetch

원격저장소만 업데이트하는 명령어
merge 명령어를 통해 업데이트 된 파일과 병합해야 한다. 신중하게 파일을 pull하고 싶을때 사용된다. 
즉, pull = fetch + merge
git fetch

// 가장 최신의 커밋을 병합해주는 명령어
git merge FETCH_HEAD
