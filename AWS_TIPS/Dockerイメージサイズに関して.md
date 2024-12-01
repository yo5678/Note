# Dockerイメージサイズに関して

## Dockerイメージサイズの軽量化のメリット

- デプロイ速度が上がる
- Lambda on Dockerは初期起動の時間短縮の可能性（https://blog.serverworks.co.jp/reinvent2020-lambda-container-spinup）

## イメージ削減方法

- ディストリビューションの軽量化（効果大）
- マルチステージビルド
- &&結合を用いてLayerを減らす（効果小）
- 不要なファイルをコンテナに含めない
- 頻繁に変更されるものは後に記述する（変更のないものはキャッシュ利用をしてスキップされるため）
  - requirements.txtなどは変わらないことが多いので、このファイルだけを先にコピー→変更点があるファイルだけをコピーとすると効率化すると思った


参考文献

-[コンテナイメージサイズ削減手法の効果](https://qiita.com/y_k_individual/items/ccd17f6f80a3024a0884)

-[軽量なDockerイメージを作るために意識すること](軽量なDockerイメージを作るために意識すること)

-[Docker Buildにおけるリードタイム短縮のための3つの改善ポイント](https://tech.plaid.co.jp/improve_docker_build_efficiency#3.%20cache%E3%81%AE%E6%9C%89%E5%8A%B9%E6%B4%BB%E7%94%A8)