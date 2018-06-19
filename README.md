使用 C++ Gradient Descent求線性方程20180609(原創)
===============================================
程式語言:使用C++   撰寫:Alan Tao  參考:無
----------------------------------------------
程式執行前先在相同目錄下建立記事本 *test20180609-1.txt*<br />
先假設理想方程式為 *y=3x+10*<br />
自己模擬實際實驗結果如下:<br />
<pre><code>1   12.5
2   17
3   18.2
4   21.4
5   27
6   28.2
7   32
8   33.5
9   39
10  40</pre></code>

其中左邊數字為輸入值 *x* 右邊數字為輸出值 *y*<br />
利用如下程式與檔案進行連結，並確認是否讀取成功

<pre><code>ifstream infile;           
infile.open("test20180609-1.txt");
if(!infile.is_open()){                
      cout<<"Can't open it";
      exit(EXIT_FAILURE);
}</pre></code>

接著將文字檔內資料依序存入 陣列 training_data_x[]和 陣列 training_data_y[]

<pre><code>for(int i=0;i<=data_members;i++){
       infile>>training_data_x[i];
 	infile.get();
 	infile>>training_data_y[i];
 	infile.get();
	} </pre></code>




Regression: Output a scalar
======================================
建立模型  Y =b+w*X
---------------------------------------

將training_data_x的資料輸入A set of function 得到的解，與training_data_y做比較<br />
比較方法便是算出*loss function*

<pre><code>L(f)=sigma(n=0->9) (Yn-f(Xn))^2</pre></code>

代入 Y = b + w ∙ X<br />

<pre><code>L(f)=sigma(n=0->9) (Yn-(b+w*Xn))^2</pre></code>

而Goodness of function 則是找到w與b始得Loss function最小

<pre><code>𝜕𝐿/𝜕𝑤 =sigma(n=0->9) 2(Yn-f(Xn))(-Xn) 
𝜕𝐿/𝜕𝑏 =sigma(n=0->9) 2(Yn-f(Xn))(-1)</pre></code>

接著修正

<pre><code>𝑤1 ← 𝑤0 −𝜂𝜕𝐿/𝜕𝑤|𝑤=𝑤0,𝑏=𝑏0
𝑏1 ← 𝑏0 −𝜂𝜕𝐿/𝜕𝑏|𝑤=𝑤0,𝑏=𝑏0</pre></code>
其中𝜂為learning_rate<br />
以下為程式的實現:

<pre><code>for(int j=0;j<=800;j++){            
	lossfunction_partial_weight=0;
	lossfunction_partial_bias=0;
	for(int i=0;i<=data_members;i++){
		lossfunction_partial_weight=lossfunction_partial_weight\
		-2*training_data_x[i]*training_data_y[i]\
		+2*current_bias*training_data_x[i]\
		+2*current_weight*training_data_x[i]*training_data_x[i];
	}	          
	for(int i=0;i<=data_members;i++){
		lossfunction_partial_bias=lossfunction_partial_bias\
		-2*training_data_y[i]\
		+2*current_bias\
		+2*current_weight*training_data_x[i];
	}		                
	current_weight=current_weight-learning_rate*lossfunction_partial_weight;
	current_bias=current_bias-learning_rate*lossfunction_partial_bias;
}</pre></code>

從原本先預設<br />

<pre><code>double current_weight=2;     
double current_bias=5;  </pre></code>

經過800次的Gradient Descent修正後<br /> 
得到結果如下:<br /> 

<pre><code>lossfunction_partial_weight=0.0596218 
lossfunction_partial_bias=-0.665702 
current_weight=3.11964 
current_bias=9.68881 </pre></code>

可以發現current_weight與current_bias已經非常接近預設的 3 和 10 了~!<br />


後記
================
後來套入別的Train data 使用斜率更大的函數<br />
卻發現利用Gradient無法收斂到該函數<br />
檢查lossfunction_partial_weight與lossfunction_partial_bias <br />
到後面完全呈現發散狀態<br />
可能是起始位置w0與b0跟函數差距過大<br />
先稍微修改w0與b0後，lossfunction_partial_weight與lossfunction_partial_bias可以逐漸收斂<br />
但結果仍是不理想<br />
在檢查發現lossfunction_partial_weight能夠收斂到1以下<br />
但lossfunction_partial_bias雖然有收斂趨勢，但非常緩慢，需要好幾倍的修正次數<br />
於是我改成設立2個learning_rate <br />
其中w的learning_rate不變，而b的learning_rate調大 <br />
測試後發現可以完全收斂了~ 表示要應付大斜率的函數，可能要考慮使用2個learning_rate <br />
當然這種近似方法還有許多問題，等之後學習更深入後再來修正~~ <br />
PS.修正後程式碼可參考Ver2.0 <br />

使用 C++ Gradient Descent求線性方程20180619
=============================================
首先載入模塊
<pre><code>import tensorflow as tf  
import numpy as np</pre></code>
接著自己設計training data數值<br />
x_data生成100個服從均勻分布的0~50數值<br />
y_data為=x_data* 3+10 微小的擾動在0~1之間
<pre><code>x_data=(np.random.rand(100))*50
y_data=x_data*3+10+(np.random.rand(100))</pre></code>
定義變量要用到variable，用隨機數列生成來生成參數<br />
random_uniform([Weights為1維結構],隨機數列Weights生成範圍為-1~1)
<pre><code>tf.Variable(initializer,name)</pre></code>
初始化參數可以有tf.random_normal,tf.constant,而name為變量名稱
<pre><code>Weights=tf.Variable(tf.random_uniform([1],-1.0,1.0))</pre></code>
biases初始值為5
<pre><code>biases=tf.Variable(tf.constant(5,dtype=tf.float32))</pre></code>

預測的y
<pre><code>y=Weights*x_data+biases</pre></code>
計算預測y(y_guess)的與實際的y(y_data)的差別<br />
tf.square(x)	计算平方 (y = x * x = x^2).<br />
tf.reduce_mean(input_tensor)	求tensor中平均值
<pre><code>loss=tf.reduce_mean(tf.square(y-y_data))</pre></code>
使用優化器 使用gradient descent (學習率)
<pre><code>optimizer=tf.train.GradientDescentOptimizer(0.001)</pre></code>
優化的目的是要來減少某個參數(loss)
<pre><code>train=optimizer.minimize(loss)</pre></code>

初始化
<pre><code>init=tf.global_variables_initializer()</pre></code>

session如指針 指向要處理的地方
<pre><code>sess=tf.Session()</pre></code>
讓init被激活
<pre><code>sess.run(init)</pre></code>
<pre><code>for step in range(6000):
    sess.run(train)
    #每訓練100次就印出結果
    if step%100==0:
        print(step,sess.run(Weights),sess.run(biases))</pre></code>
最後結果為
<pre><code>5900 [3.0030675] 10.331805</pre></code>
也算接近了........
![Imgur](https://i.imgur.com/NjD7mMJ.png)


