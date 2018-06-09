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
其中左邊數字為輸入值 *x* 右邊數字為輸入值 *y*<br />
利用如下程式與檔案進行連結，並確認是否讀取成功
-----------------------------------------------
>ifstream infile;     <br />          
>infile.open("test20180609-1.txt");<br />
>if(!infile.is_open()){      <br />            
>      cout<<"Can't open it";<br />
>      exit(EXIT_FAILURE);<br />
>}<br />
接著將文字檔內資料依序存入 陣列 training_data_x[]和 陣列 training_data_y[]<br />
-------------------------------------------------------------------------------
>for(int i=0;i<=data_members;i++){<br />
>       infile>>training_data_x[i];<br />
> 	infile.get();<br />
> 	infile>>training_data_y[i];<br />
> 	infile.get();<br />
	} <br />




Regression: Output a scalar
======================================
建立模型  *y = b + w ∙ x*<br />
將training_data_x的資料輸入A set of function 得到的解，與training_data_y做比較<br />
比較方法便是算出loss function<br />



  
  
<br />
