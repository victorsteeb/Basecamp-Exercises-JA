# Developer Platform · ビルドアロング

**Partner Basecamp参加者向けに作成。** 研修資料としての複製・再配布は禁止です。ここで学ぶパターンを、お客様のご支援に活用することは自由です。

## 作るもの
B2B SaaS企業のTechFlow（1日に500件以上のチケットを処理）向けの、マルチツールのサポートチケットエージェントです。チケットの詳細を読み、ナレッジベースを検索し、構造化された解決結果を出力します。フレームワークは使わず、Claude APIを直接呼び出します。

## 主な学び
Claude APIを使って、ツール使用を伴うエージェントループを構築する方法を学びます。ツールスキーマを定義し、複数ステップのツール呼び出しを処理し、Claude Sonnet 5のアダプティブ思考で、複雑なチケットの振り分けをモデルに自動で推論させます。

---

## 実行方法

演習はリポジトリ内で進めてください。チャット画面からコードをコピーして使わないでください。キーを一度設定したら、実行する環境を選びます。

```bash
export ANTHROPIC_API_KEY=your_key_here   # your shell, the VS Code terminal, or a local .env
```

ノートブックを実行する場合は、export は不要です。セットアップのセルが非表示の入力ボックスでキーを尋ね、緑色の「✓ API key verified」バナーで確認結果を表示します。

### VS Code / Cursor（推奨）

1. **File → Open Folder**（日本語UIでは「ファイル」→「フォルダーを開く」）で、このフォルダーを選びます。
2. 求められたら、**Python**と**Jupyter**の拡張機能をインストールします。
3. [`Developer_Platform.ipynb`](Developer_Platform.ipynb)を開き、**Python 3**カーネルを選びます。セルは **Shift+Enter** または **Run All** で実行します。これはビルドアロングです。セッションの進行に合わせて✏️のスタブを実装し、作りながら再実行してください。

### Claude Code（CLI）

このフォルダーに`cd`で移動し、Claude Codeと一緒に演習を進めます。

```bash
cd day1/02_developer-platform
claude                            # work the exercise with Claude Code as your pair
```

### Claude Desktop

AIのペアとして横に開いておきましょう。セルの解説、エラーのデバッグ、次の変更の提案をClaudeに頼めます。

セットアップのセルは、環境変数から`ANTHROPIC_API_KEY`を読み取ります（見つからない場合は非表示の入力ボックスで尋ねます）。キーをセルに直接貼り付けないでください。
