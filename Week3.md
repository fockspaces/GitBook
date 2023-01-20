# Week-3 Assignment

Status: Done
Tags: Express, HTTP, Node.js, npm

[Week-3 Note](https://www.notion.so/Week-3-Note-ba7e4aa3a3314bea9bfe0394749ed9dd)

[remote-assignments/Week-3/Assignment at main · fockspaces/remote-assignments](https://github.com/fockspaces/remote-assignments/tree/main/Week-3/Assignment)

### Assignment architecture :

![Untitled](Week-3%20Assignment%20462f7f9eeeb8448783130d189cb70ad2/Untitled.png)

### Assignment 1: Your First Web Server

**思路**

基本的 Express 建立，這裡 port 設 3000 可以防止和 HTTP(Port 80) and HTTPS(Port 443)衝突

reference :

**[Significance of port 3000 in Express apps - node.js](https://stackoverflow.com/questions/37929173/significance-of-port-3000-in-express-apps)**

### Assignment 2: Build Backend API for Front-End

**思路**

這裡透過 url 傳入的參數都會存到 req.params 內，將 parameter 轉換為數字後，再判斷是否為整數。

如果是 : renderedText = 等差級數和

如果不是 : renderedText = error message

> 📄 \***\*[How to use the Fetch API with async/await](https://rapidapi.com/guides/fetch-api-async-await)\*\***

**Tips**

1. **Params 轉換成數字**

這裡可以用 parseInt()或單純前綴補上+號的方法，我認為後者更好，分析如下:

```jsx
1. parseInt() : parseInt(’12d’) ⇒ 12 ⇒ number type

2. + : +’12d’ ⇒ NaN ⇒ not a number
```

前者無法分辨是否為純數字，而是將數字部分轉為 Number type

後者可以分辨純數字，較符合此題需求

> **(Optional) Think about what will happen when N is very large.**

Since the time complexity of the for-loop method is O(N), the runtime becomes larger when N becomes large.

We can solve this problem mathematically by simply applying the arithmetic series formula (n/2)⋅(a₁+aₙ) to reduce the time complexity to O(1), which only makes one operation performed per time.

> **Issue : 為何 Fetch API 無法順利執行 redirecting**

**Axios :**

![Untitled](Week-3%20Assignment%20462f7f9eeeb8448783130d189cb70ad2/Untitled%201.png)

**Fetch :**

![Untitled](Week-3%20Assignment%20462f7f9eeeb8448783130d189cb70ad2/Untitled%202.png)

**程式邏輯 :**

在 sending request 後，server 端會判斷數字是否合法，若合法則送回 XML file，不合法則 server 會 render “error page”。

**不能載入頁面的可能原因 :**

1. axios 內建 error handler，當發現 server 送回的訊息無法解析成 XML(or JSON)時，他會繼續執行 server 的指令(也就是 rendering page)。
2. 而 Fetch 則是會因無法解析 JSON 而 throw error，將 error catch 之後可以得到以下訊息 :

> uncaught (in promise) SyntaxError: Unexpected token '<', "<!DOCTYPE "... is not valid JSON

_render 會將整個 page 的原始碼送回去，導致 Fetch 無法以 json()解析_

如果想繼續 render page，就必須將執行權交回 server，但這裡不清楚該如何做到，因此我直接改 call axios function。等之後學得更完整再回頭解決此問題。

### Assignment 3: Connect to Backend API by AJAX

**Tips :**

1. 將 public folder 下的所有 static files cache 住

```jsx
app.use(express.static("public"));
```

1. 與上面一樣，但新增一個 virtual path ‘/static’

```jsx
app.use("/static", express.static("public"));
```

1. 要 import module 到 browser，可以使用 CDN

```jsx
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

**思路**

1. 先用 app.use(express.static("public")) render public folder 中的 index.html
2. 以 axios 或 XMLHttpRequest 與 Server Route 連結，取得資料後修改至 DOM element 的 innerHTML 中
3. 製作 Form 表單，透過 input.value 取得想要的值，再用 template literals 嵌入 URL 中，最後回到步驟二修改 element

**Todo**

- [x] 加入 error catch middleware, routes folder

> 📄 [客製化 error](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Error)

附註 : 因為/:myName 佔在 root route，這邊如果要客製化區分 user / data 會比較難
(例如我無法區分 not found page 是由 data 還是 myName path 而來，這會影響 which page to render)
故我直接改為 render page 而非 throw error 來發送錯誤訊息
如果能將 myName / data 兩個 routes 路徑區分開，用 express error handler 集中處理會是比較好的做法

- [x] 改為 render error/sucessful page

> 📄 [**如何 catch 住 404?**](https://stackoverflow.com/questions/6528876/how-to-redirect-404-errors-to-a-page-in-expressjs)

- [x] 新增未註冊重導頁面

### Assignment 4: HTTP Cookie

**思路**

1. 建立 express server，並接受 parpams 參數 “myName”傳入
2. 建立 signup page，判斷參數是否包含在 cookies 內，若無則 direct to
3. signup 內建 post method form，將 input 送至 req.body 後交給 server
4. POST /trackName 接收到參數時，將 name 傳回 browser cookie 後，導回初始頁面

**Tips :**

1. 必須加上此段，來正確接收傳入參數

```jsx
app.use(express.json()); // to support JSON-encoded bodies
app.use(express.urlencoded()); // to support URL-encoded bodies
```

1. res.redirect 加上 return，避免繼續執行下方程式碼

**驗收 :**

![Untitled](Week-3%20Assignment%20462f7f9eeeb8448783130d189cb70ad2/Untitled%203.png)

- [x] 確認當 server 關掉時，cookies 是否都還在
- [x] 確認當 chrome 重啟時，cookies 是否會消失

**To-do :**

1. 是否 cookie 可以接受<key, multi-value>型式? 如此一來只需查找該 key 中所有 name 即可
2. 更熟悉 HTTP POST / GET method 的使用，了解 express router 原理及應用(轉成 MVC 架構來設計)
3. 增加 logout 功能，並區隔登入登出畫面

ref **:**

[Day24 - Cookie 在 express 上的應用-登入實作為例。 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10187343)

### Assignment 5: Algorithm Practice (Advanced Optional)

**思路**

之前已經練習過此題，在最初我的想法與 brute force 差不多，因為那時還沒有 hashmap 的概念，但後來在學 double-pointer 後我有試著用類似的概念去實作，寫在方法三

1. brute force
   用兩層 for 迴圈比對所有 cases，再判斷是否滿足條件後回傳即可。
   時間複雜度 : O(N^2)
   空間複雜度 : O(1)
2. **hashmap**
   先建立 hashmap 以<value, index>來存 nums 的資料。
   接著直接對每一個元素 nums[i]查找 map 中是否有滿足 num[i] + [ ] = target 的值
   意即 : map.get(target - num[i]) has value or not ?
   因為答案只有一種，滿足條件即可直接 return
   時間複雜度 : O(N)
   空間複雜度 : O(N)
3. **sort and limit**
   將 nums 排序後，形成由小到大的排列數組
   接著兩個指針 left, right 指向 nums 頭尾 :

![Untitled](Week-3%20Assignment%20462f7f9eeeb8448783130d189cb70ad2/Untitled%204.png)

如果 sum < target ⇒ increase，否則 decrease，直到 target === sum 為止
這樣時間複雜度為 sort 的時間，也就是 O(N\*log(N))
空間複雜度(假設 in-place sorting)，為 O(1)

> Note :
> 特別注意，因為這裡使用 in-place sort，所以無法取得原本的 index，若要 return 真實 index，則可建立 map 再映射回去，但這樣 space-complexity 會變為 O(N)，不如方法二

> 分析 : 處理多大的數據時會開始感受到效能差異?

這裡用 for loop 迴圈來進行測試，調整 function call 的次數 N 來累計運行時間

**Bug1 : 重複測資**

![Untitled](Week-3%20Assignment%20462f7f9eeeb8448783130d189cb70ad2/Untitled%205.png)

我拿下列 test case 去重複驗證，發現結果竟然是第一種方法最好

```
const nums = [2, 7, 15, 11];
const target = 9;
```

但在思考過後，發現因為在這情況，function_1 可以馬上找到 target 並回傳

而其他兩者則是需另外做其他操作(set map, sort…)。

**To-do:**

- [x] 產生隨機測試集，自行比較三個程式的時間差異

[Google Colaboratory](https://colab.research.google.com/drive/1392Imlm8tnzNM4jhQtZBFWM1NiNxy63e?usp=sharing)

用 python 寫了 random array 來產生 test cases，這裡用 sample 的方式來避免重複數字產生(增加 target 命中的機率)

N 為 array length，而非 test cases numbers

在 N = 100 的情況 :

![Untitled](Week-3%20Assignment%20462f7f9eeeb8448783130d189cb70ad2/Untitled%206.png)

在 N = 1000 的情況 :

![Untitled](Week-3%20Assignment%20462f7f9eeeb8448783130d189cb70ad2/Untitled%207.png)

在 N = 10000 的情況 :

![Untitled](Week-3%20Assignment%20462f7f9eeeb8448783130d189cb70ad2/Untitled%208.png)

- [x] 作業將 data 改為 json files

在 N = 500 的情況 :

![Untitled](Week-3%20Assignment%20462f7f9eeeb8448783130d189cb70ad2/Untitled%209.png)

在 N = 5000 的情況 :

![Untitled](Week-3%20Assignment%20462f7f9eeeb8448783130d189cb70ad2/Untitled%2010.png)

在 N = 50000 的情況 :

![Untitled](Week-3%20Assignment%20462f7f9eeeb8448783130d189cb70ad2/Untitled%2011.png)

**結論 :**

這次實驗沒有很成功，

難點在於想出各種 edge case，進而得到 best, worst, average 的運行效能
隨機產生出的 test cases 會因為不具代表性，只能反映出特定情況下的效率，再多的 cases 總數也只是重複運算類似結果而已。

**ref:**

[How to measure time taken by a function to execute](https://stackoverflow.com/questions/313893/how-to-measure-time-taken-by-a-function-to-execute)

###

### TreeHouse :

![Untitled](Week-3%20Assignment%20462f7f9eeeb8448783130d189cb70ad2/Untitled%2012.png)
