## 推荐题目



这次来推荐一类题目,topK问题,也就是给你一堆元素,让你用一个比较关系留下K个.



| 题目                         |      |      |
| ---------------------------- | ---- | ---- |
| 347. Top K Frequent Elements |      |      |
| 692. Top K Frequent Words    |      |      |


### 主题

topK问题

### 推荐思考

这一类问题,解决思路是类似的,可以使用优先级队列实现.优先级队列的底层是小顶堆.

当然这个小的关系是你自己定义的.你总是可以得到一种比较关系的前K个元素.

## 模版

一套模板就行

讲解链接:<https://imatvoid.github.io/tag/H0ZNsfbzE/>

```java
public List<Integer> topKFrequent(int[] nums, int k) {

        Map<Integer,Integer>  map = new HashMap<>();

        for(int i =0;i<nums.length;i++){
            map.put(nums[i],map.getOrDefault(nums[i],0)+1);
        }

        PriorityQueue<Map.Entry<Integer,Integer>> priorityQueue = new PriorityQueue<>((a,b) -> b.getValue()-a.getValue());
        for (Map.Entry<Integer,Integer> e: map.entrySet()) {
            priorityQueue.add(e);
        }

        List<Integer> arrayList = new ArrayList<>();
        while (arrayList.size()<k){
            Map.Entry<Integer,Integer> e = priorityQueue.poll();
            arrayList.add(e.getKey());
        }
        return  arrayList;

    }
```



```java
 /**
     * 优先级队列
     *
     * @param words
     * @param k
     * @return
     */
    public List<String> topKFrequent(String[] words, int k) {
        LinkedList<String> result = new LinkedList<>();

        HashMap<String, Integer> map = new HashMap<>();
        for (String s : words) {
            map.put(s, map.getOrDefault(s, 0) + 1);
        }

        PriorityQueue<Map.Entry<String, Integer>> pq = new PriorityQueue<>(

                (a, b) -> {
                    return a.getValue() == b.getValue() ? a.getKey().compareTo(b.getKey()) : b.getValue() - a.getValue();
                }
        );
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            pq.offer(entry);
        }
        while (result.size() < k) {
            result.addLast(pq.poll().getKey());
        }


        return result;

    }
```

