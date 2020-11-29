# Command pattern
     
[1. Command pattern 이란?](#Command-pattern-이란)     
[2. 설계 방법](#설계-방법)     
[3. 예제](#예제)     
   
# Command pattern 이란?        
> 실행될 기능을 캡슐화함으로써 주어진 여러 기능을 실행할 수 있는 재사용성이 높은 클래스를 설계하는 패턴       
> 기능에 필요한 정보를 저장 또는 로깅, 취소할 수 있으며 객체 지향의 장점을 활용 가능하도록 만든 패턴이다. 

# 용어 설명  
Command
실행될 기능에 대한 인터페이스
실행될 기능을 execute 메서드로 선언함
ConcreteCommand
실제로 실행되는 기능을 구현
즉, Command라는 인터페이스를 구현함
Invoker
기능의 실행을 요청하는 호출자 클래스
Receiver
ConcreteCommand에서 execute 메서드를 구현할 때 필요한 클래스
즉, ConcreteCommand의 기능을 실행하기 위해 사용하는 수신자 클래스
https://gmlwjd9405.github.io/2018/07/07/command-pattern.html

        
        
# 참고         
https://gmlwjd9405.github.io/2018/07/07/command-pattern.html - 정리가 가장 깔끔하시다.  
