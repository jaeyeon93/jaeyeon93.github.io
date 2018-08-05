---
layout: post
title: 2018-8-3 메서드,생성자 참조
---

메소드 참조
---

### 메소드참조의 4가지 유형

1. static메소드 참조
2. 참조변수를 통한 인스턴스 메소드 참조
3. 클래스 이름을 통한 인스턴스 메소드 참조
4. 생성자 참조


#### static메소드 참조

```
public class ArrangeList {
    public static void main(String[] args) {
        List<Integer> ls = Arrays.asList(1, 3, 5, 7, 9);
        ls = new ArrayList<>(ls);

        Consumer<List<Integer>> c = l -> Collections.reverse(l);
        c.accept(ls); //순서뒤집기 진행
        System.out.println(ls);
    }
}
//result
[9, 7, 5, 3, 1]
```

- Consumer\<List\<Integer>> c = Collections::reverse;도 가능
- ::은 메소드 참조를 의미하는 연산자이다.
- accept 메소드 호출 시 전달되는 인자를 reverse메소드를 호출하면서 그대로 전달

```
public class ConsumerDemo {
    public static void main(String[] args) {
        Consumer<String> c = s -> System.out.println(s);
        c.accept("Pineapple"); // 출력이라는 결과를 보임
        c.accept("Strawberry");
    }
}
//result
Pineapple
Strawberry
```
- Consumer\<T> 인터페이스에는 void accept(T t); // 전달은 받지만 반환하지 않는 메소드가 존재를 한다.

```
public class ArrageList2 {
    public static void main(String[] args) {
        List<Integer> ls = Arrays.asList(1, 3, 5, 7, 9);
        ls = new ArrayList<>(ls);

        Consumer<List<Integer>> c = Collections::reverse; // 메소드 참조
        c.accept(ls);
        System.out.println(ls);
    }
}
//result
[9, 7, 5, 3, 1]
```

#### 인스턴스 메소드의 참조1 : 인스턴스가 존재하는 상황에서 참조

```
class JustSort {
    public void sort(List<?> lst) {
        Collections.reverse(lst);
    }
}

public class ArrangeList3 {
    public static void main(String[] args) {
        List<Integer> ls = Arrays.asList(1, 3, 5, 7, 9);
        ls = new ArrayList<>(ls);
        JustSort js = new JustSort();

        Consumer<List<Integer>> c = e -> js.sort(e);
        c.accept(ls);
        System.out.println(ls);
    }
}
//result
[9, 7, 5, 3, 1]
```
```
public class ArrageList4 {
    public static void main(String[] args) {
        List<Integer> ls = Arrays.asList(1, 3, 5, 7, 9);
        ls = new ArrayList<>(ls);
        JustSort js = new JustSort();

        Consumer<List<Integer>> c = js::sort;
        c.accept(ls);
        System.out.println(ls);
    }
}
//result
[9, 7, 5, 3, 1]
```

```
public class ForeachDemo {
    public static void main(String[] args) {
        List<String > ls = Arrays.asList("Box", "Robot");
        ls.forEach(s -> System.out.println(s)); // 람다식 기반
        ls.forEach(System.out::println); //메소드 참조 기반
    }
}
//result
Box
Robot
Box
Robot

```

#### 인스턴스 메소드의 참조2 : 인스턴스 없이 인스턴스 메소드 참조

```
class IBox {
    private int n;
    public IBox(int n) {
        this.n = n;
    }

    public int larger(IBox b) {
        if (n > b.n)
            return n;
        return b.n;
    }
}

public class NoObjectMethodRef {
    public static void main(String[] args) {
        IBox ib1 = new IBox(5);
        IBox ib2 = new IBox(7);

        // 두 상자에 저장된 값 비교하여 더 큰 값 반환
        ToIntBiFunction<IBox, IBox> bf = (b1, b2) -> b1.larger(b2);
        int bigNum = bf.applyAsInt(ib1, ib2);
        System.out.println(bigNum);
    }
}
```
- ToIntBiFunction<IBox, IBox> bf = (b1, b2) -> b1.larger(b2);는 ToIntBiFunction<IBox, IBox> bf = (b1, b2) -> IBox::larger;로 대체가 가능하다.

#### 생성자 참조

```
public class StringMaker {
    public static void main(String[] args) {
        Function<char[], String> f = ar -> {
            return new String(ar);
        };
        char [] src = {'R', 'o', 'b','o','t'};
        String str = f.apply(src);
        System.out.println(str);
    }
}
//result
Robot
```
