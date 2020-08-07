---
layout: post
title:  "Minimum Swaps 2"
date:   2020-08-06 19:53:00 +0000
categories: algorithms
---

## 1. 선택 정렬(Selection sort)

**선택 정렬은 최소의 Swap을 통해 배열을 정렬하는 알고리즘이다.** 따라서 선택 정렬을 사용하면 최소 Swap횟수를 구할 수 있다.



### 선택 정렬의 시간 복잡도

선택 정렬의 시간 복잡도는 어떻게 될까?

간단한 선택 정렬 알고리즘의 과정은 다음과 같다.

- 배열에서 최솟값을 찾는다.
- 그 값을 배열의 맨 앞으로 옮긴다(Swap).
- 그 다음으로 작은 값을 찾는다.
- 위 과정 반복

매번 배열 전체(정렬이 완료된 원소는 제외)를 순회하면서 최솟값을 찾아야 하기 때문에 선택 정렬은 O(N^2)의 시간이 걸린다.

그렇다면 이 문제는 O(n^2)이 최선일까?



### 사용하지 않은 조건

이 문제에서 사용하지 않은 조건이 있다. 배열의 원소는 중복이 없고 1부터 1씩 차례대로 증가하는 원소를 가지고 있다는 것이다. 그리고 이것은 **배열 원소의 값을 통해 정렬된 상태에서의 해당 원소의 위치를 알 수 있다.**



## 2. 최적화

아래 배열을 보자.

```java
[7, 1, 3, 2, 4, 5, 6]
```

원소 7은 현재 배열의 맨 앞에 있다. 그렇다면 7이 정렬되었을때 있어야 할 위치는 어디일까?

값이 7이기 때문에 7-1=6이 되고 arr[6] 위치에 7이 와야 한다.

이렇게 배열 원소의 값을 통해 정렬된 상태에서 해당 원소의 위치를 알 수 있기 때문에 일반적인 선택 정렬 처럼 매번  배열을 순회하며 최솟값을 찾을 필요가 없다. 먼저 배열을 한 번 순회하며 정렬된 상태에서 해당 원소가 있어야 하는 위치를 미리 구할 수 있다.

```java
	int[] indexes = new int[arr.length];
	// indexes[i] 는 arr[i]에 있어야 하는 원소의 현재 위치를 나타냄
	for(int i=0; i<arr.length; ++i){
	    int index = arr[i]-1;
	    indexes[index] = i;
	}
```

위 코드에선 배열을 한 번 순회한다. 먼저 i가 0인 경우를 보면,

arr[0] = 7이다. 최솟값을 찾는 과정을 생략하기 위해 원소의 현재 위치를 저장해 두는 indexes 라는 배열을 만든다.

indexes[i] 의 의미는 정렬된 상태의 arr[i]에 있어야 하는 원소의 현재 인덱스를 나타낸다. 첫 번째 원소인 7을 예로 들어보면,

indexes[7-1] = 0

이 되는데, 이 말은 arr에서 6(7-1)번 인덱스에 있어야 하는 원소(7)이 현재 0번째 인덱스에 있다는 것이다.

이렇게 indexes 배열을 만들 수 있다.

```java
arr     = [7, 1, 3, 2, 4, 5, 6]
indexes = [1, 3, 2, 4, 5, 6, 0]
```

이렇게 indexes 배열을 만들면 어떤 효과가 있을까?

arr을 순회를 해 보자.

1. i = 0

    이 경우 arr[0] = 7 이다. 문제의 조건 때문에 arr[0]에는 1이 와야 하는 것을 안다. 때문에 swap이 필요하다는 것을 알 수 있다. 그럼 1은 어디에 있을까? indexes[0]이 가리키는 위치에 있다. 

    즉, arr[indexes[0]] 에 1이 있다는 것을 알 수 있는 것이다.

    이렇게 indexes 배열을 통해 선택 정렬에서 배열 전체 순회를 통한 최솟값을 찾는 과정 없이 원하는 원소의 위치를 바로 알 수 있다.

    swap을 하고 indexes 배열을 갱신해야 한다. 0번째 원소는 제 자리를 찾았으니 swap을 한 원소(7)의 indexes 값만 swap 한 위치로 변경 해 주면 된다.

    indexes[6] = indexes[0]

    ```java
    arr     = [1, 7, 3, 2, 4, 5, 6]
    indexes = [1, 3, 2, 4, 5, 6, 1]
    ```

2. 나머지

    위 과정을 반복해 주면 된다. 이를 일반화 해 보면,

    - arr[i] - 1 ≠ i 라면,
        - arr[i] 와 arr[indexes[i]]을 swap 한다.
        - swap한 원소의 indexes 값을 갱신한다
            - indexes[arr[indexes[i]]-1] = indexes[i];
    - arr[i] - 1 == i 라면,
        - 제 위치에 있으니 skip

이를 구현한 코드는 아래와 같다.

```java
static int minimumSwaps(int[] arr) {
	int[] indexes = new int[arr.length];
	int numSwaps = 0;

	for(int i=0; i<arr.length; ++i){
	    int index = arr[i]-1;
	    indexes[index] = i;
	}
	
	for(int i=0; i<arr.length; ++i){
	    if(arr[i]-1 != i){
	        int temp = arr[i];
	        arr[i] = arr[indexes[i]];
	        arr[indexes[i]] = temp;
	
	        indexes[arr[indexes[i]]-1] = indexes[i];
	        ++numSwaps;
	    }
	}
	
	return numSwaps;
}
```

### References

[Minimum number of swaps required to sort the array in ascending order](https://codereview.stackexchange.com/questions/206158/minimum-number-of-swaps-required-to-sort-the-array-in-ascending-order)



