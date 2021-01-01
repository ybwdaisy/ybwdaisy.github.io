### 一些简单算法题

#### 1. 二分查找
```
function binarySearch(arr, x, leftIndex, rightIndex) {

    if(leftIndex > rightIndex) {
        return;
    }
    var len = arr.length,
        midIndex = Math.floor((leftIndex + rightIndex) / 2),
        midVal = arr[midIndex];

    if(midVal < x) {
        return binarySearch(arr, x, midIndex + 1, rightIndex);
    } else if(midVal > x) {
        return binarySearch(arr, x, leftIndex, midIndex - 1);
    } else {
        return midIndex;
    }
}
```

#### 2. 冒泡排序
```
function bubbleSort(arr) {
    if(arr && isArray(arr)) {
        var i, len = arr.length;
        for(i = 0; i < len - 1; i++) {
            for(j = i + 1; j < len; j++) {
                if(arr[i] > arr[j]) {
                    var item = arr[i];
                    arr[i] = arr[j];
                    arr[j] = item;
                }
            }
        }
        return arr;
    }
}
```

#### 3. 快速排序
```
function quickSort(arr) {
    if(arr && isArray(arr)) {
        if(arr.length <= 1) {
            return arr;
        }

        var leftArr = [], rightArr = [];

        var firstItem = arr[0], i, len = arr.length, item;
        for(i = 1; i < len; i++) {
            item = arr[i];
            item < firstItem ? leftArr.push(item) : rightArr.push(item);
        }

        return [].concat(quickSort(leftArr), [firstItem], quickSort(rightArr));
    }
}
```

#### 4. 去掉整型数组中重复数值。例如输入 [1,4,6,4,6,11,5]，输出 [1,4,6,11,5]。
```
function uniqueArray(arr) {
    if(!isArray(arr)) {
        return [];
    }
    var hashTable = {},
        res = [],
        i = 0,
        len = arr.length,
        item;

    for(i = 0; i < len; i++) {
        item = arr[i];
        if(!hashTable[item]) {
            hashTable[item] = true;
            res.push(item);
        }
    }
    return res;
}
```

#### 5. 将两个有序数组合并为一个有序数组。例如输入 [1,6,9] 和 [2,6,7,10]，输出 [1,2,6,6,7,9,10]。
```
function mergeSortedArray(a, b) {
    if(Array.isArray(a) && isArray(b)) {

        var aLen = a.length,
            bLen = b.length;

        var res = new Array(aLen + bLen),
            i = 0,
            j = 0,
            k = 0;

        // 巧妙之处
        while(i < aLen && j < bLen) {
            res[k++] = a[i] <= b[j] ? a[i++] : b[j++];
        }

        // 将a或b剩余的元素依次push到新数组
        while(i < aLen) {
            res[k++] = a[i++];
        }

        while(j < bLen) {
            res[k++] = b[j++];
        }

        return res;
    }
    return [];
}
```

#### 6. 判断一个字符串是否是回文。
```
function isPalindrom(str) {
    if(str && typeof str === 'string') {
        return str === str.split('').reverse().join('');
    }
    return false;
}
```

#### 7. 找出字符串中重复次数最多的字符。例如输入 aewjrkwzaadkaaeeaaa，输出 a 。
```
function findMaxDuplicateChar(str) {
    if(str && typeof str === 'string') {
         if(str.length === 1) {
             return str;
         }

         var charObj = {},
            i = 0,
            len = str.length,
            item;

        for(i = 0; i < len; i++) {
            item = str.charAt(i);
            if(!charObj[item]) {
                charObj[item] = 1;
            } else {
                charObj[item] += 1;
            }
        }

        var maxChar = '',
            maxCount = 1;

        for(var k in charObj) {
            if(charObj[k] >= maxCount) {
                maxChar = k;
                maxCount = charObj[k];
            }
        }

        return maxChar;
    }
    return ''
}
```

#### 8. 生成 Fibonacci 数组。
```
function fibonacciArr(n) {

    var fibonacciArr = [],
        i = 0;

    while(i < n) {
        if(i <= 1) {
            fibonacciArr.push(i);
        } else {
            fibonacciArr.push(fibonacciArr[i - 1] + fibonacciArr[i - 2]);
        }
        i++;
    }
    return fibonacciArr;
}
```

