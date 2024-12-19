---
theme: apple-basic
title: OAuth2.0/OIDC 概説
colorSchema: light
---

# OAuth2.0/OIDC 概説

---

# アジェンダ

1. 認可と認証
2. OAuth 2.0
3. OpenID Connect (OIDC)
4. OAuth 2.0 と OIDC の応用

---

# アジェンダ

1. **認可と認証**
2. OAuth 2.0
3. OpenID Connect (OIDC)
4. OAuth 2.0 と OIDC の応用

---

# 1. 認可と認証

- **認証** と **認可** の違いについて
- 従来の認証方式における課題

---

# **認証** と **認可** の違いについて

- 認証(Authentication)
  - ユーザーの身元を確認すること(私は～～です)
- 認可(Authorization)
  - ユーザーにリソースにアクセスする許可を与えること(～～をしてよいです)

---

# OAuth2.0/OIDC の文脈に寄せた具体例

- 認証(Authentication)
  - ○○ としてログインする
- 認可(Authorization)
  - ○○ の代わりに「写真を閲覧してもよい」「写真をアップロードしてよい」「写真を削除してよい」

---

# 従来の認証方式における課題

1. パスワードの共有
   - 第三者サービスを利用するために、ユーザーは自分のパスワードをそのサービスに提供する必要があった
2. 各サービスごとに認証が必要
   - 多くのウェブサイトやサービスで、ユーザーは個別にアカウントを作成し、パスワードを管理する必要があった

---

# 従来の認証方式

従来認証方式ではクライアントがサーバに向けて ID/Password を入力しユーザのデータにアクセス

<img src="./conventional.drawio.svg" width="600px" >

---

# パスワードの共有

そのため、3rd パーティのアプリがユーザのデータにアクセスするためには、ID/Password(に類する情報)を共有する必要

<img src="./conventional-3rdparty.drawio.svg" width="600px">

---

# 各サービスごとに認証が必要

また、サービスごとに ID が管理されているため、ユーザは複数の ID を管理する必要があった

<img src="./conventional-multi-app.drawio.svg" width="600px">

---

# アジェンダ

1. 認可と認証
2. **OAuth 2.0**
3. OpenID Connect (OIDC)
4. OAuth 2.0 と OIDC の応用

---

# OAuth 2.0

**OAuth 2.0** =
リソース所有者が自身の所有するリソースへのアクセス権を第三者アプリケーションに安全に委任するための認可フレームワーク。\
様々な仕様が RFC などに定義されている。

---

# OAuth 2.0

1. OAuth 2.0 の基本的な概念と登場人物
   (リソースオーナー、リソースサーバー、クライアントアプリ、認可サーバー)
2. OAuth 2.0 のプロトコルフロー
3. OAuth 2.0 が従来の認証方式の抱える課題を如何に解決したか？
4. OAuth 2.0 実際のリクエスト例
5. OAuth 2.0 とセキュリティ （インプリシットフロー非推奨、PKCE、state 検証）
6. トークンの再発行、検証、取り消し

---

# OAuth 2.0 の基本的な概念と登場人物

ここからは OAuth の仕様を解説していくので用語を整理...

- アクセストークン: 保護されたリソースにアクセスするための鍵
- リソースオーナー: リソースの所有者（例: Google Photo のユーザー）
- クライアントアプリ:
  リソースにアクセスしたい第三者アプリケーション（認可サーバ目線だとクライアント）
- リソースサーバー: リソースを保管しているサーバー
- 認可サーバー:アクセストークンを発行するサーバー
- 認可グラント:アクセストークンを取得するための方法
- スコープ: リソースへのアクセス範囲

---

# OAuth 2.0 の基本的な概念と登場人物

図にするとこんな感じ...

<img src="./oauth.drawio.svg" width="600px">

事前に認可サーバへのリクエストを通じて「クライアントアプリに特定の操作を許可する」というフローを追加した！

---

# OAuth 2.0 のプロトコルフロー

OAuth 2.0 のフローには複数あるが[*]、代表的な Authorization Code
Grant フロー(認可コードグラントフロー)について解説

<img src="https://www.mermaidchart.com/raw/f74a607a-0ace-4a08-bebe-241cab8d3008?theme=light&version=v0.1&format=svg" height="600px">

[*]: https://qiita.com/TakahikoKawasaki/items/200951e5b5929f840a1f

---

# OAuth 2.0 が従来の認証方式の抱える課題を如何に解決したか？

