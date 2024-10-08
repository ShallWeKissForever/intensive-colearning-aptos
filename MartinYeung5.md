---
timezone: Asia/Shanghai
---

# {你的名字}

1. 自我介绍
* Martin Yeung, 來自中國香港，計算機科學+電商專業，香港城市大學理學碩士畢業，專注於區塊鏈技術研究和應用。

2. 你认为你会完成本次残酷学习吗？
* 會的。

## Notes

<!-- Content_START -->

### 2024.09.07

Aptos是基於MOVE語言的一個公鏈，利用MOVE可以構建一個更安全的智能合約，特別是用於數字資產領域，會突顥出其價值，當然也需要看是在怎樣的應用場景。 
針對MOVE智能合約的開發，有不同的知識點需要學習，需要理解不同代碼的原理及應用方式。
要建立完整的Aptos項目，在完成MOVE智能合約的開發後，
需要設計frontend及與智能合約的進行交互，而我最近在學習與智能合約的交互，
透過web3電子錢包讓用戶可以與平台/智能合約互動。


今天想分享一下我對useWallet的學習和了解，
Frontend交互部分，我用了@aptos-labs/wallet-adapter-react，透過它使用到useWallet，然後從useWallet獲得需要用到的功能，如下:

```
const {
    connected,
    account,
    network,
    signAndSubmitTransaction,
    signMessageAndVerify,
    signMessage,
    signTransaction,
} = useWallet();
```

使用useWallet，可以讓用戶進行一些簽名及提交交易的動作。
* 第一個例子(signMessage)是讓用戶對一項訊息進行簽名:

```
  const onSignMessage = async () => {
    const payload = {
      message: "Hello，我是Martin Yeung，對Aptos相當感興趣。",
      nonce: Math.random().toString(16),
    };

    //簽名後的回應訊息為response，可以留意到const response的類型是"SignMessageResponse"，
    //這個就是通過signMessage()所獲得到訊息類型
    
    const response = await signMessage(payload);
    
    //顯示response的訊息內容，以便做debug
    //而且可以讓用戶知道自己最終是否成功完成簽名
    //當中的await就是等待signMessage的回應，如果signMessage完成就能返會一些訊息。
    //如果沒有await的話，系統就不會一直等待signMessage的回應，所以會出現跳過signMessage回應的情況
    //有機會出現的情況就是signMessage失敗，但完成onSignMessage這功能。
    
    console.log(response);
  };
```

用戶執行以上功能的流程:
1. 在frontend頁面，當用戶觸發到onSignMessage功能，就會執行到以上的動作
2. 用戶的web3電子錢包會彈出
3. 在錢包上會顯示"Hello，我是Martin Yeung，對Aptos相當感興趣"
4. 及要求用戶對這訊息進行簽名，
因為這要確保用戶是能清楚知道自己是對什麼東西進行簽名確認，
5. 如果用戶確認沒問題，就可以在錢包上點擊"Sign"進行簽名的動作
6. 用戶等待簽名完成的回應 (這個可以在Frontend設計一個顯示回應的訊息，讓用戶知道最終結果)


* 第二個例子(signAndSubmitTransaction)是讓用戶對一項交易進行簽名及提出交易動作:
假設用戶需要轉帳APTOS到某個指定錢包，需要用戶進行簽名及交易。

```
  const APTOS_COIN = "0x1::aptos_coin::AptosCoin";
  const onSignAndSubmitTransaction = async () => {
    if (!account) return;
    const transaction: InputTransactionData = {
      data: {
        function: "0x1::coin::transfer",
        typeArguments: [APTOS_COIN],
        functionArguments: [account.address, 1], // 1 is in Octas
      },
    };
    try {

      //簽名後的回應訊息為response，可以留意到const response的類型是"any" 
      const response = await signAndSubmitTransaction(transaction);
      await aptosClient(network).waitForTransaction({
        transactionHash: response.hash,
      });
      console.log(response);

    } catch (error) {
      console.error(error);
    }
  };
```

在上面第二個的例子，可以留意到response的類型是any，跟第一個例子不同，因為它們所返回的訊息是有所不同，
在第二個例子返回的訊息是一個已完成簽名及已完成交易動作的紀錄。

第三個例子(signMessageAndVerify)是讓用戶對一項訊息進行簽名及進行確認:

```
  //
  const onSignMessageAndVerify = async () => {
    const payload = {
      message: "Hello，我是Martin Yeung，對Aptos相當感興趣。",
      nonce: Math.random().toString(16),
    };

    //簽名後的回應訊息為response，可以留意到const response的類型是"boolean"
    const response = await signMessageAndVerify(payload);
    console.log(response);
  };
```

在上面第三個的例子，可以留意到response的類型是boolean，跟第一、二個例子不同，
因為這個例子的目的是要用戶進行簽名及進行確認，所以返回的訊息只有true或false。
當然在第一個例子也可以通過做一些額外判斷(需要進一步編寫代碼)而獲得true或false，
但這例子就簡單直接，所以可以根據自己的需求而使用合適的功能。


### 2024.09.08

<!-- Content_END -->
