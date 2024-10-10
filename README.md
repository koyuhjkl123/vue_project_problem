
# 기본 소개
안녕하세요 풀스택 개발자가 되기를 노력하는 "노력쟁이"입니다.

## 문서 목적
직접 진행한 프로젝트에서 발생한 문제와 그 해결 과정을 기록하기 위해 작성되었습니다


## 프로젝트 언어
프로젝트를 진행하면서 배운 언어입니다.

### 프론트
HTML, CSS, Javascript, Vue.js

### 백엔드
Java, JavaSpring

### 협업도구
Git, GitLab

<br>
<br>

# 1. 글자수 세기 적용 안됨

<br>

## 1-1 문제
### 문제유형 : bug
### 문제타입 : 긴급
### 시작일자 : 2024-09-09
### 해결일자 : 2024-09-11
### 작업 소요기간 : 3일
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
### 문제유형 : bug
### 문제타입 : 긴급
### 시작일자 : 2024-09-12
### 해결일자 : 2024-09-19
### 작업 소요기간 : 3일(16일 ~ 18일 추석 연휴)
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
### 문제유형 : bug
### 문제타입 : 긴급
### 시작일자 : 2024-09-19
### 해결일자 : 2024-09-24
### 작업 소요기간 : 4일
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

# 4. 1:1문의 제출후 화면 전환 개선

<br>

## 4-1 문제
### 문제유형 : UI개선
### 문제타입 : 보통
### 시작일자 : 2024-09-24
### 해결일자 : 2024-09-25
### 작업 소요기간 : 2일
현재는 1:1문의 이후 알람이 뜨고 닫기를 눌러도, 작성화면에 그대로 남아 있어서 제출이 된 것인지 모르겠고, <br>
다시 문의 버튼을 누르면 똑같은 내용으로 다시 제출되며, <br>
제출을 하면 1:1문의 초기 화면 즉, 입력폼이 모두 깨끗한 상태로 되어 있도록 변경해야 함.

<br>

## 4-2 원인
문의 내용을 입력한 후 문의 클릭 시 문의는 잘 적용이 되고, "문의 완료" 라는 모달 창이 띄어짐 <br> 
닫기 버튼을 클릭해도 작성화면에 입력 폼에 값을 초기화를 제대로 처리되지 않음 <br>
문의 클릭 -> 닫기 버튼 클릭하면 작성화면에 입력폼을 초기화

## 4-3 해결
문의 내용 입력한 후 문의 클릭 후 문의 완료라는 모달 창이 나오고 닫기 버튼 클릭 하면 작성화면에 입력폼 초기화

<br>

```
axios.post("/res/community/qna/insertQna.ajax"  , null, {
						params: {
            				company_no: this.$store.state.mainForm.company_no,
                            writer_nm: document.getElementById('txt_user_name').value,
                            writer_mobile_tel_no: document.getElementById('txt_user_mobile_tel_no').value,
                            writer_id: document.getElementById('txt_user_email').value,
                            writer_email: document.getElementById('txt_user_email').value,
                            reg_idx : document.getElementById('txt_user_email').value,
                            title: document.getElementById('title').value,
                            board_contents: document.getElementById('board_contents').value,
                            project_no: document.querySelector('select[name="P_IDX"]').value,
                            recruit_notic_nm: this.encodedOptionName,
                            recruit_notic_no: document.querySelector('select[name="P_IDX"]').value,
          				} 
					}) 
					.then(res =>{ 
						console.log("통신 성공", res);

                        this.isModalViewed1 = !this.isModalViewed1;
						document.body.style.overflow = "hidden";
						console.log("모달창 생성");
                        this.resetForm();  // 추가함
                        
					})
            },
// 문의내용을 적고 문의를 클릭하면 모달창이 생성된 후 닫기 버튼 누르면 해당 내용 초기화
            resetForm(){
                // 문의 클릭 -> form태그 초기화
				this.$refs.formReset.reset();
            },

```

<br>

## 4-4 느낀점
프로젝트를 하면서 문제해결에 있어서 가장 쉬웠던 부분이다.  <br>
form태그를 초기화를 안시켜서 뿐...

<br>

# 5. 지원서 작성 오류

<br>

