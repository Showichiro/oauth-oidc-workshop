## CIBA（シーバ）

### 概要

認証・認可画面ではなく、認証デバイスを用いて認証/認可するフロー。 \
[OpenID Connect Client-Initiated Backchannel Authentication Flow - Core 1.0](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html)に仕様が定義される。

詳細は割愛するが、概要としては以下の通り。

<img src="/ciba.drawio.svg"/>

---

### CIBAのすごい点

クライアントアプリと認証デバイスが物理的に離れていてもよい点。OIDCを利用できるユースケースが広がった。

- コールセンターのオペレーターが代理で操作をする
- POS端末での決済を、ユーザーのスマートフォン上で許可する
- 子の（親のカードを使った）支払いを、親がスマートフォン上で許可する

<img src="/ciba-example.drawio.svg" class="h-75" />
