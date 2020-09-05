# 주의사항

- 이 프로젝트는 가로 100px , 세로 100px 인 컨텐츠를 4배한 경우에만 올바르게 적용됩니다.
- 따라서 아래를 변화시키면 줌프레임에 잡히는 영역이 달라집니다.
  > - 컨텐츠 크기
  > - 컨텐츠 줌 정도
  > - 줌 프레임 크기
  > - css 줌 사용여부
  > - tranform : translate 사용여부
- 하지만, 적용하려는 프로젝트에서 컨텐츠 크기가 **_다이나믹하게(반응형으로) 변하는 것이 아니라면_** 문제해결 순서는 크게 변하지는 않을것입니다.

## 해결방법

1. 포인터를 컨텐츠 각 영역의 꼭지점에 있다고 가정하고, 줌프레임의 가운데에 줌 컨텐츠가 위치하는(줌컨텐츠가 정사각형으로 보이는 순간) 좌표를 도출해냈습니다.

   > 1. pointer (0,0) => (25px,25px)
   > 2. pointer (100,0) => (-75px, 25px)
   > 3. pointer (0,-100) => (25px,-75px)
   > 4. pointer (100, -100) => (-75px, -75px)

2. 위의 규칙에서 볼 수 있는 것은 pointer 이 0일땐 25px 이여야 하고, 절대값 100 일 때는 -75px 이어야 한다는 겁니다.

```
pointer => x
position => y
```

라고 가정할 때 위는 다음과 같이 풀어서 표현가능합니다.

```
x가 0일 때는 y가 25여야 한다.
x가 100일 때는 y가 -75여야 한다.
```

이걸 더 간단히 표현하면 다음과 같습니다.

```
(0,25)
(100,-75)
```

3. (0,25) (100, -75) 라는 공통규칙을 통해 이차 함수 공식을 세울 수 있습니다.  
   [이차함수 참조](https://m.blog.naver.com/PostView.nhn?blogId=niiceha3&logNo=220750069081&proxyReferer=https:%2F%2Fwww.google.com%2F)

4. 위 두개의 좌표를 통해 이차함수의 기울기를 구합니다

```
y = ax^2 + 25
-75 = (100^2)a + 25
-100 = 10000a
a = -0.01
```

5. 기울기를 이차함수에 대입하여 이를 이용해 함수를 만듭니다.

```
y = -0.01x^2 + 25

```

는 아래와 같이 표현가능하며 setPosition 함수에 사용되었습니다.

```
-0.01 * Math.pow(num, 2) + 25;
```

## 정리

복잡한 과정을 거친 것 처럼 보이지만 사실 별게 없습니다.  
또한 많은 설정에 의해 위의 공식은 바뀔 수 있습니다.  
하지만 아래의 3단계 과정은 결과를 도출하기 위해서는 무조건 적용되어야 하는 불변의 과정이라고 보면됩니다.

1. 각 꼭지점별로 줌프레임에 줌컨텐츠가 정사각형으로 보이는 포지션값을 습득
2. 그 포지션값을 통해 공통 규칙 도출
3. 도출 된 규칙을 이용해 이차함수수식을 만들어서 적용