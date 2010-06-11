require 'rubygems'
require 'rake/testtask'
require 'rake/rdoctask'

$LOAD_PATH.unshift File.expand_path("../lib", __FILE__)
require "typus/version"

desc 'Default: run unit tests.'
task :default => :test

desc 'Test the typus plugin.'
Rake::TestTask.new(:test) do |t|
  t.libs << 'lib'
  t.libs << 'test'
  t.pattern = 'test/**/*_test.rb'
  t.verbose = true
end

task :build do
  system "gem build typus.gemspec"
end

task :release => :build do
  version = Typus::VERSION
  system "git tag v#{version}"
  system "git push origin v#{version}"
  system "gem push pkg/typus-#{version}.gem"
  system "git clean -fd"
  system "gem push typus-#{version}"
end

##
# Docs
##

namespace :site do

  desc "Regenerate the documentation"
  task :build do
    command = `which asciidoc`.strip
    unless command.empty?
      puts "Building site using `asciidoc`."
      system "cd doc && #{command.strip} -a icons -a toc -o site/index.html 000-index.txt"
    else
      puts "AsciiDoc is not installed."
    end
  end

  desc "Update the website"
  task :deploy => :build do
    Dir.chdir("doc/site") do
      sh %(scp -r ./ fesplugas@labs.intraducibles.com:~/public_html/labs.intraducibles.com/current/public/projects/typus/documentation)
    end
  end

end

##
# Extras
##

desc "Generate specdoc-style documentation from tests"
task :specs do

  puts 'Started'
  timer, count = Time.now, 0

  File.open('SPECDOC', 'w') do |file|
    Dir.glob('test/**/*_test.rb').each do |test|
      test =~ /.*\/([^\/].*)_test.rb$/
      file.puts "#{$1.gsub('_', ' ').capitalize} should:" if $1
      File.read(test).map { |line| /test_(.*)$/.match line }.compact.each do |spec|
        file.puts "- #{spec[1].gsub('_', ' ')}"
        sleep 0.001; print '.'; $stdout.flush; count += 1
      end
      file.puts
    end
  end

  puts <<-MSG
\nFinished in #{Time.now - timer} seconds.
#{count} specifications documented.
  MSG

end
