---
title: 자료구조(1) - 배열(Array)
categories:
  - programming
tags:
  - programming
  - 자료구조
---

![alt]({{ site.url }}{{ site.baseurl }}/assets/images/ds1-array-intro.JPG)


__배열(Array)__ 이란 위키피디아에 따르면 다음과 같다

> In computer science, an array data structure, or simply an array, is a data structure consisting of a collection of elements (values or variables), each identified by at least one array index or key. An array is stored such that the position of each element can be computed from its index tuple by a mathematical formula. The simplest type of data structure is a linear array, also called one-dimensional array.

엄청 긴 설명인데, 요약하자면 __자료의 나열__이라고 할 수 있겟다.

처음에 있는 그림을 보면서 설명하면, 다음과 같다

배열(`arr`)은 데이터를 담는 용기다.

이 용기는 특이하게 생겼는데 총 __4개__의 작은 __박스__가 연결된 형태로 구성되어 있다.

각각의 박스에는 `A`,`B`,`C`,`D`의 __데이터__가 들어 있고 __박스의 이름__은 `0`,`1`,`2`,`3`이라고 한다.

__박스__ : 배열을 차지하는 하나의 요소가 필요한 공간이며 이는 데이터의 자료형에 따라 크기가 다르다.

__데이터__ : Values. 배열의 각 요소의 값들을 가르킴

