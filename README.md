# docker-compose-override-example

## Overview

docker-compose.yml はリポジトリで管理されているため変更するとgitで差分とされてしまい、mergeやswitch、rebaseがやりにくい。


## Description

### なぜ docker-compose.yml を変更したいか？

- Proxy環境下でイメージをビルドするため、http_proxyなどを渡したい
- Proxy環境下でコンテナを動作させるため、http_proxyなどを渡したい
- PasswordやMasterKeyなどを環境変数で指定したい
- entrypointやcommandなどを変更したい


### 対策

#### リポジトリで管理されているファイルは変更せず、ファイルを作成する

Untrackedなファイルが存在しても競合しない。
(※切り替え先のブランチに同名ファイルが存在すると競合するので.gitignoreで管理対象外にしておく)


#### composeファイルを複数読み込む

> デフォルトにおいて Compose は 2 つのファイルを読み込みます。 `docker-compose.yml`と、必要に応じて編集する`docker-compose.override.yml`です。

https://matsuand.github.io/docs.docker.jp.onthefly/compose/extends/#multiple-compose-files

上書きしたい内容を`docker-compose.override.yml`で指定する。


#### COMPOSE_FILEを指定する

.envで環境変数`COMPOSE_FILE`を指定すると、 `-f` オプション無しで `docker-compose` を実行した際に指定した順序ですべて読み込まれる。
(※ `-f` オプションありの場合`COMPOSE_FILE`は無視される)

`docker-compose.override.yml`以外のファイル名で読み込む場合に使用する。

```
COMPOSE_FILE=docker-compose.yml:docker-compose.local.yml
```

これにより `docker-compose config` と `docker-compose -f docker-compose.yml -f docker-compose.local.yml config` は同じ結果になる。

`COMPOSE_FILE`で指定したファイルが1つでも読み込めない場合はエラーになる。


#### (備考) .envファイル

docker-compose で読み込まれる.envファイルは1つのみ。

https://matsuand.github.io/docs.docker.jp.onthefly/compose/environment-variables/#the-env-file



## Conclusion

`docker-compose.override.yml`を使おう！

`.env`と`docker-compose.override.yml`はgit管理対象外にしておこう！

