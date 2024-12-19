---
marp: true
theme: default
author: Showichiro
headingDivider: 1
paginate: true
---

# OAuth2.0/OIDC概説

<!-- paginate: skip -->

# アジェンダ

1. 認可と認証
2. OAuth 2.0
3. OpenID Connect (OIDC)
4. OAuth 2.0 と OIDC の応用

<!-- paginate: true -->

# アジェンダ

1. **認可と認証**
2. OAuth 2.0
3. OpenID Connect (OIDC)
4. OAuth 2.0 と OIDC の応用

# 1. 認可と認証

- **認証** と **認可** の違いについて
- 従来の認証方式における課題

<!-- header: 1. 認可と認証 -->

# **認証** と **認可** の違いについて

- 認証(Authentication)
  - ユーザーの身元を確認すること(私は～～です)
- 認可(Authorization)
  - ユーザーにリソースにアクセスする許可を与えること(～～をしてよいです)

# OAuth2.0/OIDCの文脈に寄せた具体例

- 認証(Authentication)
  - ○○としてログインする
- 認可(Authorization)
  - ○○の代わりに「写真を閲覧してもよい」「写真をアップロードしてよい」「写真を削除してよい」

# 従来の認証方式における課題

1. パスワードの共有
   - 第三者サービスを利用するために、ユーザーは自分のパスワードをそのサービスに提供する必要があった
2. 各サービスごとに認証が必要
   - 多くのウェブサイトやサービスで、ユーザーは個別にアカウントを作成し、パスワードを管理する必要があった

# 従来の認証方式

従来認証方式ではクライアントがサーバに向けてID/Passwordを入力しユーザのデータにアクセス

![w:700](./conventional.drawio.svg)

# パスワードの共有

そのため、3rdパーティのアプリがユーザのデータにアクセスするためには、ID/Password(に類する情報)を共有する必要

![w:600](./conventional-3rdparty.drawio.svg)

# 各サービスごとに認証が必要

また、サービスごとにIDが管理されているため、ユーザは複数のIDを管理する必要があった

![w:600](./conventional-multi-app.drawio.svg)

# アジェンダ

1. 認可と認証
2. **OAuth 2.0**
3. OpenID Connect (OIDC)
4. OAuth 2.0 と OIDC の応用

<!-- header: 2. OAuth 2.0 -->

# OAuth 2.0

**OAuth 2.0** =
リソース所有者が自身の所有するリソースへのアクセス権を第三者アプリケーションに安全に委任するための認可フレームワーク。\
様々な仕様がRFCなどに定義されている。

# OAuth 2.0

1. OAuth 2.0 の基本的な概念と登場人物
   (リソースオーナー、リソースサーバー、クライアントアプリ、認可サーバー)
2. OAuth 2.0 のプロトコルフロー
3. OAuth 2.0が従来の認証方式の抱える課題を如何に解決したか？
4. OAuth 2.0 実際のリクエスト例
5. OAuth 2.0とセキュリティ （インプリシットフロー非推奨、PKCE、state検証）

# OAuth 2.0 の基本的な概念と登場人物

ここからはOAuthの仕様を解説していくので用語を整理...

- アクセストークン: 保護されたリソースにアクセスするための鍵
- リソースオーナー: リソースの所有者（例: Google Photo のユーザー）
- クライアントアプリ:
  リソースにアクセスしたい第三者アプリケーション（認可サーバ目線だとクライアント）
- リソースサーバー: リソースを保管しているサーバー
- 認可サーバー:アクセストークンを発行するサーバー
- 認可グラント:アクセストークンを取得するための方法
- スコープ: リソースへのアクセス範囲

# OAuth 2.0 の基本的な概念と登場人物

図にするとこんな感じ...

![w:500](./oauth.drawio.svg)

事前に認可サーバへのリクエストを通じて「クライアントアプリに特定の操作を許可する」というフローを追加した！

# OAuth 2.0 のプロトコルフロー

OAuth 2.0のフローには複数あるが[*]、代表的な Authorization Code
Grantフロー(認可コードグラントフロー)について解説

