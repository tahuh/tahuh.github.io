---
title: 리스트(1) - 배열을 이용한 리스트
categories:
  - programming
tags:
  - programming
  - 자료구조
  - 리스트
---

__리스트__ 는 이전 시간에 포스팅한 배열과 개념이 비슷하다. 

다만 리스트는 배열과 달리 __가변적인 길이__ 를 가질 수 있다는 것이다.

리스트가  종요한 자료형인 이유는 나중에 배울 스택, 큐, 트리, 그래프와 같은 다른 자료형들을 구현하는 기본이 된다는 점이다.

배열처럼 자료를 순서대로 저장하는 구조이기 때문에 배열에서 설명한 것은 생략한다.

리스트를 구현하는 방법은 2가지가 있는데 하나는 배열을 이용하는 방법과 다른 하나는 배열을 이용하지 않는 방법이다.

리스트에슨 기본적 다음과 같은 기능이 필요하다.

- 리스트 생성 (`list_create`)
- 리스트 제거 (`list_destroy`)
- 리스트 초기화 (`list_clear`)
- 리스트 원소 삽입 (`insert_list_element`)
- 리스트 맨 뒤에 원소 추가 (`append_list`)
- 리스트 원소 제거 (`remove_list_element`)
- 리스트 원소 개수 (`num_list_element`)
- 특정 위치의 리스트 (`get_list_element_at`)
- 리스트에 할당된 메모리의 양 (`get_list_capacity`)
- 현재 할당된 메모리의 양을 다 사용했는가 (`is_list_full`)
- 리스트의 크기 재설정 (`resize_list`)

오늘 포스팅에서 정리할 내용은 배열을 이용하는 방법을 통한 구현이다.

배열을 이용한 방법은 그렇지 않은 것과 다르게 __메모리상의 물리적인 순서가 논리적으로 생각하는 순서__ 와 같다는 점이 특징이다.

따라서 상당히 직관적 구현 가능하다.

구현을 위해 `C`의 `구조체`를 이용해 리스트를 감싸서 선언하고 이에 해당하는 함수들을 구성하겟다.

```c
// arraylist.c

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdbool.h>

typedef int elem_t; // 배열 원소의 데이터 타입
typedef struct ArrayList_s {
    elem_t* elements;
    int capa; // pre-allocated memory
    int used; // memory used
} ArrayList_t

typedef ArrayList_T al_t; // for abbreviation

al_t *list_create(int init_size)
{
    size_t mem = sizeof(elem_t) * init_size;
    al_t *l = (al_t *)malloc(sizeof(al_t));
    if(NULL == l) {
        return NULL;
    }
    l->elements = (elem_t *)malloc(mem);
    memset(l->elements, 0, mem);
    l->capa = init_size;
    l->used = 0;
    return l;
}

int list_destroy(al_t* l)
{
    if(l) {
        if(l->elements){
            free(l->elements);
        }
        l->elements = NULL;
        free(l);
        l = NULL;
        return 1;
    }
    return -1;
}

int list_clear(al_t *l)
{
    if(l){
        if(l->elements){
            memset(l->elements, 0, sizeof(elem_t) * l->capa);
            return 1;
        } else {
            return -2;
        }
    }
    return -1;
}

int insert_list_element(al_t *l, int loc, elem_t val)
{
    int ret = -1;
    if(loc < 0 || loc >= l->capa) {
        return -1;
    }
    if(l) {
        if(is_list_full(l)){
            ret = resize_list(l);
            if(ret < 0) {
                // fails on resizing
                return -1;
            }
        }
        // say we have 10 elements and add at loc=3 with number n
        // original : 0 1 2 3 4 5 6 7 8 9
        // new one  : 0 1 2 n 3 4 5 6 7 8 9 10
        // loc idx  : 0 1 2 3 4 5 6 7 8 9 10 11
        memcpy(l->elements + loc + 1, l->elements + loc, l->used - l->used);
        l->elements[loc] = val;
        l->used++;
        return 1;
    }
}

int append_list(al_t *l, elem_t val)
{
    return insert_list_element(l, l->used-1, val);
}

int remove_list_element(al_t *l, int loc)
{
    int ret = -1;
    if(loc < 0 || loc >= l->capa) {
        return -1;
    }
    if(l) {
        if(is_list_full(l)){
            ret = resize_list(l);
            if(ret < 0) {
                // fails on resizing
                return -1;
            }
        }
        // say we have 10 elements and remove element at loc=3
        // original : 0 1 2 3 4 5 6 7 8 9
        // new one  : 0 1 2 4 5 6 7 8 9 10
        // loc idx  : 0 1 2 3 4 5 6 7 8 9 10 11
        memcpy(l->elements + loc - 1 , l->elements + loc, l->used - l->used);
        l->elements[l->used-1] = 0;
        l->used--;
        return 1;
    }
}
i
int num_list_element(al_t *l)
{
    if(l){
        return l->used;
    } else {
        return -1;
    }
}
elem_t get_list_element_at(al_t *l, int loc, int *ret)
{
    if(loc < 0 || loc >= l->used){
         *ret = -2;
         return ret;
    }
    if(l){
        if(l->elements){
           *ret = 1;
           return l->elements[loc];
        } else {
           *ret = -1;
           return ret;
        }
    } else {
        *ret = -1;
        return ret;
    }
}
int get_list_capacity(al_t *l)
{
    if(l) {
        return l->capa;
    } else {
        return -1;
    }
}
bool is_list_full(al_t *l)
{
    return l->capa == l->used;
}
int resize_list(al_t *l)
{
    size_t newsz = (l->capa << 1); // double the size
    elem_t *tmp = (elem_t *)calloc(newsz, sizeof(elem_t));
    if(NULL == tmp) {
        return -1;
    }
    if(l->elements) {
        free(l->elements);
    }
    l->elements = tmp;
    l->capa = newsz;
    return 1;
}


```

