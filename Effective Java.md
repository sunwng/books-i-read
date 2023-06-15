> 이 책을 읽는 이유: ‘모던 자바 인 액션’을 읽은 후, 자바 문법이 아니라 자바 자체를 더욱 잘 쓰고싶다는 생각을 하였고 이를 위해
> 

### CH02. 객체 생성과 파괴

- **생성자 대신 정적 패토리 메소드를 고려하라**
    - 장점
        - 이름을 가질 수 있음 → 객체 생성 시, 무엇을 위한 객체인지 설명이 가능해짐
        - 호출될 때마다 인스턴스를 새로 생성하지 않아도 됨 → 인스턴스를 통제하여 클래스의 사용을 원하는 방향으로 제어할 수 있음
        - 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있음 → 유연성을 제공
        - 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있음
        - 정적 팩토리 메소드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 됨
            - 대체 무슨 말일까?
            - 하나의 예로 자바의 `Class.forName()` 메소드를 사용하여 객체를 가져오는 방법을 사용할 수 있음
            - 사용할 구현체들의 이름은 정해졌지만 존재하지는 않는 경우, 정적 팩토리 메소드에서 위 메소드에 구현체 이름만 넣어 코드를 작성함으로써 클래스 존재에 덜 의존적으로 만들어 줄 수 있음
    - 단점
        - 상속에 조건이 생김
            - 나는 정적 팩토리 메소드를 사용할 때, 생성자는 `private`으로 만들어줬었음
            - 하지만 생성자가 public인 경우에만 하위 클래스를 만들 수 있음 (상속시 생성자를 호출하기 때문)
        - API 설명에 명확히 드러나지 않기 때문에 찾기 어려움 → Naming Convention을 통해 해소함
    - Naming Convention
        - `from`: 매개변수를 ‘하나’ 받아서 인스턴스를 반환
        - `of`: 여러 매개변수를 받아 인스턴스를 반환
        - `valueOf`: 좀 더 자세하게 어떤 매개변수를 받을지 명시하고 그에 따른 인스턴스 반환
        - `instance` or `getInstance`: 정해진 인스턴스 반환
        - `create` or `newInstance`: 새로운 인스턴스를 생성해 반환
        - `getType`: `getInstance`와 동작은 같지만 호출하는 클래스가 아닌 다른 클래스를 반환할 때 사용
        - `newType`: `newInstance`와 동작은 같지만 호출하는 클래스가 아니니 다른 클래스를 반환할 때 사용
- **생성자에 매개변수가 많다면 빌더를 고려하라**
    - 정적 팩토리 메소드와 생성자의 단점 → 선택적 매개변수에 대응이 어려움
    - 예를 들어, 필요 없는 매개변수도 `0`이나 `null`로 넘겨줘야 했음
        
        → 매개변수가 많아질수록 복잡하고 순서 맞추기도 번거로움
        
    - 이를 해결하기 위해 매개변수가 없는 생성자를 통해 객체를 생성하고 필요한 필드를 setter를 통해 채워주는 방법을 사용함
        
        → 일관성 검증이 어려움
        
    - 결국 이러한 단점들을 보완하기 위해 빌더 패턴이 사용됨
        - 생성할 클래스 내부에 정적 멤버 클래스로 빌더 클래스를 생성 (`Builder`)
        - 해당 `Builder`의 생성자와 메소드를 통해 매개변수 전달
        - `Builder`의 모든 메소드는 자기 자신 (`Builder`) 를 반환하므로 연쇄적으로 메소드 호출 가능
        - 객체 생성 메소드 (`build`) 호출 시, `Builder`객체의 필드를 사용하여 원하는 클래스의 객체 생성하여 반환
- **private 생성자나 열거 타입으로 싱글톤임을 보증하라**
    - 싱글톤 객체를 만드는 방법은 총 3가지임
    - public 필드 사용
        
        ```java
        public class Elvis {
            public static final Elvis INSTANCE = new Elvis();
            private Elvis() {}
            public void leaveTheBuilding() {}
        }
        ```
        
        - 해당 클래스가 싱글톤인 것이 API에 명백히 드러남 → 왜지?
            
            (그냥 필드에 바로 접근 가능해서 그런듯함)
            
        - 간결함
    - 정적 팩토리 방식
        
        ```java
        public class Elvis {
            private static final Elvis INSTANCE = new Elvis();
            private Elvis() {}
            public static Elvis getInstance() { return INSTANCE; }
            public void leaveTheBuilding() {}
        }
        ```
        
        - 싱글톤이 아니게 변경하기 수월함 (`getInstance` 메소드만 수정해주면 됨)
        - 제네릭 싱글톤 팩토리로 변경이 수월함
            - 예를 들어, 싱글톤 객체가 여러 Type에 의존적인 상황일 때 유연하게 적용 가능
        - 객체 반환 메소드 (`getInstance`)를 Supplier 함수로 사용 가능함
    - 원소가 하나인 Enum으로 선언
        
        ```java
        public enum Elvis {
            INSTANCE;
            public void leaveTheBuilding() {}
        }
        ```
        
        - 직렬화에 편함
        - 하지만 상속을 구현해야한다면 사용 못함

