# GIT
## 연결하기
 * git config **global user.name "이름"
 * git config **global user.email "이메일"
 * git config user.name = 확인
 * git config user.email = 확인
 * git init => 해당폴더에 깃 권한주기

## 상태보기
 * git status

## 저장하기
 * git add *.html
 * git add *A	등등

## 커밋하기(히스토리)
 * git commit *m "기록"

## 커밋메세지 작성하기
 * 제목과 본문을 빈행으로 구분한다
 * 제목을 50글자 내로 제한
 * 제목 첫 글자는 대문자로 작성
 * 제목 끝에 마침표 넣지 않기
 * 제목은 명령문으로 사용하며 과거형을 사용하지 않는다
 * 본문의 각행은 72글자 내로 제한
 * 어떻게 보다는 무엇과 왜를 설명한다

## 기록보기
 * git log
 * git log **graph **all **decorate : 경로 나뭇가지까지 보여준다

## 과거로 되돌아가기(1) * revert : 과거 시점과 똑같은 기록을 만든다.
 * git log로 돌아가고 싶은 시점 확인하고 순서대로 revert (hashcode 6자리 정도) => 순서대로 안돌아가면 꼬인다.
 * 리눅스 명령어창 뜨면 :wq 입력
 * git revert d6fcd2

## 과거로 되돌아가기(2) * reset : 돌아가고 싶은 시점까지 그 이후 기록을 지운다. (비권장 * 거의 안쓴다.)
 * 사소한 실수 수정하고 싶을때 사용할 수 있지만 잘 안쓴다.
 * soft : 히스토리만 삭제 *> 변경했던 기록을 지우고 싶을때, 기록을 지우고 현재 작성해놓은 파일을 조금 수정하고 싶을때
 * mixed : 스테이지까지 삭제, 현재 폴더는 그대로
 * hard : 히스토리, 스테이지를 삭제하고 현재 폴더에 파일까지 복원
 * git reset d6fcd2 **(soft/mix/hard)

## 브랜치(branch)
 * 곁가지 : 수정사항을 바로 메인에 적용하는것은 위험하므로 새로운 백업에 작업하고 괜찮으면 메인에 결합한다.
 * git branch 브랜치이름  => 브랜치 생성만 한다
 * git checkout 브랜치이름 => 해당 브랜치로 이동한다. 만약 없는 브랜치이름을 입력하면 에러가 뜬다
 * git checkout *b 브랜치이름 => 새로운 브랜치 생성 후 이동
 * git log를 통해 각 브랜치의 위치를 알 수 있다.
 * git branch *d 브랜치이름 => 브랜치 삭제 (권장하지 않음)

## 머지(merge)
 * 병합 : 브랜치로 나눠서 작업하던 것을 기준 브랜치에 병합한다. 기준 브랜치에서 명령어를 입력한다.
 * git merge 가져올브랜치이름 => 기준 브랜치에 새로운 커밋으로 합쳐짐
 * 리눅스 명령어창 뜨면 :wq 입력

## 병합 충돌(conflict)
 * 두 브랜치가 동일한 데이터를 다르게 수정했다면 어떤 데이터를 우선시 해야할지 충돌이 일어난다.
 * 한쪽에서만 수정했다면 수정한 파일로 병합되어 충돌이 일어나지 않는다.
 * 충돌이 일어날 시 충돌이 일어난 데이터를 알려준다. (merging 이 출력됨)
 * 데이터를 수정을 하고 $ git add *A / $ git commit 를 입력하면 명령문 창이 뜨고 :wq 입력시 충돌이 해결된다.