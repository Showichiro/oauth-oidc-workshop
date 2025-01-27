# OpenID Connectのフロー

以下のように、認証情報を扱うIDトークンの発行をする。

<img src="/assets/oidc.drawio.svg" />

---

# OpenID Connectのフロー

IDトークンには偽装防止のために署名をつける。

<img src="/assets/oidc-signature.drawio.svg" />

---

# OpenID Connectのフロー

OAuth2.0のアクセストークンの発行と同時にIDトークンを発行することも可能。以下は認可コードグラントフローの場合。

<img src="/assets/oidc-with-access-token.drawio.svg" />

---

# OpenID Connectのフロー

詳細なシーケンスは以下のようになる。

```mermaid {scale:0.5}
sequenceDiagram
    autonumber
    participant User as ユーザ
    participant Client as RP(クライアントアプリ)
    box OP(認証・認可サーバ)
      participant AuthorizationEndpoint as AuthorizationEndpoint
      participant TokenEndpoint
      participant UserInfoEndpoint
    end

    note over Client,AuthorizationEndpoint: スコープにopenidを含める
    Client->>AuthorizationEndpoint: 認証リクエスト (認可エンドポイントへリダイレクト)
    AuthorizationEndpoint->>User: ユーザー認証と認可を要求
    User->>AuthorizationEndpoint: 認証情報を入力、認可を許可
    AuthorizationEndpoint->>Client: 認可コードをリダイレクト
    Client->>TokenEndpoint: 認可コード、クライアントID、リダイレクトURIなどを送信 (トークンリクエスト)
    TokenEndpoint->>Client: アクセストークンとIDトークンを返す (トークンレスポンス)
    Client->>UserInfoEndpoint: アクセストークンを送信 (プロフィール情報リクエスト)
    UserInfoEndpoint->>Client: プロフィール情報を返す
```
