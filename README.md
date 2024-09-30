# vue.js 프로젝트 문제 해결
이 문서는 필자가 직접 진행한 Vue.js 프로젝트에서 발생한 문제와 그 해결 과정을 기록하기 위해 작성되었습니다

<br>
<br>

# 1. 글자수 세기 적용 안됨

<br>

## 1-1 문제
- 실시간으로 글자 수 세기는 잘 작동하지만, 임시 저장 후 페이지를 나갔다가 다시 들어오면 textarea의 글자 수가 0으로 표시되는 현상
- ![문제](https://github.com/user-attachments/assets/683fd16e-45dd-47a6-a496-13ab5ee562ed)
<br>

## 1-2 원인
- textareas 태그에 입력받은 값이 상태 관리에서 제대로 업데이트되지 않아 글자 수가 초기화됨.
<br>

## 1-3 해결
```javascript
//기술서
res.data.selectApplFrmTechList.forEach((item, index) => {
  if(document.querySelectorAll('textarea[name=tech_itm_cont]')[index]){
    this.textareas[index+1] = this.checkChangeValue(item.tech_itm_cont);
    console.log("List 확인용 : " + item.tech_itm_cont);
    console.log("index 확인용 : " + index);
    console.log("selectApplFrmTechList : " + this.textareas[index+1].length);

    // 조회하기 메서드인데 글자수 업데이트를 하기 위해 추가함
    this.characterCount[index+1] = this.textareas[index+1].length;
  }
});
```
- 코드를 추가하여, 텍스트 영역의 값을 업데이트한 후 글자 수를 저장하도록 수정함. 이로 인해 페이지 재방문 시에도 입력된 글자 수가 올바르게 표시되도록 개선됨.

## 1-4 느낀점
- 처음으로 프로젝트에 참여하면서 코드가 굉장히 길고 복잡하다고 느꼈습니다.
- 각 메서드의 순서와 값 확인을 위해 console.log를 활용하여 디버깅을 하면서 문제를 해결할 수 있었습니다.
- 문제가 발생하는 이유를 파악하기 위해 console.log로 값이 나오는 경우와 안 나오는 경우를 확인할 수 있었습니다
- 학원 선생님이 'console로 찍어보세요'라고 하신 이유를 직접 느꼈습니다



# 2. 증명사진 첨부 오류

<br>

## 2-1 문제
- 증명사진을 넣고 임시 저장 후 나갔다가 다시 들어온 후 최종 제출을 클릭하면, '파일을 선택하세요'라는 메시지가 표시되는 현상이 발생
- ![문제](https://github.com/user-attachments/assets/995f5045-1650-4d4f-91dd-00ab0be75352)


## 2-2 원인
- input file이 페이지가 이동되면 보안상 값이 초기화 되면서 "undefined"으로 됨


## 2-3 해결
```javascript
// input file 이벤트 발생 시 나오는 코드
getPhotoFileName(e) {
  // #photo_span 요소 숨기기
  document.getElementById('photo_span').style.display = "none";
  // #photo_img 요소 보이기
  document.getElementById('photo_img').style.display = "revert";

  // 파일 가져오기
  var files = e.target.files;
  var filesArr = Array.prototype.slice.call(files);

  // 파일 정보를 로컬 스토리지에 저장
  if (filesArr.length > 0) {
    var fileName = filesArr[0].name;
    console.log("filesArr[0].name : " , filesArr[0].name);
    localStorage.setItem('photo_file_name', fileName);
    this.files_test = filesArr[0].name;
  }

  filesArr.forEach(function(f) {
    if (!f.type.match("image.*")) {
      alert("확장자는 이미지만 가능합니다.");
      e.target.value = "";

      // #photo_span 요소 보이기
      document.getElementById('photo_span').style.display = "revert";

      // #photo_img 요소 숨기기
      document.getElementById('photo_img').style.display = "none";
      return;
    }
    var reader = new FileReader();
    reader.onload = function(e) {

      // 이미지 src 설정
      var imgElement = document.getElementById('photo_img');
      console.log("e 확인 : " + e.target);
      imgElement.src = e.target.result;

      // 이벤트 발생 되면 hidden에 값 넣기
      document.getElementById('photo_img_hidden').value = "imageExits";
      }.bind(this);
      reader.readAsDataURL(f);
    }, this);
},

// 임시저장, 최종제출 로직 돌리기 전 나오는 코드
const imgFileElement = document.getElementById('imgFile');
const fileName = localStorage.getItem('photo_file_name');
const imgHidden =  document.getElementById('photo_img_hidden').value;

if (fileName && imgHidden) {
  imgFileElement.removeAttribute('required') 
}else{
  imgFileElement.setAttribute('required', '');
}
```
- input file이 이벤트 발생 시 이미지 이름을 로컬 스토리지에 임시로 값을 넣고, input hidden도 "imageExits" 값을 넣는다 <br>
- 임시저장, 최종제출 로직 돌리기 전 로컬스토리지와 hidden값을 if으로 확인하여 있다면 "required" 제거 없으면 추가

<br>

## 2-4 느낀점
- 처음에 input file의 값이 왜 안들어오는지 매우 어려웠다.. 나갔다 들어왔을 뿐인데...갑자기 값이 초기화 되니깐 <br>
- 구글링 하니 보안 정책상 값이 자동으로 초기화 된다는 사실을 알고 우선 db에 저장이 되어 있으니 <br>
- 로컬 스토리지로 이미지 이름을 저장하고 hidden도 값을 추가했다 <br>
- hidden을 추가한 이유는 file이 이벤트 발생 시 스토리지에 값을 넣는것으로 코드를 짰는데 이미지 넣고 저장안하고 나갔다 들어와도 값이 있기 때문이다 <br>

<br>

## 2-5 참고자료
- 페이지 이동 후 값 유지 <br>
https://bmangrok.tistory.com/entry/Javascript-%ED%8E%98%EC%9D%B4%EC%A7%80-%EC%9D%B4%EB%8F%99-%ED%9B%84%EC%97%90%EB%8F%84-%EA%B0%92-%EC%9C%A0%EC%A7%80%ED%95%98%EA%B8%B0 <br>
- ChatGPT <br>
<input type="file"> 요소는 페이지가 새로 고침되거나 이동 시 값이 자동으로 초기화됨, 보안상의 이유로 설계 <br>

# 3. 최종버튼 오류

<br>

## 3-1 문제
- 최종제출을 클릭하면 알림이 뜨지만 확인을 클릭해도 반응이 없는 현상이 발생(마이페이지에서 제출된것으로 확인됨)
- ![image](https://github.com/user-attachments/assets/c1c54f64-56ff-44d4-99f4-2341aa0434df)

<br>

## 3-2 원인
- 테스트 진행해 보았으나 확인을 눌르면 반응이 잘 나타남, 다만 취소 누르고 다시 임시저장, 최종제출 버튼 눌러도 반응이 없는 현상 발생 <br>
- 테스트 진행 추가 오류 발견(최종제출 클릭 시 지원서 파일 외에도 다른 관련 사항 파일첨부에도 동일한 증상 발견)
- 최종버튼 오류 증상 확인 

<br>

## 3-3 해결
- 최종 버튼 클릭, 확인 버튼 눌러도 반응 없음(서브쿼리가 문제 Subquery returns more than 1 row라는 오류는 서브쿼리가 여러 행을 반환하려고 할 때 발생
- 최종 버튼 클릭, 취소 클릭, 다시 최종 버튼 클릭 안됨 ( 버튼 비활성화가 되어 있어 취소 시 버튼 상태 값 변경)
- 최종 버튼 클릭, 파일첨부 시 required 제거(files[]는 변수에 값이 있으면 제거)

```javascript
// 한개의 값을 반환하기 위해
// 쿼리문에 LIMIT 1 추가 함

// 최종제출 클릭 시 취소 후 버튼 활성화
console.log("최종 버튼 취소 확인");
// 취소, 버튼 활성화
 this.isSave = true;



// input file에 아래와 같은 코드 작성 후 해결
:required="(!files[4 + item.id] || !files[4 + item.id].length)"
```

## 3-4 느낀점
- 처음에는 요구사항에 있는 오류가 안나와서 당황스러웠다 여러번 테스트를 진행했지만 나오지 않았다
- 지원서 얼굴 이미지 파일첨부가 아닌 관련 사항 파일첨부에서도 동일한 증상이 나왔으나 쉽게 해결이 되었음
- 버튼 비활성화는 생각보다 어려웠고 이해가 안됬다.. "isSave = false" 되어 있어서 버튼 활성화/비활성화인지 몰랐기 때문.. 코드도 보이지 않았고
- 코드를 보고 상태 관리 변수라는 것을 알고 추가하여 해결 되어 뿌듯했당

## 3-5 참고자료
- Subquery returns more than 1 row 대한 오류 (1) <br>
https://velog.io/@blessole/%EC%98%A4%EB%A5%98-Subquery-returns-more-than-1-row
- Subquery returns more than 1 row 대한 오류 (2) <br>
https://c-yhhhhh.tistory.com/118
- 버튼 활성화/비활성화 하기 <br>
https://hianna.tistory.com/477

<br>

# 4. 지원서 작성 오류

<br>

## 4-1 문제
- 학력사항에 고등학교, 전문대, 대학교, 석사, 박사 등 해당 안내문에 입력을 해도 어느곳에도 입력이 안됨
- ![image](https://github.com/user-attachments/assets/72b575ef-51d2-4346-af8e-aa0b2311e155)
- ![image](https://github.com/user-attachments/assets/687b847e-fb0e-4a88-98ce-0a6934fbdbe4)

<br>

## 4-2 원인
- 학교 졸업 여부를 선택할 때, 각 학력 단계에 맞는 안내문이 조건에 따라 표시되어야 하는데, <br> 현재 로직에서는 고등학교만 조건에 포함되어 있어 다른 학력 단계의 입력이 제대로 처리되지 않음.
- 고객사 추가 요청사항:
  - 변경 전: 안내문 태그는 하나만 있었고, 추가 버튼 클릭 시 내용이 아래에 추가되며, 학교 구분(전문대학, 대학교, 석사, 박사) 선택에 따라 안내문 설정 시 설정 값이 발생합니다.
  - 변경 후: 처음에는 안내문 태그가 보이지 않도록 하며, 사용자가 '추가' 버튼 클릭 시 안내문 태그와 함께 내용을 표시
    - 선택한 학교구분(예: 전문대, 대학교 등)에 따라 2~3개의 각 개별적으로 안내문 표시되도록 구현 해야하며,
    - 각 안내문은 선택된 학교 구분에 맞춰서 관련된 정보를 보여줘야한다.
 
<br>

## 4-3 해결
- 학교 졸업 여부를 선택할 때, 각 학력 단계에 맞는 안내문 표시
- 초기에 안내문 태그 없이 '추가' 버튼 보이게(클릭 시: 학교 구분에 맞춰서 내용 보이게) 표시
- 선택한 학교구분에 따라 2~3개의 각 개별적으로 안내문 표시

<br>

# 5. 1:1문의 제출후 화면 전환 개선

<br>

## 5-1 문제
현재는 1:1문의 이후 알람이 뜨고 닫기를 눌러도, 작성화면에 그대로 남아 있어서 제출이 된 것인지 모르겠고, 다시 문의 버튼을 누르면 똑같은 내용으로 다시 제출됨. <br>
제출을 하면 1:1문의 초기 화면 즉, 입력폼이 모두 깨끗한 상태로 되어 있도록 변경해야 함.

<br>

## 5-2 원인
문의 내용을 입력한 후 문의 클릭 시 문의는 잘 적용이 되고, "문의 완료" 라는 모달 창이 띄어짐 닫기 버튼을 클릭해도 작성화면에 입력 폼에 값을 초기화를 제대로 처리되지 않음  <br>
문의 클릭 -> 닫기 버튼 클릭하면 작성화면에 입력폼을 초기화

## 5-3 해결
문의 내용 입력한 후 문의 클릭 후 문의 완료라는 모달 창이 나오고 닫기 버튼 클릭 하면 작성화면에 입력폼 초기화
