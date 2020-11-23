# State pattern
![242DFE4A59182D801C](https://user-images.githubusercontent.com/50267433/99933249-d3b67e80-2d9d-11eb-8dd0-a08e58514385.png)     
       
[1. State pattern 이란?](#State-pattern-이란)     
[2. 관련 용어](#관련-용어)     
[3. 설계 방법](#설계-방법)     
[4. 예제](#예제)     
[5. Strategy vs State](#strategy-vs-state)
   
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

# Strategy VS State 

**Strategy client**
```java
public class Main{
    public static void main(String[] args){
        LottoNumbersAutoGenerator lottoNumbersAutoGenerator = new LottoNumbersAutoGenerator(ShuffleRandomStrategy.getInstance());
        // LottoNumbersAutoGenerator lottoNumbersAutoGenerator = new LottoNumbersAutoGenerator(ShuffleReverseStrategy.getInstance());
        // LottoNumbersAutoGenerator lottoNumbersAutoGenerator = new LottoNumbersAutoGenerator(ShuffleNothingStrategy.getInstance());
    }
}
```
Strategy 패턴은 클라이언트 쪽에서 알고리즘을 변경하기 위하여 setter를 호출해 직접 수행할 전략 객체를 주입해주었다.     
즉, 클라이언트가 구체적인 알고리즘의 수행까지는 몰라도 어느정도 무엇 무엇이 있는지 정도는 알고 있어야 한다는 것이다.    
    
**State client**
```java
public class Main {
    public static void main(String[] args) {
        final ModeSwitch modeSwitch = new ModeSwitch();
        modeSwitch.onSwitch();
        modeSwitch.onSwitch();
        modeSwitch.onSwitch();
        modeSwitch.onSwitch();
    }
}

```
State 패턴은 각 상태 구현 클래스들이 자신들의 행위를 수행하면서 직접 객체의 상태를 변경해준다.    
그렇기 때문에 클라이언트 입장에서는 직접 상태를 조작하거나 하지 않아도 된다는 점이다.    
즉, 클라이언트는 상태를 몰라도 된다라는 뜻이다.   

**결론 :**     
전략을 직접 클라이언트가 바꿔서 사용해야하는 스트래티지 패턴과는 조금은 상반되며 용도가 조금 다른 패턴이라고 볼 수 있다.   
   
* Strategy pattern : 사용자가 쉽게 알고리즘 전략을 바꿀 수 있도록 유연성을 제공. 상속의 한계를 해결하기 위하여 나온 패턴   
* State pattern : 한 객체가 동일한 동작을 상태에 따라 다르게 수행해야 할 경우 사용하는 패턴   
 
# 참조
   
* **블로그 :**    
https://coding-start.tistory.com/247 
       
* **동영상**    
얄팍한 코딩지식_디자인패턴1 : https://www.youtube.com/watch?v=lJES5TQTTWE     
  
