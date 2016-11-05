#Binary Indexed Tree
##动机
为了解决以下问题操作：

 1. 维护一个n位大数组array[n]，查询某一范围数字的和
 2. 频繁更新数组，并继续查询范围和

> 一种可行的方法是维护一个和数组。这种方法操作1的复杂度是O（1），在每次更新之后要重新更新和数组的某一部分，操作2的复杂度是O（n）。这种方法在更新次数不多的情况下很好用！
```java
int[] sum = new int [array.length]; //array是一个很大的数组
for(int i = 1; i < array.length; i++) {
	sum[i] = sum[i - 1] + array[i];
}
```
> 另一种方法是直接用原数组，这种方法操作1的复杂度是O（n），操作2的复杂度是O（1）。

我们的目标：用O(log n)的时间解决上面两种操作。
一种有效的方法是用segment tree，它允许在对数时间内进行上面两个操作。
另一种方法是binary indexed tree，对比线段树他的优点是需要更少的时间并且更易于实现。

##表示
Binary Indexed Tree是用一个数组表示的BITree[]，我们可以用和原数组一样大的数组进行表示，数组中的每一个元素表示原数组中某一范围内的数的和。
##更新
我们用0初始化整个数组，然后对每一个原数组的元素调用一次更新方法。 








##操作
###更新

在初始化BIT时我们调用n次update方法，在以后维护BIT的时候我们调用```update(index, newValue - oldValue)``` 其中index是需要更新的index，oldValue是原来该位置index的值，需要通过查询得到；newValue是该位置新的值，我们用它们之间的差进行更新

**update(index, val):更新BIT对每个index**

	原数组array不变，对于array中的每一个元素我们用update方法更新和数组BITree。
	1)index = index + 1
	2)进行如下操作直到index超过n
		a)BITree[index]+=value
		b)找到父亲的index：index+= index & -index
	这个找爸爸的意思是将每一个数最后为1的那位加1，就找到爸爸了。
![enter image description here](https://lh3.googleusercontent.com/85M8pBP5BmuRWe5rWdyNBF25FGVcjpnJnx0RXYmCF7URVIznVStsChDcqF1qVuyemhvO57c=s0 "BITUpdate12.png")

### 查找
**getSum(index):得到数组从*原数组*0……index的和**

	1)初始化sum为0
	2)当index大于0的时候，做如下两步
		a)将BITree[index]加到sum里
		b)找到儿子的index：index -= index&-index
	3)返回sum
	这里的找儿子的意思是将最低的等于1的一位置为0
