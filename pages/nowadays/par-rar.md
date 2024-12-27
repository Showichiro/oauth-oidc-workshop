# OAuth 2.0 Pushed Authorization Requests（PAR）/ OAuth 2.0 Rich Authorization Requests（RAR）

## PAR（[RFC 9126](https://www.rfc-editor.org/rfc/rfc9126.html)）

### 概要

認可リクエストの内容を事前に認可サーバへ登録するエンドポイントを追加する。

<img src="/assets/par.drawio.svg" class="h-70" />

---

### 利点

- セキュリティの向上
  - URI内に含まれるパラメータ情報から認可内容を読み取る中間者攻撃の可能性を減らす
- リクエストサイズ上限の回避
  - クエリパラメータの電文長の制限を回避できる

---

## RAR（[RFC 9396](https://www.rfc-editor.org/rfc/rfc9396)）

### 概要

scopeでは表現しきれない詳細な認可リクエスト内容を表現するための`authorization_details`というパラメータを認可エンドポイントに追加。 \
具体的には、ユーザの認可を得るうえでコンテキスト・意図を明示して認可範囲を制限したいユースケースがある。

- オンライン決済であれば、「支払い処理」を認可するにあたって、「金額」や「誰に対して支払うか」などを制限したい
- 医療分野であれば、「健康診断の結果の共有」を認可するにあたって、「特定の医師」「特定の期間」などを制限したい

---

## RARの構造

共通のJSON構造で定義する。オンライン決済の場合の具体例。

```json
[
  {
    "type": "payment_initiation",
    "actions": ["initiate", "status", "cancel"],
    "locations": ["https://example.com/payments"],
    "instructedAmount": {
      "currency": "EUR",
      "amount": "123.50"
    },
    "creditorName": "Merchant A",
    "creditorAccount": {
      "iban": "DE02100100109307118603"
    },
    "remittanceInformationUnstructured": "Ref Number Merchant"
  }
]
```

---

### RARの構造の説明

|       キー       | 指定されている値                 | 説明                                                   |
| :--------------: | :------------------------------- | :----------------------------------------------------- |
|       type       | payment_initiation               | 支払い処理に関する認可の詳細であることを明示           |
|     actions      | ["initiate", "status", "cancel"] | 支払いの開始、ステータス取得、キャンセルの認可を要求   |
|    locations     | https://example.com/payments     | https://example.com/paymentsに対して認可することを明示 |
| instructedAmount | EUR, 123.50                      | 支払額について明示。この場合123.5ユーロを支払う        |

---

|               キー                | 指定されている値       | 説明                   |
| :-------------------------------: | :--------------------- | :--------------------- |
|           creditorName            | Merchant A             | 受取人                 |
|          creditorAccount          | DE02100100109307118603 | 受取人の口座情報       |
| remittanceInformationUnstructured | Ref Number Merchant    | 支払いに関する追加情報 |

---

### PARの活用

RARは複雑な認可リクエストであるため、PARとの組み合わせが求められる（以下、PARへの登録例）。

```
  POST /as/par HTTP/1.1
  Host: as.example.com
  Content-Type: application/x-www-form-urlencoded
  Authorization: Basic czZCaGRSa3F0Mzo3RmpmcDBaQnIxS3REUmJuZlZkbUl3

  response_type=code&
  client_id=s6BhdRkqt3
  &state=af0ifjsldkj
  &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
  &code_challenge_method=S256
  &code_challenge=K2-ltc83acc4h0c9w6ESC_rEMTJ3bwc-uCHaoeK1t8U
  &authorization_details=%5B%7B%22type%22%3A%22account_information%22
  %2C%22actions%22%3A%5B%22list_accounts%22%2C%22read_balances%22%2C%
  22read_transactions%22%5D%2C%22locations%22%3A%5B%22https%3A%2F%2Fe
  xample.com%2Faccounts%22%5D%7D%2C%7B%22type%22%3A%22payment_initiat
  ion%22%2C%22actions%22%3A%5B%22initiate%22%2C%22status%22%2C%22canc
  el%22%5D%2C%22locations%22%3A%5B%22https%3A%2F%2Fexample.com%2Fpaym
  ents%22%5D%2C%22instructedAmount%22%3A%7B%22currency%22%3A%22EUR%22
  %2C%22amount%22%3A%22123.50%22%7D%2C%22creditorName%22%3A%22Merchan
  t123%22%2C%22creditorAccount%22%3A%7B%22iban%22%3A%22DE021001001093
  07118603%22%7D%2C%22remittanceInformationUnstructured%22%3A%22Ref%2
  0Number%20Merchant%22%7D%5D
```
