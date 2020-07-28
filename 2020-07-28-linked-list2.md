---
title: 리스트(2) - 배열을 사용하지 않는 리스트
categories:
  - programming
tags:
  - 자료구조
  - programming
  - 리스트
---

지난 포스트에서 정리한 것은 __Single linked list__ 를 구현해 보았다.

이번 시간에는 __Double linked list__ 를 구현해 보고자 한다.

이전과 차이점은 node의 다음을 나타내는 pointer와 전단을 나타내는 pointer 이렇게 2개가 존재 한다는 것이다.

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
    struct node_s *prev; // newly added!
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
            l->elements->prev = NULL;
        } else {
            n = l->elements;
            for(i = 0; i < loc -1; i++){
                n = n->next;
            }
            node_t *newnode = (node_t *)malloc(sizeof(node_t));
            newnode->elem = val;
            newnode->next = n->next;
            n->prex->prev = newnode;
            newnode->prev = n;
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
            next_target->prev = n;
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
          l->elements->prev = NULL;
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

다음 시간에는 많이 쓰는 자료형인 스택(Stack)을 구현 해 본다

