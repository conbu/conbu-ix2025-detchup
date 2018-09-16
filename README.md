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

