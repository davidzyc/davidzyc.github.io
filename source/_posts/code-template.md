---
title: 代码模板库
date: 2018-08-02 14:46:17
categories:
- OI
tags:
- ACM
- OI
- Programming
---

# 数据结构（data structure）

## 线段树

```cpp

#include<cstdio>
#include<iostream>

using namespace std;

typedef long long LL;
const int MAXN = 1e5+7;
int n, m;
LL a[MAXN] = {0};
LL dat[MAXN*4] = {0}, datad[MAXN*4] = {0};

void init(int node_id, int l, int r){
  if(l == r){
    dat[node_id] = a[l];
  }else{
    int mid = (l+r)/2;
    init(node_id*2, l, mid);
    init(node_id*2+1, mid+1, r);
    dat[node_id] = dat[node_id*2] + dat[node_id*2+1];
  }
}

void spread(int node_id, int l, int r){
  if(datad[node_id] != 0){
    int mid = (l+r)/2;
    dat[node_id*2] += (mid-l+1) * datad[node_id];
    dat[node_id*2+1] += (r-mid) *datad[node_id];
    datad[node_id*2] += datad[node_id];
    datad[node_id*2+1] += datad[node_id];
    datad[node_id] = 0;
  }
}

void update(int x, int y, LL p, int node_id, int l, int r){
  if(x <= l && y >= r){
    dat[node_id] += (r-l+1)*p;
    datad[node_id] += p;
    return;
  }
  spread(node_id, l, r);
  int mid = (l+r)/2;
  if(x <= mid) update(x, y, p, node_id*2, l, mid);
  if(y > mid) update(x, y, p, node_id*2+1, mid+1, r);
  dat[node_id] = dat[node_id*2] + dat[node_id*2+1];
  return;
}

LL query(int x, int y, int node_id, int l, int r){
  if(x <= l && y >= r){
    return dat[node_id];
  }
  spread(node_id, l, r);
  int mid = (l+r)/2;
  LL tsum = 0;
  if(x <= mid) tsum += query(x, y, node_id*2, l, mid);
  if(y > mid) tsum += query(x, y, node_id*2+1, mid+1, r);
  return tsum;
}

int main(){
  cin >> n >> m;
  for(int i=1; i<=n; i++){
    scanf("%lld", &a[i]);
  }
  init(1, 1, n);
  int optype, x, y;
  LL k;
  for(int mi=0; mi<m; mi++){
    scanf("%d", &optype);
    if(optype == 1){
      scanf("%d %d %lld", &x, &y, &k);
      update(x, y, k, 1, 1, n);
    }else if(optype == 2){
      scanf("%d %d", &x, &y);
      printf("%lld\n", query(x, y, 1, 1, n));
    }
  }

  return 0;
}


```

## ZKW线段树

```cpp

#include<cstdio>
#include<iostream>
#include<algorithm>
#include<cstring>

using namespace std;

const int MAXN = 2e5+7;

int n, m;
int a[MAXN];
int dat[MAXN];

void pushup(int node_id){
    dat[node_id] = dat[node_id << 1] + dat[(node_id << 1)^1];
    return;
}

void init(){
    for(m=1; m<n; m<<=1);
    for(int i=1; i<=n; i++){
        dat[m+i] = a[i];
    }
    for(int i=m-1; i>=1; i--){
        pushup(i);
    }
    return;
}

void update(int p, int val){
    p += m;
    dat[p] += val;
    for(p>>=1; p>0; p>>=1){
        pushup(p);
    }
    return;
}

int query(int l, int r){
    int ans = 0;
    for(l=l+m-1, r=r+m+1; l^r^1; l>>=1, r>>=1){
        if(~l&1){
            ans += dat[l^1];
        }
        if(r&1){
            ans += dat[r^1];
        }
    }
    return ans;
}

int main(){


    return 0;
}


```

## 树链剖分

