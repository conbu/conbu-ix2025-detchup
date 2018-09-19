### CONBU IX2025 向けでっちあげスクリプト

初期化して再起動した状態のIX2025をシリアルコンソールに接続してこのスクリプトを動かすと、よしなにセットアップしてくれる子。

```
書式
sudo ruby ./conbu-id2025-detchup.rb -c [コンフィグ] -t [テンプレート] -i [ターゲットの名前] -C [コンソールデバイス]

例
sudo ruby ./conbu-ix2025-detchup.rb -c sample.config.json -t ix2025-template.txt  -i IX00X -C /dev/tty.usbserialXXXX
```

- IX2025のみ対応
- 付属するテンプレート(ix2025-template.txt)では上流としてDHCPを想定しています
- 実行にあたりcuが必要です
- IX2025でerase startup-config と reloadをした状態で実行してください
- 他のターミナルのscreen/cuは全部落とした状態で実行してください
- エラーチェック等はないので目でgrepしてください。
- 設定可能なパラメータは sample.config.json をご参照ください

### 使い方

##### 0.機器のシリアルコンソールを握る

ここでは screen あるいは cu を想定しています

```
自分のPCで
% sudo screen /dev/tty.usbserialXXX 9600
または
% sudo cu -l /dev/tty.usbserialXXX -s 9600
```

##### 1.ターゲットのC3560を初期化する

パスワードがわかっている場合は以下の通り設定をすべて吹き飛ばしましょう。

```
# erase startup-config
# reload
```

あるいは最初からスーパーリセットによる初期化を行う場合は取り扱い説明書の「スーパーリセット」(6-35)の手順に従って初期化しましょう

  - [UNIVERGE IXシリーズ マニュアル](https://jpn.nec.com/univerge/ix/Manual/index.html#um)

##### 2.sreen/cuを終了する

以下のプロンプトが出ている状態でscreenあるいはcuを落とします
```
Router#

screenなら"^a+k"
cuなら"~~"
```

##### 3.conbu-ix2025-detchupを実行する

```
たとえばIX101をターゲットに実行する場合

% sudo ruby ./conbu-ix2025-detchup.rb -c config.json -t c3560-template.txt -i IX101 -C /dev/tty.usbserialXXX
```

途中で設定されるコンフィグが表示されるので確認してENTER、やめる場合はctrl + c


##### 4.再度シリアルコンソールを握り、コンフィグ確認 & write memory

write memoryまでは行わないため、再度screenなどで入りコンフィグをチェックした上でwrite memoryないしcopy run startしてください。


### コンフィグのパラメータ

コンフィグ(config.json)は以下のような構造のJSONファイルです。

```
{
	"common": {        # 複数機器間で共通のパラメータを書く場所
		"user": "testuser",
		...
	},

	"SW101": {         # スイッチごとに固有のパラメータを書く場所
		"port":
	},
	...                # 各スイッチの設定が並ぶ
}
```

テンプレート内では共通パラメータを common 、個別パラメータを my として参照可能です。
コマンドのsオプションで指定したスイッチ名と上記"SW101"といった文字列が照合され、マッチする名前のコンフィグ内のエントリが my に格納されます。

基本的にはハッシュに書いた内容がそのまま渡されます。
ただし、以下のパラメータがテンプレートエンジンにより自動的に生成されます。

- "hostname": iオプションで指定したターゲットの名前をそのまま利用
- "mgmt-addr-host": mgmt-prefixからホストのアドレスを生成したもの(プレフィクスを除去)
- "user-addr-host": user-prefixからホストのアドレスを生成したもの(プレフィクスを除去)
