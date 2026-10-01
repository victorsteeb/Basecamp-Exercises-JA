# セットアップとトラブルシューティング

**Partner Basecamp参加者向けの資料です。** 研修資料としての複製・再配布はご遠慮ください。ここで扱うパターンは、皆さまのお客様案件で自由にご活用いただけます。

企業管理の制限付きPCを含め、あらゆるノートPCでBasecampのノートブックを実行するための手順を1ページにまとめています。
**最も確実かつ迅速な方法は、VS Code上で仮想環境（venv）を使用することです。**

---

## 4つのコマンドによるセットアップ（初回のみ）

```bash
python3 -m venv .venv                 # create an isolated environment
source .venv/bin/activate             # macOS/Linux  (Windows: .venv\Scripts\activate)
pip install -r requirements.txt       # install everything the exercises need
export ANTHROPIC_API_KEY=sk-ant-...   # your key (Windows PowerShell: $env:ANTHROPIC_API_KEY="sk-ant-...")
```

**Anthropic APIではなくAmazon Bedrockをご利用の場合：** 4つのコマンドは同じですが、
`ANTHROPIC_API_KEY` の代わりに次の2つを設定してください。詳細は後述の「Amazon Bedrockキーの利用」をご参照ください。

```bash
export AWS_BEARER_TOKEN_BEDROCK=...   # your Bedrock API key
export AWS_REGION=us-east-1           # the region your models are enabled in
```

続いて、VS Codeで任意の `.ipynb` を開き、**カーネルとして `.venv` のインタープリターを選択してください**
（後述の「カーネルの選択ミス」を参照）。最初のセルを実行し、`✓ Dependencies ready` に続いて
`✓ APIキーを確認しました` と表示されれば準備完了です。

> venvは必須です。システム標準のPythonで実行すると、ノートブックはいずれも実行を停止します
> （後述の「ノートブックが停止する理由」を参照）。またvenvは、このページに記載した
> *すべての* 問題を一度に回避できる唯一のセットアップ方法でもあります。

---

## ノートブックが停止する理由

すべてのノートブックは、パッケージをインストールするセルの冒頭で、隔離された環境（`.venv` またはconda環境）上で
実行されているかを確認します。この確認はインストールと同じセル内で行うため、セルの実行順を変えても必ず実行されます。
隔離環境でない場合、セルはインストールを開始する前に停止し、次のメッセージを表示します。

> ⛔ This notebook is running on your SYSTEM Python, not an isolated environment...

**これは想定どおりの、意図的な動作です。** 以前は、セットアップセルがカーネルの指すPythonに
そのままインストールしていました（最悪の場合、システムのPythonに対して `--break-system-packages` を
使用することもありました）。個人のノートPCであれば環境が煩雑になる程度ですが、企業管理のマシンでは
IT部門への問い合わせが必要になる場合があります。このチェックにより、インストールの *後* ではなく
*前* に問題を検出できます。

**対処：** このページ冒頭のvenvセットアップを実施し、カーネルピッカーでその `.venv` のインタープリターを
選択してください。詳細は後述の「カーネルの選択ミス」をご参照ください。venvの代わりにcondaをご利用の場合は、
`conda activate <your-env-name>` を実行すれば（`base` 以外の環境を指定）、同じチェックを満たせます。

**意図的にすべてをグローバル環境にインストールしている場合（ファシリテーターのデモ機、自己管理のシステム
Pythonなど）：** VS Code/Jupyterを起動する前に、シェルで `BASECAMP_ALLOW_SYSTEM_PYTHON=1` を設定すると、
そのセッションに限りチェックを回避できます。これは仕組みを理解している方向けの例外措置です。
前述の企業IT環境の問題に直面している参加者に、標準の対処法として案内しないでください。

---

## Amazon Bedrockキーの利用

演習は、AnthropicのAPIキーとAmazon Bedrockキーのどちらでも実行できます。セットアップセルが
お手持ちのキーの種類を判別し、適切に接続します。演習コードの変更は不要で、異なるのは
認証情報のみです。

次の2つを **両方とも** `.env` に記載し（またはシェルでexportし）、
`ANTHROPIC_API_KEY` の行はコメントアウトまたは削除してください。

```
# ANTHROPIC_API_KEY=paste-your-key-here
AWS_BEARER_TOKEN_BEDROCK=<your Bedrock API key>
AWS_REGION=us-east-1
```

`AWS_REGION` は必須です（Bedrockにはデフォルトのリージョンがありません）。Claudeモデルを有効化している
リージョンを指定してください。接続に成功すると、プロバイダーとリージョンを示す緑色のバナーが表示されます。
**「✓ Bedrock key verified (us-east-1) — you're connected to Claude as
anthropic.claude-sonnet-5.」**

ご留意いただきたい点が2つあります。