```cpp

#include<cstdio>
#include<iostream>
#include<vector>
using namespace std;

const int MAXN = 1e5+7;

int n, m, r, gmod;
int a[MAXN];
vector<int> to[MAXN];

// segment tree
int tsum[MAXN*4] = {0}, lazytag[MAXN*4] = {0};

// tree cut
int fa[MAXN], hson[MAXN], sonsize[MAXN], dep[MAXN];
int dfn[MAXN], dfid[MAXN], top[MAXN], dfncnt = 0;

void init(int node_id, int l, int r){
  if(l == r){
    tsum[node_id] = a[dfid[l]];
  }else{
    int mid = (l+r)/2;
    init(node_id*2, l, mid);
    init(node_id*2+1, mid+1, r);
    tsum[node_id] = tsum[node_id*2] % gmod + tsum[node_id*2+1] % gmod;
    tsum[node_id] %= gmod;
  }
}

void spread(int node_id, int l, int r){
  if(lazytag[node_id] != 0){
    int mid = (l+r)/2;
    tsum[node_id*2] += (mid-l+1) * lazytag[node_id];
    tsum[node_id*2+1] += (r-mid) *lazytag[node_id];
    tsum[node_id*2] %= gmod;
    tsum[node_id*2+1] %= gmod;
    lazytag[node_id*2] += lazytag[node_id];
    lazytag[node_id*2+1] += lazytag[node_id];
    lazytag[node_id*2] %= gmod;
    lazytag[node_id*2+1] %= gmod;
    lazytag[node_id] = 0;
  }
}

void update(int x, int y, int p, int node_id, int l, int r){
  if(x <= l && y >= r){
    tsum[node_id] += ((r-l+1)*p)%gmod;
    tsum[node_id] %= gmod;
    lazytag[node_id] += p;
    lazytag[node_id] %= gmod;
    return;
  }
  spread(node_id, l, r);
  int mid = (l+r)/2;
  if(x <= mid) update(x, y, p, node_id*2, l, mid);
  if(y > mid) update(x, y, p, node_id*2+1, mid+1, r);
  tsum[node_id] = tsum[node_id*2] + tsum[node_id*2+1];
  tsum[node_id] %= gmod;
  return;
}

int query(int x, int y, int node_id, int l, int r){
  if(x <= l && y >= r){
    return tsum[node_id];
  }
  spread(node_id, l, r);
  int mid = (l+r)/2;
  int tsum = 0;
  if(x <= mid) tsum += query(x, y, node_id*2, l, mid);
  if(y > mid) tsum += query(x, y, node_id*2+1, mid+1, r);
  return tsum % gmod;
}

void predfs(int x){
  int tmaxson = 0, maxsonid = 0;
  sonsize[x] = 1;
  for(int i=0; i<to[x].size(); i++){
    int y = to[x][i];
    if(fa[x] == y) continue;
    fa[y] = x;
    dep[y] = dep[x] + 1;
    predfs(y);
    if(sonsize[y] > tmaxson){
      tmaxson = sonsize[y];
      maxsonid = y;
    }
    sonsize[x] += sonsize[y];
  }
  hson[x] = maxsonid;
  return;
}

void cutdfs(int x, int ttop){
  ++dfncnt;
  dfn[x] = dfncnt;
  dfid[dfncnt] = x;
  top[x] = ttop;
  if(!hson[x]) return;
  cutdfs(hson[x], ttop);
  for(int i=0; i<to[x].size(); i++){
    int y = to[x][i];
    if(y == fa[x] || y == hson[x]) continue;
    cutdfs(y, y);
  }
  return;
}

int tree_query(int x, int y){
  int tans = 0;
  while(top[x] != top[y]){
    if(dep[top[x]] < dep[top[y]]) swap(x, y);
    tans += query(dfn[top[x]], dfn[x], 1, 1, n);
    tans %= gmod;
    x = fa[top[x]];
  }
  if(dep[x] < dep[y]) swap(x, y);
  tans += query(dfn[y], dfn[x], 1, 1, n);
  return tans % gmod;
}

void tree_update(int x, int y, int val){
  while(top[x] != top[y]){
    if(dep[top[x]] < dep[top[y]]) swap(x, y);
    update(dfn[top[x]], dfn[x], val, 1, 1, n);
    x = fa[top[x]];
  }
  if(dep[x] < dep[y]) swap(x, y);
  update(dfn[y], dfn[x], val, 1, 1, n);
  return;
}

int son_query(int x){
  // cout << "Q Fa " << dfn[x] << " size " <<dfn[x]+sonsize[x]-1 << endl;
  return query(dfn[x], dfn[x]+sonsize[x]-1, 1, 1, n) % gmod;
}

void son_update(int x, int val){
  // cout << "UPD From " << x << " size " <<dfn[x]+sonsize[x]-1 << endl;
  update(dfn[x], dfn[x]+sonsize[x]-1, val, 1, 1, n);
}

int main(){
  // freopen("tree_cut.txt", "r", stdin);
  int tf, tt;
  scanf("%d%d%d%d", &n, &m, &r, &gmod);
  for(int i=1; i<=n; i++){
    scanf("%d", &a[i]);
  }
  // init(1, n, 1);
  for(int i=1; i<n; i++){
    scanf("%d%d", &tf, &tt);
    to[tf].push_back(tt);
    to[tt].push_back(tf);
  }
  dep[r] = 1;
  predfs(r);
  cutdfs(r, r);
  init(1, 1, n);
  // uptsume(1, n, 1, 1, 1, n);
  // cout << query(4, n, 1, 1, n) << endl;
  // for(int i=1; i<=n; i++) cout << dfid[i] << " ";
  int optype, opx, opy, opz;
  for(int opi=0; opi<m; opi++){
    // for(int i=1; i<=n; i++){
    //   cout << query(dfn[i], dfn[i], 1, 1, n) << " ";
    // }
    // cout << endl;
    scanf("%d", &optype);
    switch(optype){
      case 1:
        scanf("%d%d%d", &opx, &opy, &opz);
        tree_update(opx, opy, opz);
      break;
      case 2:
        scanf("%d%d", &opx, &opy);
        printf("%d\n", tree_query(opx, opy) % gmod);
      break;
      case 3:
        scanf("%d%d", &opx, &opz);
        son_update(opx, opz);
      break;
      case 4:
        scanf("%d", &opx);
        printf("%d\n", son_query(opx) % gmod);
      break;
    }
  }

  return 0;
}


```


