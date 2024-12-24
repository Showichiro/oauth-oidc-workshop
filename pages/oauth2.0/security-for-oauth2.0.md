# OAuth 2.0 とセキュリティ

1. インプリシットフローの回避
2. PKCE (Proof Key for Code Exchange) の導入
3. stateの検証

---

# 1. インプリシットフローの回避

OAuth2.0の複数のプロトコルフローのうちインプリシットフローは非推奨とされている
(**OAuth2.0 Security Best Current Practice** や **OAuth 2.1**)。
インプリシットフローは認可コードではなくアクセストークンをリダイレクトを通じて直接クライアントアプリが受け取るフローであり、以下の観点から脆弱性が指摘されている。

- アクセストークンの漏洩：アクセストークンがブラウザの履歴やHTTPリファラーヘッダーに残るため、漏洩のリスクが高い。
- アクセストークンのリプレイ攻撃：漏洩したアクセストークンが悪意のある第三者によって使用される可能性がある。
- トークン置換攻撃への脆弱性：Implicitフローでは、認可サーバーはクライアントを認証しないため、トークン置換攻撃に対して脆弱である。

---

# 2. PKCE (Proof Key for Code Exchange) の導入

PKCEは、認可コード横取り攻撃を防止するためのセキュリティ機構。\
特に**PublicClient**で認可コードグラントフローを使用する場合には、PKCEの導入が必須。\
PKCEを使用することで、悪意のある第三者アプリケーションが認可コードを盗み出してアクセストークンを取得することを防ぐことができる。

---

# クライアントタイプ

PublicClientではクライアント認証しない。

- Confidential Client: クライアントシークレットを安全に管理できるクライアント
- Public Client:
  SPAやネイティブアプリなどクライアントシークレットを安全に管理できないクライアント

---

# ネイティブアプリで認可コードを横取りされるシーケンス

```mermaid {scale: 0.45}
sequenceDiagram
    autonumber
    participant 攻撃者
    participant 被害者
    participant 正規クライアントアプリ
    participant 認可サーバー
    participant 攻撃者アプリ

    攻撃者->>被害者: 悪意のあるアプリをインストールさせる
    被害者->>正規クライアントアプリ: リソースアクセスを要求
    正規クライアントアプリ->>認可サーバー: 認可リクエスト (リダイレクトURIにカスタムURLスキーム)
    activate 認可サーバー
    認可サーバー->>被害者: 認証と認可を要求
    被害者->>認可サーバー: 認証と認可
    認可サーバー->>被害者: 認可コード付きリダイレクト (カスタムURLスキーム)
    deactivate 認可サーバー

    被害者-->>攻撃者アプリ: **カスタムURLスキームを悪用して攻撃者アプリにリダイレクト**
    攻撃者アプリ->>攻撃者: 認可コードを送信

    攻撃者->>正規クライアントアプリ: 認可コードを送信


    正規クライアントアプリ->>認可サーバー: アクセストークン要求 (認可コード付き)
    activate 認可サーバー
    認可サーバー->>正規クライアントアプリ: アクセストークン
    deactivate 認可サーバー

    正規クライアントアプリ->>リソースサーバー: アクセストークンでリソースへアクセス
```

※カスタムURLスキーム： ネイティブのアプリをURL形式で指定する。複数のアプリ間でユニークで**なく**てよい。

---

# PKCE(Proof Key for Code Exchange)

code_verifierとcode_challengeによって、パブリッククライアントでも疑似的にクライアント認証する。

- code_verifier
  - クライアントアプリで都度生成するランダムな文字列
- code_challenge
  - code_verifierのハッシュ（SHA-265推奨）。**ハッシュなので code_challenge から code_verifier を復元することが難しい**。

---

# PKCE を導入した認可コードグラントフロー

```mermaid {scale: 0.5}
sequenceDiagram
    autonumber
    participant クライアント
    participant 認可サーバー
    participant リソースオーナー


    クライアント->>クライアント: code_verifierを生成 (ランダムな文字列)
    クライアント->>クライアント: code_challengeを生成 (code_verifierのハッシュ値)
    クライアント->>認可サーバー: 認可リクエスト (code_challengeを含む)
    activate 認可サーバー
    認可サーバー->>リソースオーナー: 認証と認可を要求
    リソースオーナー->>認可サーバー: 認証と認可
    認可サーバー->>クライアント: 認可コードを返す (code_challengeと紐づけ)
    deactivate 認可サーバー
    クライアント->>認可サーバー: アクセストークン要求 (認可コードとcode_verifierを含む)
    activate 認可サーバー
    認可サーバー->>認可サーバー: code_verifierをハッシュ化し、保存されたcode_challengeと比較
    認可サーバー-->>クライアント: アクセストークン (ハッシュ値が一致した場合)
    deactivate 認可サーバー
    クライアント->>リソースサーバー: アクセストークンを使ってリソースにアクセス
```

---

# PKCE によって認可コード横取り攻撃を防ぐことができる理由

悪意のあるアプリは`code_verifier`を特定できないため、横取りはできてもアクセストークンを発行できない。

