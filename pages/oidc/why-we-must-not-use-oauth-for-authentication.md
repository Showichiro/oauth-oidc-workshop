# そもそもなぜOAuth 2.0で認証をやってはいけないのか

プロフィールに関する権限を認可すれば、「認可 + プロフィールAPI」によって認証機能は実現できそうだが。

認証に関する仕様が定まっていないため、認可サーバごとの仕様差異やセキュリティリスクなどが付きまとう。  
→認証機能を実現するためにOAuth2.0ではカバーできない仕様を標準化した`OpenID Connect`が誕生。
