---
layout: post
title:  "Trees: Is This a Binary Search Tree?"
date:   2020-06-09 23:50:18 +0000
categories: algorithms
---

[Hackerrank : Trees: Is This a Binary Search Tree?](https://www.hackerrank.com/challenges/ctci-is-binary-search-tree/problem?h_l=interview&playlist_slugs%5B%5D=interview-preparation-kit&playlist_slugs%5B%5D=trees)


### 풀이 1. 중위 순회 (In-Order Traversal)

이 방법은 이진 탐색 트리의 특성을 활용하는 방법입니다. 

**이진 탐색 트리를 중위 순회 하면 원소를 오름차순으로 탐색 할 수 있습니다.**  따라서 이진 탐색 트리 라면, 트리를 중위 순회 하여 값을 리스트에 집어 넣었을 때, 오름차순으로 정렬되어 있어야 합니다.

```java
boolean checkBST(Node root) {
    ArrayList<Integer> nodes = new ArrayList<>();
    inOrderTraversal(root, nodes);
    return isAscendingOrder(nodes);
}

void inOrderTraversal(Node root, ArrayList<Integer> nodes){
    if(root == null)
        return;
    inOrderTraversal(root.left, nodes);
    nodes.add(root.data);
    inOrderTraversal(root.right, nodes);
}

boolean isAscendingOrder(ArrayList<Integer> list){
    int cur = Integer.MIN_VALUE;
    for(int n : list){
        if(cur >= n)
            return false;
        cur = n;
    }
    return true;
}
```

Java로 제출하려고 보니 원인 모를 에러가 발생하여 Python 코드를 첨부합니다. Discussions을 봐도 같은 문제를 겪는 사람이 많이 있는데 이유는 모르겠네요.


```python
def checkBST(root):
    nodes = []
    inOrderTraversal(root, nodes)
    return isAscendingOrder(nodes)
    
def inOrderTraversal(root, nodes):
    if root is None:
        return
    inOrderTraversal(root.left, nodes)
    nodes.append(root.data);
    inOrderTraversal(root.right, nodes)
    
def isAscendingOrder(list):
    prev = list[0]-1;
    for n in list:
        if prev >= n:
            return False
        prev = n
    return True
```


### 시간 복잡도

위 알고리즘은 '트리 순회' 단계와 '리스트가 오름차순으로 정렬되어 있는지 확인' 하는 단계로 나뉘어져 있습니다.

1) 트리 순회

- 트리를 한 번 순회하는데는 O(n) (n은 노드의 갯수).

2) 리스트가 오름차순으로 정렬되어 있는지 확인

- 리스트를 한 번 순회 하면서 오름차순인지 확인합니다. 리스트의 길이 역시 노드의 갯수와 같기 때문에 O(n).

따라서 시간 복잡도는 O(n)이 됩니다.


### 풀이 2. 최댓값, 최솟값


이진 탐색 트리를 만족하는 조건(문제에 나와있는)은 아래와 같습니다.

- 현재 노드의 왼쪽 서브트리의 모든 노드의 값은 현재 노드 보다 작다.
- 현재 노드의 오른쪽 서브트리의 모든 노드의 값은 현재 노드 보다 크다.
- 모든 노드의 값은 서로 다르다.

root 노드부터 하위 노드를 탐색하면서 해당 노드가 가질 수 있는 최댓값 / 최솟값의 범위를 전달하여 이진 탐색 트리의 조건에 맞는지 확인할 수 있습니다.

아래의 트리를 root 노드부터 왼쪽 서브트리 → 오른쪽 서브트리 순서로 탐색을 한다고 하면,

```python
					3
				/   \
       2     5
      /     /  \
     1     4    6
```

1) 노드 '2' 방문

가장 먼저 노드 '2'를 방문하게 됩니다. 그럼 이제 해당 노드의 값이 이진 탐색 트리의 규칙에 맞는지 확인 해야 합니다. 노드 '2'는 '3'의 왼쪽 자식 노드이기 때문에 값이 3보다 작아야 하고, 최솟값의 기준은 없습니다. 조건을 만족하니 다음 노드를 방문합니다.

2) 노드 '1' 방문

노드 '1'의 경우 노드 '2'의 왼쪽 자식 노드이기 때문에 노드의 값은 2보다 작아야 하고 최솟값의 기준은 없습니다. 따라서 조건을 만족합니다.

3) 노드 '5' 방문

root 노드의 왼쪽 서브트리 방문을 마쳤으니 오른쪽 서브트리 방문을 시작합니다. 처음 방문할 노드는 노드 '5' 입니다. 해당 노드가 가질 수 있는 값의 범위는 어떻게 될까요?

우선 노드 '3'의 오른쪽 자식 노드이므로 해당 노드의 값은 3보다 커야 합니다. 오른쪽 자식 노드이므로 최댓값의 제한은 없습니다. 따라서 이진 탐색 트리의 조건을 만족합니다.

4) 노드 '4' 방문

노드 '4'는 노드 '5'의 왼쪽 자식 노드이므로 값이 5보다 작아야 합니다. 또한 root 노드의 오른쪽 서브트리에 있으므로 3보다 큰 값을 가져야 합니다. 따라서 이진 탐색 트리의 조건을 만족합니다.

5) 노드 '6' 방문

마지막으로 노드 '6'을 방문합니다. 노드 '5'의 오른쪽 자식 노드이므로 최솟값은 5이고, 최댓값은 제한이 없습니다. 따라서 이진 탐색 트리의 조건을 만족합니다. 이렇게 확인이 끝나고 해당 트리는 이진 탐색 트리의 조건을 만족하는 것을 알 수 있습니다.

위 과정을 정리하면 아래와 같습니다.

- 왼쪽 자식 노드일 경우
    - 최댓값 : 부모 노드의 값
    - 최솟값 : 부모 노드의 최솟값
- 오른쪽 자식 노드일 경우
    - 최댓값 : 부모 노드의 최댓값
    - 최솟값 : 부모 노드의 값

```python
def checkBST(root):
    min = float('-inf');
    max = float('inf');
    return checkBSTHelper(root, min, max)
    
def checkBSTHelper(node, min, max):
    if node is None:
        return True
    
    if node.data <= min or node.data >= max:
        return False
    
    return checkBSTHelper(node.left, min, node.data) and \
							checkBSTHelper(node.right, node.data, max)
```

### 시간 복잡도

이 코드는 전체 트리의 노드를 한 번 순회하기 때문에 시간 복잡도는 풀이 1과 마찬가지로 O(n) 이 됩니다.