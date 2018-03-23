---
layout: post
title: 2018-3-23 TIL Web MVC패턴
---
### MVC란?

- Model : DB, Logic
- View : UI구현
- Controller : Model과 View의 가교역할을 한다. Model의 결과를 View에 전달을 하는 역할을 한다.

- Dto클래스는 setter와 getter메소드가 허용이 된다.
- Domain클래스에서는 가능한 setter와 getter를 사용하지 말아야한다.

#### java의 setter와 getter메소드의 컨벤션
- java의 bean규약이라고도 한다
```
public class CarInputDto {
	private String carNames;
    private int tryNo;

    public void setCarNames(String carNames) {
    	this.carNames = carNames;
    }

    public String getCarNames() {
    	return CarNames;
    }

    public void setTryNo(int tryNo) {
    	this.tryNo = tryNo;
    }
}
```

- setter나 생성자가 아니면, 되도록이면 this를 쓰지말자.
- 펙토리는 인스턴스를 생성하는 역할을 한다?
