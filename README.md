# [入門 Docker](https://y-ohgi.com/introduction-docker/)

## はじめに

### Docker とは

- [ ] Docker とは、一言で言えば **「パッケージ」** のための技術。
- [ ] Docker のメリットは、
  - **「環境配布の容易性」**
    - 任意のタイミングの状態を保存(=_スナップショット_)し、復元できる
  - **「オーバーヘッドの軽減」**
    - VM は _物理レイヤーの仮想化_ から行うが、Docker は、Linux の機能を用いて 1 プロセスとして隔離環境を作成する
    - したがって仮想化レイヤーが少ない
    - VM: Hardware > Host OS > Hyperviser > VM (Gest OS > Libs > Application)
    - Docker: Hardware > Host OS > Docker Engine > Container (Libs > Application)
- [ ] Docker のアプローチ
  - **「Immutable Infrastructure」**
    - インフラを不変なものとして扱い、_スナップショットの更新_ によってサーバの変更を行う
    - `Dockerfile` = Infrastructure as a Code (インフラの構成をコードに落とし込み、宣言的に構築)
    - `Image` = スナップショット

### VM と Docker

- [ ] VM
  - コンピュータ自体(= _ハードウェア、カーネル、ユーザスペース_ の低レイヤー)を仮想化
- [ ] Docker
  - Linux の機能(= _cgroup_, _namespace_, _Capability_)を組み合わせ、プロセスを仮想化
  - ホスト OS とリソースを共有し、効率良く使用できる
  - _cgroup_: コントロールグループ。**計算リソース**(= メモリ、CPU 等)の隔離。
  - _namespace_: プロセスやネットワーク、ファイルアクセス等の隔離。
  - _Capability_: 一般 or 特権を、更に細分化したもの。Host OS の root 権限を、制限付きで&用途を限定して Docker コンテナへ付与/管理する

## コンポーネント

### Image

- [ ] Image は、**「環境のスナップショット」** としての役割を持つ
  - _OS_ や _ソフトウェア_ 、_ランタイム_(Node, Python, Ruby) など

### [Dockerfile](https://docs.docker.jp/engine/reference/builder.html)

- [ ] Dockerfile は、**イメージのビルド** に使用する
  - したがって `docker build` は、Dockerfile のコマンドライン命令を反映したビルド結果を作る
- [ ] Dockerfile の DSL 記法 (全 18 コマンド、`MAINTAINER` は非推奨なので注意)
  - 基本 7 コマンド
    - `FROM`: ベースイメージを指定する
    - `ENV`: 環境変数を定義する
    - `WORKDIR`: **Dockerfile でコマンドを実行する時の**ディレクトリを指定(& cd)する。存在しないディレクトリを指定すると自動的に作成する。デフォルトは `/` だが、既存のものを上書きしないよう基本的には指定しておくと安心。
    - `COPY`: **ホストのファイルを(第 1 引数)** **Docker 内へ(第 2 引数)** コピーする。ホスト側のディレクトリは `docker build {dirname}` で指定したもの、Docker 側は `WORKDIR` で定義されたディレクトリが参照される。
    - `RUN`: Docker 内でコマンドを実行する。**依存するライブラリやパッケージのインストール**、**ユーザの設定**などを行う。
    - `USER`: Docker Image の**起動時にログインするユーザ**を指定する。デフォルトは root だけどセキュリティリスクを回避するためにも適当に作っておくのが良い。
    - `CMD`: Docker 起動時のデフォルトコマンド。このコマンドがフォアグラウンドで実行されている時間が、Docker の生存期間になる。
  - 次点 5 コマンド
    - `EXPOSE`: ポート公開用のコマンド。コンテナ起動時に反映させるには、`-P (or -p)` オプションを使用すること！
    - `VOLUME`: ==あとで書く==
    - `ARGS`: ビルド時に変数を使用するためのコマンド。複雑になるので基本使用しない方がよい。
    - `ADD`: `COPY` コマンドを拡張したもの。URL 指定ができたり、tar の解凍ができたりする。`COPY` を使う方が安心。
    - `ENTRYPOINT`: 指定されたコマンドを実行する。`RUN` と異なり、`docker run` 時に指定したコマンドを引数として使用できる。
  - 他 5 コマンド
    - `LABEL`: イメージに対してメタデータを追加できる。
    - `MAINTAINER`: 廃止予定。Author フィールドとして使われていたが、`LABEL`でいいよねっていう。
    - `ONBUILD`: イメージに対してトリガ命令を追加できる。そのイメージがベースイメージとして使われる時に必要となってくるらしい。
    - `STOPSIGNAL`: 終了時のシステムコールシグナルを設定する。コンテナ終了を補足するのに使えるよう。
    - `HEALTHCHECK`: コンテナが動作していることをチェックする。
    - `SHELL`: 各種コマンドのデフォルトシェルを指定できる。Windows の cmd or powershell とかで使える。
  - 注意事項
    - 複数指定(`ENV`など)する時は複数行書いてもよいが、キャッシュレイヤも複数発生してしまう。スペース区切りで複数の値を設定できるコマンドもあるので、ドキュメントで確認しよう。
