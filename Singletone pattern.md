# Singletone pattern

[1. Singletone pattern 이란?](#Singletone-pattern-이란)     
[2. 관련 용어](#관련-용어)     
[3. 설계 방법](#설계-방법)     
[4. 예제](#예제)     

   
## Singletone pattern 이란?       
> 인스턴스가 오직 1개만 생성되어야 하는 경우에 사용하는 패턴               
     
* 하나의 인스턴스 메모리를 등록해서 여러 스레드가 동시에 해당 인스턴스를 공유하도록 한다.        
  * 생성자가 여러 차례 호출되더라도 실제로 생성되는 객체는 하나이고 최초 생성된 객체를 반환한다.           
* 단순히 기능만 제공하는 `유틸 클래스`는 인스턴스가 1개만 있어도 되므로 이같은 패턴을 사용한다.       
* 또한, 애플리케이션 전역에 걸쳐 공통된 상태를 유지하는 객체에서도 사용한다. (다크 모드)         
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
    private static Settings settings = new Settings();

    private Settings() { }

    public static Settings getInstance() { return settings; }

    private boolean darkMode = false; // default false
    private int fontSize = 13; // default 13

    public  boolean getDarkMode(){return darkMode;}
    public int getFontSize(){return fontSize;}
    public void setDarkMode(boolean _darkMode){darkMode = _darkMode;}
    public void setFontSize(int _fontSize){fontSize = _fontSize;}
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
  

# 참조
> 베이스 : https://github.com/kwj1270/TIL_WooWaHanTechSeminar/blob/master/OCP%EC%99%80%20%EC%A0%84%EB%9E%B5%20%ED%8C%A8%ED%84%B4.md    
     
* **위키 :**    
https://ko.wikipedia.org/wiki/%EC%A0%84%EB%9E%B5_%ED%8C%A8%ED%84%B4
* **블로그 :**    
https://velog.io/@kyle/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%A0%84%EB%9E%B5%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80
* **동영상**    
우아한 테크코스_OCP와 전략패턴 : https://www.youtube.com/watch?v=90ZDvHl8ROE      
얄팍한 코딩지식_디자인패턴1 : https://www.youtube.com/watch?v=lJES5TQTTWE   
  
