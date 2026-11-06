# app

wakeup-alarm の Android アプリ (Flutter)。アラームグループの登録・鳴動、「起きた」による当日の予備のオフ、FCM を受けての当日のアラームのオフを担う。

## 技術スタック

- Flutter (Dart)
- `alarm` パッケージ：アラームの登録・鳴動・通知・全画面表示
- drift (SQLite)：データ保存
- Riverpod：状態管理
- firebase_messaging・cloud_firestore・firebase_auth：Alexa 連携 (M7 で導入)
- 対象は Android のみ。iOS は対象外

## ディレクトリ構成

未定 (案はリポジトリルートの docs/plan.md の「ディレクトリ構成 (案)」を参照)

## コマンド

いずれも `app/` で実行する。

依存パッケージの取得

```fish
flutter pub get
```

drift のコード生成

```fish
dart run build_runner build --delete-conflicting-outputs
```

フォーマット

```fish
dart format .
```

静的解析

```fish
flutter analyze
```

テスト

```fish
flutter test
```

AVD での実行

```fish
flutter emulators --launch Pixel_6a_API_37
```

```fish
flutter run
```

実機用の APK のビルド

```fish
flutter build apk --release
```

## 動作確認

- **自動テスト** (`flutter test`)：鳴動日時の展開、スキップ判定など、端末に依存しないロジック。Firebase には接続せず、モックを使う
- **AVD** (`Pixel_6a_API_37`)：Claude が自分で確かめる環境はこれ1つに決める。画面の操作、アラームの登録・鳴動・停止を、アプリ前面・ロック中・アプリ終了後・端末再起動後の各状態で確かめる。鳴動を確かめるときは、数分後の時刻にアラームを登録して待つ。M7 以降は、FCM の受信と当日のオフも AVD で確かめる (Doze は AVD に adb で強制して確かめる)
- **実機** (Pixel 6a)：ユーザーに頼む。消灯中の全画面表示、着信音・バイブ、電池最適化の影響、一晩おいたあとに鳴るか、一晩おいたあとの FCM の受信など、AVD では確かめられない部分だけを頼む

使わない手段：

- 実機への adb 操作 (インストール・ログ取得を含む) は使わない。毎朝使う端末の設定やデータを Claude が触らないため

実機での確認を頼むとき：

1. Claude が `flutter build apk --release` で APK をビルドし、出力先のパスを伝える
2. ユーザーが Pixel 6a にインストールする
3. Claude は、確かめてほしい操作の手順 (例：3分後にアラームを登録し、画面を消してロックする) と、確かめてほしい点 (例：全画面の鳴動画面が出るか、音とバイブが出るか、「停止」で止まるか) を箇条書きで示す
4. ユーザーの報告を受けて、問題があれば AVD で再現を試みてから修正する

## このパッケージ固有のルール

- 「起きた」(当日の予備の一括オフ) は、ロックを解除して開いたアプリ画面からのみ操作できるようにする。誤操作防止のため、鳴動画面・通知・ロック画面には置かない
- 「停止」は鳴動を止めるだけで、予備には影響させない
- FCM を受けての処理でも、鳴動中のアラームは止めない
- Google Play には公開しない前提のため、Play のポリシー対応は不要。ただし権限は Android の仕様どおり、実行時の許可や設定画面での許可を確認してから使う
