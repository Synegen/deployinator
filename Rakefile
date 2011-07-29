# Stolen from github.com/defunkt/mustache

require 'rake/testtask'

#
# Helpers
#

def command?(command)
  system("type #{command} &> /dev/null")
end


#
# Tests
#

task :default => :test

if command? :turn
  desc "Run tests"
  task :test do
    suffix = "-n #{ENV['TEST']}" if ENV['TEST']
    sh "turn test/*.rb #{suffix}"
  end
else
  Rake::TestTask.new do |t|
    t.libs << 'lib'
    t.pattern = 'test/**/*_test.rb'
    t.verbose = false
  end
end


#
# Stacks
#

desc "Create a new deployinator stack. usage: STACK=my_blog rake new_stack"
task :new_stack do
  require 'mustache/sinatra'
  stack = ENV['STACK']
  raise "You must supply a stack name (like STACK=foo rake new_stack)" unless stack
  raise "Already exists" if File.exists?("./stacks/#{stack}.rb")
  
  File.open("./stacks/#{stack}.rb", "w") do |f|
    contents = <<-EOF
module Deployinator
  module Stacks
    module #{Mustache.classify(stack)}
      def #{stack}_production_version
        # %x{curl http://my-app.com/version.txt}
        "1234-abc"
      end

      def #{stack}_rsync(options={})
        log_and_stream "Fill in the #{stack}_rsync method in stacks/#{stack}.rb!<br>"
      end
    end
  end
end
    EOF
    f.print contents
  end

  File.open("./templates/#{stack}.mustache", "w") do |f|
    f.print "{{< generic_single_push }}"
  end
  
  puts "Created #{stack}!\nEdit stacks/#{stack}.rb##{stack}_rsync to do your bidding"
end


#
# Documentation
# A github page at some point
#

desc "Publish to GitHub Pages"
task :pages => [ "man:build" ] do
  Dir['man/*.html'].each do |f|
    cp f, File.basename(f).sub('.html', '.newhtml')
  end

  `git commit -am 'generated manual'`
  `git checkout site`

  Dir['*.newhtml'].each do |f|
    mv f, f.sub('.newhtml', '.html')
  end

  `git add .`
  `git commit -m updated`
  `git push site site:master`
  `git checkout master`
  puts :done
end
