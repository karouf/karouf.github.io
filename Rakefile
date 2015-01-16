require 'html/proofer'

desc 'Run tests on the generated site'
task :test do
  sh 'bundle exec jekyll build'

  jekyll_config = YAML::load_file('_config.yml')
  url_regexp = /^#{Regexp.escape(jekyll_config['url'])}/

  puts '* Testing the generated site'
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

desc 'Publish the site'
task :publish => [:test, :push]

task :check_git do
  puts '* Checking if the repository is clean to push'
  status = `git status --porcelain --untracked-files=no`
  if status =~ /\S/
    raise ' ! Warning: working directory is not clean. Please commit first!!'
  end
end

desc 'Git push to all remotes'
task :push => :check_git do
  remotes = `git remote`.split
  puts "* Pushing code to all remote repositories: (#{remotes.join(', ')})"
  remotes.each do |remote|
    sh "git push #{remote} --all"
  end
end
