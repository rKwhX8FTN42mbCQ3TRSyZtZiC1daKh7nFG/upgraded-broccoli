---
html: direct-xrp-payments.html
parent: payment-types.html
blurb: XRPによる直接支払いは、XRP Ledgerで資産を送金する最も簡単な方法です。
---
# XRPによる直接支払

金融システムの基本は、 _価値の移動_ です。一言で言えば、決済です。XRP Ledgerでの最も簡単な支払いタイプは、XRP間の直接支払で、XRP Ledgerのあるアカウントから別のアカウントにXRPを直接移動します。

## XRP間の直接支払について

一般的に、XRPは、XRP Ledgerのどのアドレスからでも、他のアドレスに直接送金できます。受信側のアドレスは _宛先アドレス_ 、送信側のアドレスは _支払元アドレス_ と呼ばれます。XRPを直接送金するには、送信者は[Paymentトランザクション][]を使用します。これは、次のように簡潔に表すことができます。

```json
{
  "TransactionType": "Payment",
  "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
  "Destination": "ra5nK24KXen9AHvsdFTKHSANinZseWnPcX",
  "Amount": "13000000"
}
```

上記のトランザクション指示によって、以下のように実行されます。rf1Bi ...からra5nK... にPaymentを送信することで、ちょうど13 XRPが送金されます。トランザクションが正常に処理されると、その内容が正確に実行されます。新しいレジャーバージョンが[検証済み](consensus.html)になるまでに、通常約4秒かかるため、現在処理中のレジャーの後にレジャーバージョンのキューに入れられても、正常なトランザクションを作成、送信、実行後、8秒以内に最終結果を出すことができます。

