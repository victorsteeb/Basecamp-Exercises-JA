# セットアップとトラブルシューティング

**Partner Basecamp 参加者向けに作成しています。** 研修教材としての複製・再配布はできません。ただし、ここで学ぶパターンをお客様の案件で活用していただくのは自由です。

どのノートパソコンでも、制限の厳しい企業管理のマシンでも、Basecamp のノートブックを動かすための1ページです。
**最も確実で速い方法は、VS Code 上の仮想環境です。**

---

## 4つのコマンドでセットアップ（最初に一度だけ）

```bash
python3 -m venv .venv                 # create an isolated environment
source .venv/bin/activate             # macOS/Linux  (Windows: .venv\Scripts\activate)
pip install -r requirements.txt       # install everything the exercises need
export ANTHROPIC_API_KEY=sk-ant-...   # your key (Windows PowerShell: $env:ANTHROPIC_API_KEY="sk-ant-...")
```

**Anthropic API ではなく Amazon Bedrock をお使いですか？** 4つのコマンドは同じですが、
`ANTHROPIC_API_KEY` の代わりに次の2つを設定します。詳しくは下の「Amazon Bedrock キーを使う」を参照してください。

```bash
export AWS_BEARER_TOKEN_BEDROCK=...   # your Bedrock API key
export AWS_REGION=us-east-1           # the region your models are enabled in
```

次に、VS Code で任意の `.ipynb` を開き、**カーネルとして `.venv` インタープリターを選択します**
（下の「カーネルの選び間違い」を参照）。最初のセルを実行すると、`✓ Dependencies ready` に続いて
`✓ API key verified` と表示されるはずです。

> venv はもう任意ではありません。すべてのノートブックは、素のシステムPythonでは実行を
> 拒否します（下の「ノートブックが急に止まった理由」を参照）。それでも、このページの
> *あらゆる* 問題を一度に回避できる唯一のセットアップです。

---

## ノートブックが急に止まった理由

すべてのノートブックは、パッケージをインストールするセルの先頭（セルを順番どおりに実行しなくても
チェックを飛ばせないようにするためです）で、隔離された環境（`.venv` または conda 環境）の中で
実行されているかどうかを確認します。そうでない場合、セルはインストールを始める前に停止し、
次のように表示します。

> ⛔ This notebook is running on your SYSTEM Python, not an isolated environment...

**これは想定どおりの意図的な動作です。** 以前は、セットアップセルが黙って、カーネルが使っている
Pythonに何でもインストールしていました（最悪の場合、システムのインストールに対して
`--break-system-packages` を使うことまでありました）。個人のノートパソコンなら散らかる
程度ですが、企業管理のマシンではIT部門へのチケット対応になりかねません。このガードは、
インストールが終わってからではなく、始まる *前に* 問題を検出します。

**対処：** このページの先頭にある venv のセットアップを行い、カーネルピッカーでその `.venv`
インタープリターを選びます。下の「カーネルの選び間違い」を参照してください。venv の代わりに conda を
使っている場合は、`conda activate <your-env-name>`（`base` ではなく）で同じチェックを満たせます。

**すべてをあえてグローバルにインストール済みの場合（ファシリテーターのデモ機、自己管理のシステム
Pythonなど）は？** VS Code/Jupyter を起動する前にシェルで `BASECAMP_ALLOW_SYSTEM_PYTHON=1` を設定すると、
そのセッションではチェックを回避できます。これは、状況を理解している人のための意図的な抜け道です。
上記の企業IT環境の問題に実際に当たった参加者に、標準の対処法として案内しないでください。

---

## Amazon Bedrock キーを使う

演習は Anthropic のAPIキーでも Amazon Bedrock キーでも動きます。セットアップセルがどちらを
お持ちかを判別し、それに応じて接続します。演習のコードを変更する必要はなく、
認証情報だけが異なります。

次の2つを **両方とも** `.env` に入れる（またはシェルで export する）とともに、
`ANTHROPIC_API_KEY` の行はコメントアウトするか削除してください。

```
# ANTHROPIC_API_KEY=paste-your-key-here
AWS_BEARER_TOKEN_BEDROCK=<your Bedrock API key>
AWS_REGION=us-east-1
```

`AWS_REGION` は必須です。Bedrock にはデフォルトがありません。Claude モデルを有効にしている
リージョンを指定してください。接続できると、プロバイダーとリージョンを示す緑色のバナーが表示されます。
**「✓ Bedrock key verified (us-east-1) — you're connected to Claude as
anthropic.claude-sonnet-5.」**

知っておいていただきたいことが2つあります。

