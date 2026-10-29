# 授業で使えるデジタル設計ラボ: Ubuntu VM で Verilog、VHDL、yosys を学ぶ

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-hdl-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-hdl-lab/actions/workflows/check.yml)

Verilog と VHDL を教えるための、すぐ使える**デジタル設計ラボ**です。学校が生徒一人ひとりに専用のマシンとブラウザー上のターミナルを提供する [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)から提供しています。生徒ごとに、Ubuntu のアーカイブから入れた **Icarus Verilog**、**GHDL**、**yosys** を備えた Ubuntu VM が用意されます。生徒はテストベンチで回路をシミュレーションし、真理値表と**波形**をテキストとして読み、Verilog と VHDL で全加算器を、そしてイネーブルと同期リセットを持つ 4 ビットカウンタを仕上げ、yosys が作るゲートの数を数えます。生徒のコンピューターには何もインストールせず、ボードも必要ありません。

## 生徒が行うこと

ラボは作業用ファイルを入れた `~/hdl` フォルダーを置き、ホームフォルダーに何をするかを説明する `README.md` を置きます。答えは書かれていません。内容は 5 つのパートです。

1. Verilog での組み合わせ回路: 与えられた半加算器の真理値表を読み、`full_adder.v` を書く。
2. クロックと波形: 与えられたカウンタが信号を VCD ファイルに記録し、`vcd2txt` でテキストの行として読むか、`vcd2svg` で画像にしてラボのポート 8000 で開く。
3. 同じ全加算器を VHDL で、GHDL の 3 つの手順（analyse、elaborate、run）で書く: `full_adder.vhd`。
4. イネーブルと同期リセットを持つカウンタを生徒が仕上げる: `counter4.v`。
5. ゲート: `hdl gates counter4.v counter4` で yosys に設計が必要とするセルを数えさせ、フリップフロップが 1 ビットを記憶すること、ラッチは間違いであることを確かめる。

`hdl reset ファイル名` でファイルを配布時の状態に戻せます。プラットフォームの Basics レッスンをこのラボで進めることができ、全加算器とカウンタは生徒に任せたままです。

## 採点のしかた

ラボには**チェック**があります。提出された VM を、先生が読まなくても採点するスクリプトです。保存されたマシンのコピーを新しく起動して実行します。`~/hdl` に `full_adder.v`、`full_adder.vhd`、`counter4.v` があることを求め、シミュレーションを終わらせたり外に出たりするシステムタスク（`$finish`、`$stop`、`$fatal`、`$fopen`、`$system` など。コメント内も同じ）を呼ぶ Verilog ファイルと、`textio`、`std.env`、`severity failure` を使う VHDL ファイルを受け付けません。次に、**生徒には見えないテストベンチ**で各設計をコンパイルします。テストベンチは参照モデルを持ち、設計と比べます。カウンタはクロックの立ち上がりの前と後の両方で比べるので、同期でないリセットは見つかります。テストベンチは、チェックが実行のたびに新しく選ぶ数を含む結果行を出力するため、設計が自分で正しい行を出すことはできません。最後に、yosys が設計を**ラッチなし**で読めること、そしてカウンタがフリップフロップを持ち、単純な設計より多くのセルを使わないことが必要です。出力の最初の行が、何が正しくないかを伝えます。

しないこと: 波形や README の問いへの答えは読みません。ボード向けの配置配線もしません。自分の VM で root 権限を持つ生徒がシミュレーターに手を加えることは、依然として可能です。教室のためのチェックであり、証明ではありません。

## ファイルの中身

`hdl-lab.lab.yaml` は、プラットフォームが実行するそのままの **VM ラボ定義**です。

| キー | 内容 |
| --- | --- |
| `cpus`, `memory`, `disk` | 生徒ごとの VM のサイズ: CPU 1 つ、1024 MB、10 GB |
| `open` | ブラウザーで開くポート: ラボが描く波形の画像を表示する 8000。それ以外はターミナルで行います |
| `setup` | 各生徒の VM が最初に起動したとき、root で一度だけ実行されるスクリプト: Ubuntu のアーカイブから `iverilog`、`ghdl`、`yosys` をインストールし（数分）、補助コマンド `hdl`、`vcd2txt`、`vcd2svg`、作業用ファイル、README、画像を配信する systemd ユニットを書きます |
| `check` | 採点スクリプト。保存されたディスクのコピーを新しく起動して root で実行します。終了ステータスが結果で、`0` が合格です |

