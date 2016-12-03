# 22.2 創建 Genesis Block

## Step 1：定義區塊資料結構

根據 22.1 節的說明，區塊的資料結構包含 4 個欄位如下：

* *hash*：區塊的 hash ID
* *previousHash*：紀錄前一個區塊的 hash ID
* *timestamp*：區塊建立的時間
* *merkleRoot*：區塊的 merkle tree

以 Node.js 來實作此資料結構，方式是以 *function* 關鍵字來定義 *Block* 類別（Class）：

```
function Block(block) {
	this.hash = block.hash || '';
	this.previousHash = block.previousHash || '';
	this.timestamp = block.timestamp || new Date();
	this.merkleRoot = block.merkleRoot || {};
}
```

## Step 2：生成 Hash ID

Genesis block 的 hash ID 要如何生成呢？根據 Merkle tree 的演算法，hash ID 的生成方式是使用 SHA256 演算法。標準的 merkle tree 是使用二次的 SHA256 來計算出 hash ID，這樣的做法也稱為 double SHA256。

Node.js 內建的 ```crypto``` 模組，就提供了 SHA256 演算法函數。先引入 ```crypto``` 模組：

```
var crypto = require('crypto');
```

使用 ```createHmac``` 函數，計算出第 1 個 hash 值，用法如下：

* 第 1 個參數，填寫 ```sha256``` 
* 第 2 個參數，填寫 secret：任意一段句子即可

執行後，```createHmac``` 會建立 ```Hmac``` 的實例化（instance），再呼叫 ```Hmac``` 物件的 ```update``` 函數，並傳入一段本文來進行 sha256 編碼運算。完成後，呼叫 ```digest``` 將結果轉為 *hex* 格式。

完整範例：

```
var secret = 'blockchain developer';

var hash1 = crypto.createHmac('sha256', secret)
                   .update('created by jollen')
                   .digest('hex');
```

得到第 1 個的 hash 值。接著，使用這個 hash 值做為新的 secret，進行第 2 次的 hash 運算：

```
var hash2 = crypto.createHmac('sha256', hash1)
                   .update('powered by flowchain')
                   .digest('hex');

console.log(hash2);
```

輸出結果：

```
dd0e2b79d79be0dfca96b4ad9ac85600097506f06f52bb74f769e02fcc66dec6
```

這就是 genesis block 的 hash ID 了。

## Step 3：定義 Genesis Block 

建立 genesis block 最簡單的方式，就是直接「定義」它。建立一個名為 *config.js* 的檔案，並且直接定義好 genesis block 的欄位資訊：

```
// Filename: config.js
'use strict';                                                                                              

exports.genesis = {
    hash: 'dd0e2b79d79be0dfca96b4ad9ac85600097506f06f52bb74f769e02fcc66dec6',

    prevHash: '0000000000000000000000000000000000000000000000000000000000000000',

    timestamp: new Date(),
    
    merkleRoot: {}
};
```

## 小結

定義好 genesis block 後，下一個步驟就是為它產生新的 merkle tree。