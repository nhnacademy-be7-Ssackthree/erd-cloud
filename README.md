# ERD, DDL
3조 서점 프로젝트 ERD 입니다.
=======

### **** 수정 시 깃허브를 바로 수정하지 마시고 변경사항과 이유를 같이 작성하여 카톡이나 두레이로 올려주세요.

# erd 
*만약 어떤 생각이 겹칠 때 여러 관점으로 생각해보기(ex 환불, 배송오류 등)* <br/>
*모든 다대다 관계는 연결 테이블을 따로 하나 생성하여 일대다, 다대일로 연결하였음* <br/>
*연관관계를 식별 비식별 잘 생각해서 작성* <br/>
# 1. 도서

### 1.1)도서 카테고리
**모든 상품(도서)은 반드시 하나이상의 카테고리에 속해야합니다. <br/>
최대 10개의 카테고리에 속할 수 있습니다.** <br/>
<br/>
카테고리와 도서를 다대다로 연결

**카테고리는 최소 2단계로 구성됩니다.(적절히 구성합니다)** <br/>
<br/>
카테고리를 자기참조 일대다 관계로 설정 

### 1.2)주요 속성
**책 제목**<br/>
String (varchar) <br/>
**책 목차**<br/>
String (varchar) <br/>
**책 설명**<br/>
String (text) <br/> <br/>
**카테고리**<br/>
다른 테이블로 생성(여러도서에 여러 카테고리가 존재할 수 있음) <br/> <br/>
**작가** <br/>
다른 테이블로 생성(여러도서에 작가가 여러명 존재할 수 있음) <br/> <br/>
**출판사** <br/>
다른 테이블로 생성(출판사에 여러 책이 존재할 수 있음) <br/>
<br/>
***주의! : 한 책이 여러 출판사에 의해 출판될 수 있으나, 우리가 만드는 DB는 고려하지 않음 만약 꼭 구현해야 한다면 그렇게 하는걸로*** <br/>
<br/>
**출판일시** <br/>
LocalDate (timestamp) <br/> <br/>
**ISBN** <br/>
도서 고유 번호 : String (varchar) <br/> <br/>
**가격** <br/>
정가 : int (int)
판매가 : int (int) <br/> <br/>
**썸네일 이미지** <br/>
컬럼으로 관리 1개만 쓸거임


### 1.3)포장
포장 가능 여부를 도서에서 판단 (boolean) <br/>
이를 이용하여 주문에서 포장 여부를 구현

### 1.4)태그 
다른 테이블로 생성(여러도서에 여러 테그가 달릴 수 있음) <br/>
0개 이상의 테그를 지정할 수 있음 <br/>
등록된 테그는 검색에 사용되어야함 <br/><br/>

### 1.5)좋아요
다른 테이브로 생성(여러 회원이 여러 도서에 좋아요 누르기 가능) <br/>
회원과 도서를 식별관계로 연결하여 중복 좋아요 방지 <br/>
***구현!! : 한 도서에 한 회원이 좋아요를 한번 누를 수 있고, 도서를 사지않은 회원은 좋아요를 누를 수 없음 이를 고려하여 구현***<br/>
<br/>

# 2.회원

### 2.1)비회원 (고객)
고객 이름 (varchar) <br/>
고객 휴대폰 번호 (varchar) <br/>
고객 이메일 (varchar) <br/>

**회원과 식별관계 고객 아이디는 곧 회원의 PK**
### 2.2)회원
회원 로그인 아이디 (varchar) <br/>
회원 비밀번호 (varchar 함호화 해서 저장해야함) <br/>
생일 (varchar) <br/>
회원 가입 날짜 (varchar) <br/>
마지막 로그인 날짜 (varchar) <br/>
회원 상태 (enum : 활성, 휴면, 탈퇴) <br/>
관리자 유무 : 관리자와 회원을 이를 이용하여 분류 (boolean) <br/>
포인트 : **포인트 지급 방식은 다른 테이블을 이용** (int) <br/>
회원 등급 아이디(FK) **회원의 등급규정이 변경될 수 있기 때문에 테이블을 따로 분리** <br/>

