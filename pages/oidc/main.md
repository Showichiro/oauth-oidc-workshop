# OpenID Connect (OIDC)

OAuth 2.0を拡張して認証のためのアイデンティティレイヤーを提供する仕様 \
OAuth 2.0の仕様＋「OIDCの仕様 + OIDCが依存するJWT関連の仕様」という構図で理解する。

<img src="/oidc-rfc.drawio.svg" class="h-100" />

---
src: ./agenda.md
---

---
src: ./why-we-must-not-use-oauth-for-authentication.md
---

---
src: ./concepts-for-oidc.md
---

---
src: ./oidc-flow.md
---

---
src: ./idtoken.md
---

---
src: ./userinfo.md
---

---

# OpenID Connectが従来の認証方式の抱える課題を如何に解決したか

- ユーザ目線：アプリケーションごとにIDを管理する必要がなくなった
  - GoogleのID/Passを覚えておけば、Qiitaを使うことができる
- アプリ目線：認証の事実（いつ、だれが、どのような方法で、いつまで有効か）やユーザ属性情報を標準的に取り扱うことができるようになった
