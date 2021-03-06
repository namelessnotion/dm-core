require 'pathname'

source 'http://rubygems.org'

SOURCE       = ENV['SOURCE']   ? ENV['SOURCE'].to_sym              : :git
REPO_POSTFIX = SOURCE == :path ? ''                                : '.git'
DATAMAPPER   = SOURCE == :path ? Pathname(__FILE__).dirname.parent : 'http://github.com/datamapper'
DM_VERSION   = '~> 1.0.2'

group :runtime do # Runtime dependencies (as in the gemspec)

  if ENV['EXTLIB']
    gem 'extlib',        '~> 0.9.15', SOURCE => "#{DATAMAPPER}/extlib#{REPO_POSTFIX}", :require => nil
  else
    gem 'activesupport', '~> 3.0.3', :require => nil
  end

  gem 'addressable',     '~> 2.2'

end

group(:development) do # Development dependencies (as in the gemspec)

  gem 'rake',           '~> 0.8.7'
  gem 'rspec',          '~> 1.3.1'
  gem 'jeweler',        '~> 1.4.0'

end

group :quality do # These gems contain rake tasks that check the quality of the source code

  gem 'rcov',           '~> 0.9.7', :platforms => :mri_18
  gem 'yard',           '~> 0.5'
  gem 'yardstick',      '~> 0.1'

end

group :datamapper do # We need this because we want to pin these dependencies to their git master sources

  gem 'dm-core', DM_VERSION, :path => File.dirname(__FILE__) # Make ourself available to the adapters

  adapters = ENV['ADAPTERS'] || ENV['ADAPTER'] || 'in_memory'
  adapters = adapters.to_s.tr(',', ' ').split.uniq

  DO_VERSION     = '~> 0.10.2'
  DM_DO_ADAPTERS = %w[ sqlite postgres mysql oracle sqlserver ]

  if (do_adapters = DM_DO_ADAPTERS & adapters).any?
    options = {}
    options[:git] = "#{DATAMAPPER}/do#{REPO_POSTFIX}" if ENV['DO_GIT'] == 'true'

    gem 'data_objects',  DO_VERSION, options.dup

    do_adapters.each do |adapter|
      adapter = 'sqlite3' if adapter == 'sqlite'
      gem "do_#{adapter}", DO_VERSION, options.dup
    end

    gem 'dm-do-adapter', DM_VERSION, SOURCE => "#{DATAMAPPER}/dm-do-adapter#{REPO_POSTFIX}"
  end

  adapters.each do |adapter|
    unless adapter == 'in_memory'
      gem "dm-#{adapter}-adapter", DM_VERSION, SOURCE => "#{DATAMAPPER}/dm-#{adapter}-adapter#{REPO_POSTFIX}"
    end
  end

  plugins = ENV['PLUGINS'] || ENV['PLUGIN']
  plugins = plugins.to_s.tr(',', ' ').split.push('dm-migrations').uniq

  plugins.each do |plugin|
    gem plugin, DM_VERSION, SOURCE => "#{DATAMAPPER}/#{plugin}#{REPO_POSTFIX}"
  end

end
