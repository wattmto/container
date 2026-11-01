# Rakazo computer with Nix

Rakazo の computer イメージにシングルユーザー版 Nix を追加します。
GitHub Actions は固定した upstream のコミットからベースイメージをビルドし、
`linux/amd64` と `linux/arm64` のマルチアーキテクチャイメージを
`ghcr.io/<owner>/rakazo-computer-nix:latest` およびコミット SHA タグで公開します。
ベースイメージも `ghcr.io/<owner>/rakazo-computer-base:<commit SHA>` に公開されます。

`main` への関連ファイルの push または Actions の手動実行でビルドします。
GHCR パッケージの公開範囲は GitHub のパッケージ設定で変更できます。
ローカルで派生イメージをビルドする場合は、ビルド済みのベースを指定してください。

```sh
docker build --build-arg RAKAZO_BASE=ghcr.io/<owner>/rakazo-computer-base:<commit-sha> .
```

Nix は uid 1000 でデーモンなしで動作し、コンテナ内のため Nix のビルドサンドボックスは無効です。
`nix-command` と `flakes` を有効にしています。
