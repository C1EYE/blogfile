- 今天的题需要了解如何在线性时间复杂度内求数组中的和为k的子数组数量
- 先通过这一题了解一下这种方法

![image-20210529113248043](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210529113248.png)

- 比较容易想到固定一个边界，然后对边界内的元素累加，每当和为k时表示有一个子数组和为k
- 不断收缩边界即可遍历所有情况
- 这种做法时间复杂度是O(n^2)

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
    	int count = 0;
		for (int i = nums.length-1; i >= 0; i--) {
			int sum = 0;
			for (int j = i; j >= 0; j--) {
				sum += nums[j];
				if(sum==k){
					count++;
				}
			}
		}
		return count;
		}
}
```

- 另一种做法，也是我们这次要用到的
- 对于一个子数组[j...i]（从下标j到i，左闭右开,j<=i），如果sum([0...i])-sum([0...j])==k,就说明有一个子数组和为k
- 因此可以用一个变量pre保存前n项和，用一个hashmap存储每个pre出现的次数
- 对于每个pre，只要能在hashmap中找到key==(pre-k)，就说明有对应个数的子数组和为k
- 认为前0项和为0出现1次

```java
public int subArrayCount(int[] nums, int k) {
            Map<Integer, Integer> map = new HashMap<>();
            int count = 0, pre = 0;
            map.put(0, 1);
            for (int i = 0; i < nums.length; i++) {
                pre += nums[i];
                if (map.containsKey(pre - k)) {
                    count += map.get(pre - k);
                }
                map.put(pre, map.getOrDefault(pre, 0) + 1);
            }
            return count;
        }
```

------

>  好了现在让我们回到今天的题目

![image-20210529112214070](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210529112214.png)

- 可以转换为求每一行数组的子数组和为k的个数，如果把第一二行的每列元素和视为数组中的一个元素，就可以求row[0...1]矩阵和为k的个数，以此类推
- 求子数组和为k个数时使用上面介绍的方法

![image-20210529120648414](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210529120648.png)

```java
class Solution {
     public int numSubmatrixSumTarget(int[][] matrix, int target) {
            int row = matrix.length;
            int col = matrix[0].length;
            int count = 0;
            for (int i = 0; i < row; i++) {
                int[] sum = new int[col];
                for (int j = i; j < row; j++) {
                    for (int c = 0; c < col; c++) {
                        sum[c] += matrix[j][c];
                    }
                    count += subArrayCount(sum, target);
                }
            }
            return count;
        }

        public int subArrayCount(int[] nums, int k) {
            Map<Integer, Integer> map = new HashMap<>();
            int count = 0, pre = 0;
            map.put(0, 1);
            for (int i = 0; i < nums.length; i++) {
                pre += nums[i];
                if (map.containsKey(pre - k)) {
                    count += map.get(pre - k);
                }
                map.put(pre, map.getOrDefault(pre, 0) + 1);
            }
            return count;
        }
}
```

![image-20210529112403726](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210529112403.png)

- 算是中等题的组合吧，又学到了:)
- 搞清楚下标对应矩阵中的位置很有帮助