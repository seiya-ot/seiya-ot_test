# Active Directoryエージェント

株式会社インターネットイニシアティブ

https://www.iij.ad.jp

(C) Internet Initiative Japan Inc.

対象 Version: v1.0.1

## READMEについて
* このドキュメントは、Active Directoryエージェントの基本的なインストールおよびセットアップについて記載しています。
* アカウントコントロール機能の詳細については、ヘルプページを参照してください。

## ライセンスについて
全ての著作権は株式会社インターネットイニシアティブに帰属します。

## 概要
* Windows環境にインストールして、Windowsサービスとして実行できます。
* 実行には以下へのネットワーク疎通が可能な環境が必要です。
  * IIJ IGAサーバーへのHTTPS接続
  * 操作したい対象となるActive DirectoryがインストールされているサーバーへのLDAPS接続
* エージェントを起動するとIIJ IGAサーバーとWebSocketで常時接続します。

## インストール手順
このセクションでは、Windows環境にインストールする手順を説明します。

### APIトークンの用意
IIJ IGA管理画面のAPIトークンを用意してください。

IIJ IGAの機能制限設定画面で、以下の項目が有効になっているロールのトークンが必要です。
* 業務アセット
* ACタスク一覧

このAPIトークンはインストール時に設定し、接続時に使用されます。

APIトークン発行の詳細手順はヘルプを参照ください。

### エージェントのインストール

#### 動作環境
エージェントの動作には以下の環境が必要です。

##### 対応OS
エージェントをインストールできるOSは、以下いずれかのx64エディションです。

* Microsoft Windows Server 2022
* Microsoft Windows Server 2019
* Microsoft Windows Server 2016
* Microsoft Windows 7 以降の Windows OS

エージェントが接続できるActive DirectoryがインストールされているサーバーのOSは、以下のいずれかです。
* Windows Server 2022
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

##### 必須コンポーネント
Windows Installer 5.0以上

#### Windowsインストーラーを使用してインストール
Windowsインストーラーを使用してエージェントをインストールできます。
```
IIJActiveDirectoryAgent.msi
```

Windowsインストーラーを起動後、画面に従ってインストールを進めてください。

APIトークンの入力画面が出たら、先ほどのAPIトークンを入力してください。

※ `%ProgramFiles%`へプログラムを設置するため、必要な権限で実行してください。
※ インストール途中でコマンドラインシェル実行環境が表示されることがあります。
※ サービス実行ユーザーでインストールを行ってください。

#### 動作確認
コマンドラインシェル実行環境で以下のコマンドを実行してください。バージョン情報が表示されます。
```
"%ProgramFiles%\IIJActiveDirectoryAgent\IIJActiveDirectoryAgent.exe" --help
```
以下のコマンドでインストール時に設定したトークンの確認ができます。
```
"%ProgramFiles%\IIJActiveDirectoryAgent\IIJActiveDirectoryAgent.exe" viewToken
```

### 設定ファイルの作成と設置
実行前に設定ファイルを作成して、エージェントをインストールしたWindows環境に設置します。

#### 設定ファイルの作成
設定ファイルはiniファイル形式で作成します。

