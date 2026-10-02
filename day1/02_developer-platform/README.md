# Claude Platform：ハンズオン

**Partner Basecamp参加者向けの資料です。** 研修資料としての複製・再配布はご遠慮ください。ここで扱うパターンは、皆さまのお客様案件で自由にご活用いただけます。

## 本演習で構築するもの
B2B SaaS企業のTechFlow（1日に500件以上のチケットを処理）向けに、マルチツールのサポートチケットエージェントを構築します。エージェントはチケットの詳細を読み込み、ナレッジベースを検索し、構造化された解決結果を出力します。フレームワークは使用せず、Claude APIを直接呼び出します。

## 主な学習内容
Claude APIを使用して、ツール使用を伴うエージェントループを構築する方法を学びます。ツールスキーマを定義し、複数ステップのツール呼び出しを処理するとともに、Claude Sonnet 5のアダプティブ思考を活用して、複雑なチケットの振り分けをモデルに自律的に推論させます。

---

## 実行方法

演習はリポジトリ内で進めてください。チャット画面からコードをコピーしないでください。キーを一度設定してから、実行環境を選択してください。

```bash
export ANTHROPIC_API_KEY=your_key_here   # your shell, the VS Code terminal, or a local .env
```

ノートブックを実行する場合、exportは不要です。セットアップセルが、入力した文字が表示されない入力欄でキーの入力を求め、緑色の「✓ APIキーを確認しました」バナーで確認結果を表示します。

### VS Code / Cursor（推奨）

1. **File → Open Folder**（日本語UIでは「ファイル」→「フォルダーを開く」）で、このフォルダーを選択します。
2. 確認が表示された場合は、**Python**と**Jupyter**の拡張機能をインストールします。
3. [`Developer_Platform.ipynb`](Developer_Platform.ipynb) を開き、**Python 3**カーネルを選択します。セルは **Shift+Enter** または **Run All** で実行します。本演習はハンズオン形式です。セッションの進行に合わせて✏️の実装課題に取り組み、実装のたびに再実行してください。

### Claude Code（CLI）

このフォルダーに `cd` で移動し、Claude Codeと協働しながら演習を進めてください。

```bash
cd day1/02_developer-platform
claude                            # work the exercise with Claude Code as your pair
```

### Claude Desktop

Claude Desktopを横に開いたまま、AIペアプログラマーとして活用してください。編集しながら、セルの解説、エラーのデバッグ、次に加える変更の提案を依頼できます。

セットアップセルは、環境変数から `ANTHROPIC_API_KEY` を読み取ります（見つからない場合は入力した文字が表示されない入力欄で入力を求めます）。キーをセルに直接貼り付けないでください。