## プラットフォームでの使い方

**クラスの課題にするには:**

1. 自分のモジュールでタスクを追加し、**Ubuntu VM ラボ**のカードを選びます。
2. デジタル設計のサンプルを押すか、`hdl-lab.lab.yaml` の内容を貼り付けます。サンプルを使うとテキストが置き換わり、元に戻すで自分のものに戻せます。
3. 公開して、まず自分用のコピーで試します。生徒にはそれぞれ専用の VM が用意され、セッションをまたいで保たれます。
4. 通常のタスクと同じように割り当てます。提出されたら、クラス一覧の生徒の行でチェックを実行します。保存されたマシンのコピーが起動されて（保存し戻されることはありません）、合格かどうかと出力内容が表示されます。結果は生徒には知らされません。

ヘルプマニュアルの[このラボのページ](https://mitsurutech.co.uk/ja/help/lab-hdl-lab)、[Ubuntu VM ラボのガイド](https://mitsurutech.co.uk/ja/help/labs)、[サンプルラボのページ](https://mitsurutech.co.uk/ja/help/sample-labs)に続きがあります。

まだ学校のアカウントがありませんか。[最初の 1 年は無料](https://mitsurutech.co.uk/ja/pricing)で、生徒 50 人まで使えます。[登録](https://mitsurutech.co.uk/ja/signup)にはメールアドレスだけが必要です。

## ファイルの検査

プッシュのたびに `.github/workflows/check.yml` が、ファイルが `cpus`、`memory`、`disk`、`setup`、`check` のキーを持つ有効な YAML であること、`setup` と `check` がそれぞれプラットフォームの 16 KB の上限内であることを確かめます。VM は起動しません。それはプラットフォーム自身のマシンで行われます。セットアップ、Basics レッスンのコマンド、チェックは、VM の代わりの Ubuntu 26.04 のコンテナーで実行して確かめました。授業の前に、実際に起動した生徒用 VM で一度試すことをお勧めします。

## ラボで動かないもの

VM が届くのは Ubuntu のパッケージアーカイブ、PyPI、npm、GitHub だけで、ほかには届きません。このラボに必要なものはすべて Ubuntu のアーカイブから入ります。ターミナルだけの VM にはデスクトップがないため、グラフィカルな波形ビューアーはありません。波形はテキスト、またはポート 8000 の画像として読みます。Ubuntu 26.04 の GHDL は GCC バックエンド版なので、`ghdl -e` はファイルの隣に実行ファイルを書き出します。

## このプロジェクトについて

[Mitsuru Technologies](https://mitsurutech.co.uk/ja) が [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)のために作りました。学校向けに、Python、Java、Go、Node、SQL、Linux をブラウザーで学べ、コンテナーラボ、VM ラボ、GitHub からデプロイする Web アプリ、自動採点のクイズがあります。アカウントなしでコードを試せる[プレイグラウンド](https://mitsurutech.co.uk/ja/playground)もあります。

同じ提供元のほかのラボ: [lab-netlab](https://github.com/mitsurutech/lab-netlab)、[lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop)、[lab-dvwa](https://github.com/mitsurutech/lab-dvwa)、[lab-webgoat](https://github.com/mitsurutech/lab-webgoat)、[lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin)、[lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea)、[lab-sample](https://github.com/mitsurutech/lab-sample)、[lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab)、[lab-reverse-lab](https://github.com/mitsurutech/lab-reverse-lab)、[lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab)、[lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab)、[lab-tofu-lab](https://github.com/mitsurutech/lab-tofu-lab)、[lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles)、[lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics)。

## ライセンス

この作品は [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) の下で提供されています。Mitsuru Technologies Ltd のクレジットを表示し、商用利用をせず、これをもとにしたものを同じライセンスで共有する場合に限り、学習と教育のために共有・改変できます。全文は `LICENSE` ファイルにあります。

[Icarus Verilog](https://steveicarus.github.io/iverilog/)（GPL-2.0 以降）、[GHDL](https://github.com/ghdl/ghdl)（GPL-2.0 以降）、[yosys](https://yosyshq.net/yosys/)（ISC）は VM の起動時に Ubuntu のアーカイブからインストールされます。ここでは何も配布していません。
