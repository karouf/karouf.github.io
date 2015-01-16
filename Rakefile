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
task :publish => [:test, :push, :ping]

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

namespace :ping do
  task :all => [:google, :yahoo, :bing, :pingomatic]

  desc 'Ping Google'
  task :google do
    begin
      require 'net/http'
      require 'uri'
      puts '* Pinging Google about our sitemap'
      Net::HTTP.get('www.google.com', '/webmasters/tools/ping?sitemap=' + sitemap)
    rescue LoadError
      puts 'Error! Could not ping about the sitemap, because Net::HTTP or URI could not be found.'
    end
  end

  desc 'Ping Yahoo'
  task :yahoo do
    begin
      require 'net/http'
      require 'uri'
      puts '* Pinging Yahoo about our sitemap'
      Net::HTTP.get('search.yahooapis.com', '/SiteExplorerService/V1/ping?sitemap=' + sitemap)
    rescue LoadError
      puts 'Error! Could not ping about the sitemap, because Net::HTTP or URI could not be found.'
    end
  end

  desc 'Ping Bing'
  task :bing do
    begin
      require 'net/http'
      require 'uri'
      puts '* Pinging Bing about our sitemap'
      Net::HTTP.get('www.bing.com', '/webmaster/ping.aspx?sitemap=' + sitemap)
    rescue LoadError
      puts 'Error! Could not ping about the sitemap, because Net::HTTP or URI could not be found.'
    end
  end

  desc 'Ping Ping-o-matic'
  task :pingomatic do
    begin
      require 'xmlrpc/client'
      puts '* Pinging Ping-o-matic'
      XMLRPC::Client.new('rpc.pingomatic.com', '/', 80, proxy_host, proxy_port).call('weblogUpdates.extendedPing', jekyll_config['title'], jekyll_config['url'], "#{jekyll_config['url']}/sitemap.xml")
    rescue LoadError
      puts 'Error! Could not ping ping-o-matic, because XMLRPC::Client could not be found.'
    end
  end
end

desc 'Let the world know the site has been updated'
task :ping => 'ping:all'

def jekyll_config
  YAML::load_file('_config.yml')
rescue
  puts "Error! Could no load Jekyll config: no '_config.yml' in the current directory."
end

def proxy_host
  if proxy_url = ENV['http_proxy']
    URI(proxy_url).host
  else
    nil
  end
end

def proxy_port
  if proxy_url = ENV['http_proxy']
    URI(proxy_url).port
  else
    nil
  end
end

def sitemap
  sitemap = URI.escape("#{jekyll_config['url']}/sitemap.xml")
end
