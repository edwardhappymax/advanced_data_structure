<h1 id="binary-indexed-tree">Binary Indexed Tree</h1>
<h2 id="动机">动机</h2>
<p>为了解决以下问题操作：</p>
<ol>
<li>维护一个n位大数组array[n]，查询某一范围数字的和</li>
<li>频繁更新数组，并继续查询范围和</li>
</ol>
<blockquote>
<p>一种可行的方法是维护一个和数组。这种方法操作1的复杂度是O（1），在每次更新之后要重新更新和数组的某一部分，操作2的复杂度是O（n）。这种方法在更新次数不多的情况下很好用！</p>
</blockquote>
<pre class=" language-java"><code class="prism  language-java"><span class="token keyword">int</span><span class="token punctuation">[</span><span class="token punctuation">]</span> sum <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">int</span> <span class="token punctuation">[</span>array<span class="token punctuation">.</span>length<span class="token punctuation">]</span><span class="token punctuation">;</span> <span class="token comment" spellcheck="true">//array是一个很大的数组</span>
<span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> <span class="token number">1</span><span class="token punctuation">;</span> i <span class="token operator">&lt;</span> array<span class="token punctuation">.</span>length<span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	sum<span class="token punctuation">[</span>i<span class="token punctuation">]</span> <span class="token operator">=</span> sum<span class="token punctuation">[</span>i <span class="token operator">-</span> <span class="token number">1</span><span class="token punctuation">]</span> <span class="token operator">+</span> array<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<blockquote>
<p>另一种方法是直接用原数组，这种方法操作1的复杂度是O（n），操作2的复杂度是O（1）。</p>
</blockquote>
<p>我们的目标：用O(log n)的时间解决上面两种操作。<br>
一种有效的方法是用segment tree，它允许在对数时间内进行上面两个操作。<br>
另一种方法是binary indexed tree，对比线段树他的优点是需要更少的时间并且更易于实现。</p>
<h2 id="表示">表示</h2>
<p>Binary Indexed Tree是用一个数组表示的BITree[]，我们可以用和原数组一样大的数组进行表示，数组中的每一个元素表示原数组中某一范围内的数的和。</p>
<h2 id="更新">更新</h2>
<p>我们用0初始化整个数组，然后对每一个原数组的元素调用一次更新方法。</p>
<h2 id="操作">操作</h2>
<h3 id="更新-1">更新</h3>
<p>在初始化BIT时我们调用n次update方法，在以后维护BIT的时候我们调用<code>update(index, newValue - oldValue)</code> 其中index是需要更新的index，oldValue是原来该位置index的值，需要通过查询得到；newValue是该位置新的值，我们用它们之间的差进行更新</p>
<p><strong>update(index, val):更新BIT对每个index</strong></p>
<pre><code>原数组array不变，对于array中的每一个元素我们用update方法更新和数组BITree。
1)index = index + 1
2)进行如下操作直到index超过n
	a)BITree[index]+=value
	b)找到父亲的index：index+= index &amp; -index
这个找爸爸的意思是将每一个数最后为1的那位加1，就找到爸爸了。
</code></pre>
<p><img src="https://lh3.googleusercontent.com/85M8pBP5BmuRWe5rWdyNBF25FGVcjpnJnx0RXYmCF7URVIznVStsChDcqF1qVuyemhvO57c=s0" alt="enter image description here" title="BITUpdate12.png"></p>
<h3 id="查找">查找</h3>
<p><em><em>getSum(index):得到数组从</em>原数组</em>0……index的和**</p>
<pre><code>1)初始化sum为0
2)当index大于0的时候，做如下两步
	a)将BITree[index]加到sum里
	b)找到儿子的index：index -= index&amp;-index
3)返回sum
这里的找儿子的意思是将最低的等于1的一位置为0
</code></pre>
<p>一些要点：</p>
<ul>
<li>0是一个dummy node</li>
<li>如果爸爸是y儿子是x，BITree[x]保存了从<code>array(y - 1:x - 1]</code>的和<br>
<img src="https://lh3.googleusercontent.com/y5CNZFxmDKE-Xs2JUjB2UE7qrF41TNCZ5E5tpKWJ8OUNuUaHd01unARFdZO9_z-E8hopwGc=s0" alt="enter image description here" title="BITSum.png"></li>
</ul>
<p>###Binary Indexed Tree是如何工作的？<br>
Binary Indexed Tree中的每一个点都储存了2的整数次幂的数字的和。因为每个数字都可以被表示成几个2的整数次幂的和。比如前12个数的和可以用9-12的和和1-8的和进行计算。<br>
另外，初始化整个树用了O(nlogn)的时间，因为调用了n次<code>update()</code>方法。</p>
<h2 id="实现">实现</h2>
<p>cpp：</p>
<pre class=" language-cpp"><code class="prism  language-cpp">
<span class="token comment" spellcheck="true">// C++ code to demonstrate operations of Binary Index Tree</span>
<span class="token macro property">#<span class="token directive keyword">include</span> <span class="token string">&lt;iostream&gt;</span></span>
<span class="token keyword">using</span> <span class="token keyword">namespace</span> std<span class="token punctuation">;</span>
 