Python의 경우에는 직접 구현 없이 이미 내장된 리스트를 사용하면 된다.

```python
L = []
# see details at https://www.w3schools.com/python/python_lists.asp
```

---

리스트를 이용하면 다양한 데이터를 담을 수 있다.

위의 예제에서는 단순하게 숫자 정보만 담았지만 리스트의 원소를 무엇으로 하느냐에 따라 다양한 것을 담을 수 있다

가령 학생의 성적을 담는 것이라면 이전에 정리한 2차원 배열과 같은 방법을 통해 컴퓨 인식하는 방법으로 구현 할 수 있지만 

*인간이 생각하는 방식* 으로 구현한다면 *구조체* 를 원소로 하는 배열 혹은 리스트를 통해 사용가능 하겟다.

아래에 대략적으로 틀만 코딩하면 다음과 같다.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct student_s
{
   char name[30]; // 나이
   int math; // 수학성적
   int phys; // 물리성적
   int chem; // 화학성적
   int biol; // 생물성적
}student_t;

typedef struct student_list_s
{
    student_t **elements;
    int capa;
    int used;
}student_list_t;

// initialize list

student_list_t *student_list = (student_list_t*)malloc(sizeof(student_list_t));
const int num_students = 100;
student_list->elements = (student_t **)malloc(sizeof(student_t *) * num_students);
size_t i = 0;
for(i = 0; i < num_students; i++){
    student_list->elements[i] = (student_t *)malloc(sizeof(student_t));
    menset(student_list->lements[i], 0, sizeof(student_t));
}

// fill data in list

// Access 10-th student and that person's score
// student_t *student = get_list_element_at(student_list, 9);
student_t *student = student_list->elements[9];
int math = student->math;
int phys = student->phys;
int chem = student->chem;
int biol = student->biol;

// do something with information above

// now destroy list
for(i = 0; i < num_students; i++){
    free(student_list->elements[i]);
}
free(student_list->elements);
free(student_list);

```

다음 정리할 내용은 배열을 이용 않고 논리적인 순서만 유지된 채로 물리적 순서는 다른 리스트를 구현해 볼까 한다.