パスワードをクライアントアプリに共有する必要がなくなった。\
= パスワードではなくアクセストークンを 3rd パーティのアプリは利用する。

> 保護されたリソースにアクセスする為にリソースオーナーのクレデンシャルを使う代わりに,クライアントはアクセストークンを取得する.
> **アクセストークンとは,ある特定のスコープ,期間およびその他のアクセス権に関する情報を示す文字列**である.[**]

[**]: https://openid-foundation-japan.github.io/rfc6749.ja.html

---

# OAuth 2.0 実際のリクエスト例

実際のリクエスト例を見てみる（GoogleDrive を利用する場合[***]）。

[***]: https://developers.google.com/identity/protocols/oauth2/web-server?hl=ja#httprest

---

# 認可リクエスト

以下の URL にアクセスすることで認証・認可画面が表示され、ユーザに認証とリクエストする権限への同意(=認可)を得ることができる.

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

---

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

---

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

---

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

---

# OAuth 2.0 とセキュリティ

1. インプリシットフローの回避
2. PKCE (Proof Key for Code Exchange) の導入
3. state の検証

---

# 1. インプリシットフローの回避

OAuth2.0 の複数のプロトコルフローのうちインプリシットフローは非推奨とされている
(**OAuth2.0 Security Best Current Practice** や **OAuth 2.1**)。
インプリシットフローは認可コードではなくアクセストークンをリダイレクトを通じて直接クライアントアプリが受け取るフローであり、以下の観点から脆弱性が指摘されている。

- アクセストークンの漏洩:アクセストークンがブラウザの履歴や HTTP リファラーヘッダーに残るため、漏洩のリスクが高い。
- アクセストークンのリプレイ攻撃:漏洩したアクセストークンが悪意のある第三者によって使用される可能性がある。
- トークン置換攻撃への脆弱性:Implicit フローでは、認可サーバーはクライアントを認証しないため、トークン置換攻撃に対して脆弱である。

---

# 2. PKCE (Proof Key for Code Exchange) の導入

PKCE は、認可コード横取り攻撃を防止するためのセキュリティ機構です。\
特に**PublicClient**で認可コードグラントフローを使用する場合には、PKCE の導入が必須\
PKCE を使用することで、悪意のある第三者アプリケーションが認可コードを盗み出してアクセストークンを取得することを防ぐことができる。

---

# クライアントタイプ

PublicClient ではクライアント認証を行わない。

- Confidential Client: クライアントシークレットを安全に管理できるクライアント
- Public Client:
  SPA やネイティブアプリなどクライアントシークレットを安全に管理できないクライアント

---

# ネイティブアプリで認可コードを横取りされるシーケンス

<img src="https://mermaid.ink/svg/pako:eNqlVU1rE0EY_ivLnGpYQ5NtPnYOPXkR9FLpRXJZstN2ocnG7a5YQyAzq1CRkhIqFbzUKjEmtkWCoKTSH_O2a_wXzsymIckmzUr3sDu8H8_7vs8-M1NFRdskCKMd8swj5SJ5YBmbjlEqlBX-VAzHtYpWxSi7SnDYD5r-oP466hqcdK_PejNdwemnQasB7Bz8r8A-AzsBvwf-nlwcgd-ZgdbZv26cA_sB_gX4B_x9SzNTOOF75L2_ujrqDSsB6wSvGkDPgDJgb0epwJqiNd4X-yVaE2W7QN8B_cDDQsgRDIdcOBNWJOpvgcQhuZnHsr5cNwctGnxnw0YXIYkBInTgG4pEEe5ph30rS8Lg1-Us3yTo3vraQ6BdYF1Z-xL84_W1R3J9KuGO74WNGEXXem64ZC75Ufskt9w_aF8Abd8ETsw5zt6cecazY5eU_p70v7nqvwe6H6WA07JwfJMsJGB6EKGDiAixkkjcUo2zwjX457AN9AhoK5ov_lVkgERiStXj6hgZI3zwYn_r9Oryo8yObowYKo6BeDcdT2yNcOcJjF6oHWVp5i--k2Tj7N05Tf2nVOJwMnlMxGEG6JeJJPpzPBKpqESckmGZ_ESvij4KyN0iJVJAmC9NsmF4224BFco1Hmp4rv1kt1xE2HU8oiKvYvKxhhcAwhvG9g638vMW4Sp6gXBKzyezK8t6Or-S1fWUnkmraBdhLacl01pGz-fSus4_ea2mope2zSGWk3r45DI5LZXVU5rEeyqdQ3xiWq7tPA5vIXkZqcixvc2tYUTtHxZrpzM" >