- **モデルへのアクセス権は、アカウント単位 *かつ* リージョン単位で管理されています。** 有効なキーでも、
  「model isn't available to you in `<region>`」というエラーが表示されることがあります。これは、
  そのリージョンでお使いのアカウントに対象モデルが有効化されていないことを示します。
  Bedrockコンソールの **Model access** で有効化するか、`AWS_REGION` を有効化済みのリージョンに変更して
  ください。セットアップセルがこの点を事前に確認するため、数セル先ではなくセットアップの時点で
  問題を把握できます。
- **モデルIDの形式が異なります。** BedrockではIDに接頭辞が付きます（`anthropic.claude-sonnet-5`）。
  Anthropic APIでは接頭辞のないIDを使用します（`claude-sonnet-5`）。ノートブックでは、
  セットアップセルで定義される `MODEL` / `FAST_MODEL` / `BIG_MODEL` 変数がこの違いを
  吸収します。IDを直接記述せずにこれらの変数を使用すれば、どちらの環境でもコードが動作します。

**両方の認証情報が設定されている場合は、AnthropicのAPIキーが優先されます。** Bedrock経由で
接続する場合は、`ANTHROPIC_API_KEY` をunsetするか、コメントアウトしてください。

**Bedrock上で動作が異なる演習が1つあります。** Day 2の「推論の最適化」ではBatch APIを扱いますが、
これはAnthropic APIのエンドポイントであり、Bedrockでは提供されていません。ノートブックはこれを
検出し、バッチリクエストの作成と表示までを実行して、実際の送信のみをスキップします。本演習の主な学習
ポイントは後続のコストモデルの検討であり、この違いによる影響はありません。（Bedrockには、API仕様の異なる
独自のバッチ推論機能があります。）

---

## よくある問題

### `error: externally-managed-environment` (PEP 668)
**原因：** `pip` によるインストールが禁止されたシステムPython（macOSのHomebrew、またはDebian/Ubuntuの
OS標準Python）にインストールしようとしています。**対処：** 通常は対応不要です。
セットアップセルが制限付きのPythonを検出し、**初回** 実行時に自動でユーザー領域へ
インストールして、`✓ Dependencies ready` のみを表示します（PEP 668の長いエラーメッセージは表示されません）。
システムのPythonに一切手を加えたくない場合は、前述のvenvをご利用ください。最もクリーンな方法です。
メッセージが表示されるのは、**すべての** インストール方法が失敗した場合のみです。主な原因は、オフラインの
マシン、またはPyPIへのアクセスのブロックです（後述のプロキシの項を参照）。

### カーネルの選択ミス（VS Codeで最も多いトラブル）
ノートブックは、ターミナルとは異なるPythonで実行されている場合があります。その場合、`pip install` は
ノートブックから参照できない場所にインストールされます。**対処：** ノートブックを開いた状態で、
右上のカーネル名をクリックし、**Select Another Kernel → Python Environments** を選択して、
`.venv` で終わる環境を選んでください。表示されない場合は、**Developer: Reload Window** を実行してから
再度確認してください。

### 管理者権限なし / インストール時の `permission denied`
venvはユーザー自身の所有となるため、管理者権限は不要です。venvをご利用ください。venvを作成できない場合は、
ノートブックが自動的に `--user` によるインストールに切り替え、ホームディレクトリにインストールします。

### 企業プロキシ / ファイアウォールによるPyPIのブロック
pipにプロキシを設定してから、インストールを実行してください。
```bash
export HTTPS_PROXY=http://user:pass@proxy.company.com:8080
export HTTP_PROXY=$HTTPS_PROXY
pip install -r requirements.txt
```
PyPI自体がブロックされている場合は、社内のパッケージミラーをIT部門にご確認のうえ、次のように指定してください。
`pip install -r requirements.txt --index-url https://<your-mirror>/simple`

### Python未インストール / バージョン不一致
**Python 3.10以上** が必要です。[python.org](https://www.python.org/downloads/)または社内の
ソフトウェアポータルからインストールし、VS Codeを再起動して認識させてください。バージョンは
`python3 --version` で確認できます。

### APIキー
セットアップセルを初回実行すると、gitignore対象の **`.env` ファイル** が作成されます。キーをノートブックの
セルに貼り付けないでください。`.env` を開き、`ANTHROPIC_API_KEY=` の後にキーを貼り付けて保存し、
セルを再実行してください。キーがノートブックに記録されることはなく、カーネルを再起動しても保持されるため、
貼り付けは一度で済みます。リポジトリのルートに置いた `.env` 1つですべての演習に対応できます（セルが
上位フォルダをさかのぼって検索します）。シェルで `ANTHROPIC_API_KEY` を設定することも可能で、その場合は
ファイルより優先されます。どちらも設定されていない場合は、代替手段として、入力内容が画面に表示されない入力欄が開きます。
キーは `sk-ant-` で始まります。

---

## 解決しない場合
最初のセルを実行し、表示されるメッセージをご確認ください。インストールとキー確認のセルは、
トレースバックをそのまま表示するのではなく、次に行うべき具体的な手順を表示します。このページを参照するよう
案内された場合は、大半のケースで冒頭のvenvによる手順で解決できます。
