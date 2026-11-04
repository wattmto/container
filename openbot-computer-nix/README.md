# OpenBot computer with Nix

OpenBot の公式サーバーイメージ(`ghcr.io/nightly-labs/openbot`)に
シングルユーザー版 Nix を追加します。
GitHub Actions はピンしたバージョン(既定 `0.40.0`)をベースに、
`linux/amd64` と `linux/arm64` のマルチアーキテクチャイメージを
`ghcr.io/<owner>/openbot-computer-nix:latest` および `sha-<コミット SHA>` タグで公開します。

PR、`main` への関連ファイルの push、または Actions の手動実行で同じ Dockerfile をビルドします。
同一リポジトリからの PR では `pr-<番号>-<ヘッド SHA>` タグで公開し、`latest` は更新しません。
フォークからの PR はビルドのみ行います。
GHCR パッケージの公開範囲は GitHub のパッケージ設定で変更できます。
ローカルで派生イメージをビルドする場合は、ベースを指定してください。

```sh
docker build --build-arg OPENBOT_BASE=ghcr.io/nightly-labs/openbot:0.40.0 openbot-computer-nix
```

Nix は uid 1000 でデーモンなしで動作し、コンテナ内のため Nix のビルドサンドボックスは無効です。
`nix-command` と `flakes` を有効にしています。
Nix の状態(プロファイル、チャンネル)は `XDG_STATE_HOME=/nix/state` でストアと同じ `/nix` に置いています。
`$HOME/.local/state`(= `/data`)に置くと OpenBot のデータボリュームと混ざり、
復元時や既存ボリュームで消えるためです。
インストーラーが `nixpkgs-unstable` のチャンネルを追加するので、
`nix-shell -p` や `nix run` は追加設定なしで nixpkgs を解決します。
`NIX_PATH` は `nixos-unstable` を指すので、`nix-shell -p` や `nix run` がそのまま使えます。

実行時の注意(公式 docs/docker.md 準拠):

- 起動には公式の `seccomp` プロファイル、`--shm-size 1g`、`--stop-timeout 60`、
  データ用の `/data` ボリュームが必要です
- Nix ストアはコンテナ層に書かれるため、`/nix` をボリュームにすると
  イメージの再ビルドをまたいで保持できます
- arm64 のベースイメージにはリモートデスクトップのランタイムが含まれません
  (live view は amd64 のみ)
