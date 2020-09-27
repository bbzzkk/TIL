# RESTful API가 뭐야?

### 먼저 API가 뭔지 알아야지!

> **A**pplication **P**rogramming **I**nterface
>
> *응용 프로그램에서 사용할 수 있도록, 운영체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스를 뜻한다*

쉽게 말해서 어떠한 응용 프로그램에서 **데이터를 주고 받기 위한 방법**을 의미한다.



### 그럼 RESTful은 뭔데?

![img](https://gmlwjd9405.github.io/images/network/rest.png)

> **Re**presentational **S**tate **T**ransfer의 약자
>
> 자원을 이름으로 구분하여 자우

**웹에 존재하는 모든 자원(이미지, 동영상, DB 자원)에 고유한 URI를 부여해 활용"**하는 것으로, 자원을 정의하고 자원에 대한 주소를 지정하는 방법론을 의미한다고 한다. 따라서 RESTful API는 REST 특징을 지키면서 API를 제공하는 것을 의미한다.



### RESTful이 아닌 API도 있나?

- API의 Endpoint가 오직 한개이거나
- 모든 요청을 POST방식으로만 요청하거나
- 응답에 대한 메타 데이터를 body에 포함하거나
- URL에 동사가 포함 되어있거나
- URL에 RPC호출 메서드 명이 있거나