※カスタム URL スキーム: ネイティブのアプリを URL 形式で指定する。複数のアプリ間でユニークで**なく**てよい

---

# PKCE(Proof Key for Code Exchange)

code_verifier と code_challenge によって、パブリッククライアントでも疑似的にクライアント認証を行う。

- code_verifier
  - クライアントアプリで都度生成するランダムな文字列
- code_challenge
  - code_verifier のハッシュ(SHA-265 推奨)。**ハッシュなので code_challenge から code_verifier を復元することが難しい**。

---

# PKCE を導入した認可コードグラントフロー

<img class="h-100" src="https://mermaid.ink/svg/pako:eNqtVN9v0lAU_lfIfZoJkkHHj_ZhT7765JshMU17gSbQYtcuTkLC7X0YGsxwwaEPhkW3BVkmMWDUyMIfcyjjz_DeUgxQOhe1D21z73fOd8733XsqSDFUjCS0h5_aWFfwA03Om3Ipq0fYU5ZNS1O0sqxbEXD6QD-BcwbOB6ADoPUgZtZ75R71wfkKdAS0yd4b8tAeONcc4PwAp-chX_5GLr-DjPd3d4OLUoS38GQfm1pOwyY4xzetzrTejGxxIIfUgJ4C6U1PDt2rtltv3_ub_EpBLhaxnsdLBKvE5DPQI6AUnG9Az93a2W08QamkhXxcILbT5QLRus-yTO82L8Gp-dllxdL2ZQuHih9c522GuOAVMeuOgHQXccezCzL94vi9hMSFd7Sc7LaCNui-AA484AteyrgF5F1AEtK9GTaBnAN57aui4j_qcmdb-D435OfcEK95FjqY6xLZWi-TdNcP5H8wbFNh6zQrx69xAqQNNTIZv3ev3gJ5A04DSCeg3LTfmo1oKHOINWGa8Eu3cgeANCbfa7PDIa-GdNzTodus_6NJq4fwLlYxcSbXYyAfgVysRJPL5RAURSVslmRNZROxwivIIquASziLJPar4pxsF60syupVBpVty3h0oCtIskwbR5FdVlkr_gBFUk4u7rFVNvSQVEHPkBQXM7HUzraYyOykRDEuJhNRdIAkIS3EEkJSzKQTosg-GaEaRc8Ng6XYjonzJ51MC_GUGBe8fI-9TT8_VjXLMB_Op7g3zKPINOx8wUdUfwHkwSZ6">

---

# PKCE によって認可コード横取り攻撃を防ぐことができる理由

悪意のあるアプリは`code_verifier`を特定できないため、横取りはできてもアクセストークンを発行できない。
<img src="https://mermaid.ink/svg/pako:eNqlVE1r20AQ_StmTzG4JrbiD-mQU6899VYMRUhrW2BLriKFpsbg3c0hTVPkhqSmEChNneAqpCY4pbR16Y8ZJOtndCXZpkRR0tI9rMTu2_fezOxOFymGipGEtvAzG-sKfqjJDVNu1_QMHx3ZtDRF68i6lfEvPwbnDtAJsE9AR0BPgU2B7d2CpK6_6wD5DIQCfRUhh8DcJDJwX3vOBOgXYDNgAz4nMfwg0J8hgH4D6kbI_RUyntO8PdjcTEpIS9mQmO-MQ2K2l1kLM_FUacqtFtYbGOihN7gA2s_GGrJiaduyhVNNJ9e5fJr7yEQwngEZL88dBufEv6IxWdq59Ij-JEvEQsbz6wGQMyBvsne4TavcKmV0GsFf-mPXc94C3Y_JVHxvdhaFShFICSvcD0v0Iy5RlA5et2mcqczaygaQIZD3N0zyqKM0bGNTq2vYBOJy3H-V8zaT95m4WKbeSVwwf3Qyvz79R70bMR1EYe1yZaAE-gSYA4wB_QrszOuP_MlRMGPchTe68o-HqVp3Vj98JPxlsZn362R-eZRZSyvM_N334MNBLJX9i7uBcqiNzbasqbwFdcMDNWQ1cRvXkMR_VVyX7ZZVQzW9x6GybRmPd3QFSZZp4xyyOyqnXXQsJNXl1hZf5V0DSV30HEkFsZovb6yLxepGWRQLYqmYQztIEipCviiUxGqlKIr8UxV6OfTCMDjFel6MR6VUEQplsSBEfE-izQU_VjXLMB_FbTPqnjlkGnajuUD0fgMhbNeu">

