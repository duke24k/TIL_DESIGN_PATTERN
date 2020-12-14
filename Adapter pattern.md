# Adapter pattern
    
`[사진]` 
   
[1. Adapter pattern 이란?](#Adapter-pattern-이란)     
[2. 용어 설명](#용어-설명)     
[3. 설계 방법](#설계-방법)     
[4. 예제](#예제)     
   
# Adapter pattern 이란?        
> 한 클래스의 인터페이스를 클라이언트에서 사용하고자하는 다른 인터페이스로 변환한다.    
> 어댑터를 이용하면 인터페이스 호환성 문제 때문에 같이 쓸 수 없는 클래스들을 연결해서 쓸 수 있다.

이름 그대로 어댑터처럼 사용되는 패턴이다.   
한국이 220V 전압을 사용하는 것과 달리 나라마다 전압의 크기와 규격이 다른 경우가 많다.   
그럴 때 우리는 '어댑터'를 통해 우리의 전자 기기가 사용될 수 있도록 설정을 해준다.     
어댑터 패턴도 마찬가지이다.  
    
기존에 있는 시스템에 새로운 써드파티 라이브러리가 추가된다던지,     
레거시 인터페이스를 새로운 인터페이스로 교체하는 경우에     
코드의 재사용성을 높일 수 있는 방법이 어댑터 패턴을 사용하는 것이다.      
    
쉽게 설명하자면 라이브러리마다 인터페이스 및 메서드의 이름이 다르다.     
만약 새로운 기능을 추가하고 싶은데 인터페이스와 메스드의 이름이 다르다면?          
일반적인 방법으로는 기존 코드를 수정하여 새로 작성해야 한다.    
하지만 우리는 OCP 원칙을 지키고 싶다.      
이럴 때 우리가 사용할 수 있는 방법이 바로 어댑터 패턴이다.     

# 용어 설명  
* **client : 사용자**
  * 써드파티 라이브러리나 외부시스템을 사용하려는 쪽이다.   
  * 전략 패턴을 기준으로 말하자면 `Context`로 라이브러리를 직접 호출한다.        
* **Adaptee : 외부 시스템** 
  * 써드파티 라이브러리나 외부 시스템을 의미한다.
  * 즉, 어댑터를 사용해야 하는 대상이다.   
* **Adapter : 어댑터-변환기**
  * `Client`와 `Adaptee`중간에서 호환성이 없는 둘을 연결시켜주는 역할을 한다. 
  * `Target Interface`를 구현하며, `Client`는 인터페이스를 통해 어댑터에 요청을 보낸다. 
  * 어댑터는 `Clinet`의 요청을 `Adaptee`가 이해할 수 있는 방법으로 전달하고, 
    처리는 `Adaptee`에서 이루어진다.
* **Target Interface : 변환 대상 인터페이스**
  * `Adapter`가 구현(implements) 하는 인터페이스이다. 
  * 전략 패턴을 기준으로 말하자면 `Strategy`이다.   
  * `Client`는 `Target Interface`를 통해 `Adaptee`라이브러리를 사용한다.
  
# 설계 방법 
1. `Adaptee`와 현재 `Target Interface`를 비교해본다.  
2. 
3. 

# 예제
## 기존 코드
> 기존 전략 패턴을 그대로 가져와 사용한 것입니다.       
> https://github.com/kwj1270/TIL_DESIGN_PATTERN/blob/main/Strategy%20pattern.md    

**Main**
```java
public class Main {
    public static void main(String[] args) {
        MyProgram myProgram = new MyProgram();
        myProgram.testProgram();
    }
}
```
**MyProgram**
```java
public class MyProgram {
    private SearchButton searchButton = new SearchButton(this);

    public void setModeAll() {
        searchButton.setSearchStrategy(new SearchStrategyAll());
    }

    public void setModeImage() {
        searchButton.setSearchStrategy(new SearchStrategyImage());
    }

    public void setModeNews() {
        searchButton.setSearchStrategy(new SearchStrategyNews());
    }

    public void setModeMap() {
        searchButton.setSearchStrategy(new SearchStrategyMap());
    }

    public void testProgram() {
        searchButton.onClick();
        setModeImage();
        searchButton.onClick();
        setModeNews();
        searchButton.onClick();
        setModeMap();
        searchButton.onClick();
    }
}
```

**SearchButton - Client**
```java
public class SearchButton {

    private MyProgram myProgram;

    public SearchButton(MyProgram _myProgram){
        myProgram = _myProgram;
    }

    private SearchStrategy searchStrategy = new SearchStrategyAll();

    public void setSearchStrategy(SearchStrategy _searchStrategy) {
        searchStrategy = _searchStrategy;
    }

    public void onClick() {
        searchStrategy.search();
    }

}
```

**SearchStrategy - Target Interface/Strategy**
```java
public interface SearchStrategy {
    public void search();
}
```

**ConcreateStrategies**
```java
class SearchStrategyAll implements SearchStrategy {

    @Override
    public void search() {
        System.out.println("SEARCH ALL");
        // 전체 검색하는 코드
    }
}
```
```java
public class SearchStrategyImage implements SearchStrategy {

    @Override
    public void search() {

        System.out.println("SEARCH IMAGE");
        // 이미지 검색하는 코드
    }
}
```
```java
public class SearchStrategyNews implements SearchStrategy {

    @Override
    public void search() {

        System.out.println("SEARCH NEWS");
        // 뉴스 검색하는 코드
    }
}
```
```java
class SearchStrategyMap implements SearchStrategy {

    @Override
    public void search() {

        System.out.println("SEARCH MAP");
        // 지도 검색하는 코드
    }
}
```

## 1. `Adaptee`와 현재 `Target Interface`를 비교해본다.  

**FindMovieAlgorithm - adaptee**
```java
interface FindAlgorithm {
    public void find(boolean global);
}

public class FindMovieAlgorithm implements FindAlgorithm {
    @Override
    public void find(boolean global) {
        System.out.println(
                "find movie" + (global ? " globally" : "")
        );
        // 동영상 검색하는 코드
        // ...
        // ...
    }
}
```

**SearchStrategy - Target Interface/Strategy**
```java
public interface SearchStrategy {
    public void search();
}
```

* adaptee :    
  * FindAlgorithm 인터페이스 
  * find 메서드
  * 메서드내 인자 있음 

* Target Interface : 
  * SearchStrategy 인터페이스 
  * search() 메서드
  * 메서드내 인자 없음 

## 2. `Adaptee`와 `Target Interface`를 연결하는 'Adpater 클래스' 작성   
**SearchFindAdapter**
```java
public class SearchFindAdapter implements SearchStrategy{
    private FindAlgorithm findAlgorithm;

    public SearchFindAdapter(FindAlgorithm _findAlgorithm){
        findAlgorithm = _findAlgorithm;
    }

    @Override
    public void search() {
        findAlgorithm.find(true);
    }
}
```   
* `Target Interface`를 구현한 `Adpater` 클래스를 만든다.         
* `FindAlgorithm`를 구현한 객체를 참조할 수 있는 인스턴스 변수를 정의한다.      
* `Target Interface`의 추상 메서드 안에서는 `Adaptee`메서드를 호출한다.     

## 3.`Adapter` 클래스를 의존성 주입하여 사용한다.  

**MyProgram**
```java
public class MyProgram {
    private SearchButton searchButton = new SearchButton(this);

    public void setModeAll() {
        searchButton.setSearchStrategy(new SearchStrategyAll());
    }

    public void setModeImage() {
        searchButton.setSearchStrategy(new SearchStrategyImage());
    }

    public void setModeNews() {
        searchButton.setSearchStrategy(new SearchStrategyNews());
    }

    public void setModeMap() {
        searchButton.setSearchStrategy(new SearchStrategyMap());
    }

    public void setModeMovie() {
        searchButton.setSearchStrategy(new SearchFindAdapter(new FindMovieAlgorithm()));
    }

    public void testProgram() {
        searchButton.onClick();
        setModeImage();
        searchButton.onClick();
        setModeNews();
        searchButton.onClick();
        setModeMap();
        searchButton.onClick();
        setModeMovie();
        searchButton.onClick();
    }
}
```
* `new 어댑터(new Adaptee_구현_클래스())` 방식으로 의존성 주입을 했다.   
* 가능한 이유는 `Adatper`를 `Target Interface`로 구현했기 때문이다.  
* 참고로 `Client`인 `SearchButton` 코드는 변경되지 않았다. 
* 즉, OCP 원칙을 위배하지 않고 기능을 추가시켰다.    

# 참고     
* **블로그 :**    
https://yaboong.github.io/design-pattern/2018/10/15/adapter-pattern/ 
   
* **동영상 :**       
얄팍한 코딩지식_디자인패턴1 : https://www.youtube.com/watch?v=lJES5TQTTWE    
