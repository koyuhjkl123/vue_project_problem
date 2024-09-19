# vue.js 프로젝트 문제 해결
이 문서는 필자가 직접 진행한 Vue.js 프로젝트에서 발생한 문제와 그 해결 과정을 기록하기 위해 작성되었습니다

<br>
<br>

# 1. 글자수 세기 적용 안됨

<br>

## 1-1 문제
- 실시간으로 글자 수 세기는 잘 작동하지만, 임시 저장 후 나갔다가 다시 들어오면 글자 수가 0으로 초기화되는 현상 발생.
- ![문제](https://github.com/user-attachments/assets/683fd16e-45dd-47a6-a496-13ab5ee562ed)
<br>

## 1-2 원인
- textareas 태그에 입력받은 값이 상태 관리에서 제대로 업데이트되지 않아 글자 수가 초기화됨.
<br>

## 1-3 해결
- this.characterCount[index+1] = this.textareas[index+1].length;
- 코드를 추가하여, 텍스트 영역의 값을 업데이트한 후 글자 수를 저장하도록 수정함. 이로 인해 페이지 재방문 시에도 입력된 글자 수가 올바르게 표시되도록 개선됨.

## 1-4 느낀점
- 처음으로 프로젝트를 참여하면서 코드가 굉장히 길고 복잡하다고 느껴짐
- 각 메서드의 순서와 어떤 값이 나오는디 console을 찍으면서 디버깅을 하다보니 문제를 해결 할 수가 있었다



# 2. 증명사진 첨부 오류

<br>

## 2-1 문제
- 증명사진을 넣고 임시저장 후 나갔다가 다시 들어오면 "파일을 선택하세요" 파일 값이 초기화되는 현상 발생
- ![문제](https://github.com/user-attachments/assets/995f5045-1650-4d4f-91dd-00ab0be75352)


## 2-2 원인
- input file이 페이지가 이동되면 보안상 값이 초기화 되면서 "undefined"으로 됨