## 5-1 문제
### 문제유형 : bug
### 문제타입 : 긴급
### 시작일자 : 2024-09-25
### 해결일자 : 2024-09-27
### 작업 소요기간 : 3일
- 학력사항에 고등학교, 전문대, 대학교, 석사, 박사 등 해당 안내문에 입력을 해도 어느곳에도 입력이 안됨
- ![image](https://github.com/user-attachments/assets/72b575ef-51d2-4346-af8e-aa0b2311e155)
- ![image](https://github.com/user-attachments/assets/687b847e-fb0e-4a88-98ce-0a6934fbdbe4)

<br>

## 5-2 원인
- 학교 졸업 여부를 선택할 때, 각 학력 단계에 맞는 안내문이 조건에 따라 표시되어야 하는데, <br> 현재 로직에서는 고등학교만 조건에 포함되어 있어 다른 학력 단계의 입력이 제대로 처리되지 않음.
- 고객사 추가 요청사항:
  - 변경 전: 안내문 태그는 하나만 있었고, 추가 버튼 클릭 시 내용이 아래에 추가되며, 학교 구분(전문대학, 대학교, 석사, 박사) 선택에 따라 안내문 설정 시 설정 값이 발생합니다.
  - 변경 후: 처음에는 안내문 태그가 보이지 않도록 하며, 사용자가 '추가' 버튼 클릭 시 안내문 태그와 함께 내용을 표시
    - 선택한 학교구분(예: 전문대, 대학교 등)에 따라 2~3개의 각 개별적으로 안내문 표시되도록 구현 해야하며,
    - 각 안내문은 선택된 학교 구분에 맞춰서 관련된 정보를 보여줘야한다.
 
<br>

## 5-3 해결
- 학교 졸업 여부를 선택할 때, 각 학력 단계에 맞는 안내문 표시
- 초기에 안내문 태그 없이 '추가' 버튼 보이게(클릭 시: 학교 구분에 맞춰서 내용 보이게) 표시
- 선택한 학교구분에 따라 2~3개의 각 개별적으로 안내문 표시

<br>

```
추가 클릭 시 안내문 + 학교구분 보이지게 하고, 선택에 따라 설정된 안내문 표기됨
<template v-for="item in Education_addedSections" :key="item.id"> // 학력사항 추가 클릭 시
  <div v-if="item" :class="'writeTable addTable tb_appl_frm_edu_bkgrnd_sbjt_addDiv_' + item.itm_grp_cd" >
    <div class="infoArea" style="overflow: auto;"> // 안내문
      <template v-for="(applFrmToolItmGrpNotic6, index) in selectApplFrmToolItmGrpNoticList" >
        <template v-if="applFrmToolItmGrpNotic6 && (applFrmToolItmGrpNotic6.itm_grp_cd == school_div[item.id])" >
          <span v-html="applFrmToolItmGrpNotic6.notic_cont" :key="index"></span> // 전문대학, 대학교, 석사, 박사 설정된 안내문 표기
        </template>
      </template>
    </div>
```

<br>

## 5-4 느낀점
Vue.js를 배운적이 없어서 처음에 어떻게 해야 추가버튼만 나오고 추가 클릭 시 나오게 하는지 많이 어려웠다
vue에서 :key라는 어떻게 활용하는지 잘 모르다 보니 많은 삽질를 했고, vue.js 공식문서를 보면서 해결이 되어서 뿌듯했다

## 5-5 참고자료
<랜더링에서 key의 역할> <br>
https://icerabbit.tistory.com/119 <br>
<Vue v-for와 key의 개념> <br>
https://lanuarius19.tistory.com/entry/Vuejs-Vue-%EA%B8%B0%EB%B3%B8-%EA%B0%9C%EB%85%90-%EB%B0%8F-%EC%98%88%EC%A0%9C10-Vue-v-for%EC%99%80-key <br>
<Vue.js Key 속성 공식문서>  <br>
https://vueframework.com/docs/v3/ko/ko-KR/guide/migration/key-attribute.html#%E1%84%80%E1%85%A2%E1%84%8B%E1%85%AD  <br>
<br>



# 6. 기업페이지 : 화면 줄어들시 메뉴 하위아이콘 보이게 처리

## 6-1 문제
### 문제유형 : UI 메뉴 개선
### 문제타입 : 높음
### 시작일자 : 2024-09-27
### 해결일자 : 2024-10-02
### 작업 소요기간 : 3일 (10월 01일 공휴일 제외)
1. 회사소개와 공지사항에는 하위메뉴가 없으므로 해당 아이콘 없애기
2. 채용문의 하위의 [FAQ], [1:1문의] 메뉴 볼 수 있게 하기
3. 마이페이지 하위메뉴가 생겼는데, 작은 화면에서는 작동하지 않음.


## 6-2 원인
회사소개, 채용문의, 채용공고, 공지사항 모든 메뉴에 a태그에 아이콘을 추가함 <br>
채용문의에 하위 메뉴는 display: none으로 되어 있기 때문에 보여지지 않고, 채용문의 클릭시  "FAQ" 로 진입 <br>
마이페이지 클릭 시  "합격자 발표, 나의 지원서, 계정관리" 작은 메뉴가 나와야 함 작은 화면에서는 클릭하면 반응 없음 <br>
id="mypage"가 display="none"으로 되어 있어서 뜨지 않은 현상


## 6-3 해결
회사소개와 공지사항 아이콘 없애기
<br>

```
a태그에 적용된 부분을 아래와 같이 수정, 채용공고, 채용문의만 적용
.layout #wrap #gnb > ul > li:nth-child(2) > a,
.layout #wrap #gnb > ul > li:nth-child(3) > a{background:url("@/assets/images/res/common/btn-arrow.png") no-repeat right 15px center !important; color: #333;}
	
```
<br>

채용문의 하위의 [FAQ], [1:1문의] 메뉴 볼 수 있게 하기

<br>

```

// display: none을 없애고 @click="isItemMenuOpen = !isItemMenuOpen"을 추가하여
// 클릭 시 해당 하위 메뉴가 보이게 처리
<li>
  <router-link to="" @click="isItemMenuOpen = !isItemMenuOpen">
    <img src="@/assets/images/res/common/ico0103.png" alt="">채용문의
  </router-link>
  <ul v-if="isItemMenuOpen">
    <li><router-link to="/res/community/faq/listForm">FAQ</router-link></li>
    <li><router-link to="/res/community/qna/insert" >1:1문의</router-link></li>
  </ul>
</li>

```

<br>

마이페이지 하위메뉴가 생겼는데, 작은 화면에서는 작동하지 않음.

<br>

```
<li @click="displayOnoff();"><router-link to="">마이페이지</router-link>
  <ul id="mypage">
    <li ><a @click="openModal()">합격자 발표</a></li>
    <li v-if="this.$store.state.certificationYN == 'Y'"><router-link to="/res/mypage/applFrmForm" >나의 지원서</router-link></li>
    <li><router-link to="/res/mypage/accountmanage">계정관리</router-link></li>
  </ul>
</li>

// 변경 전
displayOnoff() {
  if(this.menuOpened == false){
    document.getElementById("mypage").style.display= "block";
    this.menuOpened = true; 
  } else {
    document.getElementById("mypage").style.display= "none";
    this.menuOpened = false; 
  }
},


// 변경 후
// 햄버거 메뉴바를 클릭하면 menuOpened true로 변경되어서 마이페이지 메뉴가 뜨지 않은 현상 발견
// 아래와 같이 수정 후 해결됨
displayOnoff() {
  const mypage = document.getElementById('mypage')

  if(mypage.style.display == "none"){
    mypage.style.display = "block";
  } else {
    mypage.style.display = "none";
  }

},

```

<br>

## 6-4 느낀점
처음에 개발자 도구를 보면서 HTML구조와 CSS를 보고 어떻게 적용 되었는지 확인했고 <br>
개발자 도구로 css를 조금씩 수정해 나가면서 이부분은 이렇게도 적용이 되는구나 하고 느껴졌다 <br>
그리고 vue.js에서 v-if로 참, 거짓으로 구분하여 클릭에 따라 html코드를 보여줬다 안보여줬다를 간단하게 할 수 있다는 것에 매우 신기했다ㅋㅋ 

# 7. 채용공고문 작성 에디터 기능 오류

<br>

## 7-1 문제
### 문제유형 : bug
### 문제타입 : 긴급
### 시작일자 : 2024-10-02
### 해결일자 : 2024-10-07
### 작업 소요기간 : 3일(10월 3일 공휴일 제외)
에디터 일부 기능이 정상적으로 작동 하지 않음, <br>
1. 저장 자체가 안되고 있는 듯함 <파일>
2. 세 가지 서식 기능은 편집기에서는 작동이 되나, 실제 사이트에서는 반영이 되지 않음 <글자 css 서식>

<br>

## 7-2 원인
1. 파일(엑셀, ppt 등) 파일에 대한 정보 갖고옴 이상현상 발견하지 못함
2. 편집기에서는 서식 기능 일부는 작동 됨, 사이트에서는 서식 기능이 적용 안되며, CSS 적용 안되는 현상 확인 


<br>

## 7-3 해결
서식 기능이 적용 안되는 현상

<br>

```

// APP.vue에  다음 에디터 API css가 적용되지 않아 적용함
@import url('../../common/daumeditor/css/content_view.css');


```


## 7-4 느낀점
콘솔로 보니 편집기에서 서식 기능 클릭하면 css가 적용이 되었는데 <br>
홈페이지에서는 css가 적용 되지 않아서 자료를 찾아보고 확인해 보니 <br>
다음 에디터 전용 css가 적용이 되어 있지 않아서 문제가 발생 된것이었다

## 7-5 자료
https://github.com/kakao/DaumEditor/issues/292



## 8-1 문제
### 문제유형 : 
### 문제타입 :
### 시작일자 :
### 해결일자 :
### 작업 소요기간 : 



















