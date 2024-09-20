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
- 처음으로 프로젝트를 참여하면서 코드가 굉장히 길고 복잡하다고 느껴짐
- 각 메서드의 순서와 어떤 값이 나오는디 console을 찍으면서 디버깅을 하다보니 문제를 해결 할 수가 있었다



# 2. 증명사진 첨부 오류

<br>

## 2-1 문제
- 증명사진을 넣고 임시저장 후 나갔다가 다시 들어오면 "파일을 선택하세요" 파일 값이 초기화되는 현상 발생
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

# 3. 최종버튼 오류

<br>

## 3-1 문제
- 최종제출을 클릭하면 알림이 뜨지만 확인을 클릭해도 반응이 없는 현상이 발생(마이페이지에서 제출된것으로 확인됨)
- ![image](https://github.com/user-attachments/assets/c1c54f64-56ff-44d4-99f4-2341aa0434df)

<br>

## 3-2 원인
- 테스트 진행해 보았으나 확인을 눌르면 반응이 잘 나타남, 다만 취소 누르고 다시 임시저장, 최종제출 버튼 눌러도 반응이 없는 현상 발생

<br>

## 3-3 해결
- 현재 원인 파악중.. if에 confirm으로 했지만 취소 할 경우 조치되는 else 구문이 없음

