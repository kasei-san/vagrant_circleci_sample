Vagrant を Circle.ci で動作させて、serverspecを実行させるデモ

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

# chef solo を入れる
