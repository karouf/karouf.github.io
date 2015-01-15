require 'html/proofer'

desc 'Run tests on the generated site'
task :test do
  sh "bundle exec jekyll build"
  HTML::Proofer.new("./_site", { href_ignore: ['#'] }).run
end