<span class="token comment" spellcheck="true">/*            n  --&gt; No. of elements present in input array.   
    BITree[0..n] --&gt; Array that represents Binary Indexed Tree.
    arr[0..n-1]  --&gt; Input array for whic prefix sum is evaluated. */</span>
 
<span class="token comment" spellcheck="true">// Returns sum of arr[0..index]. This function assumes</span>
<span class="token comment" spellcheck="true">// that the array is preprocessed and partial sums of</span>
<span class="token comment" spellcheck="true">// array elements are stored in BITree[].</span>
<span class="token keyword">int</span> <span class="token function">getSum</span><span class="token punctuation">(</span><span class="token keyword">int</span> BITree<span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">,</span> <span class="token keyword">int</span> index<span class="token punctuation">)</span>
<span class="token punctuation">{</span>
    <span class="token keyword">int</span> sum <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token comment" spellcheck="true">// Iniialize result</span>
 
    <span class="token comment" spellcheck="true">// index in BITree[] is 1 more than the index in arr[]</span>
    index <span class="token operator">=</span> index <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">;</span>
 
    <span class="token comment" spellcheck="true">// Traverse ancestors of BITree[index]</span>
    <span class="token keyword">while</span> <span class="token punctuation">(</span>index<span class="token operator">&gt;</span><span class="token number">0</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment" spellcheck="true">// Add current element of BITree to sum</span>
        sum <span class="token operator">+</span><span class="token operator">=</span> BITree<span class="token punctuation">[</span>index<span class="token punctuation">]</span><span class="token punctuation">;</span>
 
        <span class="token comment" spellcheck="true">// Move index to parent node in getSum View</span>
        index <span class="token operator">-</span><span class="token operator">=</span> index <span class="token operator">&amp;</span> <span class="token punctuation">(</span><span class="token operator">-</span>index<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">return</span> sum<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
 
<span class="token comment" spellcheck="true">// Updates a node in Binary Index Tree (BITree) at given index</span>
<span class="token comment" spellcheck="true">// in BITree.  The given value 'val' is added to BITree[i] and </span>
<span class="token comment" spellcheck="true">// all of its ancestors in tree.</span>
<span class="token keyword">void</span> <span class="token function">updateBIT</span><span class="token punctuation">(</span><span class="token keyword">int</span> BITree<span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">,</span> <span class="token keyword">int</span> n<span class="token punctuation">,</span> <span class="token keyword">int</span> index<span class="token punctuation">,</span> <span class="token keyword">int</span> val<span class="token punctuation">)</span>
<span class="token punctuation">{</span>
    <span class="token comment" spellcheck="true">// index in BITree[] is 1 more than the index in arr[]</span>
    index <span class="token operator">=</span> index <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">;</span>
 
    <span class="token comment" spellcheck="true">// Traverse all ancestors and add 'val'</span>
    <span class="token keyword">while</span> <span class="token punctuation">(</span>index <span class="token operator">&lt;=</span> n<span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
       <span class="token comment" spellcheck="true">// Add 'val' to current node of BI Tree</span>
       BITree<span class="token punctuation">[</span>index<span class="token punctuation">]</span> <span class="token operator">+</span><span class="token operator">=</span> val<span class="token punctuation">;</span>
 
       <span class="token comment" spellcheck="true">// Update index to that of parent in update View</span>
       index <span class="token operator">+</span><span class="token operator">=</span> index <span class="token operator">&amp;</span> <span class="token punctuation">(</span><span class="token operator">-</span>index<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
 
<span class="token comment" spellcheck="true">// Constructs and returns a Binary Indexed Tree for given</span>
<span class="token comment" spellcheck="true">// array of size n.</span>
<span class="token keyword">int</span> <span class="token operator">*</span><span class="token function">constructBITree</span><span class="token punctuation">(</span><span class="token keyword">int</span> arr<span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">,</span> <span class="token keyword">int</span> n<span class="token punctuation">)</span>
<span class="token punctuation">{</span>
    <span class="token comment" spellcheck="true">// Create and initialize BITree[] as 0</span>
    <span class="token keyword">int</span> <span class="token operator">*</span>BITree <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token keyword">int</span><span class="token punctuation">[</span>n<span class="token operator">+</span><span class="token number">1</span><span class="token punctuation">]</span><span class="token punctuation">;</span>
    <span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token keyword">int</span> i<span class="token operator">=</span><span class="token number">1</span><span class="token punctuation">;</span> i<span class="token operator">&lt;=</span>n<span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span>
        BITree<span class="token punctuation">[</span>i<span class="token punctuation">]</span> <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span>
 
    <span class="token comment" spellcheck="true">// Store the actual values in BITree[] using update()</span>
    <span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token keyword">int</span> i<span class="token operator">=</span><span class="token number">0</span><span class="token punctuation">;</span> i<span class="token operator">&lt;</span>n<span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span>
        <span class="token function">updateBIT</span><span class="token punctuation">(</span>BITree<span class="token punctuation">,</span> n<span class="token punctuation">,</span> i<span class="token punctuation">,</span> arr<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
 
    <span class="token comment" spellcheck="true">// Uncomment below lines to see contents of BITree[]</span>
    <span class="token comment" spellcheck="true">//for (int i=1; i&lt;=n; i++)</span>
    <span class="token comment" spellcheck="true">//      cout &lt;&lt; BITree[i] &lt;&lt; " ";</span>
 
    <span class="token keyword">return</span> BITree<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
 
 
<span class="token comment" spellcheck="true">// Driver program to test above functions</span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{</span>
    <span class="token keyword">int</span> freq<span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">,</span> <span class="token number">4</span><span class="token punctuation">,</span> <span class="token number">5</span><span class="token punctuation">,</span> <span class="token number">6</span><span class="token punctuation">,</span> <span class="token number">7</span><span class="token punctuation">,</span> <span class="token number">8</span><span class="token punctuation">,</span> <span class="token number">9</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
    <span class="token keyword">int</span> n <span class="token operator">=</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span>freq<span class="token punctuation">)</span><span class="token operator">/</span><span class="token keyword">sizeof</span><span class="token punctuation">(</span>freq<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">int</span> <span class="token operator">*</span>BITree <span class="token operator">=</span> <span class="token function">constructBITree</span><span class="token punctuation">(</span>freq<span class="token punctuation">,</span> n<span class="token punctuation">)</span><span class="token punctuation">;</span>
    cout <span class="token operator">&lt;&lt;</span> <span class="token string">"Sum of elements in arr[0..5] is "</span>
         <span class="token operator">&lt;&lt;</span> <span class="token function">getSum</span><span class="token punctuation">(</span>BITree<span class="token punctuation">,</span> <span class="token number">5</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
 
    <span class="token comment" spellcheck="true">// Let use test the update operation</span>
    freq<span class="token punctuation">[</span><span class="token number">3</span><span class="token punctuation">]</span> <span class="token operator">+</span><span class="token operator">=</span> <span class="token number">6</span><span class="token punctuation">;</span>
    <span class="token function">updateBIT</span><span class="token punctuation">(</span>BITree<span class="token punctuation">,</span> n<span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">,</span> <span class="token number">6</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment" spellcheck="true">//Update BIT for above change in arr[]</span>
 
    cout <span class="token operator">&lt;&lt;</span> <span class="token string">"\nSum of elements in arr[0..5] after update is "</span>
         <span class="token operator">&lt;&lt;</span> <span class="token function">getSum</span><span class="token punctuation">(</span>BITree<span class="token punctuation">,</span> <span class="token number">5</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
 
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Python:</p>
<pre class=" language-python"><code class="prism  language-python"><span class="token comment" spellcheck="true"># Python implementation of Binary Indexed Tree</span>
 
<span class="token comment" spellcheck="true"># Returns sum of arr[0..index]. This function assumes</span>
<span class="token comment" spellcheck="true"># that the array is preprocessed and partial sums of</span>
<span class="token comment" spellcheck="true"># array elements are stored in BITree[].</span>
<span class="token keyword">def</span> <span class="token function">getsum</span><span class="token punctuation">(</span>BITTree<span class="token punctuation">,</span>i<span class="token punctuation">)</span><span class="token punctuation">:</span>
    s <span class="token operator">=</span> <span class="token number">0</span>  <span class="token comment" spellcheck="true">#initialize result</span>
 
    <span class="token comment" spellcheck="true"># index in BITree[] is 1 more than the index in arr[]</span>
    i <span class="token operator">=</span> i<span class="token operator">+</span><span class="token number">1</span>
 
    <span class="token comment" spellcheck="true"># Traverse ancestors of BITree[index]</span>
    <span class="token keyword">while</span> i <span class="token operator">&gt;</span> <span class="token number">0</span><span class="token punctuation">:</span>
 
        <span class="token comment" spellcheck="true"># Add current element of BITree to sum</span>
        s <span class="token operator">+=</span> BITTree<span class="token punctuation">[</span>i<span class="token punctuation">]</span>
 
        <span class="token comment" spellcheck="true"># Move index to parent node in getSum View</span>
        i <span class="token operator">-=</span> i <span class="token operator">&amp;</span> <span class="token punctuation">(</span><span class="token operator">-</span>i<span class="token punctuation">)</span>
    <span class="token keyword">return</span> s
 
<span class="token comment" spellcheck="true"># Updates a node in Binary Index Tree (BITree) at given index</span>
<span class="token comment" spellcheck="true"># in BITree.  The given value 'val' is added to BITree[i] and</span>
<span class="token comment" spellcheck="true"># all of its ancestors in tree.</span>
<span class="token keyword">def</span> <span class="token function">updatebit</span><span class="token punctuation">(</span>BITTree <span class="token punctuation">,</span> n <span class="token punctuation">,</span> i <span class="token punctuation">,</span>v<span class="token punctuation">)</span><span class="token punctuation">:</span>
 
    <span class="token comment" spellcheck="true"># index in BITree[] is 1 more than the index in arr[]</span>
    i <span class="token operator">+=</span> <span class="token number">1</span>
 
    <span class="token comment" spellcheck="true"># Traverse all ancestors and add 'val'</span>
    <span class="token keyword">while</span> i <span class="token operator">&lt;=</span> n<span class="token punctuation">:</span>
 
        <span class="token comment" spellcheck="true"># Add 'val' to current node of BI Tree</span>
        BITTree<span class="token punctuation">[</span>i<span class="token punctuation">]</span> <span class="token operator">+=</span> v
 
        <span class="token comment" spellcheck="true"># Update index to that of parent in update View</span>
        i <span class="token operator">+=</span> i <span class="token operator">&amp;</span> <span class="token punctuation">(</span><span class="token operator">-</span>i<span class="token punctuation">)</span>
 
 
<span class="token comment" spellcheck="true"># Constructs and returns a Binary Indexed Tree for given</span>
<span class="token comment" spellcheck="true"># array of size n.</span>
<span class="token keyword">def</span> <span class="token function">construct</span><span class="token punctuation">(</span>arr<span class="token punctuation">,</span> n<span class="token punctuation">)</span><span class="token punctuation">:</span>
 
    <span class="token comment" spellcheck="true"># Create and initialize BITree[] as 0</span>
    BITTree <span class="token operator">=</span> <span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token operator">*</span><span class="token punctuation">(</span>n<span class="token operator">+</span><span class="token number">1</span><span class="token punctuation">)</span>
 
    <span class="token comment" spellcheck="true"># Store the actual values in BITree[] using update()</span>
    <span class="token keyword">for</span> i <span class="token keyword">in</span> range<span class="token punctuation">(</span>n<span class="token punctuation">)</span><span class="token punctuation">:</span>
        updatebit<span class="token punctuation">(</span>BITTree<span class="token punctuation">,</span> n<span class="token punctuation">,</span> i<span class="token punctuation">,</span> arr<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">)</span>
 
    <span class="token comment" spellcheck="true"># Uncomment below lines to see contents of BITree[]</span>
    <span class="token comment" spellcheck="true">#for i in range(1,n+1):</span>
    <span class="token comment" spellcheck="true">#      print BITTree[i],</span>
    <span class="token keyword">return</span> BITTree
 
 
<span class="token comment" spellcheck="true"># Driver code to test above methods</span>
freq <span class="token operator">=</span> <span class="token punctuation">[</span><span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">,</span> <span class="token number">4</span><span class="token punctuation">,</span> <span class="token number">5</span><span class="token punctuation">,</span> <span class="token number">6</span><span class="token punctuation">,</span> <span class="token number">7</span><span class="token punctuation">,</span> <span class="token number">8</span><span class="token punctuation">,</span> <span class="token number">9</span><span class="token punctuation">]</span>
BITTree <span class="token operator">=</span> construct<span class="token punctuation">(</span>freq<span class="token punctuation">,</span>len<span class="token punctuation">(</span>freq<span class="token punctuation">)</span><span class="token punctuation">)</span>
<span class="token keyword">print</span><span class="token punctuation">(</span><span class="token string">"Sum of elements in arr[0..5] is "</span> <span class="token operator">+</span> str<span class="token punctuation">(</span>getsum<span class="token punctuation">(</span>BITTree<span class="token punctuation">,</span><span class="token number">5</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
freq<span class="token punctuation">[</span><span class="token number">3</span><span class="token punctuation">]</span> <span class="token operator">+=</span> <span class="token number">6</span>
updatebit<span class="token punctuation">(</span>BITTree<span class="token punctuation">,</span> len<span class="token punctuation">(</span>freq<span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">,</span> <span class="token number">6</span><span class="token punctuation">)</span>
<span class="token keyword">print</span><span class="token punctuation">(</span><span class="token string">"Sum of elements in arr[0..5] is "</span> <span class="token operator">+</span> str<span class="token punctuation">(</span>getsum<span class="token punctuation">(</span>BITTree<span class="token punctuation">,</span><span class="token number">5</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
 
<span class="token comment" spellcheck="true"># This code is contributed by Raju Varshney</span>

</code></pre>
<p>java(untested):</p>
<pre class=" language-java"><code class="prism  language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">BITreeClass</span> <span class="token punctuation">{</span>
	<span class="token keyword">public</span> <span class="token keyword">int</span><span class="token punctuation">[</span><span class="token punctuation">]</span> BITree<span class="token punctuation">;</span>
	<span class="token function">BITree</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">[</span><span class="token punctuation">]</span> array<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>BITree <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">int</span> <span class="token punctuation">[</span>array<span class="token punctuation">.</span>length <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">]</span><span class="token punctuation">;</span>
		<span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> i <span class="token operator">&lt;</span> array<span class="token punctuation">.</span>length<span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token function">update</span><span class="token punctuation">(</span>i<span class="token punctuation">,</span> array<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>
	<span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">getSum</span><span class="token punctuation">(</span><span class="token keyword">int</span> index<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">int</span> sum <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span>
		index <span class="token operator">=</span> index <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">;</span>
		<span class="token keyword">while</span><span class="token punctuation">(</span>index <span class="token operator">&gt;</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			sum <span class="token operator">+=</span> BITree<span class="token punctuation">[</span>index<span class="token punctuation">]</span><span class="token punctuation">;</span>
			index <span class="token operator">-=</span> index <span class="token operator">&amp;</span> <span class="token operator">-</span>index<span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
		<span class="token keyword">return</span> sum<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">update</span><span class="token punctuation">(</span><span class="token keyword">int</span> index<span class="token punctuation">,</span> <span class="token keyword">int</span> val<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		index <span class="token operator">=</span> index <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">;</span>
		<span class="token keyword">while</span><span class="token punctuation">(</span>index <span class="token operator">&lt;=</span> BITree<span class="token punctuation">.</span>length <span class="token operator">-</span> <span class="token number">1</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			BITree<span class="token punctuation">[</span>index<span class="token punctuation">]</span> <span class="token operator">+=</span> val<span class="token punctuation">;</span>
			index <span class="token operator">+=</span> index <span class="token operator">&amp;</span> <span class="token operator">-</span>index<span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>
	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">int</span><span class="token punctuation">[</span><span class="token punctuation">]</span> array <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token number">2</span><span class="token punctuation">,</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">,</span><span class="token number">4</span><span class="token punctuation">,</span><span class="token number">5</span><span class="token punctuation">,</span><span class="token number">6</span><span class="token punctuation">,</span><span class="token number">7</span><span class="token punctuation">,</span><span class="token number">8</span><span class="token punctuation">,</span><span class="token number">9</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
		BITreeClass newBITree <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">BITreeClass</span><span class="token punctuation">(</span>array<span class="token punctuation">)</span><span class="token punctuation">;</span>
		System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>newBITree<span class="token punctuation">.</span><span class="token function">getSum</span><span class="token punctuation">(</span><span class="token number">5</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		newBITree<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span><span class="token number">4</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>newBITree<span class="token punctuation">.</span><span class="token function">getSum</span><span class="token punctuation">(</span><span class="token number">5</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>	
<span class="token punctuation">}</span>
</code></pre>
<h2 id="d-binary-indexed-tree">2D Binary Indexed Tree</h2>
<h3 id="动机-1">动机</h3>
<p>为了解决2D数组的查询和和更新的操作</p>
<h3 id="思路">思路</h3>
<p>和一维一样。对于更新，先对每一行进行更新，在对每一列进行更新。对于getSum，<code>getSum(int i, int j)</code> 表示对\(x &lt;= i ,  y &lt;= j\)的数字求和。所以如果想要查询任意一个矩形内（用\((x_{1},y_{1}),(x_{2},y_{2})\)表示具喜感的左上角和右下角）的数字的和，可以用<code>getSum(x2,y2) + getSum(x1,y1) - getSum(x1,y2) - getSum(x2,y1)</code>计算。</p>
<h3 id="实现-1">实现</h3>
<pre class=" language-java"><code class="prism  language-java"><span class="token comment" spellcheck="true">/**
 * Created by zheyu on 16/11/5.
 */</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">TwoDBITreeClass</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">int</span><span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">[</span><span class="token punctuation">]</span> twoDBItree<span class="token punctuation">;</span>
    <span class="token function">TwoDBITreeClass</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">[</span><span class="token punctuation">]</span> array<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>twoDBItree <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">int</span> <span class="token punctuation">[</span>array<span class="token punctuation">.</span>length<span class="token punctuation">]</span><span class="token punctuation">[</span>array<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token punctuation">.</span>length <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">]</span><span class="token punctuation">;</span>
        <span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> i <span class="token operator">&lt;</span> array<span class="token punctuation">.</span>length<span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">int</span> j <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> j <span class="token operator">&lt;</span> array<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token punctuation">.</span>length<span class="token punctuation">;</span> j<span class="token operator">++</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token function">update</span><span class="token punctuation">(</span>i<span class="token punctuation">,</span> j<span class="token punctuation">,</span> array<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">[</span>j<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">update</span><span class="token punctuation">(</span><span class="token keyword">int</span> x<span class="token punctuation">,</span> <span class="token keyword">int</span> y<span class="token punctuation">,</span> <span class="token keyword">int</span> value<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        y <span class="token operator">=</span> y <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">;</span>
        <span class="token keyword">while</span><span class="token punctuation">(</span>y <span class="token operator">&lt;=</span> twoDBItree<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token punctuation">.</span>length <span class="token operator">-</span> <span class="token number">1</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            twoDBItree<span class="token punctuation">[</span>x<span class="token punctuation">]</span><span class="token punctuation">[</span>y<span class="token punctuation">]</span> <span class="token operator">+=</span> value<span class="token punctuation">;</span>
            y <span class="token operator">+=</span> y <span class="token operator">&amp;</span> <span class="token operator">-</span>y<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">getSum</span><span class="token punctuation">(</span><span class="token keyword">int</span> x<span class="token punctuation">,</span> <span class="token keyword">int</span> y<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">int</span> sum <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span>
        y <span class="token operator">=</span> y <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">;</span>
        <span class="token keyword">while</span><span class="token punctuation">(</span>y <span class="token operator">&gt;</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            sum <span class="token operator">+=</span> twoDBItree<span class="token punctuation">[</span>x<span class="token punctuation">]</span><span class="token punctuation">[</span>y<span class="token punctuation">]</span><span class="token punctuation">;</span>
            y <span class="token operator">-=</span> y <span class="token operator">&amp;</span> <span class="token operator">-</span>y<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> sum<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">getQuerySum</span><span class="token punctuation">(</span>Query q<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">int</span> sum <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span>
        <span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> q<span class="token punctuation">.</span>x2<span class="token punctuation">;</span> i <span class="token operator">&gt;=</span> q<span class="token punctuation">.</span>x1<span class="token punctuation">;</span> i<span class="token operator">--</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            sum <span class="token operator">+=</span> <span class="token function">getSum</span><span class="token punctuation">(</span>i<span class="token punctuation">,</span> q<span class="token punctuation">.</span>y2<span class="token punctuation">)</span> <span class="token operator">-</span> <span class="token function">getSum</span><span class="token punctuation">(</span>i<span class="token punctuation">,</span> q<span class="token punctuation">.</span>y1 <span class="token operator">-</span> <span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> sum<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">int</span><span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">[</span><span class="token punctuation">]</span> array <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">,</span> <span class="token number">4</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
                <span class="token punctuation">{</span><span class="token number">5</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">,</span> <span class="token number">8</span><span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
                <span class="token punctuation">{</span><span class="token number">4</span><span class="token punctuation">,</span> <span class="token number">6</span><span class="token punctuation">,</span> <span class="token number">7</span><span class="token punctuation">,</span> <span class="token number">5</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
                <span class="token punctuation">{</span><span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">4</span><span class="token punctuation">,</span> <span class="token number">8</span><span class="token punctuation">,</span> <span class="token number">9</span><span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
        TwoDBITreeClass new2DBITree <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">TwoDBITreeClass</span><span class="token punctuation">(</span>array<span class="token punctuation">)</span><span class="token punctuation">;</span>
        System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>new2DBITree<span class="token punctuation">.</span><span class="token function">getQuerySum</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Query</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>new2DBITree<span class="token punctuation">.</span><span class="token function">getQuerySum</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Query</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>new2DBITree<span class="token punctuation">.</span><span class="token function">getQuerySum</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Query</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span>
<span class="token keyword">class</span> <span class="token class-name">Query</span> <span class="token punctuation">{</span>
    <span class="token keyword">int</span> x1<span class="token punctuation">,</span> y1<span class="token punctuation">;</span>
    <span class="token keyword">int</span> x2<span class="token punctuation">,</span> y2<span class="token punctuation">;</span>
    <span class="token function">Query</span><span class="token punctuation">(</span><span class="token keyword">int</span> x1<span class="token punctuation">,</span> <span class="token keyword">int</span> y1<span class="token punctuation">,</span> <span class="token keyword">int</span> x2<span class="token punctuation">,</span> <span class="token keyword">int</span> y2<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>x1 <span class="token operator">=</span> x1<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>x2 <span class="token operator">=</span> x2<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>y1 <span class="token operator">=</span> y1<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>y2 <span class="token operator">=</span> y2<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h3 id="时间复杂度">时间复杂度</h3>
<p>update: O(log NM)<br>
getSum: O(log NM)<br>
初始化BIT：O(NMlogNM)</p>
<h2 id="应用">应用</h2>
<h3 id="求逆序数">求逆序数</h3>
<h4 id="问题描述">问题描述</h4>
<p>Two elements a[i] and a[j] form an inversion if a[i] &gt; a[j] and i &lt; j. For simplicity, we may<br>
assume that all elements are unique.</p>
<pre><code> Example:
 Input:  arr[] = {8, 4, 2, 1}
 Output: 6
 Given array has six inversions (8,4), (4,2),
 (8,2), (8,1), (4,1), (2,1).     
</code></pre>
<h4 id="思路1">思路1</h4>
<p>首先找到原数组中最大的数字，新建一个以该最大数字大一个的BIT。从原数组的末端开始遍历，用一个数组存储他后面比自己大的数的个数。用getSum查找后面所有比自己大的数，用update把所有在自己后面的数加一。</p>
<h4 id="实现1">实现1</h4>
<pre class=" language-java"><code class="prism  language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CountInversion</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">int</span><span class="token punctuation">[</span><span class="token punctuation">]</span> array <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token number">8</span><span class="token punctuation">,</span><span class="token number">4</span><span class="token punctuation">,</span><span class="token number">7</span><span class="token punctuation">,</span><span class="token number">1</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
        <span class="token keyword">int</span> maxNum <span class="token operator">=</span> Integer<span class="token punctuation">.</span>MIN_VALUE<span class="token punctuation">;</span>
        <span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">:</span> array<span class="token punctuation">)</span> maxNum <span class="token operator">=</span> Math<span class="token punctuation">.</span><span class="token function">max</span><span class="token punctuation">(</span>maxNum<span class="token punctuation">,</span> i<span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token comment" spellcheck="true">//找到数组中最大的数字，用它新建一个数组</span>
        BITreeClass BITree <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">BITreeClass</span><span class="token punctuation">(</span>maxNum <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">int</span> sum <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span>
        <span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> array<span class="token punctuation">.</span>length <span class="token operator">-</span> <span class="token number">1</span><span class="token punctuation">;</span> i <span class="token operator">&gt;=</span> <span class="token number">0</span><span class="token punctuation">;</span> i<span class="token operator">--</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            sum <span class="token operator">+=</span> BITree<span class="token punctuation">.</span><span class="token function">getSum</span><span class="token punctuation">(</span>array<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token comment" spellcheck="true">//这事一个index减少的过程，表示去看有多少比自己小的数字，这些数字是在之前从后往前遍历的时候加入的</span>
            BITree<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span>array<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token comment" spellcheck="true">//这是一个index增加的过程，表示把所有比当前数字大的计数加1</span>
        <span class="token punctuation">}</span>
        System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>sum<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h4 id="时间复杂度和优缺点">时间复杂度和优缺点</h4>
<p>Time Complexity :- The update function and getSum function runs for O(log(maxNum)) and we are iterating over n elements. So overall time complexity is : O(nlog(maxNum)).<br>
Auxiliary space : O(maxNum)<br>
缺点是：用了比较大的额外空间，而且由于用了额外空间，BIT更新和查找的时间也变大了。下面的方法用原数组大小的额外空间。<br>
###思路2<br>
先把原数组变味一个新数组，新数组的大小与原数组一样。新数组可取范围严格小于等于数组的大小。而且新数组中的数保留了原数组的相互大小关系。然后进行同样的。</p>
<p>例子：</p>
<pre><code>arr[] = {7, -90, 100, 1}
It gets  converted to,
arr[] = {3, 1, 4 ,2 }
as -90 &lt; 1 &lt; 7 &lt; 100.
</code></pre>
<h4 id="实现2">实现2</h4>
<pre class=" language-java"><code class="prism  language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CountInversion2</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">int</span><span class="token punctuation">[</span><span class="token punctuation">]</span> array <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token number">7</span><span class="token punctuation">,</span><span class="token operator">-</span><span class="token number">90</span><span class="token punctuation">,</span><span class="token number">100</span><span class="token punctuation">,</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">5</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
        <span class="token function">convert</span><span class="token punctuation">(</span>array<span class="token punctuation">)</span><span class="token punctuation">;</span>
        BITreeClass BITree <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">BITreeClass</span><span class="token punctuation">(</span>array<span class="token punctuation">.</span>length<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">int</span> sum <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span>
        <span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> array<span class="token punctuation">.</span>length <span class="token operator">-</span> <span class="token number">1</span><span class="token punctuation">;</span> i <span class="token operator">&gt;=</span> <span class="token number">0</span><span class="token punctuation">;</span> i<span class="token operator">--</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            sum <span class="token operator">+=</span> BITree<span class="token punctuation">.</span><span class="token function">getSum</span><span class="token punctuation">(</span>array<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            BITree<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span>array<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>sum<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">convert</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">[</span><span class="token punctuation">]</span> array<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">int</span><span class="token punctuation">[</span><span class="token punctuation">]</span> tmp <span class="token operator">=</span> Arrays<span class="token punctuation">.</span><span class="token function">copyOf</span><span class="token punctuation">(</span>array<span class="token punctuation">,</span> array<span class="token punctuation">.</span>length<span class="token punctuation">)</span><span class="token punctuation">;</span>
        Arrays<span class="token punctuation">.</span><span class="token function">sort</span><span class="token punctuation">(</span>tmp<span class="token punctuation">)</span><span class="token punctuation">;</span>
        Map<span class="token operator">&lt;</span>Integer<span class="token punctuation">,</span> Integer<span class="token operator">&gt;</span> map <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">HashMap</span><span class="token operator">&lt;</span><span class="token operator">&gt;</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token comment" spellcheck="true">//Map&lt;原数组的数，坐标（转换后的数）&gt;</span>
        <span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> i <span class="token operator">&lt;</span> array<span class="token punctuation">.</span>length<span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">if</span><span class="token punctuation">(</span><span class="token operator">!</span>map<span class="token punctuation">.</span><span class="token function">containsKey</span><span class="token punctuation">(</span>tmp<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">)</span>map<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span>tmp<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">,</span> i<span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token comment" spellcheck="true">//用map加快大规模数据的处理，这里总是选取一个数第一次出现的坐标来确保原数组中同样的数在被转换后同样的值</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> i <span class="token operator">&lt;</span> array<span class="token punctuation">.</span>length<span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            array<span class="token punctuation">[</span>i<span class="token punctuation">]</span> <span class="token operator">=</span> map<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span>array<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span>
</code></pre>
<h4 id="时间复杂度-1">时间复杂度</h4>
<p>Time Complexity :- The update function and getSum function runs for O(log(n)) and we are iterating over n elements. The convert function runs for O(nlog(n)). So overall time complexity is : O(nlog(n)).<br>
Auxiliary space : O(n) map + array</p>
