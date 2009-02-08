# use pluginized rpsec if it exists
rspec_base = File.expand_path(File.dirname(__FILE__) + '/../rspec/lib')
$LOAD_PATH.unshift(rspec_base) if File.exist?(rspec_base) and !$LOAD_PATH.include?(rspec_base)

require 'spec/rake/spectask'
require 'spec/rake/verify_rcov'
require 'rake/rdoctask'

plugin_name = 'inherit_views'

task :default => :spec

desc "Run the specs for #{plugin_name}"
Spec::Rake::SpecTask.new(:spec) do |t|
  t.spec_files = FileList['spec/**/*_spec.rb']
  t.spec_opts  = ["--colour"]
end

desc "Generate RCov report for #{plugin_name}"
Spec::Rake::SpecTask.new(:rcov) do |t|
  t.spec_files  = FileList['spec/**/*_spec.rb']
  t.rcov        = true
  t.rcov_dir    = 'doc/coverage'
  t.rcov_opts   = ['--text-report', '--exclude', "spec/,rcov.rb,#{File.expand_path(File.join(File.dirname(__FILE__),'../../..'))}"] 
end

namespace :rcov do
  desc "Verify RCov threshold for #{plugin_name}"
  RCov::VerifyTask.new(:verify => :rcov) do |t|
    t.threshold = 100.0
    t.index_html = File.join(File.dirname(__FILE__), 'doc/coverage/index.html')
  end
end

task :rdoc => :doc

desc "Generate rdoc for #{plugin_name}"
Rake::RDocTask.new(:doc) do |t|
  t.rdoc_dir = 'doc'
  t.main     = 'README.rdoc'
  t.title    = "#{plugin_name}"
  t.template = ENV['RDOC_TEMPLATE']
  t.options  = ['--line-numbers', '--inline-source']
  t.rdoc_files.include('README.rdoc', 'History.txt', 'License.txt')
  t.rdoc_files.include('lib/**/*.rb')
end

namespace :doc do
  task :gh_pages => :doc do
    `mv doc doctmp`
    `git checkout -b gh-pages-tmp origin/gh-pages`
    `rm -rf doc`
    `mv doctmp doc`
    `git add doc`
    `git commit -m "Update API docs"`
    `git push git@github.com:ianwhite/inherit_views.git gh-pages`
    `git checkout master`
    `git branch -D gh-pages-tmp`
  end
end

task :cruise do
  sh "garlic clean && garlic all"
  Rake::Task['doc:gh_pages'].invoke
  puts "The build is GOOD"
end