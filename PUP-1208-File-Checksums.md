# Puppet 3.7.4

## master

    docker run -it --name server mikaelsmith/puppet:3.7.4 bash
    # TODO: Setup manifest, add module with files to transfer
    puppet master --no-daemonize --autosign=true

## agent

    docker run -it --link server:server mikaelsmith/puppet:3.7.4 bash
    puppet agent -t --server server


# Puppet-Agent/PuppetServer

## master

    docker run -it --name server mikaelsmith/puppetserver bash
    # TODO: Setup manifest, add module with files to transfer
    echo ‘autosign = true’ >> /etc/puppetlabs/puppet/puppet.conf
    sed -i 's/#max-active-instances: 1/max-active-instances: 2/' /etc/puppetlabs/puppetserver/conf.d/puppetserver.conf
    puppetserver foreground

# agent
    docker run -it --link server:server mikaelsmith/puppet-agent bash
    puppet agent -t --server server
    # ... repeat ...

