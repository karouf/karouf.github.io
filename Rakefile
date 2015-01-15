require 'html/proofer'

desc 'Run tests on the generated site'
task :test do
  sh 'bundle exec jekyll build'
  HTML::Proofer.new("./_site", { href_ignore: ['#'] }).run
end

namespace :preview do
  task :draft do
     sh 'bundle exec jekyll serve --config _config.yml,_config_draft.yml --future --drafts --unpublished'
  end

  desc 'Preview before release (as it will be on production)'
  task :release do
    sh 'bundle exec jekyll serve'
  end
end

desc 'Preview for editing'
task :preview => 'preview:draft'
