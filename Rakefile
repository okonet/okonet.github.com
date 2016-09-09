# Rake tasks to parse haml layouts, includes and index files and sass files for jekyll
# Assumes that the haml files are in (_layouts|_includes)/_haml
# and the sass files are in css/_sass

namespace :haml do
  require 'haml'

  def convert file, destination
    base_name = File.basename(file, '.haml') + '.html'
    html = File.open(file, 'r') { |f| Haml::Engine.new(f.read).render }
    File.open(File.join(destination, base_name), 'w') { |f| f.write html }
  end

  desc 'Parse haml layout files'
  task :layouts do
    Dir.glob('_layouts/_haml/*.haml') do |path|
      convert path, '_layouts'
    end

    puts 'Parsed haml layout files'
  end

  desc 'Parse haml include files'
  task :includes do
    Dir.glob('_includes/_haml/*.haml') do |path|
      convert path, '_includes'
    end

    puts 'Parsed haml include files'
  end

  desc 'Parse haml index files'
  task :indexes do
    Dir.glob('**/index.haml') do |path|
      convert path, File.dirname(path)
    end

    puts 'Parsed haml index files'
  end

end

desc 'Parse all haml items'
task haml: ['haml:layouts', 'haml:includes', 'haml:indexes']

desc 'Convert Sass to CSS'
task :sass do
  require 'sass'

  ['styles', 'print'].each do |file|
    css = File.open("_sass/#{file}.scss", 'r') { |f| Sass::Engine.new(f.read, :syntax => :scss).render }
    File.open("css/#{file}.css", 'w') { |f| f.write css }
    puts "Converted #{file}.scss"
  end

end

namespace :data do
  require 'net/http'

  desc 'Get OSS projects JSON data from GitHub'
  task :github do
    uri = URI.parse('https://api.github.com/users/okonet/repos?sort=updated')
    http = Net::HTTP.new(uri.host, uri.port)
    http.use_ssl = true
    request = Net::HTTP::Get.new(uri.request_uri)
    response = http.request(request)
    File.open('_data/projects.json', 'w') { |f| f.write response.body }
    puts 'Created/updated _data/projects.json'
  end
end

desc 'Build all haml and sass files for deployment'
task build: [:haml, :sass]
