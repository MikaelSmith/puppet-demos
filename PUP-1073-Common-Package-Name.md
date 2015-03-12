# Puppet 3.7.4

    docker run -it mikaelsmith/puppet:3.7.4 bash

    echo 'package { "vim": }' > test.pp
    echo 'package { "gem-vim": name => "vim", provider => gem }' >> test.pp
    puppet apply test.pp

So people do awful things like:

    package { "vim":
      ensure => present,
    }
    exec { "I hate myself and I want to die":
      command => "gem install vim",
      creates  => "/usr/local/share/gems/gems/vim-0.0.1/Gemfile"
    }

# Puppet-Agent Nightly (March 10th)

    docker run -it mikaelsmith/puppet-agent bash

    puppet apply -e 'package { "vim": }; package { "gem-vim": name => "vim", provider => gem }'

# Notes

## `puppet package resource` can be surprising

- puppet resource package vim —param provider - checks providers in alphabetical order (PUP-4184)
- puppet resource package vim … - uses type properties, so default provider unless specified
- no way to view whether a package resource is installed if not the 1st one found (PUP-3721)

