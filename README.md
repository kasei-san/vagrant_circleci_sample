Vagrant の環境を chef solo で作って、 Circle.ci 上でserverspec を実行させるデモ

理解のために

# 初手

```
vagrant init
```

Vagrantfile

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure(2) do |config|
  config.vm.box = "bento/centos-7.2"
end
```

# soerverspec 導入

Gemfile

```
gem 'serverspec'
```

```
serverspec-init
```

## サンプルのテストをシンプルに

spec/default/sample_spec.rb

```
require 'spec_helper'

describe service('httpd') do
  it { should be_enabled }
  it { should be_running }
end

describe port(80) do
  it { should be_listening }
end
```

## テストがこけることを確認

```
rake spec
```

# knife solo を入れる

いまさら knife solo かよ! ってのはあるけど、現在実業務で絶賛使用中のため


Gemfile

```
gem "chef"
gem "knife-solo"
```

## vagrant ssh-config で .ssg/config に入れる情報を作成


```
vagrant ssh-config --host sample >> ~/.ssh/config
```

これで、 `ssh sample` で接続できるようになる


## 初手

```
knife solo init .
```

ディレクトリが生成される

```
data_bags/
environments/
nodes/
roles/
site-cookbooks/
```

### 各ディレクトリの役割

いまいち分かってない

- **data_bags** : cookbookに含めたくないグローバルなデータを置く
- **environments** : 環境設定ファイル
- **nodes** : サーバ毎の設定ファイル
- **roles** : 役割設定ファイル
- **site-cookbooks** : 自作cookbook


## sample に chef をインストール


```
knife solo prepare sample
```

## recipeを実装

### site-cookbooks に apache をインストールする recipe を追加

```
knife cookbook apache -o site-cookbooks
```

### インストール & 起動時に動作しているように

site-cookbooks/apache/recipes

```ruby
package "httpd" do
  action :install
end

service "httpd" do
  action [ :enable, :start ]
end
```

## nodes/sample では、 apache をインストールするように設定


nodes/sample.json

```json
  "run_list": [
    "recipe[apache]"
  ],
```

## cook 実行

```
knife solo cook sample
```

## テストが通ることを確認

```
rake spec
```

# circle-ci で実行可能に




# 参考

- [Vagrant ssh-configでvagrant sshを快適にする - Qiita](http://qiita.com/Sanche/items/43d615beef05cd9417e2)
- [knife-soloによるITインフラ構築 - Qiita](http://qiita.com/_daisuke0802/items/f5446d031a33c8659d36)
- [chef-soloのレシピのカスタマイズの記録 | IsaB](http://blog.isao.co.jp/chef-solo_custom-recipe/)
- [naoya/circleci-serverspec: Example: Continuous Integration using vagrant-aws, Chef, serverspec and CircleCI](https://github.com/naoya/circleci-serverspec)

