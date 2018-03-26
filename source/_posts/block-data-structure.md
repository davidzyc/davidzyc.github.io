---
title: 分块数据结构
date: 2018-03-26 21:21:05
categories:
- OI
tags:
- OI
- Programming
---

```cpp

// luogu-judger-enable-o2
#include<cstdio>
#include<iostream>
#include<cmath>
#include<algorithm>
#include<cstring>
using namespace std;

const int MAXN = 5e4+7;
const int MAXB = 50;
const int INF = 0x7fffffff;

int lastx = 0;
int n, m, t, tlen, nt;
int a[MAXN], b[MAXN];
int block[MAXB][MAXB][MAXN] = {0};
int pos[MAXN], l[MAXB], r[MAXB];
int tblock[MAXN];

void discrete_seq(){
  sort(b+1, b+n+1);
  nt = unique(b+1, b+n+1) - b - 1;
  for(int i=1; i<=n; i++){
    a[i] = lower_bound(b+1, b+nt+1, a[i]) - b - 1;
  }
  return;
}

void init(){
  discrete_seq();
  t = pow(n*1.0, 1.0/3.0);
  for(int i=1; i<=t; i++){
    l[i] = r[i-1] + 1;
    r[i] = l[i] - 1 + n / t;
  }
  if(r[t] < n){
    t++;
    l[t] = r[t-1] + 1;
    r[t] = n;
  }
  for(int i=1; i<=t; i++){
    for(int j=l[i]; j<=r[i]; j++){
      block[i][i][a[j]] ++ ;
      pos[j] = i;
    }
  }
  for(int len=1; len<t; len++){
    for(int tl=1; tl+len <= t; tl++){
      for(int k=0; k<nt; k++){
        block[tl][tl+len][k] = block[tl][tl][k] + block[tl+1][tl+len][k];
      }
    }
  }
  return;
}

int get_most(int tl, int tr){
  int maxcnt = 0, maxid;
  if(pos[tl] <= pos[tr] + 1){
    memset(tblock, 0, sizeof(tblock));
    for(int i=tl; i<=tr; i++){
      tblock[a[i]]++;
    }
    for(int i=0; i<nt; i++){
      if(tblock[i] > maxcnt){
        maxcnt = tblock[i];
        maxid = i;
      }
    }
  }else{
    int tbl, tbr;
    tbl = pos[tl]+1;
    tbr = pos[tr]-1;
    for(int i=tl; i<=r[pos[tl]]; i++){
      block[tbl][tbr][a[i]]++;
    }
    for(int i=l[pos[tr]]; i<=tr; i++){
      block[tbl][tbr][a[i]]++;
    }
    for(int i=0; i<nt; i++){
      if(tblock[i] > maxcnt){
        maxcnt = tblock[i];
        maxid = i;
      }
    }
    for(int i=tl; i<=r[pos[tl]]; i++){
      block[tbl][tbr][a[i]]--;
    }
    for(int i=l[pos[tr]]; i<=tr; i++){
      block[tbl][tbr][a[i]]--;
    }
  }
  return b[maxid+1];
}

int main(){
  // freopen("4168.txt", "r", stdin);
  scanf("%d%d", &n, &m);
  for(int i=1; i<=n; i++){
    scanf("%d", &a[i]);
    b[i] = a[i];
  }
  init();
  int tl, tr;
  for(int tq=0; tq<m; tq++){
    scanf("%d%d", &tl, &tr);
    tl = (tl+lastx-1)%(n)+1;
    tr = (tr+lastx-1)%(n)+1;
    if(tl > tr){
      swap(tl, tr);
    }
    lastx = get_most(tl, tr);
    printf("%d\n", lastx);
  }

  return 0;
}


```