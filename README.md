# MONO WALLET

黒基調のiPhone向け家計簿PWA。Firebase Authentication（匿名認証）とCloud Firestoreを利用。

## 導入

1. Firebase Console (https://console.firebase.google.com/) でプロジェクト作成。
2. 「Authentication → Sign-in method → 匿名」を有効化。
3. 「Firestore Database」を作成。ルールに `firestore.rules` を貼り付けて公開。**テストモードのまま公開しないこと**。
4. Firebaseプロジェクト設定でウェブアプリを追加し、SDKの `firebaseConfig` の値を `firebase-config.js` に転記。
5. 「Authentication → Settings → Authorized domains」に公開する `ユーザー名.github.io` を追加（必要な場合）。
6. GitHubの新しいリポジトリにファイルをアップロード。Settings → Pages → Deploy from a branch → main / root → Save。
7. `https://ユーザー名.github.io/リポジトリ名/` を開く。iPhone Safariの共有メニューから「ホーム画面に追加」。
8. iPhone「ショートカット」で「URLを開く」アクションに `https://ユーザー名.github.io/リポジトリ名/?page=add` を指定。iOS 18以降の対応端末ではロック画面のコントロールをカスタマイズしてショートカットを配置できる。ロック画面からの起動は端末やiOSバージョンによって設定方法が異なる。

## 開発時の動作確認

ES Modulesのため `file://` で開かず、ローカルサーバーを使う。Pythonがあればプロジェクトフォルダで `python -m http.server 8000`、ブラウザで `http://localhost:8000` にアクセスする。

## 仕様

- 支出合計 = 当該集計期間の「現金（引き落とし）」支出 + 同期間の「クレカ請求額」（手入力）。
- クレカで個別登録した支出は参考履歴として保持し、合計には加算しない。
- クレカ請求額の保存キーは集計期間の開始日。集計開始日を変更すると、別の期間として扱われるので必要に応じて再入力する。
- 集計期間は開始日から翌月の開始日の前日まで。31日など存在しない日は月末に丸める。
- 固定費はアプリ起動中に直近2暦月の登録を補完。未来分はアプリを再度開いたときに登録。固定費の削除後も生成済み支出は残る。生成済み固定費を手動削除すると再生成されることがある。
- 月別棒グラフは集計期間に基づく。
- オフライン時の閲覧は保証しない。Firebaseのオンライン接続が必要。

## 注意

匿名認証は端末・ブラウザごとに別ユーザーとなる。Safariのサイトデータ削除、ブラウザ変更、端末交換などで既存データにアクセスできなくなる可能性がある。本格利用前にメール・パスワードなどへのアカウント連携機能を追加することを推奨。初期版は1端末での試用向け。

クレカ番号やパスワードはアプリに保存しない。FirebaseのAPIキーは秘密鍵ではないが、Firestoreルールを厳格にする。請求額は利用日ではなく支払期間に紐づくため、実際の家計管理目的に応じて扱いを確認する。