- **モデルへのアクセスは、アカウントごと *かつ* リージョンごとです。** 有効なキーでも
  「model isn't available to you in `<region>`」というエラーで失敗することがあります。その
  リージョンでは、お使いのアカウントでそのモデルが有効になっていないという意味です。Bedrock
  コンソールの **Model access** で有効にするか、`AWS_REGION` を有効なリージョンに切り替えて
  ください。セットアップセルはこれを最初に確認するので、数セル進んだ後ではなく
  セットアップの時点で気づけます。
- **モデルIDが異なります。** Bedrock ではIDに接頭辞が付きます（`anthropic.claude-sonnet-5`）。
  Anthropic API では接頭辞のないIDを使います（`claude-sonnet-5`）。ノートブックでは、
  セットアップセルが定義する `MODEL` / `FAST_MODEL` / `BIG_MODEL` 変数がこの違いを
  吸収します。IDを直接書かずにこれらを使えば、コードは両方で動きます。

**両方の認証情報が設定されている場合は、Anthropic のAPIキーが優先されます。** Bedrock を
強制したい場合は、`ANTHROPIC_API_KEY` を unset するかコメントアウトしてください。

**Bedrock で動作が異なる演習が1つあります。** Day 2 の推論の最適化では Batch API を扱いますが、
これは Anthropic API のエンドポイントで、Bedrock では提供されていません。ノートブックがこれを
検出し、バッチリクエストの作成と表示までは行って、実際の送信だけをスキップします。続く
コストモデルこそが本当の学習ポイントで、影響を受けません。（Bedrock には、APIの異なる
独自のバッチ推論サービスがあります。）

---

## よくある問題

### `error: externally-managed-environment` (PEP 668)
**原因：** `pip` の使用が禁止されているシステムPython（macOS の Homebrew や、Debian/Ubuntu の
OS標準Python）にインストールしようとしています。**対処：** 通常は何もする必要はありません。
セットアップセルが制限のかかったPythonを検出し、**初回**実行時に自動でユーザー領域へ
インストールして、`✓ Dependencies ready` とだけ表示します（PEP 668 の長いエラー文は出ません）。
システムPythonにはまったく触れたくない場合は、上の venv を使ってください。これが最もきれいな
方法です。メッセージが出るのは、インストール方法が **すべて** 失敗したとき、つまりオフラインの
マシンか PyPI がブロックされている場合だけです（下のプロキシの項を参照）。

### カーネルの選び間違い（VS Code で最も多い落とし穴）
ノートブックは、ターミナルとは別のPythonで動くことがあります。その場合、`pip install` は
ノートブックから見えない場所にインストールされます。**対処：** ノートブックを開いた状態で、
右上のカーネル名をクリックし、**Select Another Kernel → Python Environments** を選んで、
`.venv` で終わるものを選びます。見当たらない場合は、**Developer: Reload Window** を実行してから
もう一度探してください。

### 管理者権限がない / インストール時の `permission denied`
venv はご自身の所有なので、管理者権限は不要です。venv を使ってください。作成できない場合は、
ノートブックの自動 `--user` フォールバックが、代わりにホームディレクトリへインストールします。

### 企業プロキシ / ファイアウォールで PyPI がブロックされている
pip にプロキシを指定してから、インストールします。
```bash
export HTTPS_PROXY=http://user:pass@proxy.company.com:8080
export HTTP_PROXY=$HTTPS_PROXY
pip install -r requirements.txt
```
PyPI そのものがブロックされている場合は、社内のパッケージミラーをIT部門に確認して、それを使います。
`pip install -r requirements.txt --index-url https://<your-mirror>/simple`

### 「Python がない」/ バージョンが違う
**Python 3.10 以上** が必要です。[python.org](https://www.python.org/downloads/) か、社内の
ソフトウェアポータルからインストールし、VS Code を開き直して認識させてください。
`python3 --version` で確認できます。

### APIキー
セットアップセルは、初回実行時に gitignore 対象の **`.env` ファイル** を作ります。キーをノートブックの
セルに貼り付けることは決してしないでください。`.env` を開き、`ANTHROPIC_API_KEY=` の後ろにキーを
貼り付け、保存して、セルをもう一度実行します。キーがノートブックに触れることはなく、カーネルを
再起動しても残るので、貼り付けるのは一度で済みます。リポジトリのルートに置いた `.env` 1つで
すべての演習をまかなえます（セルはフォルダをさかのぼって探します）。シェルで `ANTHROPIC_API_KEY` を
設定することもできます。その場合はファイルより優先されます。どちらも設定されていない場合は、
フォールバックとして非表示の入力ボックスが表示されます。キーは `sk-ant-` で始まります。

---

## まだ解決しない場合
最初のセルを実行して、表示されるメッセージを読んでください。インストールとキー確認のセルは、
生のトレースバックではなく、具体的な次の手順を表示します。そこからこのページに戻るよう
案内された場合、ほとんどのケースで、先頭の venv の手順が解決してくれます。