## 左偏树（可并堆）


```cpp

#include<cstdio>
#include<iostream>
#include<cstring>

using namespace std;

const int MAXN = 2e5+7;

int n, m;
int val[MAXN], ch[MAXN][2], fa[MAXN], dist[MAXN];

int merge(int x, int y){
  if(!x || !y){
    return x | y;
  }
  if(val[x] > val[y] || (val[x] == val[y]) && x > y){
    swap(x, y);
  }
  ch[x][1] = merge(ch[x][1], y);
  fa[ch[x][1]] = x;
  if(dist[ch[x][1]] > dist[ch[x][0]]){
    swap(ch[x][1], ch[x][0]);
  }
  dist[x] = dist[ch[x][1]] + 1;
  return x;
}

int getfa(int x){
  return !fa[x] ? x : getfa(fa[x]);
}

int main(){
  memset(val, 0xff, sizeof(val));
  memset(dist, 0xff, sizeof(dist));
  scanf("%d%d", &n, &m);
  for(int i=1; i<=n; i++){
    scanf("%d", &val[i]);
  }
  int opa, opx, opy;
  for(int i=0; i<m; i++){
    scanf("%d", &opa);
    if(opa == 1){
      scanf("%d%d", &opx, &opy);
      if(val[opx] == -1 || val[opy] == -1) continue;
      if(opx == opy) continue;
      opx = getfa(opx), opy = getfa(opy);
      if(opx == opy) continue;
      merge(opx, opy);
    }else if(opa == 2){
      scanf("%d", &opx);
      if(val[opx] == -1){
        printf("-1\n");
        continue;
      }
      opx = getfa(opx);
      printf("%d\n", val[opx]);
      fa[ch[opx][0]] = fa[ch[opx][1]] = 0;
      val[opx] = -1;
      merge(ch[opx][0], ch[opx][1]);
    }
  }


  return 0;
}


```


## 树堆（Treap）

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


## 伸展树（Splay）