##### 設定ファイルの例
以下は、設定ファイルの例です。
(`{}`内を記載して使用してください。`;`はiniファイルのコメントです。）
* ファイル名
```
setting.ini
```
* ファイル内容
```ini
; IIJActiveDirectoryAgent 設定ファイル
version="1.0" ; 必須項目
host="sample.stg.igms.iij.jp" ; 必須項目(IIJ IGA APIの接続先を設定してください)
```

##### 設定項目について
各設定項目は、このREADMEの「設定可能な項目」を参照してください。

※ エージェントのバージョン毎に使用可能な設定ファイルのバージョンが決まっています。
※ 設定ファイルのメジャーバージョン（v{番号}、"v1.0"の場合v1）が同じ場合は互換性があるため使用可能です。
※ 以下のコマンドでバージョンを確認できます。
```
"%ProgramFiles%\IIJActiveDirectoryAgent\IIJActiveDirectoryAgent.exe" --help
```

#### 設置方法 設定ファイルディレクトリに設置する
以下がエージェントのデフォルトデータフォルダーです。
```
%ProgramData%\IIJActiveDirectoryAgent\
```
デフォルトデータフォルダー配下に、作成した設定ファイルを設置してください。
```
%ProgramData%\IIJActiveDirectoryAgent\setting.ini
```

※ %ProgramData% への書込は管理者権限が必要な場合があります。適切な権限で設置してください。
※ バッチ実行ユーザーが %ProgramData% へ読取アクセスできるようにしてください。

## 実行方法
インポートエージェントの実行方法について説明します。

### 手動実行
コマンドラインシェル実行環境を開いて、以下のプログラムを実行してください
```
"%ProgramFiles%\IIJActiveDirectoryAgent\IIJActiveDirectoryAgent.exe"
```

### サービス実行
#### サービスのインストール
サービスの設定を開いてトークンを設定したユーザーをサービス実行ユーザーとして設定してください。

[ログオン]タブの[アカウント]を選択後、[参照] をクリックしてユーザーを指定し、パスワードを入力します。

※ タスク実行ユーザーの設定したAPIトークンを使用するので、APIトークンを登録したユーザーで実行するように設定してください。
※ サービス失敗時の設定は、`install`実行時に設定されますが、サービスの設定画面では詳細が表示されません。
※ サービス失敗時の設定は、コマンドラインシェル実行環境を開いて以下のプログラムで確認できます。
```
sc qfailure IIJActiveDirectoryAgent
```
※ 上記設定は、エージェントの実行に必要ですので、変更する場合はお問い合わせください。

#### サービスのスタート
サービスの設定一覧からIIJActiveDirectoryAgentサービスを開き、［開始］を行ってください。

※ サービス実行時のログはWindowsイベントログに出力されます。

#### 設定の反映
設定を反映するには、サービスの停止、再開を行ってください。

##### 手順1
サービスの設定で［停止］を行ってください。

##### 手順2
反映したい設定ファイルを以下に設置してください。
```
%PROGRAMDATA%iij\IIJActiveDirectoryAgent\setting.ini
```

##### 手順3
サービスの設定で［開始］を行ってください。

## APIトークンの管理
* ユーザーごとに1つのAPIトークンを登録できます。
* エージェントは、実行ユーザーが登録したトークンを参照します。
* APIトークンはインストールの際に登録する以外に、ターミナルからプログラムを起動して登録・確認・削除することができます。
* アンインストール時にアンインストールを実行したユーザーの登録したトークンが削除されます。

### APIトークンの登録
実行中のIIJActiveDirectoryAgentサービスを停止してください。

コマンドラインシェル実行環境を開いて、以下のプログラムを実行してください。
```
"%ProgramFiles%\IIJActiveDirectoryAgent\IIJActiveDirectoryAgent.exe" setToken
```
入力プロンプトが表示されるのでAPIトークンを入力してください。
シークレット入力に対応しているため、入力内容はターミナル上に表示されません。

APIトークンを登録後にサービスを起動してください。

### APIトークンの確認
コマンドラインシェル実行環境を開いて、以下のプログラムを実行してください。
```
"%ProgramFiles%\IIJActiveDirectoryAgent\IIJActiveDirectoryAgent.exe" viewToken
```
登録したトークンの一部が表示されます。

### APIトークンの削除
実行中のIIJActiveDirectoryAgentサービスを停止してください。

コマンドラインシェル実行環境を開いて、以下のプログラムを実行してください。
```
"%ProgramFiles%\IIJActiveDirectoryAgent\IIJActiveDirectoryAgent.exe" deleteToken
```

※ この操作はエージェントが保管するトークンを削除しますが、APIトークンは有効です。APIトークン自体を無効にしたい場合、YESOD管理画面から停止をしてください。

## アップグレード・アンインストールについて
インストーラーを使用して実行します。

### アップグレードの実行
新しいバージョンのインストーラーからインストールしてください。

### アンインストールの実行
インストーラーを起動して、REMOVEを選択してください。

※ アンインストールをしたユーザー以外のトークンは削除されないため、アンインストール前に各ユーザーのトークンを削除してください。

## 設定可能な項目
設定ファイルで指定可能な項目について説明します。

### Agent全体の設定
* version
  * 必須
  * 設定ファイルのバージョンです。
  * 現在の最新版は1.0です。
* host
  * 必須
  * エージェントが接続するIIJ IGA環境を指定します。
  * 設定フォーマット：{sample}.igms.iij.jp sampleにはサブドメインを含む実際の接続先を指定してください