![h:420](https://www.mermaidchart.com/raw/f74a607a-0ace-4a08-bebe-241cab8d3008?theme=light&version=v0.1&format=svg)

[*]: https://qiita.com/TakahikoKawasaki/items/200951e5b5929f840a1f

# OAuth 2.0が従来の認証方式の抱える課題を如何に解決したか？

パスワードをクライアントアプリに共有する必要がなくなった。\
= パスワードではなくアクセストークンを3rdパーティのアプリは利用する。

> 保護されたリソースにアクセスする為にリソースオーナーのクレデンシャルを使う代わりに,クライアントはアクセストークンを取得する.
> **アクセストークンとは,ある特定のスコープ,期間およびその他のアクセス権に関する情報を示す文字列**である.[**]

[**]: https://openid-foundation-japan.github.io/rfc6749.ja.html

# OAuth 2.0 実際のリクエスト例

実際のリクエスト例を見てみる（GoogleDriveを利用する場合[***]）。

[***]: https://developers.google.com/identity/protocols/oauth2/web-server?hl=ja#httprest

# 認可リクエスト

以下のURLにアクセスすることで認証・認可画面が表示され、ユーザに認証とリクエストする権限への同意(=認可)を得ることができる.

```
https://accounts.google.com/o/oauth2/v2/auth?
 scope=https%3A//www.googleapis.com/auth/drive.metadata.readonly%20https%3A//www.googleapis.com/auth/calendar.readonly&
 access_type=offline&
 include_granted_scopes=true&
 response_type=code&
 state=state_parameter_passthrough_value&
 redirect_uri=https%3A//oauth2.example.com/code&
 client_id=1234567
```

# 認可の結果の返却

ユーザが同意する場合、しない場合、認証に失敗した場合のいずれの場合もクライアントアプリにリダイレクトする。
クエリパラメータに成否に伴って値が付与される。

## 認可した場合

```
https://oauth2.example.com/auth?code=4/P7q7W91a-oMsCeLvIaQm6bTrgtp7
```

## 認可しなかった場合

```
https://oauth2.example.com/auth?error=access_denied
```

# アクセストークン発行

クライアントアプリは認可コードを持ってアクセストークンを要求する。

```
POST /token HTTP/1.1
Host: oauth2.googleapis.com
Content-Type: application/x-www-form-urlencoded

code=4/P7q7W91a-oMsCeLvIaQm6bTrgtp7&
client_id=your_client_id&
client_secret=your_client_secret&
redirect_uri=https%3A//oauth2.example.com/code&
grant_type=authorization_code
```

# アクセストークンの返却

正常なリクエストの場合、以下のレスポンスを得る

```json
{
  "access_token": "1/fFAGRNJru1FTz70BzhT3Zg",
  "expires_in": 3920,
  "token_type": "Bearer",
  "scope": "https://www.googleapis.com/auth/drive.metadata.readonly https://www.googleapis.com/auth/calendar.readonly",
  "refresh_token": "1//xEoDL4iW3cxlI7yDbSRFYNG01kVKM2C-259HOF2aQbI"
}
```

# OAuth 2.0 とセキュリティ

1. インプリシットフローの回避
2. PKCE (Proof Key for Code Exchange) の導入
3. stateの検証

# 1. インプリシットフローの回避

OAuth2.0の複数のプロトコルフローのうちインプリシットフローは非推奨とされている
(**OAuth2.0 Security Best Current Practice** や **OAuth 2.1**)。
インプリシットフローは認可コードではなくアクセストークンをリダイレクトを通じて直接クライアントアプリが受け取るフローであり、以下の観点から脆弱性が指摘されている。

- アクセストークンの漏洩:アクセストークンがブラウザの履歴やHTTPリファラーヘッダーに残るため、漏洩のリスクが高い。
- アクセストークンのリプレイ攻撃:漏洩したアクセストークンが悪意のある第三者によって使用される可能性がある。
- トークン置換攻撃への脆弱性:Implicitフローでは、認可サーバーはクライアントを認証しないため、トークン置換攻撃に対して脆弱である。

# 2. PKCE (Proof Key for Code Exchange) の導入

PKCE は、認可コード横取り攻撃を防止するためのセキュリティ機構です。\
特に**PublicClient**で認可コードグラントフローを使用する場合には、PKCEの導入が必須\
PKCEを使用することで、悪意のある第三者アプリケーションが認可コードを盗み出してアクセストークンを取得することを防ぐことができる。

# クライアントタイプ

PublicClientではクライアント認証を行わない。

- Confidential Client: クライアントシークレットを安全に管理できるクライアント
- Public Client:
  SPAやネイティブアプリなどクライアントシークレットを安全に管理できないクライアント

# 認可コード横取り攻撃

# PKCEが認可コード横取り攻撃を防ぐ機構

# 3. state パラメータの使用

`state` パラメータは、CSRF (Cross-Site Request
Forgery)攻撃から保護するために使用されます。認可リクエストにランダムな文字列を`state`パラメータとして含め、認可レスポンスでその値を検証することで、攻撃者が偽の認可レスポンスを送り込むことを防ぎます。

# 参考文献

- [RFC6749](https://openid-foundation-japan.github.io/rfc6749.ja.html)
- [OpenID Connect Core 1.0 incorporating errata set 1](https://openid-foundation-japan.github.io/openid-connect-core-1_0.ja.html)
- [一番分かりやすい OAuth の説明](https://qiita.com/TakahikoKawasaki/items/e37caf50776e00e733be)
- [一番分かりやすい OpenID Connect の説明](https://qiita.com/TakahikoKawasaki/items/498ca08bbfcc341691fe)
- [PKCE: 認可コード横取り攻撃対策のために OAuth サーバーとクライアントが実装すべきこと](https://qiita.com/TakahikoKawasaki/items/00f333c72ed96c4da659)
- [OAuth/OIDCをまとめてみる(下書き段階です！)](https://zenn.dev/calloc134/articles/5e8da6c491e720)

# Appendix

# なぜわざわざ認可コードを利用するのか(インプリシットフローが非推奨な理由)...

アクセストークンの漏洩とアクセストークンのリプレイ攻撃に対して脆弱になるため。

インプリシットフローでは、認可サーバーは認可コードを発行する代わりに、**アクセストークンとIDトークンを直接クライアントに返す**。
そして、トークンは**クライアントの`redirect_uri`のフラグメント部分**を介して返されるため、ブラウザの履歴やHTTPリファラーヘッダーに残る可能性があります。

# トークン発行に伴ってクライアントシークレットというパラメータがあるが、SPAやネイティブアプリの場合どうなるのか？

https://developers.google.com/identity/protocols/oauth2/web-server?hl=ja#httprest

# 各種攻撃に対する対策

- PKCE
- stateの検証

# クライアントタイプ

# アクセストークンの形式

- OAuth 2.0 の仕様では未定義
- Bearer Token, MAC タイプトークン, Proof Token など

# アクセストークンの失効

- 発行されたアクセストークンやリフレッシュトークンを失効させる仕組み

# セキュリティ

- トークン substitution 攻撃、認可コード横取り攻撃、CSRF 攻撃など
- トークンの完全性、機密性、データ生成元の認証
