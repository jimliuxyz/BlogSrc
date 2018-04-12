[ref](https://magiclen.org/sorting-algorithm/)

泡沫排序
======
> 倆倆比較 將大的往後推(交換) 每一輪結束最大的一定推到最後一個(排好一個) 所以下一輪就少推一個

優化:

- 若其中一輪完全沒有交換 表示已經完成排序

```java
public static void bubbleSort(final int[] nums) {
    for (int i = nums.length - 1; i > 0; i--) {
        for (int j = 0; j < i; j++) {
            final int next = j + 1;
            if (nums[j] > nums[next]) {
                final int buffer = nums[j];
                nums[j] = nums[next];
                nums[next] = buffer;
            }
        }
    }
}

static void test1() {
    int[] arr = new int[]{3, 17, 63, 27, 9, 15, 2, 6};
    System.out.println(Arrays.toString(arr));
    bubbleSort(arr);
    System.out.println(Arrays.toString(arr));
}
```

交換排序
======
> 從第一個位置開始 與之後的每個位置比較 交換出最小的 一輪比完再進行下個位置

```java
public static void exchangeSort(final int[] nums) {
    for (int i = 0; i < nums.length-1; ++i) {
        for (int j = i + 1; j < nums.length; ++j) {
            if (nums[i] > nums[j]) {
                final int buffer = nums[j];
                nums[j] = nums[i];
                nums[i] = buffer;
            }
        }
    }
}
```

選擇排序 (交換排序法的優化)
======
> 如同交換排序 只是記住最小值的索引位置 於當輪結束時交換

```java
public static void selectionSort(final int[] nums) {
    for (int i = 0; i < nums.length-1; ++i) {
        int k = i;
        for (int j = i + 1; j < nums.length; ++j) {
            if (nums[k] > nums[j]) {
                k = j;
            }
        }
        if (k != i) {
            final int buffer = nums[k];
            nums[k] = nums[i];
            nums[i] = buffer;
        }
    }
}
```

插入排序
======
> 記住第i個位置的值(tmp) (準備把tmp往左插入到正確位置)
> 開始往左比 一路把大於tmp的值往右推 直到找小於tmp的位置 再執行下個循環

```java
public static void insertionSort(final int[] nums) {
    for (int i = 0; i < nums.length; ++i) {
        final int tmp = nums[i];
        int j = i - 1;
        while (j >= 0 && nums[j] > tmp) {
            nums[j + 1] = nums[j--];
        }
        nums[j + 1] = tmp;
    }
}
```


快速排序
======
> 採分治法(Divide and Conquer) 找一個中間數(pivot)將 並使其歸於正確位置(左小又大) 左右邊再各自做一次同樣的循環

```java

public static void quickSortRecursive(final int[] nums, final int start, final int end) {
    final int x = nums[start]; // 將start位置的值設為pivot
    int l = start + 1; //所以start+1是最左的元素
    int r = end - 1; //end因為是length 所以要減1
    while (true) {
        //從右邊往左 找到小於pivot的位置
        while (r > start && nums[r] >= x) {
            --r;
        }
        //從左邊往右 找到大於pivot的位置
        while (l <= r && nums[l] <= x) {
            ++l;
        }
        //若l<r表示尚未巡完整個array 就已需要交換
        if (l < r) {
            final int buffer = nums[l];
            nums[l] = nums[r];
            nums[r] = buffer;
        } else {
            //lr巡完表示pivot的位置就在其交會點
            if (r > start) {
                final int buffer = nums[r];
                nums[r] = nums[start];
                nums[start] = buffer;
            }
            break;
        }
    }
    final int ls = start, le = r;
    final int rs = r + 1, re = end;
    final int ll = le - ls, rl = re - rs;
    if (ll > 1) {
        quickSortRecursive(nums, ls, le);
    }
    if (rl > 1) {
        quickSortRecursive(nums, rs, re);
    }
}

quickSortRecursive(array, 0, array.length);
```

合併排序
======

```java
```

排序
======

```java
```

排序
======

```java
```

排序
======

```java
```

排序
======

```java
```


