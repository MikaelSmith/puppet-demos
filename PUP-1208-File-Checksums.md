# Puppet 3.7.4

## master

Setup a master serving a module containing a large file, verified via sha256.

    docker run -it --name server3 mikaelsmith/puppet:3.7.4 bash

    mkdir -p /etc/puppet/manifests /etc/puppet/modules/test_module/{manifests,files}
    fallocate -l 200M /etc/puppet/modules/test_module/files/source_file
    echo 'class test_module {' > /etc/puppet/modules/test_module/manifests/init.pp
    echo 'file { "/tmp/test_file": source => "puppet:///modules/test_module/source_file", checksum => sha256 }' >> /etc/puppet/modules/test_module/manifests/init.pp
    echo '}' >> /etc/puppet/modules/test_module/manifests/init.pp
    echo 'node default { include test_module }' > /etc/puppet/manifests/site.pp

    puppet master --no-daemonize --autosign=true --certname server3

## agent

    docker run -it --link server3:server3 mikaelsmith/puppet:3.7.4 bash

    puppet agent -t --server server3

## Try with md5

No longer syncs file, but still does checksum.

## Try with mtime

Syncs every time (same as sha256).

# Puppet-Agent/PuppetServer Nightlies (March 10th)

## master

    docker run -it --name server4 mikaelsmith/puppetserver bash

    mkdir -p /etc/puppetlabs/code/environments/production/modules/test_module/{manifests,files}
    fallocate -l 200M /etc/puppetlabs/code/environments/production/modules/test_module/files/source_file
    echo 'class test_module {' > /etc/puppetlabs/code/environments/production/modules/test_module/manifests/init.pp
    echo 'file { "/tmp/test_file": source => "puppet:///modules/test_module/source_file", checksum => sha256 }' >> /etc/puppetlabs/code/environments/production/modules/test_module/manifests/init.pp
    echo '}' >> /etc/puppetlabs/code/environments/production/modules/test_module/manifests/init.pp
    echo 'node default { include test_module }' > /etc/puppetlabs/code/environments/production/manifests/site.pp

    echo autosign = true >> /etc/puppetlabs/puppet/puppet.conf
    echo certname = server4 >> /etc/puppetlabs/puppet/puppet.conf
    sed -i 's/#max-active-instances: 1/max-active-instances: 2/' /etc/puppetlabs/puppetserver/conf.d/puppetserver.conf
    puppetserver foreground

# agent
    docker run -it --link server4:server4 mikaelsmith/puppet-agent bash

    puppet agent -t --server server4

## Try with mtime

Nearly instant.

