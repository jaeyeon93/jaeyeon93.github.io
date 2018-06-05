---
layout: post
title: 2018-6-6 Collection Framework TreeSet
---

### TreeSet\<E> 클래스
- TreeSet은 트리 자료구조를 기반으로 인스턴스를 저장한다. 이는 정렬된 상태가 유지되면서 인스턴스가 저장된다.

```
package yoon;

import java.util.Iterator;
import java.util.TreeSet;

public class SortedTreeSet {
    public static void main(String[] args) {
        TreeSet<Integer> tree = new TreeSet<>();
        tree.add(3); tree.add(1);
        tree.add(2); tree.add(4);
        System.out.println("인스턴스의 수 : " + tree.size());

        for(Integer num : tree)
            System.out.print(num.toString() + "\t");
        System.out.println();

        for(Iterator<Integer> itr = tree.iterator(); itr.hasNext(); )
            System.out.print(itr.next().toString() + "\t");
        System.out.println();
    }
}
//result
인스턴스의 수 : 4
1	2	3	4
1	2	3	4
```
- TreeSet은 인스턴스가 정렬 상태를 유지하면서 인스턴스를 저장하기 때문에 TreeSet\<E>의 반복자는 인스턴스들의 참조 순서는 오름차순을 기준으로 한다는 특징이있다.

```
class Person {
	private String name;
    private int age;
    public Person(String name, int age) {
    	this.name = name;
        this.age = age;
    }

    @Override
    public String toString() { return name + " : " + age; }
}
```
- 위의 경우 어떻게 정렬을 하느냐가 기준이 애매해진다. 그것은 프로그래머가 결정할 일이다.
- public interface Comparable\<T> : 이 인터페이스 위치한 유일한 추상 메소드 int compareTo(T o)

### 인스턴스의 비교 기준을 정의하는 Comparable\<T> 인터페이스 구현기준

- int compareTo(T o)를 정의해야한다.
- o가 작다면 양의 정수 반환
- o가 크다면 음의 정수 반환
- o가 같다면 0을 반환

```
package yoon;

import java.util.TreeSet;

class Person implements Comparable<Person> {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() { return name + " : " + age; }

    @Override
    public int compareTo(Person p) {
        return this.age - p.age;
    }
}

public class ComparablePerson {
    public static void main(String[] args) {
        TreeSet<Person> tree = new TreeSet<>();
        tree.add(new Person("YOON", 37));
        tree.add(new Person("HONG", 53));
        tree.add(new Person("PARK", 22));

        for(Person p : tree)
            System.out.print(p + "\t");
        System.out.println();
    }

}
//result
PARK : 22
YOON : 37
HONG : 53
```

#### Comparator\<E> 인터페이스 기반으로 TreeSet\<E> 정렬기준 제시하기

```
package yoon;

import java.util.Comparator;
import java.util.TreeSet;

class PersonComparator implements Comparator<Person> {
    public int compare(Person p1, Person p2) {
        return p2.age - p1.age;
    }
}

public class ComparatorPerson {
    public static void main(String[] args) {
        TreeSet<Person> tree = new TreeSet<>(new PersonComparator());
        tree.add(new Person("YOON", 37));
        tree.add(new Person("HONG", 53));
        tree.add(new Person("PARK", 22));

        for(Person p : tree)
            System.out.println(p);
    }
}
//result
HONG : 53
YOON : 37
PARK : 22
```

```
package yoon;

import java.util.Comparator;
import java.util.TreeSet;

class StringComparator implements Comparator<String> {
    public int compare(String s1, String s2) {
        return s1.length() - s2.length();
    }
}

public class ComparatorString {
    public static void main(String[] args) {
        TreeSet<String> tree = new TreeSet<>(new StringComparator());
        tree.add("Box");
        tree.add("Rabbit");
        tree.add("Robot");

        for(String s : tree)
            System.out.print(s.toString() + "\t");
        System.out.println();
    }
}
//result
Box	Robot	Rabbit
```

#### 중복된 인스턴스를 삭제하기
- List\<E>는 중복된 인스턴스를 허용한다. 하지만 이 하나만 남기고 모두 지워야 할때

```
package yoon;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashSet;
import java.util.List;

public class ConvertCollection {
    public static void main(String[] args) {
        List<String> lst = Arrays.asList("Box", "Toy", "Box", "Toy");
        ArrayList<String> list = new ArrayList<>(lst);

        for(String s : list)
            System.out.print(s.toString() + "\t");
        System.out.println();

        // 중복된 인스턴스 필터하기
        HashSet<String> set = new HashSet<>(list);

        // 원래대로 ArrayList<String> 인스턴스로 옮긴다.
        list = new ArrayList<>(set);

        for(String s : list)
            System.out.print(s.toString() + "\t");
        System.out.println();
    }
}
//result
Box	Toy	Box	Toy
Box	Toy
```
- HashSet\<E> set = new HashSet<>(list); => 생성자를 통해서 컬렉션 인스턴스를 생성하는 문장이다.
- public HashSet(Collection<? extends E> c)
