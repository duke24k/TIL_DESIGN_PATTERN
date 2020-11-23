# Singletone pattern

[1. Singletone pattern 이란?](#Singletone-pattern-이란)     
[2. 관련 용어](#관련-용어)     
[3. 설계 방법](#설계-방법)     
[4. 예제](#예제)     

   
## Singletone pattern 이란?       
> 인스턴스가 오직 1개만 생성되어야 하는 경우에 사용하는 패턴               
     
* 하나의 인스턴스 메모리를 등록해서 여러 스레드가 동시에 해당 인스턴스를 공유하도록 한다.            
  * 생성자가 여러 차례 호출되더라도 실제로 생성되는 객체는 하나이고 최초 생성된 객체를 반환한다.                
* 단순히 기능만 제공하는 `유틸 클래스`는 인스턴스가 1개만 있어도 되므로 이 같은 패턴을 사용하기도 한다.          
* 애플리케이션 전역에 걸쳐 공통된 상태를 유지하는 객체에서도 사용한다. (다크 모드 같은 상태를 가진 Settings 클래스)                
* 주의할 점: 
  * 동시성(Concurrency) 문제를 고려해서 설계해야한다.    
    * 멀티쓰레드 환경에서 동기화 처리를 하지 않으면 인스턴스가 2개가 생성될 우려가 있다.  
  * 전역 변수를 사용하지 말라는 말이 있듯이 꼭 필요한 경우가 아니라면 지양해야한다.   
       
**정리 : 인스턴스를 인스턴스가 오직 1개만 생성되어야 하는 경우에 사용되며 동시성을 고려해서 설계해야한다.**        
            
## 관련 용어      
  
  
## 설계 방법
1. 인스턴스가 오직 1개만 생성되어야 하는 클래스를 식별  
2. private 접근 제어자를 이용해서 생성자 호출 제한하기    
3. 클래스 내부에서 static 형태의 객체 생성과 참조 변수 설정
4. static 인스턴스를 외부에서 참조할 수 있도록 getInstance static 메서드 생성  
5. 해당 인스턴스를 참조하는 관련 코드의 내용을 변경해주고 결과 확인

## 예제   
### 예제 기본 코드    
**MyProgram**
```java
public class MyProgram {
    public static void main (String[] args){
        new FirstPage().setAndPrintSettings();
        new SecondPage().printSettings();
    }
}
```

**FirstPage**
```java
public class FirstPage {
    private Settings settings = new Settings();

    public void setAndPrintSettings() {
        settings.setDarkMode(true);
        settings.setFontSize(15);

        System.out.println(settings.getDarkMode()+ " " + settings.getFontSize());
    }
}

```

**SecondPage**
```java
public class SecondPage {
        private Settings settings = new Settings();

        public void printSettings() {
            System.out.println(settings.getDarkMode()+ " " + settings.getFontSize());
        }
}
```

**Settings**
```java
public class Settings {

    private boolean darkMode = false; // default false
    private int fontSize = 13; // default 13

    public  boolean getDarkMode(){return darkMode;}
    public int getFontSize(){return fontSize;}
    public void setDarkMode(boolean _darkMode){darkMode = _darkMode;}
    public void setFontSize(int _fontSize){fontSize = _fontSize;}
}
```

**문제점**
* FirstPage 에서는 `darkMode : true` , `fontSize : 15`로 맞추었는데
* SecondPage 에서는 `new Settings()` 를 통해 디폴트 값으로 설정된 셋팅 클래스를 가지고 있다.   
* 해결 방법은? : SecondPage 에서도 FirstPage 와 같은 로직을 추가해주면 안되나? **안된다.**   
  * 만약 페이지가 100개 이상이라면 각 페이지마다 로직 처리 해주고 수정해야하면 또 100개 이상을 수정해야함     
* 해결 방법은 **싱글톤 패턴을 사용하자**    
     
### 1. 인스턴스가 오직 1개만 생성되어야 하는 클래스를 식별     
> 여러 클래스에서 공통된 상태를 가지거나 기능을 제공하는 클래스를 식별         
    
**Settings**
```java
public class Settings {
  
    private boolean darkMode = false; // default false
    private int fontSize = 13; // default 13

    public  boolean getDarkMode(){return darkMode;}
    public int getFontSize(){return fontSize;}
    public void setDarkMode(boolean _darkMode){darkMode = _darkMode;}
    public void setFontSize(int _fontSize){fontSize = _fontSize;}

}
```

### 2. private 접근 제어자를 이용해서 생성자 호출 제한하기     
> new 생성자를 이용하면 불변 객체라도 메모리를 새롭게 할당한다는 단점이 있다.    
     
**Settings**
```java
public class Settings {
  
    private Settings(){}
  
    private boolean darkMode = false; // default false
    private int fontSize = 13; // default 13

    public  boolean getDarkMode(){return darkMode;}
    public int getFontSize(){return fontSize;}
    public void setDarkMode(boolean _darkMode){darkMode = _darkMode;}
    public void setFontSize(int _fontSize){fontSize = _fontSize;}

}
```


### 3. 클래스 내부에서 static 형태의 객체 생성과 참조 변수 설정
> 공용으로 사용되어야 하므로 static 참조 변수와 인스턴스를 만든다.   
   
**Settings**
```java
public class Settings {

    private static Settings settings = new Settings();
    private Settings(){}

    private boolean darkMode = false; // default false
    private int fontSize = 13; // default 13

    public  boolean getDarkMode(){return darkMode;}
    public int getFontSize(){return fontSize;}
    public void setDarkMode(boolean _darkMode){darkMode = _darkMode;}
    public void setFontSize(int _fontSize){fontSize = _fontSize;}

}

```

### 4. static 인스턴스를 외부에서 참조할 수 있도록 getInstance static 메서드 생성  
> 외부에서 `클래스.메서드()` 형태로 사용해야 하므로 static 메서드로 정의한다.   
     
**Settings**
```java
public class Settings {

    private static Settings settings = new Settings();

    private Settings() { }

    public static Settings getInstance() { return settings; }

    private boolean darkMode = false; // default false
    private int fontSize = 13; // default 13

    public  boolean getDarkMode(){return darkMode;}
    public int getFontSize(){return fontSize;}
    public void setDarkMode(boolean _darkMode){darkMode = _darkMode;}
    public void setFontSize(int _fontSize){fontSize = _fontSize;}

}
```

### 5. 해당 인스턴스를 참조하는 관련 코드의 내용을 변경해주고 결과 확인   
> `new 생성자()` 패턴의 코드를 `getInstance()` 형태로 바꾸어주자      

**FirstPage**
```java
public class FirstPage {
    private Settings settings = Settings.getInstance();

    public void setAndPrintSettings() {
        settings.setDarkMode(true);
        settings.setFontSize(15);

        System.out.println(settings.getDarkMode()+ " " + settings.getFontSize());
    }
}
```  

**SecondPage**
```java
public class SecondPage {
        private Settings settings = Settings.getInstance();

        public void printSettings() {
            System.out.println(settings.getDarkMode()+ " " + settings.getFontSize());
        }
}
```

**결과**
```
true 15
true 15
```
* 같은 값이 나온것을 보면 동일한 인스턴스를 참조하고 있음을 알 수 있다.   
  
# 싱글톤 패턴을 만드는 구현 방법 
## 1. Eager Initialization (이른 초기화)      
> 싱글톤 객체를 instance라는 변수로 미리 생성해 놓고 사용하는 방식           
     
**Settings**
```java
public class Settings {

    private static Settings settings = new Settings();

    private Settings() { }

    public static Settings getInstance() { return settings; }

    private boolean darkMode = false; // default false
    private int fontSize = 13; // default 13

    public  boolean getDarkMode(){return darkMode;}
    public int getFontSize(){return fontSize;}
    public void setDarkMode(boolean _darkMode){darkMode = _darkMode;}
    public void setFontSize(int _fontSize){fontSize = _fontSize;}

}
```
**장점 :**      
* static 변수로 싱글톤 객체를 참조하기 때문에 클래스 로더에 의해 클래스가 로딩될 때 싱글톤 객체가 생성됩니다.    
* 클래스 로더에 의해 클래스가 최초 로딩 될 때 객체가 생성됨으로 `Thread-safe` 합니다.
  * **Thread-safe 한 이유 :**    
  * 최초 로딩은 1번만 실행   
  * 여러 쓰레드가 존재하지 않을때 생기므로 여러 쓰레드가 생긴 후에는 해당 객체를 공유하여 사용     
            
**단점 :**          
* 클라이언트에서 싱글톤 객체를 사용하지 않아도 싱글톤 객체가 생성(new) 되어 메모리를 차지하고 있습니다.         
* 클래스 로더에 의해 로딩된 클래스들은 다시 JVM상에서 없앨 수 없습니다.     

## 2. Lazy Initialization (늦은 초기화 방식)
> 싱글톤 클래스 타입의 instance 참조 변수만 미리 생성해 놓고 나중에 객체를 참조하는 형식  
        
**Settings**
```java
public class Settings {

    private static Settings settings;
    
    private Settings() { }
    
    public static Settings getInstance() { 
      if(settings == null) {settings = new Settings();}
      return settings; 
    }

    private boolean darkMode = false; // default false
    private int fontSize = 13; // default 13

    public  boolean getDarkMode(){return darkMode;}
    public int getFontSize(){return fontSize;}
    public void setDarkMode(boolean _darkMode){darkMode = _darkMode;}
    public void setFontSize(int _fontSize){fontSize = _fontSize;}

}
```
**장점 :**       
* 싱글톤 객체가 필요할 때 인스턴스를 얻을 수 있습니다.  (Eager initialization 방식에 단점을 보완)   
    
**단점 :**    
* multi-thread 환경에서 여러 곳에서 동시에 getInstance()를 호출할 경우 인스턴스가 두 번 생성될 여지가 있습니다. (동기화 문제) 
* 메서드가 각각의 순서에 맞게 인스턴스를 생성한다면 문제가 없지만 RaceCondition 발생하면 동시에 생성이 될 가능성이 있다.   
  * A_Thread : `if(settings == null)` 부합 
  * B_Thread : `if(settings == null)` 부합 
  * A_Thread : `settings = new Settings();` 처리
  * B_Thread : `settings = new Settings();` 처리
  * 인스턴스 2개 생김  

## 3. Lazy Initialization - Thread-safe 버전 (늦은 초기화 방식)
> Lazy Initialization 을 Thread-safe 하게 만드는 방법 : synchronized 키워드를 메서드에 붙여넣어준다.     

**Settings**
```java
public class Settings {

    private static Settings settings;
    
    private Settings() { }
    
    public static synchronized Settings getInstance() { 
      if(settings == null) {settings = new Settings();}
      return settings; 
    }

    private boolean darkMode = false; // default false
    private int fontSize = 13; // default 13

    public  boolean getDarkMode(){return darkMode;}
    public int getFontSize(){return fontSize;}
    public void setDarkMode(boolean _darkMode){darkMode = _darkMode;}
    public void setFontSize(int _fontSize){fontSize = _fontSize;}

}
```
**장점 :**      
* Thread-safe한 싱글톤 객체를 보장해줄 수 있다.   
   
**단점 :**       
* 여러 thread 가 getInstance를 호출하게 되면 높은 cost 비용으로 인해 프로그램 전반에 성능 저하가 발생           
   
## 4. Initialization on demand holder idiom (holder에 의한 초기화 방식)
> Lazy initialization 장점을 가져가면서 Thread 간 동기화 문제를 동시에 해결한 방법입니다.    
     
**Settings**
```java
package SingleTone;

public class Settings {
    
    private static class SettingsHolder {
        public static final Settings settings = new Settings();
    }
    
    private Settings() { }
    public static Settings getInstance() { return SettingsHolder.settings; }

    private boolean darkMode = false; // default false
    private int fontSize = 13; // default 13

    public  boolean getDarkMode(){return darkMode;}
    public int getFontSize(){return fontSize;}
    public void setDarkMode(boolean _darkMode){darkMode = _darkMode;}
    public void setFontSize(int _fontSize){fontSize = _fontSize;}
    
}
```
정적 클래스로 정의된 내부 클래스의 초기화는 클래스 로드 시점에 이뤄지지 않는 특성이 있습니다.         
즉, getInstance를 통해 내부 클래스의 instance를 호출할 때 뒤늦게 초기화되어 객체를 할당합니다.         
   
그렇다면 의문점은 `Lazy initialization 처러 RaceCondition으로 인해 동시에 인스턴스를 생성할 수 있지 않을까?`   
하지만 이 방식이 thread safe 한 이유는 **jvm의 클래스 초기화 과정에서 보장되는 원자적 특성(시리얼 하게 동작)을 이용하기 때문입니다.**    
즉 동기화 문제를 jvm이 처리하도록 합니다.      

```
여기서는 JVM의 원자적 특성에 관한 내용 추가해야할 듯 
```

## 5. Enum Initialization (Enum 초기화 방식)
> enum의 문법적 특성을 이용한 싱글톤 객체 생성  

```java
package SingleTone;

public enum EnumSettings {

    INSTANCE; // 생성자이자 식별자를 의미 -> 밑에 정의된 생성자에 파라미터가 있다면 여기에도 인수 넣어줘야한다.   
              // 식별자라고 말을 한 것은 해당 문구를 기준으로 객체를 참조하기에 싱글톤 기준이 된다.      
              
    private boolean darkMode = false; // 디폴트 값 
    private int fontSize = 13; // 디폴트 값 

    private EnumSettings() {} // 생성자 

    public EnumSettings getInstance() {
        return INSTANCE; 
    }

    public boolean getDarkMode(){
        return darkMode;
    }
    public int getFontSize(){
        return fontSize;
    }
    public void setDarkMode(boolean darkMode){
        this.darkMode = darkMode;
    }
    public void setFontSize(int fontSize){
        this.fontSize = fontSize;
    }
}
```
**장점 :**   
* 싱글톤의 특징(단 한 번의 인스턴스 호출, Thread간 동기화) 을 가지며 비교적 간편하게 사용할 수 있는 방법입니다.
* 단 한번의 인스턴스 생성을 보장하며 사용이 간편하고 직렬화가 자동으로 처리되고 직렬화가 아무리 복잡하게 이루어져도 여러 객체가 생길 일이 없다.   
* 리플렉션을 통해 싱글턴을 깨트릴 수도 없다.

**리플렉션이란?**   
```java
Class someThing = 클래스.class;
// Class someThing = Class.forName("클래스 이름");

Method m[] = someThing.getDeclaredMethods();
Field[] f = c.getFields();
Constructor[] cs = c.getConstructors();
Class[] inter = c.getInterfaces();
Class superClass = c.getSuperclass();

> 하나의 클래스에서 다른 클래스의 정보를 알아낼 수 있는 개념    
출처 : https://opentutorials.org/module/987/8927

> 구체적인 클래스 타입을 알지 못해도, 그 클래스의 메서드/타입/변수들을 접근할 수 있도록 해준다.   
출처 : https://brunch.co.kr/@kd4/8

> 객체를 통해 클래스의 정보를 분석해 내는 프로그램 기법을 말한다.
출처: https://gyrfalcon.tistory.com/entry/Java-Reflection [Minsub's Blog]
```

# 개인적인 생각
이펙티브 자바 1장에서 생성자 대신 static 팩토리 메서드 사용에 대해서 이야기를 한다.     
링크 : https://www.youtube.com/watch?v=X7RXP6EI-5E         
깃헙 : https://github.com/keesun/study/blob/master/effective-java/item1.md      
  
어찌보면 싱글톤 패턴은 `static 팩토리 메서드` + `static instance` 같은 느낌이 든다.   
즉, `static 팩토리 메서드`의 일부분? 파생되어서 나온 개념? 이라는 생각이다.     


# 참조
> 베이스 :   
   
* **블로그 :**    
https://beomseok95.tistory.com/239 - 깔끔한 정리          
https://yaboong.github.io/design-pattern/2018/09/28/thread-safe-singleton-patterns/ - 보충 설명           
https://medium.com/webeveloper/%EC%8B%B1%EA%B8%80%ED%84%B4-%ED%8C%A8%ED%84%B4-singleton-pattern-db75ed29c36 - 보충 설명       
https://brunch.co.kr/@kd4/8 - 리플렉션 
   
* **동영상**    
얄팍한 코딩지식_디자인패턴1 : https://www.youtube.com/watch?v=lJES5TQTTWE     
  
