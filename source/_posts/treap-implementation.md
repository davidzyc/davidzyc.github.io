---
title: Treap实现和易错点总结
date: 2018-03-25 00:26:19
categories:
- OI
tags:
- OI
- Programming
---

作为平衡BST里最简单的版本，Treap光是模板的难度就已经超过了提高+，达到了 **省选** （luogu上的紫色标签）。但是NOIP去年还真的考了一题！因此有必要认真的学习一下。（这也是大学CS数据结构课的标配，早晚要学，不如现在学掉）。    

本蒟蒻敲这个模板花了一个小时，调bug又花了半个小时。感谢lyd的算法竞赛进阶指南，他的代码是我唯一看得懂的一个treap版本。网上那些用指针实现的，阅读并不容易而且溢出调试麻烦....   

原理不说了，出门右拐百度百科。https://baike.baidu.com/item/Treap/4321536   

这里只列举一下写treap时易错的点。并且根据我的洛谷WA记录，treap一错那多半是大量RE， **很容易爆零** ，所以下面的坑一定要避而远之哦！   

# Treap易错点  

1. 初始化时添加最小点和最大点，他们之间的关系容易写错。
2. 插入操作时最后需要维护当前节点的附加信息，如子树个数等，容易遗忘。
3. root节点在初始化时需要额外手动指定，容易遗漏。
4. 函数名如new、insert、update等注意不要踩C++编译器关键字。
5. 查前驱后继时，找到并更新答案（左走到底和右走到底）应应时刻判断当前节点p为空的情况：进入while之前设置if判断是否存在左/右节点以便进入while；在while中判断tn[p].l/r是否存在，而不是判断p是否存在。后者会造成越界访问不存在的虚拟节点0，造成答案错误。
6. 任何对treap的修改（插入、修改、删除、旋转）都要执行update命令，并且注意update顺序：先update儿子节点，再update当前节点。
7. 对treap的修改、递归执行时，定义函数时把当前节点的传参设为引用。否则会导致更新失败。
8. 删除操作时判断左旋还是右旋，左旋条件为右子树为空，或者左子节点treap key堆键值比右子节点大。两条件顺序不可颠倒，关系为或。 **并且不要写成判断左子树是否存在，那是错的，会导致虚拟节点旋转上树进而造成树的断裂。** 
9. 按排名查找值和按值查找排名时，注意treap初始化时无穷大和无穷小节点的影响。为去除这两个节点的影响，输入排名时应+1，按值找到排名时应-1.

以下给出一份AC代码，对应   

luogu上的treap模板题    
https://www.luogu.org/problemnew/show/P3369


bzoj上的模板题   
http://www.lydsy.com/JudgeOnline/problem.php?id=3224

请展开阅读   

<!-- more -->

# 完整代码