```cpp

#include<cstdio>
#include<iostream>
#include<cstring>

using namespace std;

const int MAXN = 1e6+7;
const int INF = 0x3f3f3f3f;

int n;
int root = 1;
int node_cnt = 0;
int val[MAXN], fa[MAXN], ch[MAXN][2], sz[MAXN], cnt[MAXN], rev[MAXN];

int rlt(int x){
  return ch[fa[x]][1] == x;
}

void conn(int x, int f, int rlt){
  fa[x] = f;
  ch[f][rlt] = x;
}

void pushup(int x){
  sz[x] = sz[ch[x][0]] + sz[ch[x][1]] + cnt[x];
  if(ch[x][0]) fa[ch[x][0]] = x;
  if(ch[x][1]) fa[ch[x][1]] = x;
  // fa[ch[x][0]] = fa[ch[x][1]] = x;
}

void pushdown(int x){
  if(rev[x]){
    swap(ch[x][0], ch[x][1]);
    rev[ch[x][0]] ^= 1;
    rev[ch[x][1]] ^= 1;
    rev[x] = 0;
  }
}

void rotate(int x){
  int y, z, zsony, ysonx;
  y = fa[x];
  z = fa[y];
  zsony = rlt(y);
  ysonx = rlt(x);
  conn(ch[x][ysonx^1], y, ysonx);
  conn(y, x, ysonx^1);
  conn(x, z, zsony);
  pushup(y);
  pushup(x);
}

void splay(int x, int des){
  if(x == des) return;
  // cout << "HI";
  while(fa[x] != des){
    int y = fa[x];
    // printf("%d [fa] %d to %d\n", x, y, des);
    if(fa[y] != des){
      if(rlt(x) == rlt(y)){
        rotate(y);
      }else{
        rotate(x);
      }
    }
    rotate(x);
  }
  if(!des) root = x;
}

int new_node(int v){
  ++node_cnt;
  val[node_cnt] = v;
  cnt[node_cnt] = 1;
  sz[node_cnt] = 1;
  return node_cnt;
}

// int ins(int x, int v){
//   if(v == val[x]){
//     cnt[x]++;
//     pushup(x);
//     return x;
//   }
//   if(v < val[x]){
//     if(ch[x][0]){
//       ins(ch[x][0], v);
//     }else{
//       ch[x][0] = new_node(v);
//     }
//   }else{
//     if(ch[x][1]){
//       ins(ch[x][1], v);
//     }else{
//       ch[x][1] = new_node(v);
//     }
//   }
//   pushup(x);
//   return
// }

int ins(int &x, int v){
  if(!x){
    x = new_node(v);
    pushup(x);
    return x;
  }
  int ret;
  if(val[x] == v){
    cnt[x] ++;
    pushup(x);
    return x;
  }else if(v < val[x]) ret = ins(ch[x][0], v);
  else ret = ins(ch[x][1], v);
  pushup(x);
  return ret;
}

int kthnode(int x, int kth){
  pushdown(x);
  if(kth <= sz[ch[x][0]]){
    return kthnode(ch[x][0], kth);
  }else if(kth > sz[ch[x][0]] && kth <= sz[ch[x][0]]+cnt[x]){
    return x;
  }else{
    return kthnode(ch[x][1], kth-sz[ch[x][0]]-cnt[x]);
  }
  return -1;
}

void write(int x){
  pushdown(x);
  if(ch[x][0]) write(ch[x][0]);
  // printf("%d-cnt%d\n", val[x], cnt[x]);
  if(val[x]>0 && val[x] <=n) printf("%d ", val[x]);
  if(ch[x][1]) write(ch[x][1]);
}

int main(){
  root = new_node(INF);
  ch[root][0] = new_node(-INF);
  pushup(root);
  scanf("%d", &n);
  for(int i=1; i<=n; i++){
    int x = ins(root, i);
    splay(x, 0);
  }
  // for(int i=1; i<=node_cnt; i++){
    // printf("%d <- [fa %d] %d [val %d] -> %d\n", ch[i][0], fa[i], i, val[i], ch[i][1]);
  // }
  int q, ql, qr;
  scanf("%d", &q);
  for(int i=1; i<=q; i++){
    scanf("%d%d", &ql, &qr);
    ql = kthnode(root, ql);
    qr = kthnode(root, qr+2);
    // cout << ql << " " << qr << endl;
    splay(ql, 0);
    splay(qr, ql);
    rev[ch[qr][0]] ^= 1;
  }
  // printf("%d", kthnode(root, 7));
  write(root);


  return 0;
}


```


# 字符串

## 单模式串匹配（KMP）

```cpp

#include<cstdio>
#include<iostream>
#include<cstring>
#include<string>

using namespace std;

const int MAXN = 1e6+7;

int an, matn;
char a[MAXN];
char mat[MAXN];

int nxt[MAXN] = {0};

int main(){
  scanf("%s", a+1);
  scanf("%s", mat+1);
  an = strlen(a+1);
  matn = strlen(mat+1);

  for(int i=2, j=0; i<=matn; i++){
    while(j && mat[j+1] != mat[i]) j = nxt[j];
    if(mat[j+1] == mat[i]) ++j;
    nxt[i] = j;
  }

  for(int i=1, j=0; i<=an; i++){
    while(j && a[i] != mat[j+1]) j = nxt[j];
    if(mat[j+1] == a[i]) ++j;
    if(j == matn){
      printf("%d\n", i-j+1);
      j=nxt[j];
    }
  }

  for(int i=1; i<=matn; i++){
    printf("%d ", nxt[i]);
  }

  return 0;
}


```

## 多模式串匹配 （AC自动机）

```cpp

#include<cstdio>
#include<iostream>
#include<cstring>
#include<queue>
using namespace std;

const int MAXN = 1e6+7;

char c[MAXN];

struct Aho_corasick_automation {
  int cnt = 0;
  int val[MAXN] = {0};
  int e[MAXN][27] = {0};
  int fail[MAXN] = {0};

  void ins(char *s){
    int l = strlen(s);
    int x = 0, y;
    for(int i=0; i<l; i++){
      y = s[i] - 'a';
      if(!e[x][y]) e[x][y] = ++cnt;
      x = e[x][y];
    }
    val[x] += 1;
  }

  void get_fail(){
    queue<int> q;
    int x = 0, y;
    for(int i=0; i<26; i++){
      if(e[x][i]){
        fail[e[x][i]] = x;
        q.push(e[x][i]);
      }
    }
    while(!q.empty()){
      // cout << "HI ";
      int x = q.front();
      q.pop();
      for(int i=0; i<26; i++){
        if(e[x][i]){
          fail[e[x][i]] = e[fail[x]][i];
          q.push(e[x][i]);
        }else{
          e[x][i] = e[fail[x]][i];
        }
      }
    }
  }

  int ac_query(char *s){
    int x = 0, y;
    int l = strlen(s);
    int ans = 0;
    for(int i=0; i<l; i++){
      y = s[i] - 'a';
      x = e[x][y];
      for(int t = x; t && val[t] != -1; t = fail[t]){
        ans += val[t];
        val[t] = -1;
      }
    }
    return ans;
  }

}AC;

int main(){

  int n;
  scanf("%d", &n);
  for(int i=0; i<n; i++){
    scanf("%s", c);
    AC.ins(c);
  }
  AC.get_fail();
  scanf("%s", c);
  printf("%d", AC.ac_query(c));


  return 0;
}


```


