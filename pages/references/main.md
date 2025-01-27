# 参考文献

以下は、OAuth/OIDCに関する参考文献のリストです。

## RFCなど

- **RFC 6749** - The OAuth 2.0 Authorization Framework
  - OAuth 2.0の基本となるフレームワークを定義した仕様書
- **RFC 6750** - The OAuth 2.0 Authorization Framework: Bearer Token Usage
  - アクセストークンを保護リソースエンドポイントに渡す方法を定義した仕様書
  - Authorizationヘッダー、リクエストボディ、クエリパラメータの3つの方法を規定
- **RFC 7636** - Proof Key for Code Exchange by OAuth Public Clients (PKCE)
  - パブリッククライアントにおける認可コード横取り攻撃への対策を定めた仕様書
  - **code_challenge** と **code_verifier** を用いて認可コードの安全性を高める

---

- **OpenID Connect Core 1.0**
  - OAuth 2.0上にアイデンティティレイヤーを付与した仕様書
- **OpenID Connect Discovery 1.0**
  - OpenIDプロバイダーに関する情報を取得するための仕様書
- **RFC 7519** - JSON Web Token (JWT)
  - IDトークンの形式として用いられるJWTを定義した仕様書
- **RFC 7515** - JSON Web Signature (JWS)
  - JWTの署名に関する仕様書
- **RFC 7516** - JSON Web Encryption (JWE)
  - JWTの暗号化に関する仕様書
- **RFC 7517** - JSON Web Key (JWK)
  - JSON形式の鍵に関する仕様書

---

- **RFC 7518** - JSON Web Algorithms (JWA)
- JSON Webアルゴリズムに関する仕様書
- **RFC 6819** - OAuth 2.0 Threat Model and Security Considerations
  - OAuth 2.0の脅威モデルとセキュリティに関する考慮事項をまとめた仕様書
- **RFC 7009** - OAuth 2.0 Token Revocation
  - アクセストークンとリフレッシュトークンの取り消し方法を定めた仕様書
- **RFC 7662** - OAuth 2.0 Token Introspection
  - アクセストークンやリフレッシュトークンの情報を取得する方法を定めた仕様書
- **RFC 8705** - OAuth 2.0 Mutual TLS Client Authentication and Certificate Bound Access Tokens (MTLS)
  - クライアント証明書によるOAuthクライアント認証と、証明書に紐づくトークンに関する処理を定義した仕様書
- **OAuth 2.0 Multiple Response Type Encoding Practices**
  - 複数のレスポンスタイプを扱う際のエンコーディング方法を規定した仕様書

---

- **Financial-grade API Security Profile 1.0 - Part 2: Advanced** (FAPI)
  - 金融業界など高セキュリティが求められるAPIのためのセキュリティプロファイルを定義した仕様書
- **Financial-grade API: JWT Secured Authorization Response Mode for OAuth 2.0 (JARM)**
  - 認可レスポンスをJWT形式で返すための仕様書
- **OAuth 2.0 Form Post Response Mode**
  - `response_mode`パラメータの値としてform_postを指定した場合の処理を定義した仕様書

---

## 日本語記事

- [一番分かりやすい OAuth の説明](https://qiita.com/TakahikoKawasaki/items/e37caf50776e00e733be)
- [一番分かりやすい OpenID Connect の説明](https://qiita.com/TakahikoKawasaki/items/498ca08bbfcc341691fe)
- [IDトークンが分かればOpenID Connectが分かる](https://qiita.com/TakahikoKawasaki/items/8f0e422c7edd2d220e06)
- [OAuth & OpenID Connect 関連仕様まとめ](https://qiita.com/TakahikoKawasaki/items/185d34814eb9f7ac7ef3)
  - OAuthとOpenID Connectの関連仕様の一覧記事
- [OAuth/OIDCをまとめてみる(下書き段階です！)](https://zenn.dev/calloc134/articles/5e8da6c491e720)
- [PKCE: 認可コード横取り攻撃対策のためにOAuthサーバーとクライアントが実装すべきこと](https://qiita.com/TakahikoKawasaki/items/00f333c72ed96c4da659)
- [図解 DPoP (OAuth アクセストークンのセキュリティ向上策の一つ)](https://qiita.com/TakahikoKawasaki/items/34c82fb5c0595b6fc289)
- [実装者による CIBA 解説](https://qiita.com/TakahikoKawasaki/items/9b9616b999d4ce959ba3)
- [実装者による Financial-grade API (FAPI) 解説](https://qiita.com/TakahikoKawasaki/items/83c47c9830097dba2744)
