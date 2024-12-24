# OAuth 2.0 実際のリクエスト例

実際のリクエスト例を見てみる（GoogleDriveを利用する場合[***]）。\
登場するパラメータの紹介もつけるが、詳細は[RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749#section-4.1)を参照のこと。

[***]: https://developers.google.com/identity/protocols/oauth2/web-server?hl=ja#httprest

---

## 認可リクエスト

以下のURLにアクセスすることで認証・認可画面が表示され、ユーザに認証とリクエストする権限への同意（=認可）を得ることができる。

```
https://accounts.google.com/o/oauth2/v2/auth?
 scope=https%3A//www.googleapis.com/auth/drive.metadata.readonly%20https%3A//www.googleapis.com/auth/calendar.readonly&
 access_type=offline&
 response_type=code&
 state=state_parameter_passthrough_value&
 redirect_uri=https%3A//oauth2.example.com/code&
 client_id=1234567
```

---

### 認可リクエストの各パラメータについて

| パラメータ名  | 説明                                                       |
| :-----------: | :--------------------------------------------------------- |
|     scope     | 認可する権限範囲                                           |
| response_type | codeまたはtoken.認可コードグラントフローの場合は `code`    |
|     state     | 任意のランダムな文字列（役割については後述）               |
| redirect_uri  | リダイレクト先の指定（事前に登録しているものから一つ選ぶ） |
|   client_id   | クライアントアプリのID                                     |

---

## 認可の結果の返却

ユーザが同意する場合、しない場合、認証に失敗した場合のいずれの場合もクライアントアプリにリダイレクトする。
クエリパラメータに成否を表す値が付与される。

### 認可した場合

```
https://oauth2.example.com/auth?code=4/P7q7W91a-oMsCeLvIaQm6bTrgtp7
```

### 認可しなかった場合

```
https://oauth2.example.com/auth?error=access_denied
```

---

### リダイレクト時の各パラメータについて

| パラメータ名 | 説明                                       |
| :----------: | :----------------------------------------- |
|     code     | 認可コード（アクセストークンの発行に利用） |
|    error     | 認可が失敗した理由を表す文字列             |

---

## アクセストークン発行

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

### アクセストークン発行時の各パラメータについて

| パラメータ名  | 説明                                                |
| :-----------: | :-------------------------------------------------- |
|     code      | 認可コード                                          |
|   client_id   | クライアントアプリのID                              |
| client_secret | クライアントアプリのシークレット                    |
| redirect_uri  | リダイレクトURI（認可リクエスト時に利用したもの）   |
|  grant_type   | 認可コードグラントフローの場合 `authorization_code` |

---

## アクセストークンの返却

正常なリクエストの場合、以下のレスポンスを得る。

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

### レスポンスの各パラメータについて

| パラメータ名  | 説明                                       |
| :-----------: | :----------------------------------------- |
| access_token  | アクセストークン                           |
|  expires_in   | アクセストークンの有効期限(**秒**)         |
|  token_type   | トークンの種別                             |
|     scope     | 認可権限の範囲                             |
| refresh_token | アクセストークンを再発行するためのトークン |

---

## 実際にアクセストークンを取得してみる

https://developers.google.com/oauthplayground/

---

## アクセストークンの利用・リソースサーバでの検証

クライアントアプリからリソースサーバへのリクエストの際にアクセストークンを付与してリクエストする。\
リソースサーバへのリクエストにアクセストークンを付与する仕様は[RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750)に定められている（Authorizationヘッダーに埋め込むのが一般的）。 \
リソースサーバ側でアクセストークンの情報を取得するための仕様は[RFC 7662](https://datatracker.ietf.org/doc/html/rfc7662)に定められている。

```mermaid {scale: 0.33}
sequenceDiagram
    autonumber
    participant Client App as クライアントアプリ
    participant Resource Server as リソースサーバ
    participant Authorization Server as 認可サーバ

    Client App->>Resource Server: 保護されたリソースへのリクエスト (アクセストークンを含む)
    activate Resource Server
    Resource Server->>Resource Server: アクセストークンが含まれているか確認
    Resource Server-->>Authorization Server: アクセストークンの検証リクエスト(RFC 7662)
    activate Authorization Server
    Authorization Server-->>Resource Server: アクセストークンの有効性、スコープ、その他のクレームに関する情報
    deactivate Authorization Server
    Resource Server->>Resource Server:  アクセストークンの有効性を確認
    alt アクセストークンが有効
        Resource Server->>Resource Server: スコープを確認
        alt スコープがリクエストされたリソースへのアクセスを許可する場合
            Resource Server-->>Client App: リクエストされたデータ
        else スコープがリクエストされたリソースへのアクセスを許可しない場合
             Resource Server-->>Client App: エラーレスポンス (権限不足)
        end
    else アクセストークンが無効
         Resource Server-->>Client App: エラーレスポンス (無効なトークン)
    end
    deactivate Resource Server
```

---

## スコープの設計について

リソースサーバでのアクセストークンの検証の際、アクセスしようとするリソースとスコープの設定範囲の突合を行う必要がある。 \
OAuth 2.0の仕様ではリソースとスコープの対応関係に関する設計については定められていないため、リソースサーバの特性に合わせて設計する必要がある。 \
以下、少しOAuth2.0自体から離れてスコープ設計について考察する。

---

# 作成中