**注意:** [Paymentトランザクションタイプ][Payment]は、[通貨間の支払い](cross-currency-payments.html)や[Partial Payment](partial-payments.html)を含む、より特殊な支払いにも使用できます。Partial Paymentの場合、トランザクションで非常に少ない金額しか送金しなかった場合でも、多額のXRPが`Amount`に表示される可能性があります。誤った金額を顧客に入金しないようにする方法については、[Partial Paymentの悪用](partial-payments.html#partial-paymentの悪用)を参照してください。

XRP間の直接支払ではPartial Paymentは使用できませんが、Partial Paymentでは複数の送金元通貨から変換後にXRPを送金できます。


## アカウントの資金提供

XRP Ledgerにそのアドレスの記録が事前に存在していなくても、支払いで[口座準備金](reserves.html)の最少額を満たすのに十分なXRPが送金されれば、数学的に有効なアドレスで支払いを受け取ることができます。支払いで十分なXRPを送金できない場合は失敗します。

詳細は、[アカウント](accounts.html#アカウントの作成)を参照してください。


## アドレスの再利用

XRP Ledgerでは、支払いを受け取ることができるアドレスは永続的ですが、XRPの重要な[必要準備金](reserves.html)は消費できません。つまり、他の一部のブロックチェーンシステムとは異なり、トランザクションごとに異なる使い捨てアドレスを使用することはお勧めできません。XRP Ledgerでは、ベストプラクティスとして、複数のトランザクションに同じアドレスを再利用することをお勧めします。アドレスを定期的に使用する場合（特にインターネット接続サービスによって管理されている場合）は、[レギュラーキー](cryptographic-keys.html)を設定し、キーの漏えいのリスクを低減するためにキーを定期的に事前変更する必要があります。

送金元は、目的の受取人が最後に支払いを送信したときと同じアドレスを使用していると仮定しないことが重要です。必然的に、セキュリティの侵害が発生し、ユーザーまたは企業はアドレスを変更しなければならない場合があります。送金する前に、現在の受取アドレスを受取人に尋ねてください。これにより、漏えいした古いアドレスを制御している不正ユーザーに誤ってお金を送信することはありません。


## XRPによる直接支払の処理方法

大まかに言えば、XRP Ledgerのトランザクション処理エンジンでは、XRPによる直接支払を次のように適用します。

1. [Paymentトランザクション][]のパラメータを検証します。トランザクションがXRPを送信、送金するように構成されている場合、トランザクション処理エンジンはそのトランザクションをXRP間の直接支払として認識します。検証チェックは次のように行います。

   - すべてのフィールドが正しいフォーマットであることを確認します。たとえば、XRPによる直接支払の場合、`Amount`フィールドは[XRPのdrop数][]でなければなりません。
   - 送信元アドレスがXRP Ledgerの資金供給された[アカウント](accounts.html)であることを確認します。
   - 指定された署名がすべて、送信元アドレスに対して有効であることを確認します。
   - 宛先アドレスと送金元アドレスが異なることを確認します。（[宛先タグ](source-and-destination-tags.html)が異なる同一アドレスに送金するだけでは不十分です。）
   - Paymentを送信するのに十分なXRP残高が送金元にあることを確認します。

   いずれかのチェックに失敗すると、支払いは失敗します。

2. 受取アドレスが、資金供給されたアカウントかどうかを確認します。

   - 受取アドレスに資金が供給されている場合は、[DepositAuth](depositauth.html)や[RequireDest](source-and-destination-tags.html#requiring-tags)など、支払いの受け取りに関する制限が受取アドレスにあるかどうかを確認します。そのような制限を支払いが満たしていない場合、支払いは失敗します。
   - 受取アドレスに資金が供給されていない場合は、[必要準備金](reserves.html)の最低額を満たすのに十分なXRPが支払いで送金されるかどうかを確認します。十分でない場合、支払いは失敗します。

3. `Amount`フィールドで指定されたXRPの金額と、[トランザクションコスト](transaction-cost.html)用に消却されるXRPの金額の合計を送金元アカウントから引き落とし、受取アカウントに同じ金額を送金します。

   必要に応じて、受取アドレス用に新規アカウント([AccountRootオブジェクト](accountroot.html)) を作成します。新規アカウントの開始残高は、支払いの`Amount`と同額になります。

   エンジンは、[トランザクションのメタデータ](transaction-metadata.html)に`delivered_amount`フィールドを追加して、送金金額を示します。正しい金額のXRPを受け取ったことを確認できるように、必ず`delivered_amount`を使用する必要があります。`Amount`フィールドでは**ありません**。（通貨間の支払「Partial Payment」では、`Amount`フィールドに記載されているよりも少額のXRPが送金される場合があります。）詳細は、[Partial Payments](partial-payments.html)を参照してください。


## 他の支払いタイプとの比較

- **XRPによる直接支払**は、単一のトランザクションでXRPを送受信する唯一の方法です。この方法は、速度、シンプルさ、低コストの面でバランスが取れています。
- [通貨間の支払い](cross-currency-payments.html)でも[Payment][]トランザクションタイプを使用しますが、XRPとXRP以外の[発行済み通貨](issued-currencies.html)を組み合わせて送金できます。ただし、XRP間の支払いは除きます。また、[Partial Payment](partial-payments.html)でも使用できます。通貨間の支払いは、XRPで指定されていない支払いや、[分散型取引所](decentralized-exchange.html)で裁定取引の機会を得るのに適しています。
- [Checks](checks.html) :not_enabled: すぐに送金せずに送金元に債務を設定してもらいます。受取人は有効期間内であればいつでも換金できますが、その金額は保証されません。Checksでは、XRPまたは発行済み通貨のいずれかを送金できます。Checksは、受取人に支払いを請求する自律性を与えるのに適しています。
- [Escrow](escrow.html)では、特定の条件が満たされたときに、意図した受取人が要求できるXRPを確保します。XRPの金額は完全に保証されており、Escrowの有効期限が切れない限り、送金元が使用することはできません。Escrowは、巨額のスマートコントラクトに適しています。
- [Payment Channel](payment-channels.html)では、XRPが確保されます。受取人は、署名による認証を使用して、チャネルから一括でXRPを要求できます。XRP Ledgerの全トランザクションを送信せずに、認証を個々に確認できます。Payment Channelは、極めて大量の小口決済または「ストリーミング」支払いに適しています。


## 関連項目

- **コンセプト:**
    - [決済システムの基本](payment-system-basics.html)
- **チュートリアル:**
    - [XRPの送金（対話型チュートリアル）](send-xrp.html)
    - [WebSocketを使用した着信ペイメントの監視](monitor-incoming-payments-with-websocket.html)
- **リファレンス:**
    - [Paymentトランザクション][]
    - [トランザクションの結果](transaction-results.html)
    - [account_infoメソッド][] - XRP残高を確認します。


<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}
