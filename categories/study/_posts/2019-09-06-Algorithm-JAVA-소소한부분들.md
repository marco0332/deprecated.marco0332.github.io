# JAVA_알고리즘 풀기 위한 소소한 코드

## ArrayList<Integer> convert to int[]

```java
ArrayList<Integer> arr = new ArrayList<Integer>();
arr.add(13);
arr.add(2);
arr.add(16);

int[] arr2 = arr.stream().mapToInt(i->i.intValue()).toArray();
```

## 배열에서 일정 영역을 분리하고 싶을 때

<b>Arrays.copyOfRange(배열, 시작, 끝)</b>

```java
int[] array = {1,5,3,4,7,2,5};
int left = 2;
int right = 5;
int[] ret = Arrays.copyOfRange(array, left, right);
```