**주민번호를 등록하지 않은 이유** <br/>
한 사용자가 여러 계정을 만드는 것을 가능하게 하기 위해서, 해당 서비스에서 필요한 정보라고 생각되지 않음. <br/>
<br/>
**회원탈퇴 후 다시 회원가입 시 쿠폰의 지급 여부** <br/>
쿠폰은 지급할 예정임. 그러나 최소주문 비용을 정해두어 함부로 계속 사용하지는 못하게 할 것(해당 쿠폰이 그리 효율적이지 못하게 설정)<br/>
-> ex) 주문 금액이 최소 50,000원 이상일 경우에만 적용 가능합니다. (총 결제 금액에서 최대 10,000원 할인이 적용됩니다.)

**구현!!: 회원 등급 변경은 로직 구현시 회원이 로그인을 했을 때 지난 90일 간의 구매 이력을 확인하여 업데이트를 수행한다.(과부하 방지 업데이트는 로그인 기준)**

### 2.3)주소
고객 아이디(FK) **회원마다 여러개의 주소목록을 가질 수 있음** <br/>

**주의!!: 기본 주소를 쓸지 말지는 현재는 사용하지 않는 걸로 통일 나중에 수정 할 수 있음!** <br/>

### 2.4)포인트 적립 정책
포인트 적립 방법에 따라 여러가지의 적립(회원가입, 리뷰쓰기 등등)이 나올 수 있기 때문에 따로 테이블 작성 <br/>
**주문에 따라 여러가지의 포인트가 적립 사용되는 것을 알기 위해 포인트 내역에 주문 아이디 추가** <br/>

**회원 포인트 적립률을 정책으로 넣어둔 이유** <br/>
1. 회원 포인트 적립 정책이 변경시 이전 적립 방식을 찾을 수 없음
2. 옛날에 적립된 기록을 확인하기 위해서
**회원 포인트 적립유형이 enum인 이유** <br/>
해당 포인트가 비율로 적용되는 것인지 정수 그대로 적용되는 것인지 확인하기 위해서 <br/>

### 2.5)관리자 
관리자와 사용자는 권한에따라서 할 수 있는 권한을 막는 식으로 구현(Role_admin,_Role_member) <br/>

# 3.주문
고객 아이디 : 주문을 시킨 고객(회원) <br/>
포인트 아이디 : 해당 회원이 포인트를 어떻게 사용하였는지(만약 환불이나 반품시 포인트 수정을 하기위함) <br/>
주문 일자 : 주문을 시킨 시점에 바로 등록(timestamp) <br/>
어떤 도서를 얼마나 사는지는 주문상세에서 기록 
  
### 3.1)주문 상태
주문의 상태가 변경 시 주문 상태의 레코드가 새로 추가로 생성됨 <br/>
**주문의 상태를 변경하는 것이 아닌 주문 상태를 주문이 여러개를 가지고 있는 이유?** <br/>
주문을 한 고객에게 언제 배송되었고, 언제 도착하는지를 보여주기 위해서 (변경시간은 기록함) <br/>
현재 주문의 상태를 파악하는 방법은 enum을 이용하여 판단<br/>
**주문과 주문 상태는 다대다로 연결하는 이유** <br/>
한 주문에 여러 주문상태를 가져서 주문한 고객에게 보여주기 위함


### 3.2)주문 상세
여러 도서를 얼마나 살지를 기록하는 테이블 <br/>
도서 테이블과 연결되어 어떤 도서를 사는지 확인하고 얼마나 살지를 정함 (**만약 주문 수량이 도서가 남아있는 수량보다 많으면 오류가 나야할듯?**) <br/>
쿠폰 테이블과 연결되어 해당 도서에 어떤 쿠폰을 사용하는지를 확인함. <br/>
**결제시 도서 가격이 들어있는 이유?** <br/>
결제 시점 기준으로 해당 도서의 가격이 얼마인지 확인하고 이를 반품, 환불 때 사용하기 위해서

