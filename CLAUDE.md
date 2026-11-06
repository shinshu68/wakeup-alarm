# wakeup-alarm

本命アラームと予備アラームをグループで登録し、起きられた日は「起きた」ボタン1つ、または Echo Dot への「アレクサ、おはよう」で、その日の残りのアラームをまとめてオフにできる Android 向けアラームアプリ (自分用)。

docs/plan.md を起点に、docs/ 配下の各ドキュメントを参照する。開発方針・スコープ・設計は docs/plan.md、マイルストーンと各完了条件は docs/roadmap.md、Alexa 連携の仕様は docs/alexa-integration.md に書く。

## パッケージ

| フォルダ | 役割 |
|---|---|
| `app/` | Android アプリ (Flutter)。アラームの登録・鳴動、「起きた」、FCM を受けての当日のオフ |
| `alexa-skill/` | Alexa-hosted スキル (Node.js)。「アレクサ、おはよう」で FCM を送り、Firestore の結果を読み上げる |

パッケージごとの技術スタック・コマンド・動作確認は、それぞれのフォルダの CLAUDE.md に書く。

## パッケージをまたぐルール

- アプリとスキルの間の仕様 (FCM のデータメッセージ、Firestore のコレクション・フィールド、待つ時間など) は、docs/alexa-integration.md の「アプリとスキルの取り決め」を唯一の正とする
- 仕様を変えるときは、docs/alexa-integration.md → 提供する側 → 利用する側の順に直す (FCM のデータはスキルが提供側、処理結果と FCM トークンの登録はアプリが提供側)
- 仕様の更新は、実装より先のコミットにする
- 実装の修正で、待つ時間や再試行などの振る舞いが変わるときは、その修正と同じコミットで docs/alexa-integration.md も直す
- Firebase は無料の Spark プランのまま使う。Blaze プランが必要な機能 (Cloud Functions など) を使う案は出さない

## アプリとスキルでそろえる名前

FCM のデータのキー、Firestore のコレクション名・フィールド名など、片方だけ変えるとエラーが出ずに動かなくなる名前は、実装したらこの節に「`app/` 側のファイル ⇔ `alexa-skill/` 側のファイル」の対で書く。

未定 (M7 で決める)