__박스의 이름__ : Index. 배열의 요소의 번호이다. 첫번째 요소는 항상 `0` 번이다 ([Programmer's Humor](https://www.reddit.com/r/ProgrammerHumor/comments/7svl7x/another_meme_regarding_the_indexing_of_arrays/))

`C`에서는 배열을 다음과 같이 정의 한다

```c
// 자료형 변수명[배열크기] - 정적 배열
// 자료형* 변수명 = (자료형*)malloc(sizeof(자료형) * 배열크기) - 동적 배열

// 정적 배열의 예시
int scores[4]; // scores for Mathematics, Physics, Chemistry, Biology
// 동적 배열의 예시. <stdlib.h> 헤더가 include 되어 있어야 함
int *scores = (int *)malloc(sizeof(int) * 4);
```

`Python` 에서는 다음과 같다

```python
scores = []
```

__NOTE__
> C와 달리 python은 배열 자체가 나중에 배울 리스트와 같은 방법으로 선언되고 배열의 크기에 관심이 없다(항상 가변적).
> 또 python에는 배열의 요소로 들어가는 자료형의 종류가 달라도 상관 없다

이제 이 배열을 학생의 성적을 저장하는데 사용한다고 생각해 보자.
```c
int scores[4]; // scores for Mathematics, Physics, Chemistry, Biology

// access physics score
int phy_score = scores[1];
```

한명에 대한 성적이라면 변수 1개로 해결 할 수 있지만 여러명이 된다면 어떻게 될까?

학생이 `100명` `1,000명` `10,000명`이 된다면 이에 따라 변수의 개수를 `100개` `1,000개` `10,000개`를 선언해야 할 것이다.

이에 대한 대안이 2가지가 있다.

- 2차원 배열

말 그대로 2차원의 배열이다. 행렬을 생각하면 쉽다.

```c
// 자료형 변수명[행의 개수][열의 개수]
int student_scores[10][4] // 10 students with 4 scores
```

- 1차원 배열을 길게 뽑는 방법

이는 학생 성적처럼 열의 개수가 고정값일 때 유용한 방법이다

이 때 i번째 행과 j번째 열의 데이터를 접근할 때 다음의 식을 이용한다

`a[i][j] = a[i* numRows  + j]`

```c
const int numElements = 10 * 4;
int student_scores[numElements];

// assume Thomas is 5-th student and want to access his chemistry score
// Index(Row) of Thomas : 4
// Column of Chemistry : 2 (because it is the third column)
int thomas_chem = student_scores[ 4 * 10 + 2];
```

1번 방법이 보다 직관적이기 때문에 사용에 용이하다. 

그러나 GPU 작업(주로 __CUDA__)의 경우 많은 경우 1차원 배열을 건네 주게 되니 2번도 유용한 방법이다.

디먄 CPU의 architecture에 따라 너무 큰 크기의 배열은 문제가 될 수 있으니 주의하면서 사용해야겟다.

위의 방법들을 사용해서 3차원, 4차원 이상의 고차원으로 확장 가능하다. 

---

이처럼 배열을 여러 용도로 사용할 수 있지만, 데이터를 주로 사용하는 분야에서 주로 배열은 일종의 벡터(vector)로 생각할 수 있다.

많은 machine learning algorithm들은 주어진 데이터가 행렬(matrix)의 형태이며 다음과 같은 형식을 가진다.

`sample x features`

가령 bioinformatics에서 많이 보는 single cell gene expression데이터를 생각해 보면 다음과 같다

`cell x expression of genes`

| cell id | gene1 | gene2 | gene3 | gene4 | ... |
|:-------:|:-----:|:-----:|:-----:|:-----:|:---:|
| cell A | 0.9 | 0.27 | 0.5 | 0.6 | ... |
| cell B | 0.7 | 0.97 | 0.6 | 0.0 | ... |
| cell C | 0.5 | 0.37 | 0.1 | 0.8 | ... |
| cell D | 0.8 | 0.17 | 0.4 | 0.2 | ... |

이 때 cell A의 gene expression을 알고 싶을 때 배열을 사용한다거나 전체 cell에서 gene2의 expression을 알고 싶을 때 배열을 통해 자료를 구성할 수 있다.

위의 예제를 통해 실제 사용례에 대해서 하나 더 알아 보자.

총 4개의 single cell에 대해서 4종류의 gene expression profile을 얻었다고 가정한다.

이 중에서 cell A의 gene expression profile을 cellA라는 변수에 모든 cell의 gene2에 해당하는 expression profile을 gene2라는 변수에 담는 다고 하자.

__C STYLE__

```c


#include <stdio.h>
#include <string.h> // memcpy

float expr_matrix[4][4] = {
    //gene1, gene2, gene3, gene4
    { 0.9,   0.27,  0.5,   0.6}, // cell A
    { 0.7,   0.97,  0.6,   0.0}, // cell B
    { 0.5,   0.37,  0.1,   0.8}, // cell C
    { 0.8,   0.17,  0.4,   0.2}  // cell D
}

int main(int argc, char **argv) 
{
    float cellA[4] = {0.0,};
    float gene2[4] = {0.0,}
    size_t i = 0;

    // get expression of cell A
    memcpy(cellA, expr_matrix[0], sizeof(float) * 4);

    // get expression of gene2
    for(i = 0; i < 4; i++){
        gene2[i] = expr_matrix[i][1];
    }

    return 0;
}
```

__Python STYLE__

```python
expr_matrix= [
      //gene1, gene2, gene3, gene4
    [ 0.9,   0.27,  0.5,   0.6], // cell A
    [ 0.7,   0.97,  0.6,   0.0], // cell B
    [ 0.5,   0.37,  0.1,   0.8], // cell C
    [ 0.8,   0.17,  0.4,   0.2]  // cell D
]

cellA = expr_matrix[0]
gene2 = []
for i in range(4):
    gene2.append(expr_matrix[i][1])
```

위의 예제에서는 데이터를 미리 내부에 정적으로 선언한 후에 보았지만 실무에서는 gene expression을 계산한 파일을 받아서 데이터를 읽어 들이게 된다.

Python을 이용해서 쉽게 해결 가능하다.

```python

import pandas as pd

# Reading matrix file
df = pd.read_csv("gene_expression.csv")

# convert to numpy matrix (pandas itself can do this but wanted to be clear)
matrix = df.to_numpy()

cellA = matrix[1]
gene2 = matrix[,1]
```

이제 배열에 대한 기본적인 정리는 끝났고 다음 번에는 __리스트(LIST)__ 에 대해 정리해 보겟다


