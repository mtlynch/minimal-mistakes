source "https://rubygems.org"

require 'json'
require 'open-uri'
versions = JSON.parse(open('https://pages.github.com/versions.json').read)

gem 'github-pages', versions['github-pages'], group: :jekyll_plugins
gem 'jekyll-admin', group: :jekyll_plugins

gem "wdm", "~> 0.1.0" if Gem.win_platform?
gem 'html-proofer', '3.6.0'
gem 'mdl', '0.4.0'

group :jekyll_plugins do
  # gem "jekyll-archives"
end