### 3.3)포장
일단 포장의 경우 다양한 종류에 책을 한번에 포장할 수 없게 세팅하였음 (책마다 사이즈가 달라서 현실에서 여러 불편한 점들이 생길듯?) <br/>
포장의 경우 여러 책을 한번에 포장할 수 도 있고, 한 권씩 포장하는 것도 가능함(**그러나 구현에서 최대 포장 개수를 정해줘야함**) <br/>

### 3.4)배송
가상의 택배사가 있다고 생각하고 테이블 작성 <br/>
**배송 날짜를 지정할 수 있게 구현** 및 이를 저장해야함. 만약 지정하지 않으면 최대한 빠른 시간 <br/>
주문하는 사람과 받는 사람이 다를 수 있으므로 따로 배송에 저장(주소, 연락처, 우편번호, 상세주소 등)

### 3.5)배송 정책
배송 가격에 따른 배송정책을 지정하기 위한 테이블 <br/>
회원은 30000원 이상 주문시 배송비 무료이지만, 나중에 관리자가 수정을 할 수 도 있으니 테이블로 저장

### 3.6)반품
**출고일에 따라서 반품이 가능한 것은 구현의 문제** <br/>
주문 중 일부만 반품이 가능하기 때문에 주문 상세와 연결 <br/>
반품 사유를 enum으로 할지는 보류 (아마 바꿀듯?)

# 4.검색
일단은 검색의 경우는 전부 구현의 문제로 넘김 erd에서 따로 뭘 추가하지는 않았음 (pass?)

# 5.쿠폰
쿠폰의 경우 쿠폰의 종류를 생성하는 테이블과 해당 쿠폰을 찍어내는 테이블로 나눔(쿠폰, 회원 쿠폰) <br/>
회원마다 여러 쿠폰을 가질 수 있고 쿠폰의 종류는 여러 회원을 가질 수 있으니 다대다로 연결 <br/>
ex(회원 쿠폰 아이디 : 1, 고객 아이디 : 1, 쿠폰 아이디 1 이면, 고객 1에게 1이라는 쿠폰 종류의 쿠폰을 1개 만듦) <br/>
같은 쿠폰 종류를 다른 사람에게 주고 싶으면 (아이디:2,쿠폰아이디1,고객아이디2) <br/>

**쿠폰과 연결되는 테이블 종류** <br/>
도서 쿠폰 <br/>
카테고리 쿠폰 <br/>
회원 쿠폰 <br/>
주문 상세 (해당 쿠폰이 쓰인걸 확인하기 위함) <br/>

### 5.1)쿠폰 테이블
쿠폰 테이블에 도서 아이디와 카테고리 아이디를 연결하여 어떤 쿠폰인지 확인하는 용도(NULL 가능) <br/>
회원의 쿠폰시간과 쿠폰을 사용할 수 있는 기한은 다름 <br/>




# 6.장바구니
비회원의 경우 session을 이용하여 장바구니를 생성해줌 <br/>
회원의 경우 DB를 이용하여 해당 사용자가 장바구니를 저장하면 DB에 저장했다가 다시 보여줌 <br/>


# 7.리뷰
리뷰는 회원만 작성이 가능하도록 설정 예정? 아님 고객도? <br/>
고객이 작성한 리뷰는 수정할 수는 있지만 삭제 불가능(ON DELETE CASCADE 쓰면 될듯) <br/>
상품을 산 고객만 리뷰를 적어야 해서 주문 상세와도 연결 <br/>
**리뷰에 여러 이미지를 달기 위해서 리뷰 이미지 테이블 따로 지정** <br/>
**회원이 얼마나 리뷰했는지 알 방법이 있나?** <br/>
