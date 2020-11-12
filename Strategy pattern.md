# Strategy pattern     
<img width="832" alt="스크린샷 2020-11-11 오후 2 36 26" src="https://user-images.githubusercontent.com/50267433/98773008-5b5dce00-242b-11eb-9f09-149c6710d370.png">

[1. Strategy pattern 이란?](#Strategy-pattern-이란?)     
[2. 관련 용어](#관련-용어)     
[3. 설계 방법](#설계-방법)     
[4. 예제](#예제)     

   
## Strategy pattern 이란?       
> 실행 중에 알고리즘을 선택할 수 있게 하는 행위 소프트웨어 디자인 패턴이다.               
   
* 특정한 계열의 알고리즘들을 정의하고    
* 각 알고리즘을 캡슐화하며     
* 이 알고리즘들을 해당 계열 안에서 상호 교체가 가능하게 만든다. (OCP 수행)       
    
**정리 :** `has-a` 방식을 도입하여 특정 객체에서 알고리즘을 별도로 분리하는 설계 방법 (인터페이스)       
       
## 관련 용어    
* **Context :** 특정 알고리즘을 사용하는 객체 
  * Strategy 패턴을 이용하는 역할 수행      
  * 즉, ConcreateStrategy 인스턴스를 가진 대상            
  * 필요에 따라 동적으로 구체적인 전략을 바꿀수 있도록 한다.(DI)       
* **Strategy :** 특정한 알고리즘을 추상화된 인터페이스나 클래스로 정의한 오브젝트                
  * 주로 Functional Interface 로 구현하며 추상 메서드는 1개
  * 다형성을 이용하여 코드의 변경없이 다양한 알고리즘을 호출하는 역할 
* **ConcreateStrategy :** Strategy 의 추상 메서드를 구현한 클래스   
  * 전략 패턴에서 명시한 알고리즘을 실제로 각 용도에 알맞게 구현한 클래스    
  
## 설계 방법
1. Context에서 변경(확장)될 것과 변하지 않을 것을 엄격히 구분
2. 변경(확장)될 것을 인터페이스로 추출하여 Strategy 정의
3. Strategy 인터페이스를 구현한 ConcreateStrategy 클래스 정의
4. Context에서 Strategy 인터페이스에 의존하도록 코드를 작성
5. 다형성을 이용하여 참조된 ConcreateStrategy 인스턴스의 메서드 사용 
    
## 예제   
### 1. 변경(확장)될 것과 변하지 않을 것을 엄격히 구분
**LottoNumbersAutoGenerator - Context**
```java
public class LottoNumbersAutoGenerator {
  
  // 변경이 되지 않는 코드 //
  public List<Intenger> generate(int shuffle) {
    List<Integer> numbers = new ArrayList<>();
    for(int i=LottoNumber.MIN; i <= LottoNumber.MAX; i++){
      numbers.add(i);
    }
  /////////////////////  
  
  // 변경이 일어나는 코드 // 
  if(shuffle == RANDOM){
      Collections.Shuffle(numbers);
  } else if(shuffle == NOTHING){
      Collections.sort(numbers);
  } else if(shuffle == REVERSE){
      Collections.reverse(numbers);
  }
  /////////////////////
  
  // 변경이 일어나지 않는 코드 // 
  return numbers.subList(0. Lotto.LOTTO_NUMBER_SIZE);
  /////////////////////
  
  }
}
```
### 2. 변경(확장)될 것을 인터페이스로 추출하여 Strategy 정의
**LottoNumbersAutoGenerator - Context**
```java
public class LottoNumbersAutoGenerator {
  
  public List<Intenger> generate(int shuffle) {
    List<Integer> numbers = new ArrayList<>();
    for(int i=LottoNumber.MIN; i <= LottoNumber.MAX; i++){
      numbers.add(i);
    }

      // 없어진 부분 // 
      
      
    return numbers.subList(0. Lotto.LOTTO_NUMBER_SIZE);
  
  }
}
```
**ShuffleStrategy - Strategy**

```java
@FunctionalInterface
public interface ShuffleStrategy{
    public List<Integer> shuffle(List<Integer> numbers);
}
```

### 3. Strategy 인터페이스를 구현한 ConcreateStrategy 클래스 정의
**ShuffleRandomStrategy - ConcreateStrategy**
```java
pulblic class ShuffleRandomStrategy implements ShuffleStrategy {
    private static ShuffleRandomStrategy shuffleRandomStrategy = new ShuffleRandomStrategy();
    
    private ShuffleRandomStrategy(){}
    
    public static ShuffleRandomStrategy getInstance(){
        return instance;
    }
    
    public List<Integer> shuffle(final List<Integer> numbers){
        Collections.shuffle(numbers);
        return numbers;
    }
}
```
**ShuffleReverseStrategy - ConcreateStrategy**
```java
pulblic class ShuffleReverseStrategy implements ShuffleStrategy {
    private static ShuffleReverseStrategy shuffleReverseStrategy = new ShuffleReverseStrategy();
    
    private ShuffleReverseStrategy(){}
    
    public static ShuffleReverseStrategy getInstance(){
        return instance;
    }
    
    public List<Integer> shuffle(final List<Integer> numbers){
        Collections.reverse(numbers);
        return numbers;
    }
}
```
**ShuffleNothingStrategy - ConcreateStrategy**
```java
pulblic class ShuffleNothingStrategy implements ShuffleStrategy {
    private static ShuffleNothingStrategy shuffleNothingStrategy = new ShuffleNothingStrategy();
    
    private ShuffleNothingStrategy(){}
    
    public static ShuffleNothingStrategy getInstance(){
        return instance;
    }
    
    public List<Integer> shuffle(final List<Integer> numbers){
        Collections.sort(numbers);
        return numbers;
    }
}
```

### 4. Context에서 Strategy 인터페이스에 의존하도록 코드를 작성
**Main**
```java
class Main{
    public static void main(String[] args){
        LottoNumbersAutoGenerator lottoNumbersAutoGenerator = new LottoNumbersAutoGenerator(ShuffleRandomStrategy.getInstance());
        // LottoNumbersAutoGenerator lottoNumbersAutoGenerator = new LottoNumbersAutoGenerator(ShuffleReverseStrategy.getInstance());
        // LottoNumbersAutoGenerator lottoNumbersAutoGenerator = new LottoNumbersAutoGenerator(ShuffleNothingStrategy.getInstance());
    }
}
```

**LottoNumbersAutoGenerator - Context**
```java
public class LottoNumbersAutoGenerator {
  private final ShuffleStrategy shuffleStrategy; // 인터페이스 의존 -> 관련 구현 클래스들을 받을 수 있고 변경하지 않아도 됨   
    
  public LottoNumbersAutoGenerator(){
    this(ShuffleRandomStrategy.getInstance());
  }   
  
  pulic LottoNumbersAutoGenerator(ShuffleStrategy shuffleStrategy){
    this.shuffleStrategy = shuffleStrategy;
  } 
    
  public List<Intenger> generate(int shuffle) {
    List<Integer> numbers = new ArrayList<>();
    for(int i=LottoNumber.MIN; i <= LottoNumber.MAX; i++){
      numbers.add(i);
    }
    
  return numbers.subList(0. Lotto.LOTTO_NUMBER_SIZE);
  
  }
}
```

### 5. 다형성을 이용하여 참조된 ConcreateStrategy 인스턴스의 메서드 사용 
**LottoNumbersAutoGenerator - Context**
```java
public class LottoNumbersAutoGenerator {
  private final ShuffleStrategy shuffleStrategy; // 인터페이스 의존 -> 관련 구현 클래스들을 받을 수 있고 변경하지 않아도 됨   
    
  public LottoNumbersAutoGenerator(){
    this(ShuffleRandomStrategy.getInstance());
  }   
  
  pulic LottoNumbersAutoGenerator(ShuffleStrategy shuffleStrategy){
    this.shuffleStrategy = shuffleStrategy;
  } 
    
  public List<Intenger> generate(int shuffle) {
    List<Integer> numbers = new ArrayList<>();
    for(int i=LottoNumber.MIN; i <= LottoNumber.MAX; i++){
      numbers.add(i);
    }
  
  numbers = shuffleStrategy.shuffle(numbers); // 참조하고 있는 인스턴스에 맞는 shuffle 메서드 실행 -> 내부 로직 몰라도 됨 -> 추가로 이름 잘 지어야하는 이유 중 하나이기도  
  
  return numbers.subList(0. Lotto.LOTTO_NUMBER_SIZE);
  
  }
}
```


# 참조
> 베이스 : https://github.com/kwj1270/TIL_WooWaHanTechSeminar/blob/master/OCP%EC%99%80%20%EC%A0%84%EB%9E%B5%20%ED%8C%A8%ED%84%B4.md    
     
* **위키 :**    
https://ko.wikipedia.org/wiki/%EC%A0%84%EB%9E%B5_%ED%8C%A8%ED%84%B4
* **블로그 :**    
https://velog.io/@kyle/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%A0%84%EB%9E%B5%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80
* **동영상**    
우아한 테크코스_OCP와 전략패턴 : https://www.youtube.com/watch?v=90ZDvHl8ROE      
얄팍한 코딩지식_디자인패턴1 : https://www.youtube.com/watch?v=lJES5TQTTWE   
  
