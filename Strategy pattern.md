# 들어가면서  
목차
   
## 전략 패턴이란?   
실행 중에 알고리즘을 선택할 수 있게 하는 행위 소프트웨어 디자인 패턴이다.            
   
* 특정한 계열의 알고리즘들을 정의하고    
* 각 알고리즘을 캡슐화하며     
* 이 알고리즘들을 해당 계열 안에서 상호 교체가 가능하게 만든다. (OCP 수행)       
    
**정리 :** `has-a` 방식을 도입하여 특정 객체에서 알고리즘을 별도로 분리하는 설계 방법 (인터페이스)       
       
## 관련 용어    
* **Context :** 특정 알고리즘을 사용하는 객체 
  * Strategy 패턴을 이용하는 역할 수행      
  * 즉, ConcreateStrategy 인스턴스를 가진 대상            
  * 필요에 따라 동적으로 구체적인 전략을 바꿀수 있도록 한다.(DI)       
* **Strategy :** 특정한 알고리즘을 추상화된 인터페이스나 클래스로 정의한 오브젝트                
  * 주로 Functional Interface 로 구현하며 추상 메서드는 1개
  * 다형성을 이용하여 코드의 변경없이 다양한 알고리즘을 호출하는 역할 
* **ConcreateStrategy :** Strategy 의 추상 메서드를 구현한 클래스   
  * 전략 패턴에서 명시한 알고리즘을 실제로 각 용도에 알맞게 구현한 클래스    
  
## 전략 패턴 설계 방법


## 


# 참조
> 베이스 : https://github.com/kwj1270/TIL_WooWaHanTechSeminar/blob/master/OCP%EC%99%80%20%EC%A0%84%EB%9E%B5%20%ED%8C%A8%ED%84%B4.md    
     
* **위키 :**    
https://ko.wikipedia.org/wiki/%EC%A0%84%EB%9E%B5_%ED%8C%A8%ED%84%B4
* **블로그 :**    
https://velog.io/@kyle/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%A0%84%EB%9E%B5%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80
* **동영상**    
우아한 테크코스_OCP와 전략패턴 : https://www.youtube.com/watch?v=90ZDvHl8ROE      
얄팍한 코딩지식_디자인패턴1 : https://www.youtube.com/watch?v=lJES5TQTTWE   
  
