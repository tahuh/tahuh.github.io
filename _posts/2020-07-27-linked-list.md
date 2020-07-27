---
title: 리스트(2) - 배열을 사용하지 않는 리스트
categories:
  - programming
tags:
  - 자료구조
  - programming
  - 리스트
---

지난 포스트에서 정리한 것은 __배열을 이용한 리스트__ 의 구현법이다.

이번 시간에는 __물리적인 순서와 논리적인 순서가 다른 리스트__ 를 구현해 보고자 한다.

이 방법의 장점은 배열을 미리 선언 하지 않기 때문에 배열의 크기가 곧 우리가 리스트에 가지고 있는 원소의 개수 만큼이 된다.

즉, 메모리의 낭비가 없어진다.

이 방법을 통해 "Single linked list"를 먼저 구현해 보고자 한다.

아래에서 직접 구현해 보자

```c

// linkedlist.c

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// element type
typedef int elem_t;
typedef struct node_s
{
    elem_t elem;
    struct node_s *next;
}node_t;

typdef struct linked_list_s
{
   int N; // number of elements in list
   node_t *elements;
}ll_t;

ll_t *init_list()
{
    ll_t *l = (ll_t *)malloc(sizeof(ll_t));
    if(NULL == l) { return NULL;}
    l->N = 0;
    l->elements = 0;
    return l;
}
int destroy_list(ll_t *l)
{
    node_t *n, *nn;
    if(l){
        if(l->elements){
            n = l->elements;
            while(n != NULL){
               nn = n->next;
               free(n); 
                n = nn;
            }
        }
        free(l);
        return 1;
    } else {
        return -1;
    }
}
int list_num_element(ll_t *l)
{
    return l->N;
}
int list_insert(ll_t *l, int loc, elem_t val)
{
    node_t *n;
    size_t i = 0;
    if(loc < 0 || loc >= l->N){
        return -1;
    }
    if(l) {
        if(l->elements == NULL) {
            l->elements = (node_t *)malloc(sizeof(node_t));
            l->elements->elem = val;
            l->elements->next = NULL;
        } else {
            n = l->elements;
            for(i = 0; i < loc -1; i++){
                n = n->next;
            }
            node_t *newnode = (node_t *)malloc(sizeof(node_t));
            newnode->elem = val;
            newnode->next = n->next;
            n->next = newnode;
        }
        l->N++;
        return l->N;
    } 
    return -1;
}


int list_remove(ll_t *l, int loc, int *ret)
{
    node_t *n;
    size_t i = 0;
    if(loc < 0 || loc >= l->N){
        *ret = -1;
        return -1;
    }
    if(l) {
        if(l->elements == NULL) {
            *ret = -1;
            return -1;
        } else {
            n = l->elements;
            for(i = 0; i < loc -1; i++){
                n = n->next;
            }
            node_t *target = n->next;
            node_t *next_target = target->next;
            n->next = next_target;
            free(target);
        }
        l->N--;
        *ret = 1;
        return l->N;
    }
    *ret = -1;
    return -1;
}

int list_append(ll_t *l, elem_t val)
{
    if(l) {
        if(l->elements) {
          l->elements = (node_t *)malloc(sizeof(node_t));
          l->elements->next = NULL;
          l->elements->elem = val;
        } else {
          // this can be optimized if we use alternate pointer in struct
          return list_insert(l, l->N-1, val);
        }
        l->N++;
        return l->N;
    } else {
        return -1;
    }
}
elem_t list_at(ll_t *l, int loc, int *ret)
{
    node_t *n = l->elements;
    for(size_t i = 0; i < loc; i++){
        n=n->next;
    }
    return n->val;
}

```

위의 리스트의 구현은 사실 최적화를 더 할 수 있으나 일단은 구현 않는다.

다음시간에는 이중연결리스트를 구현해 보도록한다
