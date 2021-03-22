# 你不知道的js（中）

## 1.数组

* 1.使用delete可以删数组元素，但是数组长度不变

  ```js
  const arr = [1,2,3]
  delete arr[1]
  
  arr // [1,empty,3]
  ```

  

