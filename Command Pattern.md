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
## 기존 코드

```java
package command_before;

public class MyProgram {
    public static void main(String[] args) {
        RobotKit robotKit = new RobotKit();
        robotKit.addCommand(Robot.command.FORWARD);
        robotKit.addCommand(Robot.command.TURN_LEFT);
        robotKit.addCommand(Robot.command.TURN_RIGHT);
        robotKit.addCommand(Robot.command.PICKUP);
    }
}
```
```java
package command_before;

import java.util.ArrayList;

public class RobotKit {
    private Robot robot = new Robot();
    private ArrayList<Robot.command> commands;

    public void addCommand(Robot.command command) {
        commands.add(command);
    }

    public void start() {
        for (Robot.command command : commands) {
            if (command == Robot.command.FORWARD) {
                robot.moveForward(1);
            } else if (command == Robot.command.TURN_LEFT) {
                robot.turn(Robot.Direction.LEFT);
            } else if (command == Robot.command.TURN_RIGHT) {
                robot.turn(Robot.Direction.RIGHT);
            } else if (command == Robot.command.PICKUP) {
                robot.pickup();
            }
        }
    }
}
```
```java
package command_before;

public class Robot {

    public enum Direction {LEFT, RIGHT}

    public enum command {FORWARD, TURN_LEFT, TURN_RIGHT, PICKUP}

    public void moveForward(int space) {
        System.out.println(space + " 칸 전진");
    }

    public void turn(Direction _direction) {
        System.out.println(
                (_direction == Direction.LEFT ? "왼쪽" : "오른쪽") + "으로 방향전환"
        );
    }

    public void pickup() {
        System.out.println("앞의 물건 집어 들기");
    }

}
```

## Command 적용
* client : MyProgram
* Invoker : RobotKit
* Receiver : Robot 
* Command : Command 인터페이스 
* ConcreteCommand : MoveForwardCommand, TurnCommand, PickupCommand

### 변경이 이루어질 코드를 추상 클래스 or 인터페이스로 분리한다.   
**RobotKit 변경 전**
```java
package command_before;

import java.util.ArrayList;

public class RobotKit {
    private Robot robot = new Robot();
    private ArrayList<Robot.command> commands;

    public void addCommand(Robot.command command) {
        commands.add(command);
    }

    public void start() {
        for (Robot.command command : commands) {
            if (command == Robot.command.FORWARD) {
                robot.moveForward(1);
            } else if (command == Robot.command.TURN_LEFT) {
                robot.turn(Robot.Direction.LEFT);
            } else if (command == Robot.command.TURN_RIGHT) {
                robot.turn(Robot.Direction.RIGHT);
            } else if (command == Robot.command.PICKUP) {
                robot.pickup();
            }
        }
    }
}
```
**RobotKit 변경 후**
```java
package command_before;

import java.util.ArrayList;

public class RobotKit {
    private Robot robot = new Robot();
    private ArrayList<Robot.command> commands;

    public void addCommand(Robot.command command) {
        commands.add(command);
    }

    public void start() {
        for (Robot.command command : commands) {
        }
    }
}
```
* 기능이 추가되면 if 구문에 있는 값들이 변경될 것이므로 if 구문을 지워준다.    

**Command 추상 클래스 생성**
```java
abstract class Command {
    protected Robot robot;

    public void setRobot(Robot _robot) {
        robot = _robot;
    }
    public abstract void execute();
}
```
* RobotKit에서 지운 구문을 공통으로 묶어줄 수 있는 추상클래스 or 인터페이스를 정의합니다.   
* 인터페이스로 정의할 경우 Robot을 ConcreteCommand 에서 의존받도록 설정해야합니다.     
* 저희의 ConcreteCommand는 Command를 상속받기에 자동으로 의존 관계를 가집니다.
* 단, 추상 클래스 Command에서 주입받는 것을 생각해야 합니다.   
* 인터페이스 : ConcreteCommand 에서 주입
* 추상 클래스 : Command 에서 주입      
* 물론 이 같은 방법이 절대적인 것은 아니고 본인의 스타일대로 작성해주시길 바랍니다.       

**RobotKit이 Command를 호출하도록 수정**
```java
package command;

import java.util.ArrayList;

public class RobotKit {
    private Robot robot = new Robot();
    private ArrayList<Command> commands = new ArrayList<>();

    public void addCommand(Command command) {
        commands.add(command);
    }

    public void start() {
        for(Command command : commands) {
            command.setRobot(robot);
            command.execute();
        }
    }
}
```
* 기존 Enum 타입을 Commnad 타입으로 변경한다.(command enum은 삭제해도 된다.)
* Command가 Robot을 주입 받도록 설정했으므로 Command에 객체를 넣어준다.  
* 그리고 Command의 공통 메서드인 `execute()`를 실행한다. 

### Command 추상 클래스를 상속한 ConcreteCommand를 정의한다.  

**MoveForwardCommand**
```java
public class MoveForwardCommand extends Command {
    int space;

    public MoveForwardCommand(int _space) {
        space = _space;
    }

    @Override
    public void execute() {
        robot.moveForward(space);
    }
}
```
* 이전과 달리 움직이는 칸 갯수도 넣어줬다.       
* 기존 코드에서 이 같은 작업을 하려면 코드가 엄청 길어질 것이다.     
* 왜냐하면 나머지 메서드는 정수형 타입이 필요 없기에 이를 처리하는 로직도 넣어야했다.   
       
**TurnCommand**
```java
class TurnCommand extends Command {
    Robot.Direction direction;
    public TurnCommand(Robot.Direction _direction) {
        direction = _direction;
    }

    @Override
    public void execute() {
        robot.turn(direction);
    }
}
```

**PickupCommand**
```java
class PickupCommand extends Command {

    @Override
    public void execute() {
        robot.pickup();
    }
}
```

### 클라이언트에서 ConcreteCommand 의존성 주입을 시켜준다.    
```java
public class MyProgram {
    public static void main(String[] args) {
        RobotKit robotKit = new RobotKit();

        robotKit.addCommand(new MoveForwardCommand(2));
        robotKit.addCommand(new TurnCommand(Robot.Direction.LEFT));
        robotKit.addCommand(new MoveForwardCommand(1));
        robotKit.addCommand(new TurnCommand(Robot.Direction.RIGHT));
        robotKit.addCommand(new PickupCommand());

        robotKit.start();
    }
}
```


## 기본 코드  
정리 필요 

# 참고         
https://gmlwjd9405.github.io/2018/07/07/command-pattern.html - 정리가 깔끔하시다.  
https://jusungpark.tistory.com/18 - 용어에 대한 쉬운 이해를 도와주신다.      