# 图论（Graph Theory）

## 单源最短路（Single Source Shortest Path）

```cpp

#include<cstdio>
#include<iostream>
#include<cstring>
#include<algorithm>
#include<queue>
#include<stack>
#define MAXM 1000001
#define MAXN 20001
#define INF 2147483647
using namespace std;

typedef pair<int, int> pii;
int n, m, s;
int from[MAXM] = {0}, to[MAXM] = {0}, c[MAXM], first[MAXN] = {0}, nxt[MAXM] = {0};
int d[MAXN], v[MAXN];
priority_queue<pii, vector<pii>, greater<pii> > pq;
stack<int> st;

void dijkstra(){
  fill(d, d+n+1, INF);
  memset(v, 0, sizeof(v));
  d[s] = 0;
  pq.push(make_pair(d[s], s));
  while(!pq.empty()){
    pii cur = pq.top();
    pq.pop();
    int tc, tid;
    // tc = cur.first;
    tid = cur.second;
    if(v[tid]) continue;
    v[tid] = 1;
    for(int edge=first[tid]; edge!=0; edge=nxt[edge]){
      int tto = to[edge];
      if(v[tto]) continue;
      if(d[tid] < INF && d[tid] + c[edge] < d[tto]){
        d[tto] = d[tid] + c[edge];
        pq.push(make_pair(d[tto], tto));
      }
    }
  }
}

void spfa(){
  fill(d, d+n+1, INF);
  memset(v, 0, sizeof(v));
  d[s] = 0;
  st.push(s);
  while(!st.empty()){
    int tid = st.top();
    st.pop();
    for(int edge=first[tid]; edge != 0; edge = nxt[edge]){
      if(d[tid] < INF && d[tid]+c[edge]<d[to[edge]]){
        d[to[edge]] = d[tid]+c[edge];
        st.push(to[edge]);
      }
    }
  }
}

int main(){
  int t;
  cin >> n >> m >> s;
  for(int i=1; i<=m; i+=1){
    cin >> from[i] >> to[i] >> c[i];
    // from[i+1] = to[i];
    // to[i+1] = from[i];
    // c[i+1] = c[i];
    t = first[from[i]];
    first[from[i]] = i;
    nxt[i] = t;
    // t = first[from[i+1]];
    // first[from[i+1]] = i+1;
    // nxt[i+1] = t;
  }
  dijkstra();
  for(int i=1; i<=n; i++){
    cout << d[i] << " ";
  }

  return 0;
}



```


## 有向图强连通分量/缩点（Tarjan）

