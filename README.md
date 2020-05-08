# liff-hand

liff-hand is a helper for LINE front-end framefwork (LIFF) to resolve some trouble at hash router (like Vue-router).

It is because LIFF sdk.js need take information in hash, so liff-hand can help you run vue-router correctly.

liff-hand 是一個 LIFF 小幫手用來解決依靠 hash 改變路由的前端套件（例如 Vue-router）正常運作。

# How to use ?

### 1. In the target page, add this code :

```HTML
<!--Inclue after liff sdk.js -->
<!--在 LIFF SDK 之後載入 liffhand.js -->
<script type="text/javascript" src="https://d.line-scdn.net/liff/1.0/sdk.js"></script>
<script type="text/javascript" src="https://melixyen.github.io/liff-hand/liffhand.js"></script>
```

```javascript
//The liff-hand will append to window.liff.$hand.
//liff-hand 將會附加到 window.liff.$hand 這個位置
liff.$hand.changeHash();
```

If you are using LIFF 2.0, please call liffhand2.js  
使用 LIFF 2.0 時請改載入 liffhand2.js 搭配。

```HTML
<!--Inclue after liff sdk.js -->
<!--在 LIFF SDK 之後載入 liffhand.js -->
<script type="text/javascript" src="https://static.line-scdn.net/liff/edge/2.1/sdk.js"></script>
<script type="text/javascript" src="https://melixyen.github.io/liff-hand/liffhand2.js"></script>
```

```javascript
//The liff-hand will append to window.liff.$hand and window.liffHand .
//liff-hand 將會附加到 window.liff.$hand 以及 window.liffHand這個位置
liff.$hand.changeHash();
liffHand.initAll(successFunction, errorFunction, loginConfig, systemConfig);
```

### 2. In the link url, use hashpath to take router value.

(Web App 網址) If your LIFF page is 

```HTML
http://www.myapp.com/test.html?a=1&b=2&c=3#/index
```

(LIFF 連結) Your LIFF link maybe like 

```HTML
line://app/1483775317-abcd43g5/?a=1&b=2&c=3#/index
```

When user click this link, it will fail because hash change to LIFF access token information, Now You can change to:

請將 LIFF 連結的 hash 參數部分放到 GET 參數的 hashpath 值內

```HTML
line://app/1483775317-abcd43g5/?a=1&b=2&c=3&hashpath="%23%2Findex
```

Sure, "#/" need use encodeURIComponent to encode to "%23%2F".

當然，別忘記編碼

And then in the target page, you need call changeHash function after liff initial an app.

然後在 Web App 頁裡，liff.init 的成功 callback 中執行 changeHash。

```javascript
liff.init(function(data){
    liff.$hand.changeHash();
    //This moment will change hashpath value to location.hash and trigger vue-router.
    //這個執行後就會把 hash 換成 hashpath 參數內容觸發 vue-router
});
```

# Initial after get profile

liff-hand provide a function for initial then get profile and check the user information then call success function, when it successed, liff.$hand will already get the profile information.

liff-hand 提供一個 initAll 方法幫助您初始化並取得使用者 profile 後才執行 callback。

you can use

```javascript
liff.$hand.initAll(function(data){
    liff.$hand.changeHash();
    console.info(liff.$hand.profile); //Profile will bind on this node.
});
```

Same callback, same error process, just change to use liff.$hand.initAll.

# Initial liff 2.0 with auto login

liff-hand V2 provide a function for initial then auto login or do not need login

liff-hand V2 提供的 initAll 方法判斷是否為 Web 而需要自動登入，以是否有帶第三參數決定 init 是否使用 LIFF 2.0。第三參數為 LIFF 2.0 init config，第四參數為 liff-hand 使用。

```javascript
liffHand.initAll(function(data){
    liff.$hand.changeHash();
    console.info(liff.$hand.profile); //Profile will bind on this node.
},
function(err){
    console.log(err);
},
{
    liffId:'abcdefgh-12345678'
},
{
    autoLogin: true
});
```

# Go to other page but still use LIFF

Now you can click an hyperlink or use location.href to other page but still use liff sdk. liff.$hand.goto() can hold LIFF status to next page and still with hash router. Just give link a hashpath parameter.

你可以跳到其他頁面但繼續維持 LIFF 的功能，透過 liff.$hand.goto() 可在下一個頁面中保持 LIFF 的狀態。

```javascript
location.href = "http://balabala.com/next.html?hashpath=%23%2Fabout";
// Change to 
liff.$hand.goto("http://balabala.com/next.html?hashpath=%23%2Fabout");
```

Sure you need initial liff sdk.js and liff-hand at next.html too.

# DEMO

目前使用到的專案

[LINE chatbot 台幣匯率機器人](https://line.me/R/ti/p/sCsZnuBg5V)

輸入數字金額查價後，將價錢加入筆記本、開啟團購，即為 LIFF Web App 介面。

LIFF 2.0

[團購訂單小助手](https://jiapi.net/linebotGroupBuy/liff.html)

可用來與朋友訂購商品、餐點。