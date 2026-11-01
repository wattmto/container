# Rakazo computer with Nix

Rakazo の computer イメージにシングルユーザー版 Nix を追加します。
GitHub Actions は固定した upstream のコミットからベースイメージをビルドし、
`linux/amd64` と `linux/arm64` のマルチアーキテクチャイメージを
`ghcr.io/<owner>/rakazo-computer-nix:latest` およびコミット SHA タグで公開します。
ベースイメージはビルド中のローカルレジストリにのみ保存します。

PR、`main` への関連ファイルの push、または Actions の手動実行で同じ Dockerfile をビルドします。同一リポジトリからの PR では `pr-<番号>-<ヘッド SHA>` タグで公開し、`latest` は更新しません。フォークからの PR はビルドのみ行います。
GHCR パッケージの公開範囲は GitHub のパッケージ設定で変更できます。
ローカルで派生イメージをビルドする場合は、ビルド済みのベースを指定してください。

```sh
git clone https://github.com/elie222/rakazo.git /tmp/rakazo
git -C /tmp/rakazo checkout 7e452be093027365637b84a45c3ca4463600bcc1
docker build -t rakazo-computer-base:local /tmp/rakazo/infra/sandboxes/computer
docker build --build-arg RAKAZO_BASE=rakazo-computer-base:local rakazo-computer-nix
```

Nix は uid 1000 でデーモンなしで動作し、コンテナ内のため Nix のビルドサンドボックスは無効です。
`nix-command` と `flakes` を有効にしています。