```cpp

#include<cstdio>
#include<iostream>
#include<cstring>
#include<vector>
#include<queue>
using namespace std;

const int MAXN = 2e5+7;

struct Graph{
  int n;
  int en = 0;
  int from[MAXN], to[MAXN], head[MAXN], nxt[MAXN];
  int cnt = 0;
  int dfn[MAXN], low[MAXN];
  int sccn = 0, sccid[MAXN];
  vector<int> scc[MAXN];
  int stan = 0, sta[MAXN], ins[MAXN];
  int dot[MAXN];
  int indeg[MAXN], outdeg[MAXN];
  int dp[MAXN];

  void add_edge(int f, int t){
    ++en;
    from[en] = f;
    to[en] = t;
    nxt[en] = head[f];
    head[f] = en;
    outdeg[f] ++;
    indeg[t] ++;
    return;
  }

  void tarjan(int x){
    dfn[x] = low[x] = ++cnt;
    sta[stan++] = x;
    ins[x] = 1;
    for(int e=head[x]; e; e=nxt[e]){
      int y = to[e];
      if(!dfn[y]){
        tarjan(y);
        low[x] = min(low[x], low[y]);
      }else if (ins[y]){
        low[x] = min(low[x], low[y]);
      }
    }
    if(low[x] == dfn[x]){
      ++sccn;
      int y;
      do{
        y = sta[--stan];
        ins[y] = 0;
        sccid[y] = sccn;
        scc[sccn].push_back(y);
      }while(x != y);
    }
  }

  void get_dag_dp(){
    queue<int> q;
    for(int i=1; i<=n; i++){
      if(indeg[i] == 0){
        q.push(i);
      }
    }

    while(!q.empty()){
      int x = q.front();
      q.pop();
      dp[x] += dot[x];
      for(int e = head[x]; e; e=nxt[e]){
        int y = to[e];
        dp[y] = max(dp[y], dp[x]);
        --indeg[y];
        if(!indeg[y] && !ins[y]){
          q.push(y);
          ins[y] = 1;
        }
      }
    }
  }

};

struct DJSet{
  int fa[MAXN];
  void init(int n){
    for(int i=1; i<=n; i++) fa[i] = i;
  }
  int get_fa(int x){
    return fa[x] == x ? x : fa[x] = get_fa(fa[x]);
  }
};

Graph a, b;
DJSet d;

int main(){
  int f, t, m;
  scanf("%d%d", &a.n, &m);
  for(int i=1; i<=a.n; i++){
    scanf("%d", &a.dot[i]);
  }
  for(int i=1; i<=m; i++){
    scanf("%d%d", &f, &t);
    a.add_edge(f, t);
  }
  for(int i=1; i<=a.n; i++){
    if(!a.dfn[i]){
      a.tarjan(i);
    }
  }
  b.n = a.sccn;
  d.init((b.n*2)+2);
  for(int i=1; i<=a.en; i++){
    f = a.sccid[a.from[i]], t = a.sccid[a.to[i]];
    // cout << f << " -> " << t << " " << d.get_fa(f) << " " << d.get_fa(t + b.n) <;
    if(f != t && (d.get_fa(f) != d.get_fa(t + b.n))){
      b.add_edge(f, t);
      d.fa[d.get_fa(f)] = d.get_fa(t + b.n);
    }
  }
  for(int i=1; i<=a.n; i++){
    b.dot[a.sccid[i]] += a.dot[i];
  }
  b.get_dag_dp();
  int maxdp = 0;
  for(int i=1; i<=b.n; i++){
    // cout << b.dp[i] << " ";
    maxdp = max(maxdp, b.dp[i]);
  }
  printf("%d\n", maxdp);
  // for(int i=1; i<=a.n; i++) cout << a.sccid[i] << " ";
  // cout << endl;
  // for(int i=1; i<=a.sccn; i++){
  //   for(int j=0; j<a.scc[i].size(); j++){
  //     printf("%d ", a.scc[i][j]);
  //   }
  //   printf("\n");
  // }

  return 0;
}


```


## Dinic最大流

```cpp

#include<cstdio>
#include<iostream>
#include<cstring>
#include<queue>
using namespace std;

const int MAXN = 1e5+7;
const int INF = 0x3f3f3f3f;

class Network {
private:
  int n, en;
  int s, t;
  int head[MAXN], nxt[MAXN], from[MAXN], to[MAXN], flow[MAXN];
  int lev[MAXN], cur[MAXN], v[MAXN];
public:
  void init(int n, int s, int t) {
    this->n = n;
    this->s = s;
    this->t = t;
    en = 1;
  }

  void add_edge(int f, int t, int w){
    ++en;
    from[en] = f;
    to[en] = t;
    flow[en] = w;
    nxt[en] = head[f];
    head[f] = en;
    return;
  }

  bool bfs() {
    memset(lev, 0, sizeof(lev));
    queue<int> q;
    lev[s] = 1;
    q.push(s);
    while(!q.empty()){
      int x = q.front();
      q.pop();
      for(int e=head[x]; e; e=nxt[e]){
        int y = to[e];
        if(!lev[y] && flow[e] > 0){
          lev[y] = lev[x] + 1;
          q.push(y);
        }
      }
    }
    return lev[t] > 0 ? true : false;
  }

  int dfs(int x, int xflow){
    if(x == t){
      return xflow;
    }
    for(int& e=cur[x]; e; e=nxt[e]){
      int y = to[e];
      if(lev[y] == lev[x] + 1 && flow[e] > 0){
        int rflow = dfs(y, min(xflow, flow[e]));
        if(rflow > 0){
          flow[e] -= rflow;
          flow[e^1] += rflow;
          return rflow;
        }
      }
    }
    return 0;
  }

  int dinic(){
    int ans = 0, tmp;
    while(bfs()){
      for(int i=1; i<=n; i++) cur[i] = head[i];
      // cout << ans << " ";
      while(tmp = dfs(s, INF)){
        ans += tmp;
      }
    }
    return ans;
  }

};

Network nt;
int n, m, s, t, w;

int main(){

  scanf("%d%d%d%d", &n, &m, &s, &t);
  nt.init(n, s, t);
  for(int i=0; i<m; i++){
    scanf("%d%d%d", &s, &t, &w);
    nt.add_edge(s, t, w);
    nt.add_edge(t, s, 0);
  }
  printf("%d", nt.dinic());

  return 0;
}


```

## 最小费用最大流