- **인스턴스화를 막으려거든 private 생성자를 사용하라**
    - 정적 멤버와 정적 메소드만을 담은 Utility 클래스는 인스턴스를 만들기 위해 설계된 것이 아님
        
        → private 생성자를 추가하여 인스턴스화를 막자
        
- **자원을 직접 명시하지 말고 의존 객체 주입을 사용하라**
    - 다른 객체에 의존하는 경우, 직접 명시하여 의존하게 하면 유연성이 떨어짐
        
        ```java
        public class SpellChecker {
        	private final Lexicon dictionary = new SampleDictionary();
        	...
        }
        ```
        
        - 이 경우, `SampleDictionary` 만을 사용할 수 있게됨
    - 생성자를 통해 인스턴스 생성시 의존성을 주입받도록 하면 유연성이 증가함
        
        ```java
        public class SpellChecker {
        	private final Lexicon dictionary;
        
        	public SpellChecker(Lexicon dictionary) {
        		this.dictionary = Objects.requireNonNull(dictionary);
        	}
        }
        ```
        
    - 스프링에서 이를 굉장히 잘 활용하고 있음을 다시 한번 생각해보자
- **불필요한 객체 생성을 피하라**
    - 불변하는 객체가 계속해서 생성된다면 지속적으로 GC의 대상이 되고 좋지 않은 성능을 보일 수 밖에 없음
    - 예를 들어
        
        ```java
        static boolean isRomanNumeral(String s) {
        	return s.matches("대충 정규표현식");
        }
        ```
        
        - 이 메소드는 s가 요구하는 정규표현식에 맞는지 체크함
        - `String.matches` 는 input으로 들어오는 정규표현식에 대해 `Pattern` 이라는 객체를 항상 새로 만드는 로직을 포함하고 있음
        - 즉, 위 메소드는 매번 변하지 않는 `Pattern` 객체를 새로 만들고 있다는 것
        
        ```java
        public class RomanNumerals {
        	private static final Pattern ROMAN = Pattern.compile("대충 정규표현식");
        	static boolean isRomanNumeral(String s) {
        		return ROMAN.matcher(s).matches();
        	}
        }
        ```
        
        - 위처럼 수정해주면 `Pattern`객체를 매번 새로 만들지 않고 재사용하게 함으로써 성능 상승을 이룰 수 있음
    - AutoBoxing도 이러한 문제를 보일 수 있음
        
        ```java
        private static long sum() {
        	Long sum = 0L;
        	for (long i = 0; i < Integer.MAX_VALUE; i++) {
        		sum += i;
        	}
        }
        ```
        
        - 여기서 Primitive type `long`인 `i`를 Wrapper type인 `Long`으로 AutoBoxing 시, 매번 `Long` 인스턴스가 만들어지게 됨
        - `sum`을 `Long`이 아닌 `long`으로만 바꿔줘도 성능 향상이 됨
- **다 쓴 객체 참조를 해제하라**
    - 다 쓴 객체를 `null`로 처리해주면 참조가 해제되어 메모리 관리에 이점이 있음
- **finalizer와 cleaner 사용을 피하라**
    - finalizer와 cleaner는 GC 대상이 될 때 실행됨
    - 따라서 개발자가 원하는 시기에 실행되지 않음 → 이로 인해 문제가 많으므로 사용을 지양해야함
    - 이 둘의 대안으로는 `AutoCloseable`를 구현하여 클래스를 만드는 방법이 있음
        
        (인스턴스 사용 후 `close` 메소드 호출)
        
    - 그러면 저 둘은 왜 존재하는가?
        
        → 중요하지 않은 자원 회수용으로는 사용하면 좋음 (즉 안정망 용도임)
        
