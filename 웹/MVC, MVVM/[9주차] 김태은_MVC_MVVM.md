## MVC 아키텍쳐

- Model, View, Controller 영역으로 나눈 것
- 화면을 다루는 문제와, 데이터를 다루는 문제의 성격이 달라 분리
- Model과 View의 의존관계를 최소화 하여 화면의 수정이 데이터의 수정에 영향을 미치지 않도록 하고자 함

### View (화면)

- 사용자에게 보여지는 UI (프론트엔드에서는 HTML과 CSS로 만들어지는 결과물을 의미)

### Model (데이터)

- 화면의 어딘가는 데이터가 반영되어 나타나는데, 이런 데이터를 주관하는 영역

### Controller (컨트롤러)

- Model의 데이터를 받아 화면을 그리고, 화면으로부터 사용자의 인풋을 받아 Model을 변경하게 된다.
- 이 때 Model과 View의 중간 역할을 함

### 동작

1. Controller가 사용자 인풋을 받아들인다.
2. Controller가 사용자 인풋에 따라 Model 업데이트를 요청한다.
3. Controller가 Model을 나타낼 View를 선택(UI 갱신)한다.
4. View는 Model을 참조하여 UI를 업데이트 한다.

### 특징

- Controller는 여러개의 View를 선택할 수 있는 1:n 구조이다.
- Controller는 View를 선택할 뿐 직접 업데이트 하지 않는다.
- View와 Model 간의 의존성이 높아질 수 밖에 없고, Controller의 동작이 무거워질 수 있다.
  → 유지보수가 어려워진다.

## MVVM 아키텍쳐

- Model, View, View Model 영역으로 나눈 것

### View Model

- View를 나타내기 위한 Model이자 View를 나타내기 위한 데이터를 처리

### 동작

1. View가 사용자의 인풋을 받아온다.
2. 사용자 인풋에 따라 View Model로 데이터를 요청한다.
3. View가 요청한 데이터를 Model로 요청한다.
4. Model은 ViewModel이 요청한 데이터를 API호출 등을 사용하여 반환한다.
5. View Model은 Model로부터 데이터를 받아온다.
6. View는 View Model과 Data Binding을 하여 화면을 나타낸다.

### 특징

- MVC 패턴과 다르게 View가 직접 DB에 접근하는 것이 아닌, UI 업데이트에만 집중한다.
- View가 View Model의 데이터를 관찰하고 있어 UI 업데이트가 간편하다.
- 기능별 모듈화가 잘 되어 유지보수가 용이하다.
- 구조가 복잡하여 진입장벽이 높다는 단점이 있다.

참고

[프론트엔드에서 MV\* 아키텍쳐란 무엇인가요?](https://velog.io/@teo/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C%EC%97%90%EC%84%9C-MV-%EC%95%84%ED%82%A4%ED%85%8D%EC%B3%90%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80%EC%9A%94)

[[Android] 깔쌈하게 MVVM 패턴과 AAC 알아보기](https://velog.io/@haero_kim/Android-%EA%B9%94%EC%8C%88%ED%95%98%EA%B2%8C-MVVM-%ED%8C%A8%ED%84%B4%EA%B3%BC-AAC-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0)
