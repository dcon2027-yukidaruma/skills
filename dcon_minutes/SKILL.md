# DCON2027 議事録作成

## 受け取る

- ユーザーから音声ファイルを受け取る。Google Drive 内の場所を特定できる情報（ファイル名、URL、フォルダ）が入力になる。

- 会議の日付を確認する。ファイル名やメタデータで判別できない場合は推測せず、ユーザーに聞く。ファイル名はこの日付で決まる。

- 対象フォルダの実在を Drive で確認してから進める。DCON2027_☃️ の ID は `1V6GbXDklZ1gHfKeM4b0nVuYvMbR4yK9p`、01_議事録 は `10x-OwRzUAQDTqyUyLcksR9CG6PD31Tod`、template.md は `1A68g7UEl8KznjDkc4EUTvNfYCb24Po0V`、録音 は `1AdUEPFOlGEk2VDIr6rVLffyz8grtLCQd`、録音/音声 は `1VbXCTO8cKxZbc1I4CyfJZ_0wpaQW11Ub`、録音/文字起こし は `11GRFHVejRJLKFXdzUraY1Ss7toB5r6VJ`（2026-10-24 にコネクタで確認済み）。

- 同じ日付の議事録ファイルが既にある場合は上書きしない。停止してユーザーに扱いを確認する。同日複数録音の命名ルールは未確定で、ユーザーの決定待ち。

## 文字起こしする

- 音声を自宅マシン dcon2027 に転送し、Whisper で処理する。接続は `ssh -F ~/.ssh/config dcon2027` を使う。

- dcon2027 上には Docker で動く dcon-minutes システム（議事録Botとポータル、/opt/dcon-minutes）が常駐している。ホスト側 Python には Whisper は入っていない（2026-10-24 確認）。実行前に、コンテナ側で処理するかホストへ導入するかを確認し、無い方を前提に進めない。

- 出力は txt と srt の両方を取る。srt のタイムスタンプが後の照合に使える。

## 要約して議事録にする

- 01_議事録/template.md を読み、その見出し構成のまま議事録を作る。テンプレと違う見出しを勝手に作らない。

- 要約はさくらのAI Engine で行い、文字起こし全文を入力にする。要約だけを根拠にせず、決定事項や担当、期限は原文と照合する。

- API の認証情報はこの Skill にも作業ファイルにも書かない。接続が未整備で呼び出せない場合は、文字起こしの保存までを完了させ、要約が未実施であることを報告する。

## 保存する

- 議事録は 01_議事録 へ `YYYY-MM-DD.md` で保存する。

- 元の音声ファイルは 録音/音声 へ `YYYY-MM-DD.拡張子` で保存する。拡張子は元のファイルのものを維持する。

- 文字起こしは 録音/文字起こし へ `YYYY-MM-DD.srt` と `YYYY-MM-DD.txt` で保存する。

- 保存後に Drive で各ファイルの存在を読み返して確認する。アップロードの応答だけで完了としない。

## 報告する

- 保存した議事録、音声、文字起こしの3点が揃ったことを報告する。欠けたものがある場合は、何が欠けて何が原因かを明記する。

- 議事録は下書きとして扱い、内容の確定はユーザーの確認に委ねる。

## Available connectors

- [Google Drive](/opt/hatch/connectors/google_drive/CONNECTOR.md) for 音声の取得と、議事録・文字起こし・元音声の保存先。

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
