task :default => :generate

desc 'Deploy with rake "depoly[comment]"'
task :deploy, [:comment] => :generate do |t, args|
  if args.comment then
    `git commit . -m '#{args.comment}' && git push`
  else
    `git commit . -m 'new deployment' && git push`
  end
end

desc 'deploy to server via rsync'
task :rsync do
  # uploads ALL files b/c I often do site-wide changes and prefer overwriting all
  puts 'deploying by rsync'
  sh "rsync -rtzh --progress --delete _site/ --rsh='ssh -pXX' YOUR_SERVER:path/to/blog"
  puts 'done!'
end

desc 'Clean up'
task :clean do
  `rm -rf _site`
end

desc 'Build site with Jekyll'
task :generate do
  sh 'rm -rf _site'
  jekyll
end

def jekyll
  # time to give me generation times
  # I'm just curious about how long it takes
  sh 'time jekyll'
  # compass already configured via config.rb in root
  sh 'compass compile'
end

desc 'Start server'
task :server => :clean do
  `bundle exec jekyll serve`
end

desc 'create new post or bit. args: title, future (# of days)'
# rake new future=0 title="New post title goes here" slug="slug-override-title" link="http://jma.im"
task :new do
  require 'rubygems'
  require 'chronic'

  title = ENV["title"] || "New Title"
  future = ENV["future"] || 0
  slug = (ENV["slug"] ? ENV["slug"].gsub(' ','-').downcase : nil) || title.gsub(' ','-').downcase
  link = ENV["link"] || ""

  if future.to_i < 3
    TARGET_DIR = "_posts/#{Time.new.strftime('%Y')}"
  else
    TARGET_DIR = "_drafts"
  end

  if future.to_i.zero?
    filename = "#{Time.new.strftime('%Y-%m-%d')}-#{slug}.md"
  else
    stamp = Chronic.parse("in #{future} days").strftime('%Y-%m-%d')
    filename = "#{stamp}-#{slug}.md"
  end
  uuid = `uuidgen | tr "[:upper:]" "[:lower:]" | tr -d "\n"`
  path = File.join(TARGET_DIR, filename)
  post = <<-HTML
---
layout: single
title: "#{title}"
date: #{Time.new.to_s}
guid: urn:uuid:#{uuid}
tags:
  -
categories:
  -
external-url: #{link}
feature: false
---

HTML
  File.open(path, 'w') do |file|
    file.puts post
  end
  puts "new post generated in #{path}"
  # system "open -a atom #{path}"
  system "code #{path}"
end