```cpp

#include<cstdio>
#include<iostream>
#include<cstring>
#include<queue>

using namespace std;

typedef pair<int, int> pii;

const int MAXN = 1e5+7;
const int INF = 0x3f3f3f3f;

class Network {

private:

  int n, en, s, t;
  int head[MAXN], nxt[MAXN], from[MAXN], to[MAXN], flow[MAXN], cost[MAXN];
  int dist[MAXN], inq[MAXN], pedge[MAXN], xflow[MAXN];

  void _add(int f, int t, int w, int c){
    ++en;
    from[en] = f;
    to[en] = t;
    flow[en] = w;
    cost[en] = c;
    nxt[en] = head[f];
    head[f] = en;
  }

public:

  void init(int n, int s, int t){
    this -> en = 1;
    this -> n = n;
    this -> s = s;
    this -> t = t;
  }

  void add_edge(int f, int t, int w, int c){
    _add(f, t, w, c);
    _add(t, f, 0, -c);
  }

  bool spfa(){
    queue<int> q;
    memset(dist, 0x3f, sizeof(dist));
    memset(inq, 0, sizeof(inq));
    memset(pedge, 0, sizeof(pedge));
    // memset(xflow, 0, sizeof(xflow));
    q.push(s);
    inq[s] = 1;
    dist[s] = 0;
    while(!q.empty()){
      int x = q.front();
      inq[x] = 0;
      q.pop();
      for(int e=head[x]; e; e=nxt[e]){
        int y = to[e];
        if(flow[e] > 0 && dist[y] > dist[x] + cost[e]){
          dist[y] = dist[x] + cost[e];
          pedge[y] = e;
          if(!inq[y]){
            inq[y] = 1;
            q.push(y);
          }
        }
      }
    }
    return dist[t] < INF;
  }

  int dfs(int x, int xf){
    if(!pedge[x]){
      // cout << x;
      return xf;
    }
    int ans = dfs(from[pedge[x]], min(xf, flow[pedge[x]]));
    flow[pedge[x]] -= ans;
    flow[pedge[x]^1] += ans;
    // cout << "->" << x;
    return ans;
  }

  pii ek(){
    int tmp, ans = 0, cst = 0;
    while(spfa()){
      tmp = dfs(t, INF);
      ans += tmp;
      cst += tmp * dist[t];
      // for(int i=1; i<=n; i++) cout << dist[i] << " ";
      // cout << endl;
      // cout << cst<< " ";
    }
    return make_pair(ans, cst);
  }

};

Network nt;
int n, m, s, t, w, c;

int main(){

  scanf("%d%d%d%d", &n, &m, &s, &t);
  nt.init(n, s, t);
  for(int i=0; i<m; i++){
    scanf("%d%d%d%d", &s, &t, &w, &c);
    nt.add_edge(s, t, w, c);
  }
  pii ans = nt.ek();
  printf("%d %d", ans.first, ans.second);

  return 0;
}


```

# 数论

## 康托展开（排列编码/解码）

```cpp

#include<cstdio>
#include<iostream>
#define MAXN 105
using namespace std;

const int fac[10] = {1, 1, 2, 6, 24, 120, 720, 5040, 40320, 362880};

int main(){
  int n, a[MAXN];
  cin >> n;
  for(int i=0; i<n; i++){
    cin >> a[i];
  }
  int lg, ans = 0;
  for(int i=0; i<n; i++){
    lg = 0;
    for(int j=i+1; j<n; j++){
      if(a[i] > a[j]){
        lg++;
      }
    }
    ans += lg*fac[n-i-1];
  }
  cout << ans;

  return 0;
}


```

## 线性筛素数（欧拉筛）

```cpp

#include<cstdio>
#include<iostream>
#include<cmath>

using namespace std;

const int MAXN = 1e7+7;

int n, q, qp, m;
int minp[MAXN] = {0};
int p[MAXN];

int main(){

  scanf("%d", &n);
  for(int i=2; i<=n; i++){
    if(minp[i] == 0){
      minp[i] = i;
      p[m++] = i;
    }
    for(int j=0; j<m; j++){
      if(p[j] > minp[i] || i*p[j] > n) break;
      minp[i*p[j]] = p[j];
    }
  }
  scanf("%d", &q);
  for(int i=0; i<q; i++){
    scanf("%d", &qp);
    printf(minp[qp] == qp ? "Yes\n" : "No\n");
  }

  return 0;
}


```


## 扩展欧几里得（ExGCD）

```cpp

#include<cstdio>
#include<iostream>
#include<cmath>

using namespace std;

typedef long long ll;

const ll MAXINT = 2100000000;

ll an, bn;
ll ggcd, xa, ya;

ll exgcd(ll a, ll b, ll &x, ll &y){
  if(b == 0){
    x = 1, y = 0;
    return a;
  }
  ll ret = exgcd(b, a%b, x, y);
  ll z = x;
  x = y, y = z - y * (a/b);
  return ret;
}

int main(){

  scanf("%lld %lld", &an, &bn);
  ggcd = exgcd(an, bn, xa, ya);
  printf("%lld", (xa + (MAXINT*bn))%bn);
  // printf("%lld %lld %lld", xa, ya, ggcd);

  return 0;
}


```

