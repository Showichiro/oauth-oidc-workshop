# OAuth 2.0 のプロトコルフロー

OAuth 2.0のフローには複数あるが[*]、代表的なAuthorization Code
Grantフロー（認可コードグラントフロー）について解説する。

```mermaid {scale: 0.5}
sequenceDiagram
    autonumber
    participant User as リソースオーナー
    participant Client as クライアントアプリ
    box 認可サーバ
      participant AuthorizationEndpoint
      participant AuthorizationDecisionEndpoint
      participant TokenEndpoint
    end
    participant ResourceServer as リソースサーバ

    User->>Client: リクエスト
    Client->>AuthorizationEndpoint: 認可リクエスト
    AuthorizationEndpoint->>User: 認証・認可同意のプロンプト
    User-->>AuthorizationDecisionEndpoint: 認証＆同意
    AuthorizationDecisionEndpoint-->>Client: 認可コード付きでクライアントアプリにリダイレクト
    Client->>TokenEndpoint: 認可コードでアクセストークンを要求(クライアントアプリのサーバ認証も行う)
    TokenEndpoint-->>Client: アクセストークンを返却
    Client->>ResourceServer: アクセストークン付きでリクエスト
    ResourceServer-->>Client: リソースデータを返却

```

[*]: https://qiita.com/TakahikoKawasaki/items/200951e5b5929f840a1f

---

## 認証・認可同意のプロンプトの具体例

Googleカレンダーを利用する場合。

<img src="/oauth2.0-example.drawio.svg" class="h-90" />
