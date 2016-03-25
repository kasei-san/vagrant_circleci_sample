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

# chef solo を入れる
