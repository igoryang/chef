#
# Author:: Adam Jacob (<adam@chef.io>)
# Author:: Daniel DeLeo (<dan@chef.io>)
# Copyright:: Copyright 2008-2016, Chef Software Inc.
# License:: Apache License, Version 2.0
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

VERSION = IO.read(File.expand_path("../VERSION", __FILE__)).strip

require "rubygems"
require "chef/version"
require "chef-config/package_task"
require "rdoc/task"
require_relative "tasks/rspec"
require_relative "tasks/maintainers"
require_relative "tasks/cbgb"
require_relative "tasks/dependencies"
require_relative "tasks/changelog"

ChefConfig::PackageTask.new(File.expand_path("..", __FILE__), "Chef", "chef") do |package|
  package.component_paths = ["chef-config"]
  package.generate_version_class = true
end
# Add conservative dependency update to version:bump (which was created by PackageTask)
task "version:bump" => %w{version:bump_patch version:update}
task "version:bump" => %w{version:bump_patch version:update}

task "version:bump_minor" do
  Rake::Task["changelog:archive"].invoke
  maj, min, _build = Chef::VERSION.split(".")
  File.open("VERSION", "w+") { |f| f.write("#{maj}.#{min.to_i + 1}.0") }
  Rake::Task["version"].invoke
  Rake::Task["bundle:install"].invoke
end

task "version:bump_major" do
  Rake::Task["changelog:archive"].invoke
  maj, _min, _build = Chef::VERSION.split(".")
  File.open("VERSION", "w+") { |f| f.write("#{maj.to_i + 1}.0.0") }
  Rake::Task["version"].invoke
  Rake::Task["bundle:install"].invoke
end

task :pedant, :chef_zero_spec

task :build_eventlog do
  Dir.chdir "ext/win32-eventlog/" do
    system "rake build"
  end
end

task :register_eventlog do
  Dir.chdir "ext/win32-eventlog/" do
    system "rake register"
  end
end

begin
  require "chefstyle"
  require "rubocop/rake_task"
  RuboCop::RakeTask.new(:style) do |task|
    task.options += ["--display-cop-names", "--no-color"]
  end
rescue LoadError
  puts "chefstyle/rubocop is not available.  gem install chefstyle to do style checking."
end

begin
  require "yard"
  DOC_FILES = [ "README.rdoc", "LICENSE", "spec/tiny_server.rb", "lib/**/*.rb" ]
  namespace :yard do
    desc "Create YARD documentation"

    YARD::Rake::YardocTask.new(:html) do |t|
      t.files = DOC_FILES
      t.options = ["--format", "html"]
    end
  end

rescue LoadError
  puts "yard is not available. (sudo) gem install yard to generate yard documentation."
end
