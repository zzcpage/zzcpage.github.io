#### 如何将Map集合中的key转化为升序和降序排序 

>简单的方法是通过TreeSet进行转化
```python

HashMap<Integer,Integer> map = new HashMap<>() ;
TreeSet<Integer> keys = new TreeSet<>(map.keySet()) ;
```