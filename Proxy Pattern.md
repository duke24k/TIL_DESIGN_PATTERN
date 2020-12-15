# Proxy pattern
              
[1. Proxy pattern 이란?](#Proxy-pattern-이란)     
[2. 용어 설명](#용어-설명)     
[3. 설계 방법](#설계-방법)     
[4. 예제](#예제)     
[5. 프록시 패턴의 다양한 방법들](#프록시-패턴의-다양한-방법들)     
   
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
 
우선 프록시 패턴은 3가지로 나뉘어진다.   
   
1. **Virtual Proxy :**     
   주체 클래스가 리소스 집약적인 경우이다.     
   예를들어, 주체 클래스가 해상도가 아주 높은 이미지를 처리해야 하는 경우     
   인스턴스화 할때 많은 메모리를 사용하게 되는데,     
   이런 이미지들에 동시에 많은 접근이 이루어진다면 시스템에 부하가 많이 가게 될 것이다.     
   프록시 클래스에서 자잘한 작업들을 처리하고    
   리소스가 많이 요구되는 작업들이 필요할 때에만 주체 클래스를 사용하도록 구현할 수 있다.    
2. **Protection Proxy :**    
   주체 클래스에 대한 접근을 제어하기 위한 경우이다.      
   프록시 클래스에서 클라이언트가 주체 클래스에 대한 접근을 허용할지 말지 결정하도록 할 수 있다.     
   어떤 접근권한을 가지고 있는지에 따라 그에 맞는 주체 클래스의 메소드를 호출하도록 구현할 수 있다.       
3. **Remote Proxy :**   
   프록시 클래스는 로컬에 두고, 주체 클래스는 Remote 로 존재하는 경우이다.      
   Google Docs 같은 것이 대표적인 예시이다.    
   브라우저는 브라우저대로 필요한 자원을 로컬에 가지고 있고,      
   또다른 일부 자원은 Google 서버에 있는 형태이다.     

# 용어 설명  
* **Client : 사용자**
  * 기존에는 `Real Subject`를 직접 호출하는 역할을 수행    
  * 프록시 패턴이 되면서 `proxy`객체를 호출하여 `Real Subject`를 사용하는 역할이다.   
* **Proxy : 대리인** 
  * `Real Subject`와 동일한 `Subject`를 구현하지만, 간단한 데이터만 처리한다.   
  * 무거운 데이터를 사용하고자 한다면 `Real Subject` 호출하여 사용한다.  
  * `Protection Proxy`에서는 접근 가능 여부를 확인하는 역할이다.   
* **Subject : 공통된 주제-인터페이스**
  * `Proxy`와 `Real Subject`를 묶어줄 수 있는 일련의 특성이다.
  * 쉽게 설명하면 실제 객체의 특성이라고 생각해도 된다.  
* **Real Subject : 실제 객체**
  * 실제 실행되어야 할 객체를 의미한다.   
  * `Proxy` 객체로부터 호출되어 실제 동작을 처리한다.    
  
# 설계 방법 
> 여기서는 Virtual Proxy 방법을 사용합니다.        
      
1. `Real Subject`의 특성을 분석하여 `Subject` 인터페이스 작성   
2. `Subject` 인터페이스를 구현한 `Proxy` 클래스 작성 
3. `Proxy` 클래스에서 가벼운 작업과 무거운 작업을 분리 및 `Real Subject` 호출
4. `Client`에서 OCP를 위한 인터페이스 작업으로 변경    

# 예제
## 기존 코드

**MyProgram**
```java
import java.util.ArrayList;

public class MyProgram {
    public static void main(String[] args) {
        ArrayList<RealThumbnail> thumbnails = new ArrayList<>();

        thumbnails.add(new RealThumbnail("Git 강좌", "/git.mp4"));
        thumbnails.add(new RealThumbnail("Rest API란?", "/rest-api.mp4"));
        thumbnails.add(new RealThumbnail("도커 사용법", "/docker.mp4"));
        thumbnails.add(new RealThumbnail("객체지향 프로그래밍", "/oodp.mp4"));
        thumbnails.add(new RealThumbnail("블록체인의 원리", "/blockchain.mp4"));

        for (RealThumbnail thumbnail : thumbnails) {
            thumbnail.showTitle();
        }
        // 제목:Git 강좌
        // 제목:Rest API란?
        // 제목:도커 사용법
        // 제목:객체지향 프로그래밍
        // 제목:블록체인의 원리

        thumbnails.get(2).showPreview();
        thumbnails.get(2).showPreview();
        thumbnails.get(4).showPreview();
        thumbnails.get(1).showPreview();
        thumbnails.get(3).showPreview();

        // /docker.mp4로부터 도커 사용법의 영상 데이터 다운
        // 도커 사용법의 프리뷰 재생
        // 도커 사용법의 프리뷰 재생
        // /blockchain.mp4로부터 블록체인의 원리의 영상 데이터 다운
        // 블록체인의 원리의 프리뷰 재생
        // /rest-api.mp4로부터 Rest API란?의 영상 데이터 다운
        // Rest API란?의 프리뷰 재생
        // 객체지향 프로그래밍의 프리뷰 재생

    }
}
```

**RealThumbnail**
```java
class RealThumbnail{
    private String title;
    private String movieUrl;

    public RealThumbnail(String _title, String _movieUrl) {
        title = _title;
        movieUrl = _movieUrl;
        // URL로부터 영상을 다운받는 작업 - 시간 소모
        System.out.println(movieUrl + "로부터 " + title + "의 영상 데이터 다운");
    }

    public void showTitle() {
        System.out.println("제목:" + title);
    }

    public void showPreview() {
        System.out.println(title + "의 프리뷰 재생");
    }
}
```

## 1. `Real Subject`의 특성을 분석하여 `Subject` 인터페이스 작성   

**Thumbnail**
```java
public interface Thumbnail {
    public void showTitle();
    public void showPreview();
}
```
* `Real Subject`의 기능을 인터페이스로 추상화시켰다.   

**RealThumbnail**
```java
public class RealThumbnail implements Thumbnail {
    private String title;
    private String movieUrl;

    public RealThumbnail(String _title, String _movieUrl) {
        title = _title;
        movieUrl = _movieUrl;
        // URL로부터 영상을 다운받는 작업 - 시간 소모
        System.out.println(movieUrl + "로부터 " + title + "의 영상 데이터 다운");
    }
    
    @Override
    public void showTitle() {
        System.out.println("제목:" + title);
    }
    
    @Override
    public void showPreview() {
        System.out.println(title + "의 프리뷰 재생");
    }
}
```
* 기존 `Real Subject` 다형성 OCP를 위해 인터페이스를 구현하도록 하자   

## 2. `Subject` 인터페이스를 구현한 `Proxy` 클래스 작성   

**ProxyThumbnail**
```java
public class ProxyThumbnail implements Thumbnail {
    private String title;
    private String movieUrl;
    private RealThumbnail realThumbnail; 

    public ProxyThumbnail(String _title, String _movieUrl) {
        title = _title;
        movieUrl = _movieUrl;
    }

    @Override
    public void showTitle() {
        
    }
    
    @Override
    public void showPreview() {
    }
}
```

## 3. `Proxy` 클래스에서 가벼운 작업과 무거운 작업을 분리 및 `Real Subject` 호출

**ProxyThumbnail**   
```java
public class ProxyThumbnail implements Thumbnail {
    private String title;                 // 가벼움
    private String movieUrl;              // url -> 데이터 다운
    private RealThumbnail realThumbnail;  // 무거움

    public ProxyThumbnail(String _title, String _movieUrl) {
        title = _title;
        movieUrl = _movieUrl;
    }

    public void showTitle() {
        System.out.println("제목:" + title); // 가벼운 작업 
    }
  
    public void showPreview() {
        if (realThumbnail == null) {     
            realThumbnail = new RealThumbnail(title, movieUrl); // 없으면 다운 
        }    
        realThumbnail.showPreview(); // 무거운 작업 
    }
}
```

## 4. `Client`에서 OCP를 위한 인터페이스 작업으로 변경 및 실행

**MyProgram**
```java
import java.util.ArrayList;

public class MyProgram {
    public static void main(String[] args) {
        ArrayList<Thumbnail> thumbnails = new ArrayList<Thumbnail>();

        thumbnails.add(new ProxyThumbnail("Git 강좌", "/git.mp4"));
        thumbnails.add(new ProxyThumbnail("Rest API란?", "/rest-api.mp4"));
        thumbnails.add(new ProxyThumbnail("도커 사용법", "/docker.mp4"));
        thumbnails.add(new ProxyThumbnail("객체지향 프로그래밍", "/oodp.mp4"));
        thumbnails.add(new ProxyThumbnail("블록체인의 원리", "/blockchain.mp4"));

        for (Thumbnail thumbnail : thumbnails) {
            thumbnail.showTitle();
        }
        // 제목:Git 강좌
        // 제목:Rest API란?
        // 제목:도커 사용법
        // 제목:객체지향 프로그래밍
        // 제목:블록체인의 원리

        thumbnails.get(2).showPreview();
        thumbnails.get(2).showPreview();
        thumbnails.get(4).showPreview();
        thumbnails.get(1).showPreview();
        thumbnails.get(3).showPreview();

        // /docker.mp4로부터 도커 사용법의 영상 데이터 다운
        // 도커 사용법의 프리뷰 재생
        // 도커 사용법의 프리뷰 재생  -> 한번 다운 받았으니 다시 다운 안받는다.  
        // /blockchain.mp4로부터 블록체인의 원리의 영상 데이터 다운
        // 블록체인의 원리의 프리뷰 재생
        // /rest-api.mp4로부터 Rest API란?의 영상 데이터 다운
        // Rest API란?의 프리뷰 재생
        // /oodp.mp4로부터 객체지향 프로그래밍의 영상 데이터 다운
        // 객체지향 프로그래밍의 프리뷰 재생
    }
}
```

# 5. 프록시 패턴의 다양한 방법들


# 참고     
* **블로그 :**    
https://jdm.kr/blog/235    
https://yaboong.github.io/design-pattern/2018/10/17/proxy-pattern/ 

* **동영상 :**       
얄팍한 코딩지식_디자인패턴1 : https://www.youtube.com/watch?v=lJES5TQTTWE    
