# 認証レベルの標準化

## OpenID Connect for Identity Assurance（OIDC4IDA、IDA）

OIDCは認証の**事実**を事後的に検証可能な形で扱っている（IDトークンをデコードすると氏名などのクレームを得られる）。\
しかし、IDトークンに登録された情報の正しさは公的には保障されない（Googleアカウントを作るときにパスポートや運転免許証を提出する必要はない）。

そこで、IDトークンに**公的に検証されたクレームを格納する仕様**を技術仕様として定義した（[こちらなど](https://openid.net/specs/openid-connect-4-identity-assurance-1_0-final.html)）。

---

### 具体例

IDトークンの`verified_claims`に検証済みクレームを格納する。

- verification
  - 検証プロセスに関する情報。どの法規に基づく情報か、どの証拠を使って、いつ、誰がなど。
- claims
  - 検証済みのクレーム。

---

```json
{
  "iss": "https://server.example.com",
  "sub": "248289761",
  "aud": "https://rs.example.com/",
  "exp": 1544645174,
  "client_id": "s6BhdRkqt3_",
  "verified_claims": {
    "verification": {
      "trust_framework": "example",
      "time": null,
      "evidence": [
        {
          "type": {
            "value": "document"
          },
          "method": null,
          "document_details": {
            "type": null
          }
        }
      ]
    },
    "claims": {
      "given_name": "Max",
      "family_name": "Mustermann"
    }
  }
}
```
