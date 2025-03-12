# frozen_string_literal: true

source "https://rubygems.org"

gem "jekyll", "~> 4.4.1"
gem 'jekyll-theme-chirpy', '~> 7.2', '>= 7.2.4'
gem 'base64'

group :jekyll_plugins do
  gem 'jekyll-sitemap'
  gem 'jekyll-feed'
  gem 'jekyll-seo-tag'
  gem 'jekyll-archives'
  gem 'jekyll-paginate'
end

group :test do
  gem "html-proofer", "~> 5.0.9"
  gem "rake"
end

group :development do
  gem 'webrick'
  gem 'nokogiri', '~> 1.18'
  gem 'sass-embedded', '~> 1.83'
end

platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end

gem "wdm", "~> 0.2.0", :platforms => [:mingw, :x64_mingw, :mswin]

gem "http_parser.rb", "~> 0.8.0", :platforms => [:jruby]

gem "csv"
gem "logger"