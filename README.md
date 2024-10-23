
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

### 나의 성과
#### Bug : 7개
#### UI개선 : 5개
#### 오류 발견 : Bug 5개(해결 2개)
<br>
<br>

# 1. 글자수 세기 적용 안됨

<br>

## 1-1 문제
### 문제유형 : bug (긴급)
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
### 문제유형 : bug (긴급)
### 시작일자 : 2024-09-12
### 해결일자 : 2024-09-19
### 작업 소요기간 : 3일(16일 ~ 18일 추석 연휴)
![2-1번-이미지](https://github.com/user-attachments/assets/095469fc-0d2a-44cc-9ad6-e363da38084f)
증명사진을 넣고 임시 저장 후 나갔다가 다시 들어온 후 최종 제출을 클릭하면, '파일을 선택하세요'라는 메시지가 표시되는 현상이 발생



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
### 문제유형 : bug (긴급)
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
### 문제유형 : UI개선 (높음)
### 시작일자 : 2024-09-24
### 해결일자 : 2024-09-25
### 작업 소요기간 : 2일
![4-1번-이미지](https://github.com/user-attachments/assets/2741e676-2f8e-426e-b9d6-30191036aa7a)
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
### 문제유형 : bug (긴급)
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
### 문제유형 : UI 메뉴 개선 (높음)
### 시작일자 : 2024-09-27
### 해결일자 : 2024-10-02
### 작업 소요기간 : 3일 (10월 01일 공휴일 제외)
![6-1번-이미지](https://github.com/user-attachments/assets/d32e003b-881f-4051-a880-93d103b1d697)
회사소개와 공지사항에는 하위메뉴가 없으므로 해당 아이콘 없애기 <br>
채용문의 하위의 [FAQ], [1:1문의] 메뉴 볼 수 있게 하기  <br>
마이페이지 하위메뉴가 생겼는데, 작은 화면에서는 작동하지 않음.


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
vue.js에서 v-if로 참, 거짓으로 구분하여 클릭에 따라 html코드를 보여줬다 안보여줬다를 간단하게 할 수 있다는 것에 매우 신기했다ㅋㅋ <br>

<br>


# 7. 채용공고문 작성 에디터 기능 오류

<br>

## 7-1 문제
### 문제유형 : bug (긴급)
### 시작일자 : 2024-10-02
### 해결일자 : 2024-10-07
### 작업 소요기간 : 3일(10월 3일 공휴일 제외)
![7-1번-이미지](https://github.com/user-attachments/assets/35cc6679-c015-4a55-9ad4-44e48253baa7)
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

<br>

## 7-4 느낀점
콘솔로 보니 편집기에서 서식 기능 클릭하면 css가 적용이 되었는데 <br>
홈페이지에서는 css가 적용 되지 않아서 자료를 찾아보고 확인해 보니 <br>
다음 에디터 전용 css가 적용이 되어 있지 않아서 문제가 발생 된것이었다

<br>

## 7-5 자료
<인용구가 적용되지 않습니다>
https://github.com/kakao/DaumEditor/issues/292

# 8. 기업페이지 화면 비율에 따라 이미지 축소

<br>

## 8-1 문제
### 문제유형 : UI 개선 (보통)
### 시작일자 : 2024-10-08
### 해결일자 : 2024-10-10 (10월 09일 한글날 제외)
### 작업 소요기간 : 1일
반응형 페이지에서, 메인이미지가 오른쪽이 점차 잘리는 형태로 되어 있습니다. 혹시, 이미지 전체가 비율로 축소되는 형태로 수정가능할까요?

## 8-2 원인
기존에 오른쪽으로 점차 잘리는 형태로 했으나 고객사에서 수정 요청

## 8-3 해결

이미지 전체가 비율로 축소되는 형태로 수정

<br>

```
// 기존 css를 수정 및 추가함
.layout.main.v5 #container .content .quickArea {top:450px; left:0; padding:0 10px; transform:translate(0, 0); -webkit-transform:translate(0, 0); -moz-transform:translate(0, 0)}
.layout.main.v5 #container .content .quickArea a{display:block; padding:25px 15px 28px; height:124px; max-width: 80%;}
.layout.main.v5 #container .content .quickArea a strong {margin-bottom:7px; line-height:30px; font-size:17px}
.layout.main.v5 #container .content .quickArea a p {line-height:17px; font-size:11px}
.layout.main.v5 #container .content .quickArea a span {top:5px; right:5px; height:35px}

/* 추가 */
.layout.main.v5 #container .content .quickArea a span img {height:80%}
```
<br>

## 8-4 느낀점
처음에 개발자 도구를 보면서 HTML구조와 CSS를 보고 어떻게 적용 되었는지 확인했고 <br>
개발자 도구로 css를 조금씩 수정해 나가면서 이부분은 이렇게도 적용이 되는구나 하고 느껴졌다 <br>


<br>

# 9. 필기도구 등록 팝업에 도구 코드 열 추가

<br>

## 9-1 문제
### 문제유형 : UI 개선 (보통)
### 시작일자 : 2024-10-14
### 해결일자 : 2024-10-14
### 작업 소요기간 : 1일
![image](https://github.com/user-attachments/assets/e2d02a8a-2b0d-4e33-bf8d-9cf0d69d8684)
1. 도구가 가지고 있는 코드를 추가하기
2. 텍스트 길 때에도 한 줄로만 보이도록 하기


## 9-2 원인
고객사에서 도구가 가지고 있는 고유코드를 추가 및 개선

<br>

## 9-3 해결

도구가 가지고 있는 코드를 추가하기 <br>
텍스트 길 때에도 한 줄로만 보이도록 하기

<br>             

```
<div class="popup__header">
	<h3 class="popup__title">필기도구 등록</h3>
</div>
<div class="popup__contents popup__scroll-view" style="max-height: 485px;">
	<div class="board-body">
		<div class="table table-type1">
			<table id="writeToolRegTable" class="type-s type-line">
				<colgroup>
					<col style="width: 50px" />
					<col style="width: 50px" />
					<col style="width: 70px" /> // 해당 th 넓이 추가
					<col style="width: 200px" />
					<col>
					<col style="width: 100px;">
					<col style="width: 100px;">
				</colgroup>
				<thead>
					<tr>
						<th scope="col"></th>
						<th scope="col">NO</th>
						<th scope="col">코드</th> // 코드 추가
						<th scope="col">도구명</th>
						<th scope="col">정의</th>
						<th scope="col">총점</th>
						<th scope="col">검사시간</th>
					</tr>
				</thead>
				<tbody class="first-td-bdt0">
				</tbody>
			</table>
		</div>
	</div>

</div>


function writeToolRegList() {

	$('#writeToolRegTable').DataTable().destroy();

	var searchType = '';
	var searchVal = '';
	var tableId = "#writeToolRegTable";
	var urlVal = "/content/wri/getList.json";
	var columnsVal = [
		{ "data" : "twCode",
		sortable: false,
						  
		"render" : function(data, type, row, meta) {
					
			if(type === 'display') {
				data = '<label class="input-chk"> <input type="radio" name="writeToolRegRadio" value="' + data + '"/> <span class="isHidden">선택</span> </label>';
			}
			return data;
		}
		},
		{ data: "rownum",
			className: "wd-20",
			sortable: false,
			render: function (data, type, row, meta) {
				return meta.row + meta.settings._iDisplayStart + 1;
			}
			},
			{ "data" : "twCode"}, // 고유 코드 추가
			{ "data" : "twTitle"},
			{ "data" : "twDefinition"},
			{ "data" : "twtsTitle"},
			{ "data" : "twExamTime",
				"render" : function(data, type, row, meta) {
				if(type === 'display') {
					 data = '<td>' + getTETText(row.twExamTimeGbn, row.twExamTime) + '</td>';
				}
					return data;
				}
			}
	];

	var paramsVal = function (d) {
		d = dataVal(d,columnsVal);
		d.length = $("#length").val();
		d.companyNo = 0;
		d.menuId = '30000300';
		d.searchType = searchType;
		d.searchVal = searchVal;
					
		return d;
	}
		
	setTableGrid(tableId, urlVal, paramsVal, columnsVal);
		
}
```
<br>

## 9-4 느낀점
DataTables() 라이브러리를 처음 사용해 보았고, JSON 타입으로 데이터를 주고받는 구조가 인상적이었다. <br>
최상위 값을 쉽게 받을 수 있고, 테이블 형태로 데이터를 표시할 수 있다는 점에서 매우 유용하다고 느껴졌다 <br>
그러나 데이터를 추가하는 과정에서 어려움을 겪었고. 예를 들어, {"data" : "twCode"}로 설정했을 때 <td> 태그만 추가되고 <th>는 추가되지 않아 한참을 삽질을 해야했고ㅠㅠ <br>
<br>
결국 <th>코드</th>처럼 명시적으로 추가해주면 간단하게 해결되는 문제였다는 것을 깨달았고, 자료도 중요하지만, <br>
이전 개발자의 코드를 꼼꼼히 살펴보는 것이 얼마나 중요한지 다시 한번 느껴지는 하루! 


## 9-5 자료

datatables 사용법 <br>
https://blog.naver.com/jskimmail/222006260605  <br>

DataTables - Ajax Table Data 출력 <br>
https://drg2524.tistory.com/163 <br>


Grid 라이브러리-Datatables 사용법/예제 <br>
https://kutar37.tistory.com/entry/Grid-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%ACDatatables-%EC%82%AC%EC%9A%A9%EB%B2%95%EC%98%88%EC%A0%9C <br>


<br>

# 10. 기업페이지 > 채용관리 > 서류전형 : 서류접수기간

<br>

## 10-1 문제
### 문제유형 : UI 개선 (보통)
### 시작일자 : 2024-10-16
### 해결일자 : 2024-10-16
### 작업 소요기간 : 1일
![image](https://github.com/user-attachments/assets/1db9d719-3b67-49d1-b591-ed05fb17bcb9)
채용공고는 종료일을 지정한 상태임(채용시까지가 아님)  <br>
서류접수기간을 설정하려고 하는데 채용시까지 체크박스에 체크가 되어있는 상태로 비활성화처리 되어 있어서 헷갈림.  <br>
체크가 안되어있는 상태로 비활성화 처리되는게 좋을 듯 함.

<br>

## 10-2 원인
체크박스의 체크 상태가 CSS로 조작되고 있어, 실제 체크 상태와 시각적으로 표시되는 상태 간에 불일치가 발생  <br>
채용정보 수정 화면에서 "채용시까지" 버튼을 클릭하지 않고 저장 버튼을 누른 후, 다시 수정 화면에 들어가면 체크박스가 체크된 상태로 나타나는 현상이 발생  <br>

<br>


## 10-3 해결
css 체크박스 시각적 체크 표현 수정  <br>
js 체크 여부에 따라 그 상태값 유지 수정 <br>

```

---------- css ----------
// 변경 전
.input-chk input[type=checkbox]~span:before {
	background: var(--bg-color2) var(--icon-url) no-repeat -74px -26px; // 원인이 되는 코드
	border: 2px solid var(--border-color4);
	border-radius: 5px;
}
.input-chk input[type=checkbox]:checked~span:before {
	border-color: var(--primary);
	background-color: var(--primary);
}


// 변경 후
// 고객사 요청에 따라 체크되지 않은 체크박스는 css 적용하지 않고 체크된 부분만 적용
.input-chk input[type=checkbox]~span:before {
	border: 2px solid var(--border-color4);
	border-radius: 5px;
}
.input-chk input[type=checkbox]:checked~span:before {
	background: var(--bg-color2) var(--icon-url) no-repeat -74px -26px;
	border-color: var(--primary);
	background-color: var(--primary);
}


---------- js ----------

// 'Y' = 체크,  'N' 체크해제
// 공고기간,시간 설정

// 변경 전
$('#calendar1_1').val(info.startDt);
$('#timepicker1_1').val(info.startTime);
if (info.endDt == "9999.12.31"||info.endDt == "채용시까지") {

	// 원인이 되는 코드 / 채용정보 수정 시 상태 유지가 안됨
	$('#calendarLimitOut').click();
	$('#calendar1_2').val("9999.12.31");
	$('#calendar1_2').prop('disabled', true);
	$('#timepicker1_2').prop('disabled', true);

} else {
	$('#calendar1_2').val(info.endDt);
	$('#timepicker1_2').val(info.endTime);
}

// 변경 후
$('#calendar1_1').val(info.startDt);
$('#timepicker1_1').val(info.startTime);
if (info.endDt == "9999.12.31"||info.endDt == "채용시까지") {

	// 체크여부에 따라 그 상태값을 유지 필요
	if(info.recruitInfinityYn == 'Y'){
		$('#calendarLimitOut').click();
		$('#calendar1_2').val("9999.12.31");
		$('#calendar1_2').prop('disabled', true);
		$('#timepicker1_2').prop('disabled', true);
	}

} else {
	$('#calendar1_2').val(info.endDt);
	$('#timepicker1_2').val(info.endTime);
}


```

<br>

## 10-4 느낀점
고객사가 요청한 부분만 수정 할 수도 있지만 좀더 사용자의 편의성을 위해 "채용시 까지" 라는 버튼을 수정 했습니다 <br>
이전 코드는 무조건 체크된 상태만 보여져서 체크가 안된 상태라면 직관적이지 않기 때문에 상태 유지가 필요해 보인다고 생각했고, <br>
js 코드 수정으로 인해 사용자는 체크박스의 상태를 더 직관적으로 이해 할 수 있습니다


<br>

# 11. 채용홈페이지 -> 지원서 작성 : 지원서 불러오기 증명사진 오류

<br>

## 11-1 문제
### 문제유형 : bug (긴급)
### 시작일자 : 2024-10-18
### 해결일자 : 2024-10-18
### 작업 소요기간 : 
![11-1번-이미지](https://github.com/user-attachments/assets/3d8a42cc-1780-4e61-961f-061dbbd9ff35)
입사지원서에서 "지원서 불러오기" 클릭 후  <br>
최종제출 클릭하면 "파일을 선택하세요" 라는 문구가 나오면서 제출이 안됨 <br>
저장 없이 나가기 버튼 누르고 다시 들어가면 증명사진 이름 값이 그대로 있음

<br>

## 11-2 원인
둘다 if를 잘못 적용했으며, 해당 변수의 값이 특정 기능 시 나오는 값을 생각하지 않고 조건을 걸어서 나온 오류였다 

<br>


## 11-3 해결
지원서 불러오기 후 최종제출 클릭하면 정상적으로 제출 수정 완료 <br>
저장 없이 나가기 버튼 누르고 다시 들어가면 이미지 이름 값 없음 수정 완료

<br>

```
// 지원서 불러오기 후 최종제출 클릭 오류 해결
// 변경 전
async BeforeSaveApplicant(appl_frm_submit_yn){//임시저장/ 최종제출 로직 돌리기 전에 작업해야하는 것
	if(this.isSave){
		if(appl_frm_submit_yn == 'Y'){
	
			const imgFileElement = document.getElementById('imgFile');
			const fileName = localStorage.getItem('photo_file_name');
			var imgHidden =  document.getElementById('photo_img_hidden').value;
		
			if (fileName && imgHidden) {
				imgFileElement.removeAttribute('required') 
			}else{
			 	imgFileElement.setAttribute('required', '');
	}

// 변경 후
async BeforeSaveApplicant(appl_frm_submit_yn){//임시저장/ 최종제출 로직 돌리기 전에 작업해야하는 것
	if(this.isSave){
		if(appl_frm_submit_yn == 'Y'){
	
			const imgFileElement = document.getElementById('imgFile');
			const fileName = localStorage.getItem('photo_file_name');
			var imgHidden =  document.getElementById('photo_img_hidden').value;

// && --> || and에서 or로 변경 로컬스토리지 저장된 이미지 이름 혹은 이미지가 들어가기만 하면 나오는 hidden값 
			if (fileName || imgHidden) {
				imgFileElement.removeAttribute('required') 
			}else{
			 	imgFileElement.setAttribute('required', '');
			}


// 나가기 버튼 누른 후 이미지 이름 값 없애기
// 변경 전
cancleApplicant(){
	if(this.test_yn == 'Y'){
		const userConfirmed  = confirm("테스트를 종료합니다.");
		if(userConfirmed ){
			window.close();
		}
	}else{
		this.clickedExitButton = true;
		const userConfirmed  = confirm("임시저장하지 않은 내용은 저장되지 않을 수 있습니다.");
		// 만약 파일 이미지 넣고 나가기만 했을 경우
		const fileHidden = document.getElementById('photo_img_hidden').value;
		const fileName = localStorage.getItem('photo_file_name');
		
		console.log("fileHidden : ", fileHidden);
		console.log("imgElement : ", fileName);
		if(fileHidden != "imageExits" && fileName){
			localStorage.removeItem('photo_file_name');
		}
		if(userConfirmed ){
			this.$router.push('/');
		}
	}
},

// 변경 후
cancleApplicant(){
	if(this.test_yn == 'Y'){ㅣ
		const userConfirmed  = confirm("테스트를 종료합니다.");
		if(userConfirmed ){
			window.close();
		}
	}else{
		this.clickedExitButton = true;
		const userConfirmed  = confirm("임시저장하지 않은 내용은 저장되지 않을 수 있습니다.");
		// 만약 파일 이미지 넣고 나가기만 했을 경우
		const fileHidden = document.getElementById('photo_img_hidden').value;
		const fileName = localStorage.getItem('photo_file_name');
		
		console.log("fileHidden : ", fileHidden);
		console.log("imgElement : ", fileName);
		if(fileName){ // 지원서 불러오기 할 때 해당 증명사진이 있을 경우 fileHidden = "imageExits"이 넣어짐 
			localStorage.removeItem('photo_file_name');
		}
		if(userConfirmed ){
			this.$router.push('/');
		}
	}
},

```

<br>

## 11-4 느낀점
이번 문제를 해결하면서 각 변수의 역할과 상태 변화에 대한 이해가 얼마나 중요한지 깨달았고 <br>
특히, 특정 기능을 수행할 때 어떤 변수들이 어떻게 작용하는지를 명확히 파악해야 오류를 예방할 수 있다는 것을 느꼈습니다. <br>
또한, 조건문에서 &&와 ||의 차이점을 잘 이해하고 적용하는 것이 문제 해결에 큰 영향을 미친다는 점도 인상 깊었고, <br>
앞으로는 코드 작성 시, 각 변수의 상태와 의미를 더 철저히 검토하고, 다양한 시나리오를 고려하여 코드를 작성해야겠다고 다짐하기로 내 자신과 약속


<br>

# 12. 기업페이지 -> 채용관리 -> 필기전형 -> 전형기간 설정 시 오류

<br>

## 12-1 문제
### 문제유형 : bug (긴급)
### 시작일자 : 2024-10-21
### 해결일자 : 2024-10-21
### 작업 소요기간 : 1일
![image](https://github.com/user-attachments/assets/e44f54b3-42d4-4f8f-9ef0-a3622613224b)
전형기간은 공고기간 내에 설정 시 정상적으로 작동함 <br>
하지만 공고기간 시작일보다 이전, 또는 공고기간 종료일보다 이후에 전형기간을 설정할 경우, 오류 메시지 없이 빈 팝업이 나타나며 저장되지 않음.

<br>

## 12-2 원인
전형기간 설정 시, 공고기간과의 관계를 검증하는 if 조건이 코드에 누락되어 발생한 문제.  <br>
전형기간이 공고기간보다 이전이거나 이후로 설정될 경우, 이를 검증하는 로직이 없기 때문에 오류 메시지 없이 빈 팝업이 나타남.

<br>


## 12-3 해결
전형기간이 공고기간 내에 설정되도록 검증 로직을 추가하여 문제 해결.

<br>

```

// 변경 전

let scrnStartDt = $('#txtWriteStartDate').val();
let scrnStartTime = $('#txtWriteStartTime').val();
let scrnEndDt = $('#txtWriteEndDate').val();
let scrnEndTime = $('#txtWriteEndTime').val();
		
let startDate = scrnStartDt + ' ' + scrnStartTime;
let endDate = scrnEndDt + ' ' + scrnEndTime;
		
let startDateValidate = new Date(scrnStartDt);
let endDateValidate = new Date(scrnEndDt);

console.log(startDateValidate + '########' + endDateValidate);

if(scrnStartDt === '' || scrnStartDt === null || scrnEndDt === '' || scrnEndDt === null) {
	alert('필기전형 기간을 입력하세요.');
	return;
}
				
if(scrnStartTime === '' || scrnStartTime === null || scrnEndTime === '' || scrnEndTime === null) {	
	alert('필기전형 시간을 입력하세요.');
	return;
}
		
if(startDateValidate > endDateValidate) {
	alert('필기전형 완료일자가 시작일자보다 앞설 수 없습니다.');
	return;
}


// 변경 후

let scrnStartDt = $('#txtWriteStartDate').val();
let scrnStartTime = $('#txtWriteStartTime').val();
let scrnEndDt = $('#txtWriteEndDate').val();
let scrnEndTime = $('#txtWriteEndTime').val();

// hidden으로 보낸 공고기간 값을 가져옴
let recruitDt = $('#recruitDt').val();
		
// ~ 기준으로 앞, 뒤 값이 나뉘어짐
let dataReg = recruitDt.split(" ~ ");
let recruitStartDt = dataReg[0];
let recruitEndDt = dataReg[1];
		
		
let startDate = scrnStartDt + ' ' + scrnStartTime;
let endDate = scrnEndDt + ' ' + scrnEndTime;
		
let startDateValidate = new Date(scrnStartDt);
let endDateValidate = new Date(scrnEndDt);
// 위 값과 일관되게 하기위해
let recruitStartDtValidate = new Date(recruitStartDt);
let recruitEndDtValidate = new Date(recruitEndDt);
		
// 값이 제대로 되었는지 로그
console.log("recruitStartDtValidate : ", recruitStartDtValidate);
console.log("recruitEndDtValidate : ", recruitEndDtValidate);
console.log(startDateValidate + '########' + endDateValidate);

if(scrnStartDt === '' || scrnStartDt === null || scrnEndDt === '' || scrnEndDt === null) {
	alert('필기전형 기간을 입력하세요.');
	return;
}
// 전형기간 시작일자 < 공고기간 시작 일자 || 전형기간 종료일자 > 공고기간 종료일자
if(startDateValidate < recruitStartDtValidate || endDateValidate > recruitEndDtValidate){
	alert('전형기간은 공고기간 내에 설정해야 합니다. 다시 확인해 주세요.');
	return;
}
				
if(scrnStartTime === '' || scrnStartTime === null || scrnEndTime === '' || scrnEndTime === null) {	
	alert('필기전형 시간을 입력하세요.');
	return;
}
		
if(startDateValidate > endDateValidate) {
	alert('필기전형 완료일자가 시작일자보다 앞설 수 없습니다.');
	return;
}

```

<br>

## 12-4 느낀점
이번 테스트를 진행하며, 스스로 문제를 발견하고 해결할 수 있었던 점에 큰 의미가 있었습니다.  <br>
코드를 분석하면서 전형 기간 설정 시 공고기간과의 관계를 검증하지 않은 것이 문제의 원인임을 파악했고, 공고기간 값을 hidden 필드를 통해 가져와 검증 로직을 추가했습니다. <br>
이를 통해 문제를 해결하였고, 매우 보람찬 경험이었습니다.

<br>

# 13. 사용자등록 팝업 수정

<br>

## 13-1 문제
### 문제유형 : feat UI개선 (보통)
### 시작일자 : 2024-10-23
### 해결일자 : 2024-10-23
### 작업 소요기간 : 
![13-1번-이미지](https://github.com/user-attachments/assets/0b589f4b-fcab-4391-82fe-eecd7151b17f)
문구 수정 및 위치 변경
- 권한에서 기업담당자 선택 시 : ID는 영문, 숫자, 특수문자(_@.) 사용하여 4-50자 이내로 작성
- 권한에서 평가자 선택 시 : ID 및 PW는 영문, 숫자, 특수문자(_@.) 사용하여 4-50자 이내로 작성
- 위치변경 : ID 인풋박스 아래로

<br>

## 13-2 원인
고객사에서 문구 수정 및 위치 변경 요청

<br>


## 13-3 해결
고객사 요청에 따라 문구 수정 및 위치 변경 완료

<br>

```
curidx = 3 : 기업담당자
curidx = 4 : 평가자
// 변경 전  
if(curidx === "4"){
	$('.pw').show();				 
} else {
	$('.pw').hide();
	$('.pw').val('');
 }


// 변경 후
if(curidx === "4"){
	$('.pw').show();
	$('p[class=form-help-txt]').text('ID 및 PW는 영문, 숫자, 특수문자(_@.) 사용하여 4-50자 이내로 작성');
} else {
	$('.pw').hide();
	$('.pw').val('');
	$('p[class=form-help-txt]').text('ID는 영문, 숫자, 특수문자(_@.) 사용하여 4-50자 이내로 작성');
}
```

<br>

## 13-4 느낀점
문구 수정 및 위치변경이라 javascript으로 선택에 따라 문구만 다르게 주면 되는 부분이라  <br>
if를 통해 이 문제를 간단하게 해결했다  <br>

<br>
