#　AWS CDKスタック間参照に関して

AWS CDKのスタック間参照に関して調べてみた。

##　スタックを分けるべきか

できるだけスタックを分けないほうが望ましい。理由としては以下がある。

- スタック間参照によるトラブルの回避
- 同一スタック内ではCloudFormationで並列処理を実施してくれるが、その利点がなくなる

どうしてもスタックをわけないといけない場合も多々ある
- スタック1つのリソースの制約(500個)
- 更新サイクルが異なる
- リージョンが異なる
- リソースをデプロイする間に手作業が必要（ECRスタックへのDockerイメージpushを手作業でやる、フロントエンドをデプロイする）

##　スタック間参照の前提

- 深い参照
  CloudFormationに変換された際に、importとなっているもの cdkoutputやprops渡し
- 浅い参照
  パラメータストアや命名での縛り、fromlookbyNameを使用する

深い参照になると困ること→被参照された側のリソースが削除されないと、参照しているリソースが更新でないこと（俺がみているやつ勝手に削除するな）
。データベースを削除しないと更新できないとなると困る。また、循環参照（2つのスタックで相互に深い参照担っている場合）は、更新できないこともある→CDK側をだます作業が必要になり、運用が複雑になる

※　パラメーターストア経由でも、DBなど更新できないリソースを参照している場合、実質的に深い参照になっている場合がある
※　props参照は依存関係の向きが逆なるので注意

パラメーターストアにも気をつけないといけないことがある
- １スタック200個まで
- CDK側で依存関係を見てデプロイ順序を解決してくれないので、明示的に指定する必要がある（addDependencyメソッドでこのパラメータに依存していますという依存関係を明示）
- valueFromLookupに格納された場合、cdk.context.jsonにキャッシュされる。キャッシュがある場合はそちらを参照するので注意（キャッシュ削除するコマンドもある）

## 参考文献

- [CDKでスタック間のパラメーターを受け渡す5つの方法とケース別の最適解について考えてみた](https://dev.classmethod.jp/articles/best-way-to-reference-parameters-in-cdk/)

- [SSMパラメーターストアでクロススタック参照の罠を回避する](https://speakerdeck.com/shuyakinjo/ssmharametasutoatekurosusutatukucan-zhao-nomin-wohui-bi-suru?slide=28)

- [AWS CDK Tips: スタックの分け方について](https://tmokmss.hatenablog.com/entry/20221121/1669032738)

-[3年間運用したCDKの失敗から学ぶCDK開発のプラクティス](https://www.docswell.com/s/integrated1453/5GXL7N-AWS-CDK-Conference-Japan-2023#p39)

-[CDK初心者が開発で遭遇したトラブルと解決法](https://speakerdeck.com/takanariohata/cdkchu-xin-zhe-gakai-fa-dezao-yu-sitatoraburutojie-jue-fa?slide=9)

-[CDKでスタック間参照してはならない](https://tech.uzabase.com/entry/2024/09/27/103506)

-[クロススタック参照で、参照先から参照を削除する場合は、参照元で exportValue を使おう](https://dev.classmethod.jp/articles/aws-cdk-closs-stack-reference-exportvalue/)

-[実践 AWS CDK 〜 いろいろな参照のカタチと使い分け 〜](https://speakerdeck.com/konokenj/reference-patterns-in-aws-cdk?slide=12)