# Copyright (c) 2015 Yahoo Inc.
# Copyrights licensed under the New BSD License.
# See the accompanying LICENSE file for terms.

require 'bundler'
require 'bundler/gem_tasks'

begin
  Bundler.setup(:default, :development)
rescue Bundler::BundlerError => e
  $stderr.puts e.message
  $stderr.puts 'Run `bundle install` to install missing gems'
  exit e.status_code
end

require 'rake'
require 'rspec/core/rake_task'
require 'rubocop/rake_task'
require 'cucumber/rake/task'
require 'coveralls/rake/task'

desc 'Clean up build artifacts.'
task :clean do
  print 'Cleaning up build artifacts...'
  FileUtils.rm_rf('coverage')
  FileUtils.rm_rf('pkg')
  FileUtils.rm_rf('tmp')
  FileUtils.rm('mysqldump.xml') if File.file?('mysqldump.xml')
  puts 'DONE'
end

RSpec::Core::RakeTask.new

RuboCop::RakeTask.new

Cucumber::Rake::Task.new do |t|
  t.cucumber_opts = %w(--format progress)
end

Coveralls::RakeTask.new

desc 'By default run clean, rspec tests, rubocop, and cucumber tests.'
task default: [:clean, :spec, :rubocop, :cucumber, 'coveralls:push']

desc 'Run the irb console and require mysql_expectations.'
task :console do
  require 'irb'
  require 'irb/completion'
  require 'mysql_expectations'
  ARGV.clear
  IRB.start
end

RELISH_PROJECT = 'mysql-expectations/mysql-expectations'
RELISH_STAGING_PROJECT = 'mysql-expectations/mysql-expectations-staging'

require 'mysql_expectations/version'

desc 'Push cucumber features to http://relishapp.com'
task :relish do
  version = MySQLExpectations::Version::VERSION
  fail 'rake relish[VERSION]' unless version
  if `relish versions #{RELISH_PROJECT}`.split.map(&:strip).include? version
    puts "Version #{version} already exists"
  else
    sh "relish versions:add #{RELISH_PROJECT}:#{version}"
  end
  sh "relish push #{RELISH_PROJECT}:#{version}"
end

desc 'Push to relish staging environment of http://relishapp.com'
task :relish_staging do
  sh "relish push #{RELISH_STAGING_PROJECT}"
end
