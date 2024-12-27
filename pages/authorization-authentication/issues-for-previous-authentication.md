# 従来の認証方式における課題

1. パスワードの共有
   - 第三者サービスを利用するために、ユーザーは自分のパスワードをそのサービスに提供する必要があった
2. 各サービスごとに認証が必要
   - 多くのウェブサイトやサービスで、ユーザーは個別にアカウントを作成し、パスワードを管理する必要があった

---

# 従来の認証方式

従来認証方式ではクライアントがサーバに向けてID/Passwordを入力しユーザのデータにアクセス。

<img src="/assets/conventional.drawio.svg" width="600px" >

---

# パスワードの共有

そのため、3rdパーティのアプリがユーザのデータにアクセスするためには、ID/Password（に類する情報）を共有する必要。

<img src="/assets/conventional-3rdparty.drawio.svg" width="600px">

---

# 各サービスごとに認証が必要

また、サービスごとにIDが管理されているため、ユーザは複数のIDを管理する必要があった。

<img src="/assets/conventional-multi-app.drawio.svg" width="600px">
