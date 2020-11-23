# State pattern

[1. State pattern 이란?](#State-pattern-이란)     
[2. 관련 용어](#관련-용어)     
[3. 설계 방법](#설계-방법)     
[4. 예제](#예제)     

   
## State pattern 이란?       
> 일련의 규칙에 따라 객체의 상태(State)를 변화시켜, 객체가 할 수 있는 행위를 바꾸는 패턴이다.
     
* 객체의 특정 상태를 클래스로 선언하고, 클래스에서는 해당 상태에서 할 수 있는 행위들을 메서드로 정의한다.    
* 자신이 직접 상태를 체크하여 상태에 따라 행위를 호출하지 않고, 상태를 객체화 하여 행동을 할 수 있도록 위임하는 패턴을 말합니다.   
* 전략패턴과 비교하자면 아래와 같습니다.   
  * **전략패턴 :** 모드를 체크하여 특정 모드에 따라 알고리즘을 갈아 끼우는 방식
  * **상태패턴 :** 모드 자체를 바꾸어서 `모드+알고리즘`을 한번에 갈아 끼워 실행하는 방식   
       
**정리 : 모드에 알맞는 알고리즘을 사용하는 것이 아닌 모드 자체를 바꾸는 과정을 통해 알맞은 알고리즘을 사용하는 디자인 패턴**              
            
## 관련 용어      
* **Context :**   
  * 여러 가지 내부 상태를 가질 수 있는 클래스     
  * 특정 메서드가 호출되면 상태 객체에게 그 작업을 위임한다.    
   
* **State :**   
  * 모든 상태 클래스에 대한 공통 인터페이스를 정의한다.    
  * 모든 상태 클래스에서 이를 구현하기 때문에 다형성을 이용하여 사용할 수 있다. (OCP)     
   
* **ConcreateState :**    
  * Context로 부터 전달된 요청을 처리하는 상태 클래스.        
  * 각각의 구상 클래스들은 요청을 처리하는 방법을 자기 나름의 방식으로 구현한다.    
  * Context에서 상태를 바꾸기만 하면 행동도 같이 바뀌게 된다.   
  
## 설계 방법
1. Context에서 변경(확장)될 것과 변하지 않을 것을 엄격히 구분
2. 변경(확장)될 것을 인터페이스로 추출하여 State 정의
3. State 인터페이스를 구현한 ConcreateState 클래스 정의
4. Context에서 State 인터페이스에 의존하도록 코드를 작성
5. 다형성을 이용하여 참조된 ConcreateState 인스턴스의 메서드 사용 


## 예제   
### 예제 기본 코드    
**MyProgram**
```java
public class MyProgram {

    public static void main(String[] args) {

        final ModeSwitch modeSwitch = new ModeSwitch();
        modeSwitch.onSwitch();
        modeSwitch.onSwitch();
        modeSwitch.onSwitch();
        modeSwitch.onSwitch();
    }
}
```

**ModeSwitch**
```java
public class ModeSwitch {

    private boolean darkMode = false;

    public void onSwitch() {
        if(darkMode) {
            System.out.println("FROM DARK TO LIGHT");
            darkMode = false;
        } else {
            System.out.println("FROM LIGHT TO DARK");
            darkMode = true;
        }
    }
}
```

**문제점**
* 최초 기능 수행에는 문제가 없지만 만약 기능이 추가된다면 `ModeSwitch`의 `if-else`구문의 길이가 커진다.
* 또한, 새로운 모드가 추가된다면 `if-else`구문을 `if-elseif-else` 구문으로 바꾸고 코드 길이도 길어진다.  
* 코드의 길이가 길어지면 가독성이 낮아지는 것은 물론, 재사용에도 비효율적이다.   
* 해결 방법은 **상태 패턴을 사용하자**    
     
### 1. Context에서 변경(확장)될 것과 변하지 않을 것을 엄격히 구분   
> 클래스로 표현할 상태 구분 및 인터페이스로 만들어야 할 부분을 구분한다.   
   
**ModeSwitch**
```java
public class ModeSwitch {
    
    // 객체로 만들 부분 // 
    private boolean darkMode = false;
    
    // 변경이 일어나야 하는 부분 //
    public void onSwitch() {
        if(darkMode) {
            System.out.println("FROM DARK TO LIGHT");
            darkMode = false;
        } else {
            System.out.println("FROM LIGHT TO DARK");
        }
    }
    /////////////////////////
}
```

### 2. 변경(확장)될 것을 인터페이스로 추출하여 State 정의    
> 특정 상태를 클래스로 만들어 표현하고 변경이 일어날 부분을 인터페이스로 정의한다.

**ModeSwitch**
```java
public class ModeSwitch {
    
    // 일단 지워짐 
    
    public void onSwitch() {
      // 아직 코드 넣지 않음
    }
}
```

**ModeState**
```java
public interface ModeState {
    public void toggle(ModeSwitch modeSwitch);
}
```

### 3. State 인터페이스를 구현한 ConcreateState 클래스 정의
> State 패턴은 알고리즘 뿐만 아니라 상태까지 바뀌는 로직이 있어야 한다.       
> Context에서 상태를 바꿔도 되고 ConcreateState 에서 상태를 바꿔도 된다.   

**ModeStateLight**
```java
public class ModeStateLight implements ModeState {

    @Override
    public void toggle(ModeSwitch modeSwitch) {
        System.out.println("FROM LIGHT TO DARK");
        modeSwitch.setState(new ModeStateDark());
    }
}
```

**ModeStateLight**
```java
public class ModeStateDark implements ModeState {

    @Override
    public void toggle(ModeSwitch modeSwitch) {
        System.out.println("FROM DARK TO LIGHT");
        modeSwitch.setState(new ModeStateLight());
    }
}
```

### 4. Context에서 State 인터페이스에 의존하도록 코드를 작성
> OCP 원칙에 맞게끔 인터페이스 자료형을 이용하여 의존하도록 설정     
     
**ModeSwitch**
```java
public class ModeSwitch {

    private ModeState modeState = new ModeStateLight();

    public void setState(ModeState modeState) {
        this.modeState = modeState;
    }

    public void onSwitch() {
        // 아직 코드 넣지 않음
    }

}
```


### 5. 다형성을 이용하여 참조된 ConcreateState 인스턴스의 메서드 사용    
> 동일한 메서드를 사용하지만 상태에 따라 다른 동작을 실행한다.         
    
**ModeSwitch**
```java
public class ModeSwitch {

    private ModeState modeState = new ModeStateLight();

    public void setState(ModeState modeState) {
        this.modeState = modeState;
    }

    public void onSwitch() {
        modeState.toggle(this);
    }

}
```  

# 참조
> 베이스 :   
   
* **블로그 :**    
https://beomseok95.tistory.com/239 - 깔끔한 정리             
https://jobjava00.github.io/language/java/basic/singleton/ - 깔끔한 정리    
https://yaboong.github.io/design-pattern/2018/09/28/thread-safe-singleton-patterns/ - 보충 설명             
https://medium.com/webeveloper/%EC%8B%B1%EA%B8%80%ED%84%B4-%ED%8C%A8%ED%84%B4-singleton-pattern-db75ed29c36 - 보충 설명         
https://brunch.co.kr/@kd4/8 - 리플렉션   
     
* **동영상**    
얄팍한 코딩지식_디자인패턴1 : https://www.youtube.com/watch?v=lJES5TQTTWE     
  