- **try-finally보다는 try-with-resources를 사용하라**
    - `InputStream`, `OutputStream`, `java.sql.Connection` 등은 `close` 메소드를 직접 호출해 닫아줘야함
    - 이를 위해 기존에는 `finally` 영역에 `close` 메소드를 추가해주었음
        
        → 코드도 복잡해지고 실수로 호출하지 않는 경우가 많았음
        
        → 또한 에러 스택 트레이스가 누락되기도 함
        
        → 또한 `finally` 영역에서 다른 객체에 의해 `Exception`이 발생하면 `close` 메소드가 누락되기도 함
        
    - 이를 해결하기 위해 `try-with-resources`가 추가되었음
        - `AutoCloseable` 클래스를 구현하는 객체라면 자동으로 `close` 메소드를 호출함
        - 아래는 사용 예시
            
            ```java
            public static void main(String args[]) throws IOException {
                try (FileInputStream is = new FileInputStream("file.txt"); BufferedInputStream bis = new BufferedInputStream(is)) {
                    int data;
                    while ((data = bis.read()) != -1) {
                        System.out.print((char) data);
                    }
                }
            }
            ```
### CH03. 모든 객체의 공통 메소드

- **equals는 일반 규약을 지켜 재정의하라**
    - 애매하게 재정의하면 오히려 혼돈을 초래할 수 있음
    - 그렇기에 아래의 상황에서는 왠만하면 재정의하지 않는 것을 권장함
        - 각 인스턴스가 본질적으로 고유함
            - 예를 들면 Thread
        - 인스턴스의 ‘논리적 동치성 (logical equality)’를 검사할 일이 없음
            - 예를 들어 Map.equals는 내부의 key - value 를 검사하도록 재정의되었음 → 논리적 동치성을 검사하는 것임
        - 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞음
        - 클래스가 private이거나 package-private이고 equals 메소드를 호출할 일이 없음
            
            ```java
            @Override
            public boolean equals(Object o) {
            	throw new AssertionError(); 
            }
            ```
            
            - 이런식으로 호출하지 못하게 해두면 됨
    - 그러면 언제 정의해야할까? (이미 위에서 대충 정답이 나왔음)
        
        ⇒ 객체 식별성 (두 객체가 물리적으로 같은지) 이 아닌 논리적 동치성을 확인해야하는데, 상위 클래스에서 equals를 그렇게 재정의하지 않았을 때!
        
    - 따라야하는 일반 규약
        - 반사성 (Flexibility): `null`이 아닌 모든 참조 값 `x`에 대해, `x.equals(x)`는 `true`
        - 대칭성 (Symmetry): `null`이 아닌 모든 참조 값 `x`, `y`에 대해, `x.equals(y)`가 `true`라면 `y.equals(x)`도 `true`
        - 추이성 (Transitivity): `null`이 아닌 모든 참조 값 `x`, `y`, `z`에 대해, `x.equals(y)`가 `true`면서 `y.equals(x)`도 `true` 라면, `x.equals(z)`도 `true`
        - 일관성 (Consistency): `null`이 아닌 모든 참조 값 `x`, `y`에 대해, `x.equals(y)`를 반복해서 호출하면 항상 `true`를 반환하거나 항상 `false`를 반환
        - null-아님: `null`이 아닌 모든 참조 값 `x`에 대해, `x.equals(null)`은 `false`
    - 이를 지키기 위한 팁
        - `==` 연산자를 사용해 입력이 자기 자신의 참조인지 확인
            
            → 자신이라면 `true` 반환
            
        - `instanceof` 연산자로 입력이 올바른 타입인지 확인
        - 입력을 올바른 타입으로 형변환
        - 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 하나씩 검사
- **equals를 재정의하려거든 hashCode도 재정의하라**
    - `equals`가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환해야 함
    - 그래야 `HashMap`, `HashSet` 등의 Collection의 원소로 사용할 수 있음
- **toString을 항상 재정의하라**
    - 일반적으로 우리가 원한 적합한 문자열을 반환하는 경우는 거의 없음
        
        (보통 객체의 클래스 이름 + 16진수로 표시된 해시코드 를 반환함)
        
    - 객체가 간단하다면 객체를 설명하는 자세한 String을 반환하도록 오버라이드하고
    - 객체가 복잡하다면 객체를 요약하여 설명하는 String을 반환하도록 오버라이드함
