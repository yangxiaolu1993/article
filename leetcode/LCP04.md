解题思路：
一块骨牌需要占用2个格子，某个格子可放置一块骨牌的方式最多有 4 种：例如格子 [2,4] ，最多可与格子 [1,4]、[3,4]、[2,5]、[2，3] 分别组合放置骨牌。所以可以将其改无向图：

例：n = 3, m = 3, broken = []

[0,0] => [0,1]、[1,0]
[0,1] => [0,0]、[0,2]、[1,1]
[0,2] => [0,1]、[1,2]

[1,0] => [0,0]、[2,0]、[1,1]
[1,1] => [0,1]、[1,0]、[1,2]、[2,1]
[1,2] => [0,2]、[1,1]、[2,2]

[2,0] => [2,1]、[1,0]
[2,1] => [1,1]、[2,0]、[2,2]
[2,2] => [2,1]、[1,2]


根据二分图的特性，又可以将其转化为二分图：

![](https://img12.360buyimg.com/imagetools/jfs/t1/194982/4/15231/120991/60ff7b8eEc2773a39/b2939d1b0151cda5.png)  


不难发现 A 集合 n+m 为偶数，B 集合 n+m 为奇数，所以可以根据根据这个特性把 n*m 的棋盘分为 A/B 两个集合。同时我们也就将问题进行了转化，一个骨牌需要占用2个格子，这2个格子分别属于 A、B 集合，则 A、B 集合的最大匹配数，即为最终求解。求解 A/B 集合，需要将破损格子去掉，即不包含在格子中。

```
bipartiteGraph(n, m, broken) {
    let maxMatch = (A)=>{...} // 获取集合的最大匹配，在下面有详细介绍
    // 将破损数组重新排列，转化成 i&j 的形式，方便后面进行筛选
    let tranBroken = new Set();

    broken.forEach((item) => {
        tranBroken.add(`${item[0]}&${item[1]}`);
    });

    // 通过循环将数组拆分成 A，B 两个集合
      let A = new Map(); // i+j == 偶数
      let B = new Map(); // i+j == 奇数
      for (let i = 0; i < n; i++) {
        for (let j = 0; j < m; j++) {
          if(tranBroken.has(`${i}&${j}`)) continue;   // 若当前格子是破损的，则跳过；  

          let  gather = (i + j) % 2 == 0 ?A:B 

          gather.set(`${i}&${j}`,[]) 
        
            // 判断格子四周是否可与其进行组合，放置骨牌，计算时要注意边界值 
          if(!tranBroken.has(`${i-1}&${j}`) && i>0){
            gather.get(`${i}&${j}`).push(`${i-1}&${j}`)
          }

          if(!tranBroken.has(`${i}&${j-1}`) && j>0){
            gather.get(`${i}&${j}`).push(`${i}&${j-1}`)
          }
          if(!tranBroken.has(`${i+1}&${j}`) && i+1<n){
            gather.get(`${i}&${j}`).push(`${i+1}&${j}`)
          }

          if(!tranBroken.has(`${i}&${j+1}`) && j+1<m){
            gather.get(`${i}&${j}`).push(`${i}&${j+1}`)
          }
        }
      }
        // 取 集合A/B最大匹配的最大值
      let size = Math.max(maxMatch(A),maxMatch(B))

      return size
    
}

```

```
// 按照 ’ 匈牙利算法‘ 获取集合的最大匹配
let maxMatch = (A)=>{
      let noUse = new Map() // B 集合中被占用项
      let matchList = new Map() // A/B 集合已经匹配项

        // 递归匹配
      let matchFun = (key,value,path)=>{
        
        let isMatch = false
        
        for(let i=0;i<value.length;i++){
          if(!noUse.get(value[i])){

            // 查询到有未占用项，停止循环，找到匹配项
            matchList.set(key,value[i])
            noUse.set(value[i],key)

            let keys = [...path.keys()]
            for(let j=path.size;j>0;j--){
          
              matchList.set(keys[j-1],path.get(keys[j-1]))
              noUse.set(path.get(keys[j-1]),keys[j-1])
            }

            isMatch = true
            break
          } 
        }
        
        // 未查询到未占用项
        if(!isMatch){
          if(path.get(key)) return false
          path.set(key,value[0])

          let keyn = noUse.get(value[0])
          let valuen = A.get(keyn)

          let index = valuen.indexOf(value[0]); 
          if (index > -1) valuen.splice(index, 1); 
          
          valuen.length>0 && matchFun(keyn,valuen,path)
        }
      }
      
      // 循环遍历 A 集合中的每一项，并记录匹配路径，用于回溯查找
      for(let [key,value] of A){
        let p = new Map()
        value.length>0&&matchFun(key,value,p)
      }
  
      return matchList.size
    }
```