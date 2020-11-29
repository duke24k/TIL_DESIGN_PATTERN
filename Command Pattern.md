# Command pattern
     
[1. Command pattern 이란?](#Command-pattern-이란)     
[2. 용어 설명](#용어-설명)     
[3. 설계 방법](#설계-방법)     
[4. 예제](#예제)     
   
# Command pattern 이란?        
> 실행될 기능을 캡슐화함으로써 주어진 여러 기능을 실행할 수 있는 재사용성이 높은 클래스를 설계하는 패턴       
> 기능에 필요한 정보를 저장 또는 로깅, 취소할 수 있으며 객체 지향의 장점을 활용 가능하도록 만든 패턴이다. 
    
기존에 메서드로만 행해지던 각 작업, 기능들을 클래스로 캡슐화하여 사용하는 패턴을 의미합니다.       
전략 패턴은 말 그대로 각 전략마다 알맞는 객체를 주입시켜 로직을 '갈아끼우는 방식'이라면        
커멘드 패턴은 각각의 기능들을 넣어주면서 '순서대로 실행하는 방식'이라 생각하면 됩니다.      
쉽게 생각하면 콘솔기의 커멘드를 입력하는 것이라 이해하면 될 것 같습니다.   
     
주로 인터페이스를 사용하지만 추상 클래스도 사용가능하기에       
구현이 아닌 상속을 이용한 방법을 통해 알아보도록 하겠습니다.      
   
# 용어 설명  
* **client : 사용자**
  * 직접 Invoker를 이용해 프로그램을 사용하는 자를 일컫는다.  
* **Invoker : 호출자**
  * 기능의 실행을 요청하는 호출자 클래스이다.  
  * 즉 Command 의존성을 이용해 실제 기능을 수행하는 메서드를 호출한다.
* **Receiver : 수신자**
  * ConcreteCommand에서 execute 메서드를 구현할 때 필요한 클래스
  * 즉, ConcreteCommand의 기능을 실행하기 위해 사용하는 수신자 클래스
  * '실제 어떤 동작을 해야할지 알고 있는 객체'를 의미한다.      
  * 쉽게 말하자면 커멘드 명령의 대상이 되는 클래스이다. 
* **Command : 명령**
  * 실행될 기능에 대한 인터페이스
  * 실행될 기능과 관련된 추상 메서드를 선언함
  * 예시에는 `execute()` 메서드를 사용할 것이다.   
* **ConcreteCommand : 구체적 명령**
  * 실제로 실행되는 기능을 구현한 클래스 
  * 즉, Command 를 구현한 클래스  
  * 인터페이스의 추상 메서드를 오버라이딩 하여 각 클래스에 맞게 정의한다.   
    
# 설계 방법 


# 예제
* client : MyProgram
* Invoker : RobotKit
* Receiver : Robot 
* Command : Command 인터페이스 
* ConcreteCommand : MoveForwardCommand, TurnCommand, PickupCommand

## 기본 코드  


# 참고         
https://gmlwjd9405.github.io/2018/07/07/command-pattern.html - 정리가 깔끔하시다.  
https://jusungpark.tistory.com/18 - 용어에 대한 쉬운 이해를 도와주신다.      