---

# 3. state パラメータの使用

- state
  - クライアントが認可リクエストを開始する際に生成するランダムな文字列で、リダイレクト URI を介してクライアントに返送される。
  - クライアントアプリ側で認可リクエスト時に生成した値をキャッシュしておき、リダイレクト時に認可サーバからの戻りとの一致を確認する

---

# state を検証しない場合

攻撃者の作った認可リクエストを踏まされて、不正にアクセストークンを取得される

<img scr="https://mermaid.ink/svg/pako:eNq1Vctu2kAU_RVrVlSiKEB42Iusuumim1bdVGwscBJL4VFjV00REp5JJSpaQawoJFI2pBGl0IAqWikpSfiYCw79i84Yg0JsExSpXoztueNzzr1zrqeAktmUhASUl95qUiYpPZPFLUVMJzIcvXKiospJOSdmVM48GJgGmZQ-OkOT086423cNmedfJ80q4B6Q74DPAJ8C6QMpu4C0v4yrPcC_gVwBqdFxumY6ztmfbmzMnwXOxG1zrwp6F3QMuGJjkDYjxC3Al5QKsDG6PjHLNc7HIqTEdJAflqby65fPQe_NERkSxnR84qSdZ-mgtWD7Fqfxt6SPhg3ON7qpCByQhpVNB_Ae4Cbgb0AOWYpMQdnmEJOq_E5UpftlnL8ybkdxVsud8-VVCg1kn9V_qgYPQW-DXnfQe2yAc36xGDQ-aV2B3potNCZN3fyJV8vi3tcrU1rxvhX_xCiHB6AfP2KHU9LDBbiTwh3vOUXM9n6BzF1QxznvImjREAs94NFXbrLMi4ZVG69vPFXSxhlUqFNAb876wQPCY3NZnLlxYHcic1_PahXbI__Pny5q3Km65tkJcyA2mEh8DoQAqXtCL639knxvj_9MGp9X9tyD5X6UiMUuoMs67K9km6Bz-6s2GhyBvr_0lzs3pW0bfG0xXIJ-MUO1yc0WVWaMq4fjmzryo7SkpEU5Rc-aAiNIIHVbSksJJNDHlLQpajtqAiUyRbpU1NTsq91MEgmqokl-pOVStFj20YSETXEnT2fpsYGEAnqPhCAfD0TX1_hQfD3K80E-EvKjXSSEY-FAKBzh47EQz9NbPFz0ow_ZLIVYC_DTKxaJhYNRPhi28N5YQRtfSslqVnkxPR-tY9KPlKy2tW2vKP4DWjsG7A">

---

# state を検証する認可コードグラントフロー

クライアントアプリは state を発行して認可リクエストのパラメータとして付与する。リダイレクト時にキャッシュした state とリダイレクトのパラメータの state を検証する

<img src="https://mermaid.ink/svg/pako:eNqVVU1P2zAY_iuRT0XqEG2BNjlw2TRph11Au0y9RI2BSv1am05jFVJsjwEC1A6hAQe0wSbatYNqYtM-ytYf8zZt9i9mJ6F0JC0lB8fx-8TP4_d9bJdQIqthpKACflbEmQR-kFSX8mo6npH4k1PzejKRzKkZXQLaBPYJ6EegJ8AugG14Md29VneXWcaaN2SdNMzzC_9QfccsN4F-A3YJrMJbB-O0Xt57c3PeQUUSA6JrAHsPpF7QVR2bBkfs9vbedTcqQPbBIEBbwBjQ78CqAk4anfaReXYwgqwvXbmSyuoCSGtAf3IIZ_hrkE77WArYpMDe2FqOxXpou9M6ALIz4RCoCT35nGNu5qP_Kfg8-RhCfCe-IUn2jntWbNUugdSugLvWKel-oWPLHvx7bEo7fmHHN_nfA7W02ntADqWASAUvNa8U-2xXbePJ_CMgze7ZB-u07K0jkHOglLduYjR8e2oGFudnuJE6rxwxSo__EhrecR_N_9tn_E1y0_5bQDcHZJvlt-affXerCHrusyMB5B1y3geSWm-talbcI0BN6dJ1aLvzw7DWv_IqAd1yAMO3lo9nRFyYvOXuLmHqpi3BtZ4UGJl5s9IAakxcM99a6KGm9M_gUHm9w1_W8fb1nGN4DKcK2C93vAR1IK9uSZ-_vpp9Glya66e9ymspcH9h_qFzMPMKchEW-901qm5-cEZDQZTG-bSa1Pg1UBLDcaQv4zSOI4V3NbyoFlN6HMUzqxyqFvXswkomgRQ9X8RBVMxpXLx7ayBlUeULCiJ-riOlhF4gJSTHJmenp-RwbHpWlkPyTDiIVpASiUYmw5EZORYNyzJ_xSKrQfQym-VTTE3KzhOdiUZCs3IoYs_31A6682MtqWfzj52ry77BgiifLS4tu4jVf7BwsFw" class="h-100">