#### 9. 给定数组，生成所有长度为 size 的组合。
```
function choose(arr, size, m) {
    var allResult = [];

    var choosing = function(arr, size, result) {
        var arrLen = arr.length;
        if (size > arrLen) {
            return;
        }
        if (size === arrLen) {
            allResult.push([].concat(result, arr));
        } else {
            for (var i = 0; i < arrLen; i++) {
                var newResult = [].concat(result);
                newResult.push(arr[i]);
                if (size === 1) {
                    allResult.push(newResult);
                } else {
                    var newArr = [].concat(arr);
                    newArr.splice(0, i + 1);
                    choosing(newArr, size - 1, newResult);
                }
            }
        }
    };

    choosing(arr, size, []);

    var sumArr = [];

    allResult.map(function(item) {

        var sum = 0;
        item.map(function(item) {
            sum += item;
        });
        sumArr.push(sum)
    });

    var matchIndex = -1;
    sumArr.map(function(item, index) {
        if(item === m) {
            matchIndex = index;
            return;
        }
    });


    return allResult[matchIndex] || [];
}
```

#### 10. 给定数组，生成所有长度为 size 的排列。
```
function queue(arr, size) {
    if (size > arr.length) {
        return;
    }
    var allResult = [];

    var queuing = function(arr, size, result) {
        if (result.length == size) {
            allResult.push(result);
        } else {
            for (var i = 0, len = arr.length; i < len; i++) {
                var newArr = [].concat(arr),
                    curItem = newArr.splice(i, 1);
                queuing(newArr, size, [].concat(result, curItem));
            }
        }
    }

    queuing(arr, size, []);

    return allResult;
}
```



#### 11. 给定不重复元素数组 arr，从中选取 size 个元素使它们的和为 m

```
function getSizeItemOfSumMFromArr(arr, size, m) {

    var allResult = choose(arr, size);

    var sumArr = [];

    allResult.map(function(item) {

        var sum = 0;
        item.map(function(item) {
            sum += item;
        });
        sumArr.push(sum)
    });

    var matchIndex = -1;
    sumArr.map(function(item, index) {
        if(item === m) {
            matchIndex = index;
            return;
        }
    });

    return allResult[matchIndex] || [];
}


/**
 * 给定数组arr，获取固定size的所有组合
 */

function choose(arr, size) {
    var allResult = [];

    var choosing = function(arr, size, result) {
        var arrLen = arr.length;
        if (size > arrLen) {
            return [];
        }
        if (size === arrLen) {
            allResult.push([].concat(result, arr));
        } else {
            for (var i = 0; i < arrLen; i++) {
                var newResult = [].concat(result);
                newResult.push(arr[i]);
                if (size === 1) {
                    allResult.push(newResult);
                } else {
                    var newArr = [].concat(arr);
                    newArr.splice(0, i + 1);
                    choosing(newArr, size - 1, newResult);
                }
            }
        }
    };

    choosing(arr, size, []);

    return allResult;
}


/**
 * 给定数组arr，获取固定size的所有排列
 */

function queue(arr, size) {

    var allResult = [];

    if (size > arr.length) {
        return [];
    }

    var queuing = function(arr, size, result) {
        if (result.length === size) {
            allResult.push(result);
        } else {
            for (var i = 0, len = arr.length; i < len; i++) {
                var newArr = [].concat(arr),
                    curItem = newArr.splice(i, 1);
                queuing(newArr, size, [].concat(result, curItem));
            }
        }
    }

    queuing(arr, size, []);

    return allResult;
}
```

#### 12. 将两个任意长度的字符串数字求和。例如，
```
var a = '2020'
var b = '19700101'
输出 '19702121'
```

```
function addTwoStringNumber(a, b) {

    if(!a || !b) {
        return;
    }

    if(typeof a !== 'string' || typeof b !== 'string') {
        return;
    }

    var strA = a.toString(),
        strB = b.toString();

    var lenA = strA.length,
        lenB = strB.length;

    if(lenA < lenB) {
        strA = new Array(lenB - lenA + 1).join(0) + strA;
    } else {
        strB = new Array(lenA - lenB + 1).join(0) + strB;
    }

    var len = strA.length,
        resArr = new Array(len),
        addOne = false;

    for(var i = 0; i < len; i++) {
        var curNumA = strA[len - i - 1] - '0',
            curNumB = strB[len - i - 1] - '0';

        var sum = curNumA + curNumB;

        if(addOne) {
            sum++;
        }

        if(sum > 9) {
            sum -= 10;
            addOne = true;
        } else {
            addOne = false;
        }

        resArr[i] = sum;
    }

    return resArr.reverse().join('');

}
```