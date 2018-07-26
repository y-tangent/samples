# gitlab install

## git



## gitlab

- https://docs.gitlab.com/ee/README.html
  - https://docs.gitlab.com/ee/install/
- https://about.gitlab.com/installation/
- https://qiita.com/shela/items/b62dc775015755d92ad7


外部urlを変更する(添付画像のアップロード先のurlともなる)

- 変更前

``` :/etc/gitlab/gitlab.rb
external_url 'http://gitlab.example.com'
```

- 変更後

``` :/etc/gitlab/gitlab.rb
external_url 'http://my_address'
```

### settings

- https://qiita.com/mikoski01/items/7a7795a8a1e98d9ba6d9

運用ルール

- グループメンバーはどの権限でも参照は可能なため基本的にはグループとプロジェクトは一対一
  - 一対一の場合はグループ名とプロジェクト名を同一にする
  - 一対多の場合は適切なグループ名をつける