```mermaid {scale: 0.6}
sequenceDiagram
    autonumber
    participant 正規クライアント
    participant 悪意のあるアプリ
    participant 認可サーバー
    participant リソースオーナー

    正規クライアント->>認可サーバー: 認可リクエスト (code_challengeを含む)
    activate 認可サーバー
    認可サーバー->>リソースオーナー: 認証と認可を要求
    リソースオーナー->>認可サーバー: 認証と認可 (code_challengeと紐づけ)
    認可サーバー->>悪意のあるアプリ: 認可コード横取り
    deactivate 認可サーバー

    悪意のあるアプリ->>認可サーバー: アクセストークン要求 (横取りした認可コードとcode_verifierなし)
    activate 認可サーバー
    認可サーバー->>認可サーバー: 横取りした認可コードに紐づくcode_challengeを検索
    認可サーバー->>認可サーバー: code_verifierがないため、ハッシュ値比較に失敗
    認可サーバー-->>悪意のあるアプリ: エラー応答 (アクセストークン発行失敗)
    deactivate 認可サーバー
```

---

# 3. state パラメータの使用

- state
  - クライアントが認可リクエストを開始する際に生成するランダムな文字列で、リダイレクトURIを介してクライアントに返送される。
  - クライアントアプリ側で認可リクエスト時に生成した値をキャッシュしておき、リダイレクト時に認可サーバからの戻りとの一致を確認する。

---

# state を検証しない場合

攻撃者の作った認可リクエストを踏まされて、不正にアクセストークンを取得される。

```mermaid {scale: 0.4}
sequenceDiagram
    autonumber
    participant 攻撃者
    participant 被害者
    participant 正規クライアント
    participant 認可サーバー

    攻撃者->>攻撃者: 悪意のある認可リクエストを作成 (リダイレクトURIは攻撃者のもの)
    攻撃者->>被害者: 悪意のあるリンクを送信 (例: メールやウェブサイト)
    activate 被害者
    被害者->>認可サーバー: 悪意のある認可リクエスト (stateパラメータなし)
    activate 認可サーバー
    認可サーバー->>被害者: 認証と認可を要求
    被害者->>認可サーバー: 認証と認可
    認可サーバー->>被害者: 認可コードを返す (リダイレクトURIは攻撃者のもの)
    deactivate 認可サーバー
    被害者->>攻撃者: 認可コードを送信 (攻撃者のリダイレクトURIにリダイレクト)
    deactivate 被害者
    攻撃者->>正規クライアント: 認可コードを渡す (正規クライアントのリダイレクトURIを介して)
    正規クライアント->>認可サーバー: アクセストークンを要求 (stateパラメータなし)
    activate 認可サーバー
    認可サーバー->>認可サーバー: stateパラメータの検証をスキップ
    認可サーバー-->>正規クライアント: アクセストークンを発行
    deactivate 認可サーバー

    正規クライアント->>正規クライアント: アクセストークンを攻撃者のアカウントに紐付け
    攻撃者->>攻撃者: 被害者のリソースへのアクセス権を取得
```

---

# state を検証する認可コードグラントフロー

クライアントアプリはstateを発行して認可リクエストのパラメータとして付与する。リダイレクト時にキャッシュしたstateとリダイレクトのパラメータのstateを検証する。

```mermaid {scale: 0.35}
sequenceDiagram
    autonumber
    participant クライアント
    participant 攻撃者
    participant 被害者
    participant 認可サーバー

    クライアント->>クライアント: ランダムなstate値を生成し、セッションに保存
    クライアント->>被害者: 認可リクエストを送信 (stateパラメータ付き)
    activate 被害者
    被害者->>認可サーバー: 認可リクエスト (stateパラメータ付き)
    activate 認可サーバー
    認可サーバー->>被害者: 認証と認可を要求
    被害者->>認可サーバー: 認証と認可
    認可サーバー->>被害者: 認可コードとstate値を返す (リダイレクトURIは正規クライアントのもの)
    deactivate 認可サーバー
    被害者->>クライアント: 認可コードとstate値を送信 (正規クライアントのリダイレクトURIにリダイレクト)
    deactivate 被害者
    クライアント->>クライアント: セッションからstate値を取得し、レスポンスのstate値と照合
    alt state値が一致する
        クライアント->>認可サーバー: アクセストークンを要求 (認可コードとstate値を含む)
        activate 認可サーバー
        認可サーバー->>クライアント: アクセストークンを発行
        deactivate 認可サーバー
    else state値が一致しない
        クライアント->>クライアント: エラー処理 (CSRF攻撃の可能性)
    end
```

---

# state の検証が CSRF を防ぐ理由

悪意のあるアプリはクライアントアプリが生成するstateをわからないため、クライアントアプリが必ずトークンを発行せずエラーとする。

```mermaid
sequenceDiagram
    autonumber
    participant クライアント
    participant 攻撃者
    participant 被害者
    participant 認可サーバー

    攻撃者->>攻撃者: 悪意のある認可リクエストを作成 (リダイレクトURIは攻撃者のもの)
    攻撃者->>被害者: 悪意のあるリンクを送信 (例: メールやウェブサイト)
    activate 被害者
    被害者->>認可サーバー: 悪意のある認可リクエスト (stateパラメータなし)
    activate 認可サーバー
    認可サーバー->>被害者: 認証と認可を要求
    被害者->>認可サーバー: 認証と認可
    認可サーバー->>被害者: 認可コードを返す (リダイレクトURIは攻撃者のもの)
     deactivate 認可サーバー
    被害者->>攻撃者: 認可コードを送信 (攻撃者のリダイレクトURIにリダイレクト)
    deactivate 被害者
    攻撃者->>クライアント: 認可コードを渡す (正規クライアントのリダイレクトURIを介して)
    クライアント->>クライアント: セッションからstate値を取得し、レスポンスのstate値と照合
    クライアント->>クライアント: エラー処理 (CSRF攻撃の可能性)
```