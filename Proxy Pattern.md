# Proxy pattern
              
[1. Proxy pattern 이란?](#Proxy-pattern-이란)     
[2. 용어 설명](#용어-설명)     
[3. 설계 방법](#설계-방법)     
[4. 예제](#예제)     
   
# Proxy pattern 이란?        
> 실제 기능을 수행하는 객체 대신 가상의 객체를 사용해 로직의 흐름을 제어하는 디자인 패턴입니다.

어느정도 규모가 있는 회사 대표는 만나기 어렵다.  
스케줄을 조율하거나 메시지를 남기거나 하는 등 
어지간한 일은 비서, 대리인을 통해 처리가 된다.   
즉, 회사 대표가 직접 등장하는 일은 그만큼 중요도가 있고 권한이 필요한 일이다.   
    
프로그래밍에서 사용되는 클래스들 중에서도     
대량의 데이터를 인터넷에서 받아와야 해서 시간이 오래 걸리거나     
메모리를 많이 차지하는 등의 이유로 객체로 여럿 생성하기가 부담이 되는 것들이 있다.      
            
그럴때는 그 클래스의 `proxy`, 대리자 역할을 하는 클래스를 따로 두어서      
가벼운 일은 프록시 객체가 처리하고 무거운 작업을 할 때는 실제 객체를 생성하면 된다.   
그리고 이러한 기법이 바로, Proxy Pattern 이다.    

대표적인 예로 유튜브 썸네일이 있다.       
유튜브의 썸네일은 제목과 프리뷰 영상으로 이루어져있다.        
하지만 제목은 일관성 있게 출력이 되는데 썸네일은 마우스를 올려야만 실행된다.     

* 제목 : 가벼운 작업
* 프리뷰 영상 : 무거운 작업 

프리뷰 영상은 데이터를 받고서 실행해야하므로 무거운 작업이다.        
또한, 한 페이지에 여러개의 썸네일이 존재하니 메모리적으로도 부담이 된다.      
그렇기 때문에 일반적으로 보여지는 썸네일은 프록시 객체로 처리를 하고
마우스를 올렸을 때 동작하는 작업은 실제 객체로 처리하는 방법을 사용하면 된다.    


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
2. `Adaptee`와 `Target Interface`를 연결하는 'Adpater 클래스' 작성    
3. `Adapter` 클래스를 의존성 주입하여 사용한다.    

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

## 3. `Adapter` 클래스를 의존성 주입하여 사용한다.  

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
https://jdm.kr/blog/235

* **동영상 :**       
얄팍한 코딩지식_디자인패턴1 : https://www.youtube.com/watch?v=lJES5TQTTWE    