一些要点：
 - 0是一个dummy node
 - 如果爸爸是y儿子是x，BITree[x]保存了从`array(y - 1:x - 1]`的和
 ![enter image description here](https://lh3.googleusercontent.com/y5CNZFxmDKE-Xs2JUjB2UE7qrF41TNCZ5E5tpKWJ8OUNuUaHd01unARFdZO9_z-E8hopwGc=s0 "BITSum.png")

###Binary Indexed Tree是如何工作的？
Binary Indexed Tree中的每一个点都储存了2的整数次幂的数字的和。因为每个数字都可以被表示成几个2的整数次幂的和。比如前12个数的和可以用9-12的和和1-8的和进行计算。
另外，初始化整个树用了O(nlogn)的时间，因为调用了n次`update()`方法。

##实现

cpp：
```cpp

// C++ code to demonstrate operations of Binary Index Tree
#include <iostream>
using namespace std;
 
/*            n  --> No. of elements present in input array.   
    BITree[0..n] --> Array that represents Binary Indexed Tree.
    arr[0..n-1]  --> Input array for whic prefix sum is evaluated. */
 
// Returns sum of arr[0..index]. This function assumes
// that the array is preprocessed and partial sums of
// array elements are stored in BITree[].
int getSum(int BITree[], int index)
{
    int sum = 0; // Iniialize result
 
    // index in BITree[] is 1 more than the index in arr[]
    index = index + 1;
 
    // Traverse ancestors of BITree[index]
    while (index>0)
    {
        // Add current element of BITree to sum
        sum += BITree[index];
 
        // Move index to parent node in getSum View
        index -= index & (-index);
    }
    return sum;
}
 
// Updates a node in Binary Index Tree (BITree) at given index
// in BITree.  The given value 'val' is added to BITree[i] and 
// all of its ancestors in tree.
void updateBIT(int BITree[], int n, int index, int val)
{
    // index in BITree[] is 1 more than the index in arr[]
    index = index + 1;
 
    // Traverse all ancestors and add 'val'
    while (index <= n)
    {
       // Add 'val' to current node of BI Tree
       BITree[index] += val;
 
       // Update index to that of parent in update View
       index += index & (-index);
    }
}
 
// Constructs and returns a Binary Indexed Tree for given
// array of size n.
int *constructBITree(int arr[], int n)
{
    // Create and initialize BITree[] as 0
    int *BITree = new int[n+1];
    for (int i=1; i<=n; i++)
        BITree[i] = 0;
 
    // Store the actual values in BITree[] using update()
    for (int i=0; i<n; i++)
        updateBIT(BITree, n, i, arr[i]);
 
    // Uncomment below lines to see contents of BITree[]
    //for (int i=1; i<=n; i++)
    //      cout << BITree[i] << " ";
 
    return BITree;
}
 
 
// Driver program to test above functions
int main()
{
    int freq[] = {2, 1, 1, 3, 2, 3, 4, 5, 6, 7, 8, 9};
    int n = sizeof(freq)/sizeof(freq[0]);
    int *BITree = constructBITree(freq, n);
    cout << "Sum of elements in arr[0..5] is "
         << getSum(BITree, 5);
 
    // Let use test the update operation
    freq[3] += 6;
    updateBIT(BITree, n, 3, 6); //Update BIT for above change in arr[]
 
    cout << "\nSum of elements in arr[0..5] after update is "
         << getSum(BITree, 5);
 
    return 0;
}
```
Python:
```python
# Python implementation of Binary Indexed Tree
 
# Returns sum of arr[0..index]. This function assumes
# that the array is preprocessed and partial sums of
# array elements are stored in BITree[].
def getsum(BITTree,i):
    s = 0  #initialize result
 
    # index in BITree[] is 1 more than the index in arr[]
    i = i+1
 
    # Traverse ancestors of BITree[index]
    while i > 0:
 
        # Add current element of BITree to sum
        s += BITTree[i]
 
        # Move index to parent node in getSum View
        i -= i & (-i)
    return s
 
# Updates a node in Binary Index Tree (BITree) at given index
# in BITree.  The given value 'val' is added to BITree[i] and
# all of its ancestors in tree.
def updatebit(BITTree , n , i ,v):
 
    # index in BITree[] is 1 more than the index in arr[]
    i += 1
 
    # Traverse all ancestors and add 'val'
    while i <= n:
 
        # Add 'val' to current node of BI Tree
        BITTree[i] += v
 
        # Update index to that of parent in update View
        i += i & (-i)
 
 
# Constructs and returns a Binary Indexed Tree for given
# array of size n.
def construct(arr, n):
 
    # Create and initialize BITree[] as 0
    BITTree = [0]*(n+1)
 
    # Store the actual values in BITree[] using update()
    for i in range(n):
        updatebit(BITTree, n, i, arr[i])
 
    # Uncomment below lines to see contents of BITree[]
    #for i in range(1,n+1):
    #      print BITTree[i],
    return BITTree
 
 
# Driver code to test above methods
freq = [2, 1, 1, 3, 2, 3, 4, 5, 6, 7, 8, 9]
BITTree = construct(freq,len(freq))
print("Sum of elements in arr[0..5] is " + str(getsum(BITTree,5)))
freq[3] += 6
updatebit(BITTree, len(freq), 3, 6)
print("Sum of elements in arr[0..5] is " + str(getsum(BITTree,5)))
 
# This code is contributed by Raju Varshney

```
java(untested):
```java
public class BITreeClass {
	public int[] BITree;
	BITree(int[] array) {
		this.BITree = new int [array.length + 1];
		for(int i = 0; i < array.length; i++) {
			update(i, array[i]);
		}
	}
	public int getSum(int index) {
		int sum = 0;
		index = index + 1;
		while(index > 0) {
			sum += BITree[index];
			index -= index & -index;
		}
		return sum;
	}
	public void update(int index, int val) {
		index = index + 1;
		while(index <= BITree.length - 1) {
			BITree[index] += val;
			index += index & -index;
		}
	}
	public static void main(String[] args) {
		int[] array = {2,1,1,3,2,3,4,5,6,7,8,9};
		BITreeClass newBITree = new BITreeClass(array);
		System.out.println(newBITree.getSum(5));
		newBITree.update(4,3);
		System.out.println(newBITree.getSum(5));
	}	
}
```
##2D Binary Indexed Tree
###动机
为了解决2D数组的查询和和更新的操作
###思路
和一维一样。对于更新，先对每一行进行更新，在对每一列进行更新。对于getSum，`getSum(int i, int j)` 表示对$x <= i ,  y <= j$的数字求和。所以如果想要查询任意一个矩形内（用$(x_{1},y_{1}),(x_{2},y_{2})$表示具喜感的左上角和右下角）的数字的和，可以用`getSum(x2,y2) + getSum(x1,y1) - getSum(x1,y2) - getSum(x2,y1)`计算。
###实现
```java
/**
 * Created by zheyu on 16/11/5.
 */
public class TwoDBITreeClass {
    public int[][] twoDBItree;
    TwoDBITreeClass(int[][] array){
        this.twoDBItree = new int [array.length][array[0].length + 1];
        for(int i = 0; i < array.length; i++) {
            for(int j = 0; j < array[0].length; j++) {
                update(i, j, array[i][j]);
            }
        }
    }
    public void update(int x, int y, int value) {
        y = y + 1;
        while(y <= twoDBItree[0].length - 1) {
            twoDBItree[x][y] += value;
            y += y & -y;
        }
    }
    public int getSum(int x, int y) {
        int sum = 0;
        y = y + 1;
        while(y > 0) {
            sum += twoDBItree[x][y];
            y -= y & -y;
        }
        return sum;
    }
    public int getQuerySum(Query q){
        int sum = 0;
        for(int i = q.x2; i >= q.x1; i--) {
            sum += getSum(i, q.y2) - getSum(i, q.y1 - 1);
        }
        return sum;
    }
    public static void main(String[] args) {
        int[][] array = {{1, 2, 3, 4},
                {5, 3, 8, 1},
                {4, 6, 7, 5},
                {2, 4, 8, 9}};
        TwoDBITreeClass new2DBITree = new TwoDBITreeClass(array);
        System.out.println(new2DBITree.getQuerySum(new Query(1,1,3,2)));
        System.out.println(new2DBITree.getQuerySum(new Query(2,3,3,3)));
        System.out.println(new2DBITree.getQuerySum(new Query(1,1,1,1)));
    }

}
class Query {
    int x1, y1;
    int x2, y2;
    Query(int x1, int y1, int x2, int y2) {
        this.x1 = x1;
        this.x2 = x2;
        this.y1 = y1;
        this.y2 = y2;
    }
}
```
###时间复杂度
update: O(log NM)
getSum: O(log NM)
初始化BIT：O(NMlogNM)


