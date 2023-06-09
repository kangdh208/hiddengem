# :fork_and_knife: Hidden Gem (숨겨진 나의 맛집 공유 웹사이트)
> Hidden Gem은 자신이 알고 있는 숨겨진 맛집을 뜻합니다.

<img src="https://github.com/kangdh208/hiddengem/blob/master/26%EC%A1%B0-HiddenGem-compressed.gif" width="1000" height="500"/>


## 1. 제작기간 & 팀원소개
 - 2023년 3월 26일 ~ 2023년 3월 30일
 - 26조 온보딩 미니프로젝트
 - 팀원 정보
<table class="tg">
<tbody>
    <tr>
        <td>강동현</td>
        <td>김시원</td>
        <td>김영기</td>
        <td>조우필</td>
    </tr>
    <tr>
        <td><a href="https://github.com/kangdh208">@kangdh208</a></td>
        <td><a href="https://github.com/Siwon-Kim">@Siwon-Kim</a></td>
        <td><a href="https://github.com/youngkikim14">@youngkikim14</a></td>
        <td><a href="https://github.com/Cho-woo-pil">@Cho-woo-pil</a></td>
    </tr>
</tbody>
</table>


## 2. 사용기술
- back-end
  - Python
  - Flask
  - MongoDB

- front-end
  - javascript
  - jQuery
  - Bulma

- deploy
  - AWS EC2


## 3. User Stories
```
유저는 자신의 hidden gem에 대한 정보가 담긴 망고플레이트 URL을 입력하여 사람들과 공유할 수 있습니다.
유저는 웹사이트에 회원가입, 로그인, 로그아웃을 할 수 있습니다.
유저는 hidden gem 게시글에 좋아요 버튼을 누를 수 있습니다.
유저는 hidden gem 게시글을 삭제할 수 있습니다.
```
- 로그인,회원가입
  - 로그인 전 로그인, 회원가입 버튼 표기
  - 로그인시 로그아웃 버튼만 표기
  - 로그아웃

- 메인 페이지에서 맛집 공유 기능
  - 유저가 기입하는 링크를 통해 망고플레이트 사이트 크롤링
  - 식당명, 주소, 음식 카테고리, 이미지 크롤링
  - 유저 기입 코멘트, 별점 첨부

- 좋아요 기능
  - 추천하는 식당 좋아요 기능
  - 유저별 한번만 좋아요 가능
  - 버튼을 다시 누를시 좋아요 취소

- 맛집 게시글 삭제
  - 삭제 버튼 클릭시 게시글 삭제
  - 작성자만 삭제 권한 부여

- 맛집 게시글 수정
  - 수정 버튼 클릭시 게시글 수정 토글 상단 표시
  - 작성자만 수정 권한 부여


## 4. 프로젝트간 해결한 과제
### 1) 게시글 카드 구분하기
- 해결과제
  - 게시글을 삭제할 때 해당 카드를 삭제한다
- 문제점
  - 삭제 버튼을 누를 때 카드를 구별하지 못하고 DB상 첫번째 row에 해당하는 값이 삭제된다
- 수정
  - store API에 id값 부여하기
    - MongoDB에서 default로 생성되는 _id 활용하기
    - `db.stores.find()`후 바로 jsonify를 하면 _id가 ObjectId type이라 TypeError: ObjectId('') is not JSON serializable 에러 발생
    - id의 ObjectID  type을 `str(_id)`을 통해 stringify해줌
    - hex string type으로 id가 api에 잘 저장됌
  - 버튼 태그에 `value=${id}` 부여해주어서 카드를 id별로 구분할 수 있게 해줍니다
  
### 2) 게시글별 좋아요 버튼 구현하기
- 해결과제
  - 게시글마다 달린 좋아요 버튼을 유저 1명당 1번만 누를 수 있고, 다시 누르는 경우 좋아요를 취소한다
- 문제점
  - 좋아요를 누를 때 DB에 반영하고 다시 그 값을 가져올 때 reload가 필요하다
- 수정
  - 페이지 상 보여지는 좋아요 숫자와 DB에 저장하는 숫자를 처리하는 기능을 분리한다
    - FE: 단순히 버튼 클릭시 화면상의 숫자가 변경된다
    - BE: 버튼 클릭시 페이지 변경과 동시에 값을 DB에 저장하여 reload되는 경우도 처리한다
    
- 문제점
  - 좋아요 개수가 무한대로 올라간다
- 수정
  - 좋아요 증가와 감소 케이스를 분리한다
    - 각 유저별로 좋아요를 누른 가게의 id를 DB에 저장하고 이 값을 토대로 해당 가게에 좋아요를 눌렀는지 여부를 판단한다
    - 좋아요를 이미 눌렀으면 감소, 새로 누르는 경우 증가시킵니다
    - DB에도 동시에 저장하여 reload되는 경우도 처리한다
    
 ### 3) 게시글 수정하기 
- 해결과제
  - 게시글마다 달린 수정 버튼을 통해 게시글의 코멘트와 별점을 수정한다
- 문제점
  - 팝업창에서 변수 받아옴의 어려움으로 인한 시행착오
- 수정
  - update-box를 toggle을 통해 수정페이지를 불러온다
  - 게시물의 DB id를 전역변수 id로 받아와 저장하여 다른 함수에 사용한다
  - update function 구현, 전역변수로 받아온 id와 수정된 comment, star값을 update에 전달한다


 ### 4) 메인페이지와 로그인, 회원가입 페이지 연동하기
- 문제점
  - app.py 외에 계정을 관리하는 서버 파일인 account.py를 만들어 두 파일 간의 연동이 되지 않았다
- 수정
  - flask의 blueprint를 이용하여 2개의 백엔드 파일을 연동한다
  - 한개의 파일에 복잡한고 긴 코드 작성을 피하고 기능별로 나누어진 2개의 백엔드 파일을 이용해 편리하고 깔끔한 코드작성 및 수정이 가능하다
        
## 5. 개선해야할 점
- Like 버튼 reload시 HTML 버튼 스타일 처리를 case별로 하드코딩하였다
- Reload시 게시글들을 불러오는 과정에서 delay가 1초 정도 생긴다
- 회원가입 시 ID, nickname의 중복 처리
- MySave 페이지 저장 기능 추가
