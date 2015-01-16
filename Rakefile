require 'html/proofer'

desc 'Run tests on the generated site'
task :test do
  sh 'bundle exec jekyll build'

  jekyll_config = YAML::load_file('_config.yml')
  url_regexp = /^#{Regexp.escape(jekyll_config['url'])}/

  HTML::Proofer.new("./_site", { href_ignore: ['#', url_regexp] }).run
end

namespace :preview do
  task :draft do
    sh 'bundle exec jekyll serve --config _config.yml,_config_draft.yml --future --drafts --unpublished'
  end

  desc 'Preview locally (site.draft flag set and only published posts)'
  task :local do
    sh 'bundle exec jekyll serve --config _config.yml,_config_draft.yml'
  end

  desc 'Preview before release (exactly as it will be live)'
  task :release do
    sh 'bundle exec jekyll serve'
  end
end

desc 'Preview for editing (site.draft flag set and draft, unpublished and future posts showing)'
task :preview => 'preview:draft'
