# Template Method Pattern   

## Template Method Pattern 이란?   
> 알고리즘의 구조를 메소드에 정의하고, 하위 클래스에서 알고리즘 구조의 변경없이 알고리즘을 재정의 하는 패턴이다.     
> 알고리즘이 단계별로 나누어 지거나, 같은 역할을 하는 메소드이지만 여러곳에서 다른형태로 사용이 필요한 경우 유용한 패턴이다.    
 
**토비의 스프링 3.1**      
```
상속을 통해 슈퍼클래스의 기능을 확장할 때 사용하는 가장 대표적인 방법.           
변하지 않는 기능은 슈퍼클래스에 만들어두고 자주 변경되며 확장할 기능은 서브클래스에서 만들도록 한다.      
```    

**얄팍한 코딩사전**   
```
어떤 일을 수행하는 몇가지 방법이 있는데 그 전반적 과정에 공통된 절차가 있을 때 코드를 효율적으로 짜기 위해 만들어진 패턴이다.   
```
   
알고리즘의 기능들을 **각각의 메서드**로 정의하여 이를 호출하는 방식으로 사용하고       
특정 메서드에 대한 로직을 변경하고자 한다면 이를 상속받는 클래스에서 오버라이딩 변형하여 사용하는 것을 의미한다.       

즉, 1개의 메인 메서드에 존재하는 **여러 기능을 각각의 서브 메서드로 만들어서 호출**하는 방식으로 사용하고     
**하위 클래스에서는 상황에 알맞게 서브 메서드를 오버라이딩**해서 OCP 원칙에 알맞게 사용하는 것이다.         
그리고 중요한 점은 메인 메서드의 형식을 따라주어야 한다.   
    
## 관련 용어  
* Abstract class : 전체적인 틀을 가진 메인 메서드와 메인 메서드 내부에서 돌아가는 추상 메스드들을 가진 클래스     
* Concreate class : Abstract class 의 메서드를 직접 구현한 구현체 클래스     

## 설계 방법 
1. 메인 메서드내에 존재하는 여러 기능들을 구분하여 각각의 서브 메서드를 만들어준다.     
2. 메인 메서드에서는 이들을 통칭적으로 사용하는 로직을 구현한다.      
3. 해당 클래스의 하위 클래스들에서는 서브 메서드를 각각의 클래스에 맞게 오버라이딩 정의한다. (단, 메인 메서드는 건들지 말것)    
4. 외부에서는 그저 메인 메서드를 호출하면 참조 인스턴스에 따라 세부 처리가 다르다.


## 예제 
### 1. 메인 메서드내에 존재하는 여러 기능들을 구분하여 각각의 서브 메서드를 만들어준다.    
**MapView**
```java
public abstract class MapView{
    protected abstract void connectMapServer();
    protected abstract void ShowMapOnScreen();
    protected abstract void moveToCurrentLocation();
    
    public void initMap(){
    // 맵 연결 기능
    // 맵 보여주기 기능
    // 맵 좌표 이동 기능 
    }
}
```
### 2. 메인 메서드에서는 이들을 통칭적으로 사용하는 로직을 구현한다.
**MapView**
```java
public abstract class MapView{
    protected abstract void connectMapServer();
    protected abstract void ShowMapOnScreen();
    protected abstract void moveToCurrentLocation();

    public void initMap(){
        connectMapServer();
        ShowMapOnScreen();
        moveToCurrentLocation();
    }
}
```
### 3. 해당 클래스의 하위 클래스들에서는 서브 메서드를 각각의 클래스에 맞게 오버라이딩 정의한다. (단, 메인 메서드는 건들지 말것)
**KakaoMapView**   
```java
public class KakaoMapView extends MapView {

    @Override
    protected void connectMapServer() {
        System.out.println("카카오 지도 연결");
    }

    @Override
    protected void ShowMapOnScreen() {
        System.out.println("카카오 지도를 보여줌");
    }

    @Override
    protected void moveToCurrentLocation() {
        System.out.println("카카오 지도에서 현 좌표로 이동");
    }
}
```
**NaverMapView**
```java
class NaverMapView extends MapView {

    @Override
    protected void connectMapServer() {
        System.out.println("네이버 지도 연결");
    }

    @Override
    protected void ShowMapOnScreen() {
        System.out.println("네이버 지도를 보여줌");
    }

    @Override
    protected void moveToCurrentLocation() {
        System.out.println("네이버 지도에서 현 좌표로 이동");
    }
}
```

### 4. 외부에서는 그저 메인 메서드를 호출하면 참조 인스턴스에 따라 세부 처리가 다르다.
```java
public class Main {
   new TemplateExample(new KakaoMapView()).initMap();
   new TemplateExample(new NaverMapView()).initMap();
}
```
```java
public class TemplateExample {
    private final MapView mapView;
    
    public TemplateExample(MapView mapView){
       this.mapView = mapView;
    }
    
    public void run(){
       mapVire.initMap();
    }
}
```
또한, 같은 클래스를 상속하기에 다형성을 이용한 OCP 설계 패턴을 지킬 수 있다.   

### 5. 이외에 - 모든 서브 메서드를 오버라이딩 할 필요는 없으며 정의된 서브 메서드를 추가해줘도 된다.      
**MapView**
```java
public abstract class MapView{
    protected abstract void connectMapServer();
    private void ShowMapOnScreen(){
        System.out.println("맵을 보여줍니다.");
    }
    protected abstract void moveToCurrentLocation();

    public void initMap(){
        connectMapServer(); // 오버라이딩 
        ShowMapOnScreen(); // 디폴트로 사용할 메서드 
        moveToCurrentLocation(); // 오버라이딩
    }
}
```
**KakaoMapView**   
```java
public class KakaoMapView extends MapView {

    @Override
    protected void connectMapServer() {
        System.out.println("카카오 지도 연결");
    }

    @Override
    protected void moveToCurrentLocation() {
        System.out.println("카카오 지도에서 현 좌표로 이동");
    }
}
```

# 참조 
블로그 :    
https://gmlwjd9405.github.io/2018/07/13/template-method-pattern.html       
https://yaboong.github.io/design-pattern/2018/09/27/template-method-pattern/       
