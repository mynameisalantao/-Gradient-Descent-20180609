使用Gradient Descent求線性方程20180609(原創)
===============================================
題目來源:Hung-yi Lee老師機器學習線上課程
----------------------------------------------
程式執行前先在相同目錄下建立記事本 *test20180609-1.txt*<br />
先假設理想方程式為 *y=3x+10*<br />
自己模擬實際實驗結果如下:<br />
>1   12.5<br />
>2   17<br />
>3   18.2<br />
>4   21.4<br />
>5   27<br />
>6   28.2<br />
>7   32<br />
>8   33.5<br />
>9   39<br />
>10  40<br />

其中左邊數字為輸入值 *x* 右邊數字為輸出值 *y*<br />
利用如下程式與檔案進行連結，並確認是否讀取成功

>ifstream infile;     <br />          
>infile.open("test20180609-1.txt");<br />
>if(!infile.is_open()){      <br />            
>      cout<<"Can't open it";<br />
>      exit(EXIT_FAILURE);<br />
>}<br />

接著將文字檔內資料依序存入 陣列 training_data_x[]和 陣列 training_data_y[]<br />

>for(int i=0;i<=data_members;i++){<br />
>       infile>>training_data_x[i];<br />
> 	infile.get();<br />
> 	infile>>training_data_y[i];<br />
> 	infile.get();<br />
	} <br />




Regression: Output a scalar
======================================
建立模型  Y =b+w*X<br />
---------------------------------------

將training_data_x的資料輸入A set of function 得到的解，與training_data_y做比較<br />
比較方法便是算出*loss function*<br />

>>L(f)=sigma(n=0->9) (Yn-f(Xn))^2<br />

代入 Y = b + w ∙ X<br />

>>L(f)=sigma(n=0->9) (Yn-(b+w*Xn))^2<br />

而Goodness of function 則是找到w與b始得Loss function最小<br />

>> 𝜕𝐿/𝜕𝑤 =sigma(n=0->9) 2(Yn-f(Xn))(-Xn) <br />
>> 𝜕𝐿/𝜕𝑏 =sigma(n=0->9) 2(Yn-f(Xn))(-1)

接著修正<br />

>>𝑤1 ← 𝑤0 −𝜂𝜕𝐿/𝜕𝑤|𝑤=𝑤0,𝑏=𝑏0<br />
>>𝑏1 ← 𝑏0 −𝜂𝜕𝐿/𝜕𝑏|𝑤=𝑤0,𝑏=𝑏0<br />

其中𝜂為learning_rate<br />
以下為程式的實現:<br />

>for(int j=0;j<=800;j++){            <br /> 
	lossfunction_partial_weight=0;<br />
	lossfunction_partial_bias=0;<br />
	for(int i=0;i<=data_members;i++){<br />
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
}

從原本先預設<br />

>double current_weight=2;     
>double current_bias=5;  

經過800次的Gradient Descent修正後<br /> 
得到結果如下:<br /> 

>lossfunction_partial_weight=0.0596218 <br />
>lossfunction_partial_bias=-0.665702 <br />
>current_weight=3.11964 <br />
>current_bias=9.68881 <br />

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
當然這種近似方法還有許多問題，等之後學習更深入後再來修正~~



  
  
<br />
