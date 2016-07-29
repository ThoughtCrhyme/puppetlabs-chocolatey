require 'rototiller'
require 'rake'
require 'rspec/core/rake_task'
require 'puppetlabs_spec_helper/rake_tasks'
begin
  require 'beaker/tasks/test' unless RUBY_PLATFORM =~ /win32/
rescue LoadError
  #Do nothing, only installed with system_tests group 
end

# These lint exclusions are in puppetlabs_spec_helper but needs a version above 0.10.3 
# Line length test is 80 chars in puppet-lint 1.1.0
PuppetLint.configuration.send('disable_80chars')
# Line length test is 140 chars in puppet-lint 2.x
PuppetLint.configuration.send('disable_140chars')

task :default => [:test]

desc 'Run RSpec'
RSpec::Core::RakeTask.new(:test) do |t|
  t.pattern = 'spec/{unit}/**/*.rb'
#  t.rspec_opts = ['--color']
end

desc 'Generate code coverage'
RSpec::Core::RakeTask.new(:coverage) do |t|
  t.rcov = true
  t.rcov_opts = ['--exclude', 'spec']
end

flags = [
    {:name => '--preserve-hosts', :default => 'never', :override_env => 'PRESERVE_HOSTS'},
    {:name => '--config', :default => 'tests/configs/$PLATFORM'},
    {:name => '--keyfile', :default => '~/.ssh/id_rsa-acceptance'},
    {:name => '--load-path', :default => 'tests/lib'},
]

desc 'Executes reference tests (agent only) intended for use in CI'
rototiller_task :reference_tests do |t|

  t.add_flag(*flags)
  t.add_flag(
      {:name => '--tests', :default => 'tests/reference/tests'},
      {:name => '--pre-suite', :default => 'tests/reference/pre-suite'},
      {:name => '--type', :default => 'aio'},
  )

  t.add_command(name: 'beaker --debug')

  t.add_env do |env|
    env.name = 'PLATFORM'
    env.default = 'windows-2012r2-64a'
    env.message = 'Platform set to windows-2012r2-64a'
  end

  t.add_env do |env|
    env.name = 'MODULE_VERSION'
    env.default = '0.8.0-b20048-68d7507d'
  end
end

desc 'Executes acceptance tests (master and agent) intended for use in CI'
rototiller_task :acceptance_tests do |t|

  t.add_flag(*flags)

  t.add_flag(
       {:name => '--tests', :default => 'tests/acceptance/tests'},
       {:name => '--pre-suite', :default => 'tests/acceptance/pre-suite'},
       {:name => 'BEAKER_PE_DIR', :default => 'http://enterprise.delivery.puppetlabs.net/archives/releases/3.8.2'},
  )

  t.add_command(name: 'beaker --debug')

  t.add_env do |env|
    env.name = 'PLATFORM'
    env.default = 'windows-2012r2-64mda'
    env.message = 'Platform set to windows-2012r2-64mda'
  end

  t.add_env do |env|
    env.name = 'MODULE_VERSION'
    env.default = '0.8.0-b20048-68d7507d'
  end
end

