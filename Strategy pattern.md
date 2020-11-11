# 전략 패턴

## 전략 패턴이란?   
**전략 패턴 :** 실행 중에 알고리즘을 선택할 수 있게 하는 행위 소프트웨어 디자인 패턴이다.      
* has-a 방식을 도입하여 특정 Context에서 알고리즘을 별도로 분리하는 설계 방법 (인터페이스)

**용어 설명**    
* Context : LottoNumbersAutoGenerato 와 같이 인스턴스를 가진 대상
  * Strategy 패턴을 이용하는 역할을 수행
  * 필요에 따라 동적으로 구체적인 전략을 바꿀수 있도록 한다.(DI)
* Strategy : ShuffleStrategy 와 같이 추상화된 인터페이스나 클래스
  * 인터페이스나 추상 클래스로 외부에서 동일한 방식으로 알고리즘을 호출하는 방법을 명시한다.
* ConcreateStrategy : ShuffleRandomStrategy 와 같이 Strategy 를 구현한 클래스
  * 전력패턴에서 명시한 알고리즘을 실제로 구현한 클래스





1. 하나의 오브젝트를 2가지 변경여부에 관한 관점으로 구분짓는다.
  * 코드의 변경이 이루어지지 않는 **정적인 부분**      
  * 코드의 변경이 이루어질 수 있는 **동적인 부분**   
2. 동적인 부분을 Function Interface로 추출한다.     


* 코드에서 동적으로 변경 될 수 있는 부분을 기준으로 행함
* 

객체들이 할 수 있는 행위 각각에 대해 전략 클래스를 생성하고, 유사한 행위들을 캡슐화 하는 인터페이스를 정의하여,
객체의 행위를 동적으로 바꾸고 싶은 경우 직접 행위를 수정하지 않고 전략을 바꿔주기만 함으로써 행위를 유연하게 확장하는 방법을 말합니다.

# 참조
* **위키 :**    
https://ko.wikipedia.org/wiki/%EC%A0%84%EB%9E%B5_%ED%8C%A8%ED%84%B4
* **블로그 :**    
https://velog.io/@kyle/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%A0%84%EB%9E%B5%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80
* **동영상**    
우아한 테크코스_OCP와 전략패턴 : https://www.youtube.com/watch?v=90ZDvHl8ROE      
얄팍한 코딩지식_디자인패턴1 : https://www.youtube.com/watch?v=lJES5TQTTWE   
  
