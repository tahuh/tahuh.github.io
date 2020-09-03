---
title : 트리(2) - 이진 트리(binary tree)
categories:
  - programming
tags:
  - programming
  - 자료구조
---

긴 장마와 태풍이 지나고 간만에 하는 포스팅이다.

지난 번에는 Tree의 구조에 대해서 알아 보았다.

오늘은 트리 중 하나인 이진트리(Binary Tree)에 대해서 알아 보고자 한다.

Binary tree의 특징은 자식 노드가 최대 2개라는 점이다.

이 Binary tree에는 다음의 3가지 종 있다

- Full binary tree

- Complete binary tree

- Balanced binary tree

각각에 대한 설명은 다음과 같다.

Full binary tree

모든 level에서 node들이 꽉 채워진 형태이다. 따라서 level이 n이라면 총 2^n - 1 개의 node를 가진다.

Compete binary tree

마지막 level을 제외한 모든 level에서 node가 다 채워져 있다.

Balanced binary tree

모든 노드에서 왼쪽 sub-tree와 오른쪽 sub-tree의 height이 최대 1만큼 차이나는 tree

이제 C를 이용해 구현해 보고자 한다.

```c
// binary_tree.h

#ifndef _BINARY_TREE_H
#define _BINARY_TREE_H
#ifdef __cplusplus
extern "C"
{
#endif

// leaves
typedef struct bt_node_s
{
    int data;
    struct bt_node_s *left;
    struct bt_node_s *right;
}bt_node_t;

// the tree body
typedef struct btree_s
{
    bt_node_t* root;
}btree_t;

btree_t *create_btree(bt_node_t *node);
bt_node_t* get_root_node(btree_t *bt);
bt_node_t* insert_left_child(bt_node_t *parent, bt_node_t elem);
bt_node_t *insert_right_child(bt_node_t *parent, bt_node_t elem);
bt_node_t *get_left_child(bt_node_t *node);
bt_node_t *get_right_child(bt_node_t *node);
void delete_btree(btree_t *bt);
void delete_btree_node(bt_node_t *n);

#ifdef __cplusplus
}
#endif
#endif


// binary_tree.c
btree_t *create_btree(bt_node_t node)
{
   btree_t *bt = NULL;
   bt = (btree_t *)malloc(sizeof(btree_t));
   if(NULL != bt){
       bt->root = (bt_node_t *)malloc(sizeof(bt_node_t));
       if(bt->root != NULL){
           *(bt->root) = node;
           bt->root->left = NULL; bt->root->right=NULL;
           return bt;
       } else {
           free(bt);
           return NULL;
       }
   } else {
       return NULL;
   }
}
bt_node_t* get_root_node(btree_t *bt)
{
    bt_node_t *n = NULL;
    if(bt != NULL) {
        n=bt->root;
    }
    return n;
}
bt_node_t* insert_left_child(bt_node_t *parent, bt_node_t elem)
{
    if(parent != NULL){
        if(parent->left == NULL){
           parent->left = (bt_node_t*)malloc(sizeof(bt_node_t));
           if(parent->left != NULL){
              *(parent->left) = elem;
              parent->left->left = NULL;
              parent->left->right = NULL;
              return parent->left;
           }
        }
    } else {
       return NULL;
    }
}
bt_node_t *insert_right_child(bt_node_t *parent, bt_node_t elem)
{
    if(parent != NULL){
        if(parent->right == NULL){
           parent->lright = (bt_node_t*)malloc(sizeof(bt_node_t));
           if(parent->right != NULL){
              *(parent->right) = elem;
              parent->right->left = NULL;
              parent->right->right = NULL;
              return parent->right;
           }
        }
    } else {
       return NULL;
    }

}
bt_node_t *get_left_child(bt_node_t *node)
{
    if(node){
        return node->left;
    } else {
        return NULL;
    }
}
bt_node_t *get_right_child(bt_node_t *node)
{
    if(node){
        return node->right;
    } else {
        return NULL;
    }
}
void delete_btree(btree_t *bt)
{
    if(bt != NULL){
        delete_btree_node(bt->root);
        free(bt);
    }
}
void delete_btree_node(bt_node_t *n)
{
    if(n != NULL){
        delete_btree_node(n->left);
        delete_btree_node(n->right);
        free(n);
    }
}


```

다음 시간에는 이 이진 트리를 이용해서 트리를 탐색하는 연산을 해본다.

이 방법론이 바로 이진 탐색트리의 기초가 된다