```cpp

#include<cstdio>
#include<iostream>
#include<cstdlib>
using namespace std;

const int RNDS = 8247252;
const int RNDM = 2394522123;
const int MAXN = 1e5+7;
const int INF = 0x7fffffff;

int treap_root;
int tncnt = 0;

struct treap_node {
  int key, val;
  int l, r;
  int size, cnt;
};

treap_node tn[MAXN];

int new_node(int val){
  ++tncnt;
  tn[tncnt].val = val;
  tn[tncnt].key = rand() % RNDM;
  tn[tncnt].size = tn[tncnt].cnt = 1;
  return tncnt;
}

void update(int p){
  tn[p].size = tn[tn[p].l].size + tn[tn[p].r].size + tn[p].cnt;
  return;
}

void init(){
  new_node(-INF);
  new_node(INF);
  tn[1].r = 2;
  treap_root = 1;
  update(treap_root);
  return;
}

int get_val_by_rank(int p, int trank){
  if(p == 0) return INF;
  if(trank <= tn[tn[p].l].size){
    return get_val_by_rank(tn[p].l, trank);
  }
  if(trank > tn[tn[p].l].size && trank <= tn[tn[p].l].size + tn[p].cnt){
    return tn[p].val;
  }
  if(trank > tn[tn[p].l].size + tn[p].cnt){
    return get_val_by_rank(tn[p].r, trank - tn[tn[p].l].size - tn[p].cnt);
  }
}

int get_rank_by_val(int p, int tval){
  if(p == 0) return 0;
  if(tn[p].val == tval){
    return tn[tn[p].l].size + 1;
  }
  if(tval < tn[p].val){
    return get_rank_by_val(tn[p].l, tval);
  }
  if(tval > tn[p].val){
    return get_rank_by_val(tn[p].r, tval) + tn[tn[p].l].size + tn[p].cnt;
  }
}

void zig(int &p){
  int q = tn[p].l;
  tn[p].l = tn[q].r;
  tn[q].r = p;
  p = q;
  update(tn[p].r);
  update(p);
  return;
}

void zag(int &p){
  int q = tn[p].r;
  tn[p].r = tn[q].l;
  tn[q].l = p;
  p = q;
  update(tn[p].l);
  update(p);
  return;
}

void tn_insert(int &p, int tval){
  if(p == 0){
    p = new_node(tval);
    return;
  }
  if(tn[p].val == tval){
    tn[p].cnt++;
  }else if(tval < tn[p].val){
    tn_insert(tn[p].l, tval);
    if(tn[tn[p].l].key > tn[p].key){
      zig(p);
    }
  }else if(tval > tn[p].val){
    tn_insert(tn[p].r, tval);
    if(tn[tn[p].r].key > tn[p].key){
      zag(p);
    }
  }
  update(p);
  return;
}

int get_pre(int tval){
  int ans = 1;
  int p = treap_root;
  while(p){
    if(tn[p].val == tval){
      if(tn[p].l){
        p = tn[p].l;
        while(tn[p].r){
          p = tn[p].r;
        }
        ans = p;
      }
      break;
    }
    if(tn[p].val > tn[ans].val && tn[p].val < tval){
      ans = p;
    }
    p = tval < tn[p].val ? tn[p].l : tn[p].r;
  }
  return tn[ans].val;
}

int get_post(int tval){
  int p = treap_root;
  int ans = 2;
  while(p){
    if(tn[p].val == tval){
      if(tn[p].r){
        p = tn[p].r;
        while(tn[p].l){
          p = tn[p].l;
        }
        ans = p;
      }
      break;
    }
    if(tn[p].val < tn[ans].val && tn[p].val > tval){
      ans = p;
    }
    p = tval < tn[p].val ? tn[p].l : tn[p].r;
  }
  return tn[ans].val;
}

void tn_remove(int &p, int tval){
  if(p == 0) return;
  if(tval == tn[p].val){
    if(tn[p].cnt > 1){
      tn[p].cnt--;
      update(p);
      return;
    }
    if(tn[p].l || tn[p].r){
      if(!tn[p].r || tn[tn[p].l].key > tn[tn[p].r].key){
        zig(p);
        tn_remove(tn[p].r, tval);
      }else{
        zag(p);
        tn_remove(tn[p].l, tval);
      }
      update(p);
    }else{
      p = 0;
    }
    return;
  }
  if(tval < tn[p].val){
    tn_remove(tn[p].l, tval);
  }else{
    tn_remove(tn[p].r, tval);
  }
  update(p);
  return;
}

int main(){
  srand(RNDS);
  init();
  int opn, optype, opx;
  scanf("%d", &opn);
  for(int opnt=0; opnt<opn; opnt++){
    scanf("%d%d", &optype, &opx);
    switch(optype){
      case 1:
        tn_insert(treap_root, opx);
        break;
      case 2:
        tn_remove(treap_root, opx);
        break;
      case 3:
        printf("%d\n", get_rank_by_val(treap_root, opx) - 1);
        break;
      case 4:
        printf("%d\n", get_val_by_rank(treap_root, opx + 1));
        break;
      case 5:
        printf("%d\n", get_pre(opx));
        break;
      case 6:
        printf("%d\n", get_post(opx));
        break;
    }
  }

  return 0;
}


```

{% asset_img treap_ac.png Treap AC Record on BZOJ %}