---

# state の検証が CSRF を防ぐ理由

悪意のあるアプリはクライアントアプリが生成する state がわからないため、クライアントアプリが必ずトークンを発行せずエラーとする。

<img src="https://mermaid.ink/svg/pako:eNqdVE1v2kAQ_SvWnohEUYDwYR9yaVWph14S9VJxsbCTWAofBbtqipDY3aQlQhEURYkqtQfSCii0QRWq2ggSfsxgx_0XXS8OghhU2j2s1zuz772ZnZ0CSmYUFUkor74w1HRSfaTJuzk5lUgLbGTlnK4ltayc1gUgPaBfgHwGcgG0D7Ts9bFOB1ad2qUjr8m-6JqX_cWmzolZ7QH5AXQItMbmic9knmI-2NycriXBIh3rsAr4EjABUnExaMeRSdpArphAIPXx9QerXBN8joWWHPX0K4-k_GzrCeDeFNFBIoTNa17aqXYPLYftc8767xIejxqCb3xTkQSgDR5NF8ghkCaQFtAzJ0RHQdnlkJO69lLW1fvJmf463J7krBa74MvrDBroO-fWJmrICHAH8LmHfskFePfnk8HsdnsIuH3nWLeb2PpOVovi3umVKbm9z-3HDuXoFPD7_7hhQVH_noGZGGaKz6vi7vLn2BYr6nr3XUWzguYrYrYavQ9xkSDrV4Onxfr2yW5WvWeW6mNvZlBhRQK46cryHl6iAsgAKAXyE2jL2cMVIMe8DM0S86ub1TPz5tzBLmHOyQr1I38_V0zO1BG3b49aZq38z-xtXupD823ztvZG8D3c3no8SRxDZ-mx6bVVaq0hP0qpuZSsKaztFRySBNL31JSaQBJbKuqObOzrCZRIF5mrbOiZ7YN0Ekl6zlD9yMgqTKbbJZG0I-_n2S7rY0gqoFdICorxQHRjXQzFN6KiGBQjIT86QFI4Fg6EwhExHguJIvvEw0U_ep3JMIj1gDgZsUgsHIyKwTDHe86NLr6qaHom93TSqnnH9qNcxtjdcz2KfwDSQx-w">

---

# トークンの再発行、検証、取り消し

- トークンの再発行
  - アクセストークン発行時に共に返却される`refresh_token`を利用してアクセストークンを再発行することができる
- トークンの検証
  - イントロスペクションエンドポイントなどへのリクエストを通してアクセストークンのメタデータ(トークンの有効期限、スコープ、関連付けられたクライアントなど)を取得できる
- トークンの失効
  - アクセストークンが漏洩した場合や、利用者の許可を取り消す場合など、トークンを無効化したい場合は、リヴォケーションエンドポイントへのリクエストによって無効化する

---

# 参考文献

- [RFC6749](https://openid-foundation-japan.github.io/rfc6749.ja.html)
- [OpenID Connect Core 1.0 incorporating errata set 1](https://openid-foundation-japan.github.io/openid-connect-core-1_0.ja.html)
- [一番分かりやすい OAuth の説明](https://qiita.com/TakahikoKawasaki/items/e37caf50776e00e733be)
- [一番分かりやすい OpenID Connect の説明](https://qiita.com/TakahikoKawasaki/items/498ca08bbfcc341691fe)
- [PKCE: 認可コード横取り攻撃対策のために OAuth サーバーとクライアントが実装すべきこと](https://qiita.com/TakahikoKawasaki/items/00f333c72ed96c4da659)
- [OAuth/OIDC をまとめてみる(下書き段階です！)](https://zenn.dev/calloc134/articles/5e8da6c491e720)