- **clone 재정의는 주의해서 진행하라**
    - `Cloneable` 인터페이스를 구현하는 객체는 복제해도 되는 클래스임을 명시함
    - 신기한 것은, `Cloneable` 인터페이스에는 정의된 메소드가 없다는 것
        - 그러면 어디에?
        - `Object` 클래스에 `clone` 메소드가 `protected`로 선언되어있음
        - 다른 객체에서 접근하기 위해서는 `public`으로 오버라이드를 해줘야함
        - 왜 이렇게 설계되었을까…흠…
    - 아무튼, `clone`이 호출되면 해당 객체의 필드들을 하나하나 복사한 객체를 반환함
    - `cloneable` 인스턴스를 구현한 상위 클래스를 상속하는 하위 클래스의 `clone` 메소드 호출 시에는 생각할 것들이 꽤 있음
        - 만약 모든 필드가 불변 필드라면 → 그냥 `super.clone()` 호출하면 됨
        - 하지만 가변 필드가 있다면? → 가변 필드들은 같은 주소를 보게 될것임
            
            (deep copy가 아니게 됨)
            
        - 그러면 어떻게 해야할까?
        
        → 가변 필드에 대해서 다시 `clone()`을 호출해주면 됨
        
    - 이외에도 정말 많은 문제가 있기에
        - `final Class` (상속 불가) 인 경우에는 `cloneable` 구현해도 됨
        - 하지만 대부분의 경우에는 생성자와 팩토리를 사용하여 복사하는 것이 좋음
- **Comparable을 구현할지 고려하라**
    - `Comparable`을 구현함으로써 해당 클래스의 인스턴스들에게 순서를 제공할 수 있음
        
        (`compareTo` 메소드를 구현해줘야 함)
        
    - 구현하지 않았다면, 정렬을 원할 때 `Comparator`를 사용하면 됨
    - 아래 처럼 편한 방법도 가능
        
        ```java
        private static final Comparator<Sample> COMPARATOR = 
        	comparingInt((Sample sample) -> sample.num1)
        		.thenComparintInt(sample -> sample.num2)
    			.thenComparingInt(sample -> sample.num3);
        ```
        
        - 만들어두고 계속 재사용도 가능

### CH04. 클래스와 인터페이스

- **클래스와 멤버의 접근 권한을 최소화하라**
    - “어설프게 설계된 컴포넌트와 잘 설계된 컴포넌트의 가장 큰 차이는 바로 클래스 내부 데이터와 내부 구현 정보를 외부 컴포넌트로부터 얼마나 잘 숨겼느냐” 임!
        - 잘 설계된 컴포넌트는 내부 구현을 완벽히 숨기고, 구현과 API를 깔꼼하게 분리함
    - 정보 은닉 (구현을 숨기는 것)의 장점은 뭘까?
        - 병렬로 개발을 가능하게 하여 시스템 개발 속도를 높임
        - 시스템 관리 비용을 낮춤 (빠른 디버깅과 낮은 결합도)
        - 성능 최적화에 도움
        - 높아지는 재사용성
    - 정보 은닉의 핵심은 접근 제한자 (`private`, `protected`, `public`)의 활용
    - 기본 원칙: 모든 클래스와 멤버의 접근성을 가능한 한 좁혀야 함
    - 클래스의 공개 API를 세심히 설계 후, 그 외의 모든 멤버(함수와 변수)를 `private`으로 만들어주자
    - 권한을 바꿔주는 일이 자주 발생한다면? → 컴포넌트를 더 분해해야할 가능성이 높은 상태임
    - 단, 상속 시 상위 클래스에서 정의된 접근제한자보다 좁힐 수는 없음 (리스코프 치환 원칙이 지켜지기 위함)
    - 특히, 테스트를 위해 접근제한자를 변경하는 일은 발생해서는 안됨
- **public 클래스에서는 public 필드가 아닌 접근자 메소드를 사용하라**
    - `public` 필드를 `private`으로 바꾸고 `getter`를 통해 접근하라는 뜻
- **변경 가능성을 최소화하라**
    - 변경 가능성을 최소화하는 하나의 방법은 불변 클래스를 사용하는 것
        - 객체의 상태를 변경하는 메소드를 제공하지 않음
        - 클래스를 확장할 수 없음
        - 모든 필드를 final로 선언
        - 모든 필드를 private으로 선언
        - 자신 외에는 내부의 가편 컴포넌트에 접근할 수 없도록 함
    - 불변 객체는 근본적으로 스레드 세이프함
    - 하지만 다른 값을 갖게하려면 다른 독립된 객체를 만들어야 한다는 단점도 존재함
- **상속보다는 컴포지션을 사용하라**
    - 상속은 코드를 재사용하는 강력한 수단이지만, 최선은 아님
    - 상속은 캡슐화를 깨뜨림
        - 상위 클래스의 수정이 하위 클래스의 작동에 영향을 미치게됨
        - 특히 메소드가 재정의되거나 추가되는 경우
    - 이를 피하는 방법으로는 컴포지션 (클래스를 필드에 선언하여 사용) 방법을 사용할 수 있음
    - 상속은 상위 클래스와 하위 클래스가 무조건 is-a 관계일 때만 사용하자!!