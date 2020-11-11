# 들어가면서  
목차
   
## 전략 패턴이란?   
**전략 패턴 :**      
실행 중에 알고리즘을 선택할 수 있게 하는 행위 소프트웨어 디자인 패턴이다.            
   
* 특정한 계열의 알고리즘들을 정의하고    
* 각 알고리즘을 캡슐화하며     
* 이 알고리즘들을 해당 계열 안에서 상호 교체가 가능하게 만든다.       
    
**정리 :** `has-a` 방식을 도입하여 특정 객체에서 알고리즘을 별도로 분리하는 설계 방법 (인터페이스)       
        
   
     
**용어 설명**    
* Context : ConcreateStrategy 인스턴스를 가진 대상
  * Strategy 패턴을 이용하는 역할을 수행
  * 필요에 따라 동적으로 구체적인 전략을 바꿀수 있도록 한다.(DI)
* Strategy : Context로 부터 추출된 동적인 기능의 큰 틀을 정의하기위한 추상화된 인터페이스나 클래스
  * 인터페이스나 추상 클래스로 외부에서 동일한 방식으로 알고리즘을 호출하는 방법을 명시한다.
* ConcreateStrategy : Strategy 를 구현한 클래스
  * 전략 패턴에서 명시한 알고리즘을 실제로 구현한 클래스

## 전략 패턴 설계 방법


# 참조
* **위키 :**    
https://ko.wikipedia.org/wiki/%EC%A0%84%EB%9E%B5_%ED%8C%A8%ED%84%B4
* **블로그 :**    
https://velog.io/@kyle/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%A0%84%EB%9E%B5%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80
* **동영상**    
우아한 테크코스_OCP와 전략패턴 : https://www.youtube.com/watch?v=90ZDvHl8ROE      
얄팍한 코딩지식_디자인패턴1 : https://www.youtube.com/watch?v=lJES5TQTTWE   
  