## 中国剩余定理（Chinese Reminder Theorem）

```cpp

#include<cstdio>
#include<iostream>
#include<cstring>

using namespace std;

const int MAXN = 10;

int n, gm = 1, tm;
int a[MAXN], mod[MAXN];

int exgcd(int a,int b, int &x, int &y){
  if(b == 0){
    x = 1, y = 0;
    return a;
  }
  int ret;
  ret = exgcd(b, a%b, x, y);
  int t = x;
  x = y, y = t - (a/b) * y;
  return ret;
}

int main(){

  int gmod, gdec;
  n = 3;
  mod[1] = 23;
  mod[2] = 28;
  mod[3] = 33;
  gmod = mod[1] * mod[2] * mod[3];
  int gset;
  scanf("%d", &gset);
  while(gset--){

    int tset = 0;
    while(true){
      gm = 1;
      scanf("%d%d%d%d", &a[1], &a[2], &a[3], &gdec);
      if(a[1] == -1 && a[2] == -1 && a[3] == -1 && gdec == -1) break;
      int ans = 0;
      for(int i=1; i<=n; i++){
        gm *= mod[i];
      }
      int x, y;
      for(int i=1; i<=n; i++){
        tm = gm / mod[i];
        exgcd(tm, mod[i], x, y);
        ans += a[i]*x*tm;
      }
      ans = (((ans-gdec) % gmod) + gmod) % gmod;
      printf("Case %d: the next triple peak occurs in %d days.\n", ++tset, ans ? ans : gmod);
    }
    if(gset > 0) printf("\n");
  }

  return 0;
}


```





# 动态规划（Dynamic Programming）


## 01背包问题

```cpp

#include<cstdio>
#include<iostream>
#include<cstring>
using namespace std;

int n, maxw;
int w[105], k[105];
int dp[105][5005];

int main(){

    memset(dp, 0, sizeof(dp));
    cin >> maxw >> n;
    for(int i=0; i<n; i++){
        cin >> w[i] >> k[i];
    }

    for(int i=0; i<n; i++){
        for(int j=0; j<=maxw; j++){
            if(j-w[i] >= 0){
                dp[i+1][j] = dp[i][j] > dp[i+1][j-w[i]]+k[i] ? dp[i][j] : dp[i+1][j-w[i]]+k[i];
            }else{
                dp[i+1][j] = dp[i][j];
            }
        }
    }

    cout << dp[n][maxw];
    return 0;
}

```


## 最长上升子序列（Longest Increasing Sequence）

```cpp

#include<cstdio>
#include<iostream>
using namespace std;

const int MAXN = 1000;

int n;
int a[MAXN];
int sprev[MAXN] = {0};
int dp[MAXN];

void print_seq(int p){
  if(!p) return;
  print_seq(sprev[p]);
  printf("%d ", a[p]);
  return;
}

int main(){
  scanf("%d", &n);
  for(int i=1; i<=n; i++){
    scanf("%d", &a[i]);
  }
  int gans = 0;
  int gend;
  //DP
  for(int i=1; i<=n; i++){
    for(int j=1; j<i; j++){
      if(a[i] > a[j] && dp[j] > dp[i]){
        dp[i] = dp[j];
        sprev[i] = j;
      }
    }
    dp[i] ++;
    if(dp[i] > gans){
      gans = dp[i];
      gend = i;
    }
  }
  printf("%d\n", gans);
  print_seq(gend);
  return 0;
}


```

## 最长公共子序列（Longest Common Sequence）

```cpp

// Longest Common Sequence
#include<cstdio>
#include<iostream>
#include<cstring>
using namespace std;

char s[1005], t[1005];
int dp[1005][1005];
int n, m;

int solve(int a, int b){
    for(int i=0; i<n; i++){
        for(int j=0; j<m; j++){
            if(s[i] == t[j] && ((i-1 >= 0 && j-1 >= 0 && s[i-1] == t[j-1]) || (i+1 < n && j+1 < n && s[i+1] == t[j+1])) ){
                // cout << s[i] << " " << t[j] <<endl;
                dp[i+1][j+1] = dp[i][j]+1;
            }else{
                dp[i+1][j+1] = max(dp[i+1][j], dp[i][j+1]);
            }
        }
    }
}

int main(){
    memset(dp, 0, sizeof(dp));
    cin >> n >> m >> s >> t;

    // for(int i=0; i<=n; i++){
    //     for(int j=0; j<=m; j++){
    //         cout << dp[i][j] << " ";
    //     }
    //     cout << endl;
    // }

    cout << dp[n][m];

    return 0;
}

```