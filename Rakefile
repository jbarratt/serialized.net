task :default => :dev

desc 'Notify Google of the new sitemap'
task :sitemap do
  begin
    require 'net/http'
    require 'uri'
    puts '* Pinging Google about our sitemap'
    Net::HTTP.get('www.google.com', '/webmasters/tools/ping?sitemap=' + URI.escape('http://serialized.net/sitemap.xml'))
  rescue LoadError
    puts '! Could not ping Google about our sitemap, because Net::HTTP or URI could not be found.'
  end
end

desc 'Run Jekyll in development mode'
task :dev => ["style/merged.css"] do
  puts '* Running Jekyll with auto-generation and server'
  puts jekyll('--auto --server')
end

desc 'Run Jekyll to generate the site'
task :build => ["style/merged.css"] do
  puts '* Removing old site'
  puts `rm -r ./_site/*`
  puts '* Generating static site with Jekyll'
  puts jekyll("--lsi")
  #puts jekyll
end

file "style/merged.css" => ["style/syntax.css", "style/template.css", "style/libraries.css", "style/grids.css", "style/content.css", "style/mod.css"] do |t|
    #sh "cat #{t.prerequisites.join(' ')} > #{t.name}"
    sh "cat #{t.prerequisites.join(' ')} > #{t.name}.devel"
    sh "java -jar ~/.java/yuicompressor-2.4.2.jar --type css -o style/merged.css #{t.name}.devel"
end


desc 'rsync the contents of ./_site to the server'
task :sync do
  puts '* Publishing files to live server'
  puts `rsync -avz --delete "_site/" serialized.net@serialized.net:domains/serialized.net/html/`
end

desc 'rsync the contents of ./_site to the staging path on the server'
task :stage do
  puts '* Publishing files to live server'
  puts `rsync -avz --delete "_site/" serialized.net@serialized.net:domains/stage.serialized.net/html/`
end



desc 'Push source code to Github'
task :push do
  puts '* Pushing to Github'
  puts `git push github master`
end

desc 'Create and push a tag'
task :tag, :title, :message do |t, args|
  t = args.title
  m = args.message
  unless t && m
    puts "USAGE: rake tag T='1.0-my-tag-name' M='My description of this tag'"
    exit(1)
  end

  puts '* Creating tag'
  puts `git tag -a -m "#{m}" #{t}`

  puts '* Pushing tags'
  puts `git push github master --tags`
end

desc 'Generate and publish the entire site, and send out pings'
task :publish => [:build, :push, :sync, :sitemap]  do
end

desc 'create a new draft post'
task :post, :title do |t, args|
  title, slug = get_title(args.title)
  file = File.join(File.dirname(__FILE__), '_posts', slug + '.markdown')
  create_blank_post(file, title)
  open_in_editor(file)
end

desc 'List all draft posts'
task :drafts do
  puts `find ./_posts -type f -exec grep -H 'published: false' {} \\;`
end

def jekyll(args = nil)
  `~/.ruby/lib/ruby/gems/1.8/bin/jekyll #{args}`
end

# Helper method for :draft and :post, that required a TITLE environment
# variable to be set. If there is none, the task will exit.
#
# If there is a title given, then this method will return it and a escaped
# version suitable for filenames.
def get_title(title)
  require 'date'
  if title.nil?
    puts "USAGE: rake post TITLE='the post title'"
    exit(1)
  end
  return [title, "#{Date.today}-#{title.downcase.gsub(/[^\w]+/, '-')}"]
end

# Helper method for :draft and :post, that will create a file at a given
# location and fill it with an empty post.
def create_blank_post(path, title)
  # Create the directories to this path if needed
  FileUtils.mkpath(File.dirname(path))

  # Write the template to the file
  File.open(path, "w") do |f|
    f << <<-EOS.gsub(/^    /, '')
    ---
    layout: post
    title: #{title}
    published: false
    ---

    EOS
  end
end

# Helper method to open a file in the default text editor.
def open_in_editor(file)
  if (ENV['EDITOR'])
    system ("#{ENV['EDITOR']} #{file}")
  end
end
