# 調整さん未回答リマインド

## 準備する

- ユーザーから集める情報は、調整さんの投票URL、回答が必要なメンバーの名簿（氏名と Discord ユーザーID）、申込締切、リマインド先の Discord チャンネル、リマインドの頻度。

- 繰り返し実行は `cron.add` で定期ジョブにする。ジョブの本文には、この Skill の手順と、その案件の URL、名簿、締切、停止条件をすべて書き込む。実行側がこの Skill を探しに行く前提にしない。

- 現在稼働中の実例は cron `dcon-chouseisan-reminder`（DCON2027 アドバイザー相談会、月水金 18:00、締切 2026-11-20 13:00）。新しい案件ではこれを雛形にする。

## 回答状況を確認する

- 調整さんのページは JavaScript アプリで、通常の HTTP 取得では回答者一覧が読めない。`browser.browse` で実際のページを開いて回答者名を読む。

- メンバーが回答済みかどうかは、回答者一覧に氏名または姓が含まれるかで判定する。

## リマインドする

- 未回答のメンバー全員を1通のメッセージでメンションする。宛先は `<@ユーザーID>` の形式で、回答済みのメンバーは含めない。

- 投稿には asari-muse Bot の送信ヘルパーを使う。`python3 ~/workspace/discord-bot/post.py <channel_id> '<text>'` で、文面には投票URLと締切を入れる。

- 投稿がプロキシ認証エラー（407 など）で失敗したら、`bash ~/workspace/discord-bot/refresh_proxy.sh` を1回実行して1回だけ再送する。それでも失敗したらユーザーに報告する。

## 停止する

- 全員が回答したら、チャンネルに完了を1通投稿し、cron ジョブを削除して、ユーザーに完了を報告する。

- 締切を過ぎても未回答が残っていたら、最終リマインドを1通投稿し、未回答者をユーザーに報告して、cron ジョブを削除する。

- ユーザーが途中で解除を指示したら、その時点で cron ジョブを削除する。

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.

## Websites

- [調整さん](https://chouseisan.com/) for 回答状況の確認。ブラウザで開いたレンダリング済みページから回答者一覧を読む。
