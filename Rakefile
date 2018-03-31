# frozen_string_literal: true

require 'fileutils'
require 'rainbow'
require 'rake'
require 'sass'
require 'sass/plugin'

SASS_OPTIONS = {
  style: :expanded,
  syntax: :scss,
  sourcemap: :auto
}.freeze

def create_dir
  Dir.mkdir('dist') unless Dir.exist?('dist')
end

task :clean do
  FileUtils.rm_rf 'dist'
end

task build: %i[clean] do
  create_dir

  src_files = Rake::FileList['src/*.scss']
  src_files.each do |file|
    src_content = File.read(file) # Read file content
    filename = File.basename(file) # Get base file name
    sourcemap_filename = filename.ext('css.map') # Sourcemap file name
    dist_filename = filename.ext('css') # Dist file name

    options = SASS_OPTIONS.merge(filename: filename) # Options
    engine = Sass::Engine.new(src_content, options) # Create engine

    # Create CSS & sourcemap
    dist_content, sourcemap = engine.render_with_sourcemap(sourcemap_filename)
    sourcemap_content = sourcemap.to_json(css_uri: dist_filename, type: :auto)
    File.write("dist/#{dist_filename}", dist_content)
    File.write("dist/#{sourcemap_filename}", sourcemap_content)
  end

  puts Rainbow('Build succeed.').green
end

task watch: %i[build] do
  create_dir

  options = SASS_OPTIONS.merge( # Options
    template_location: 'src',
    css_location: 'dist'
  )
  compiler = Sass::Plugin::Compiler.new(options) # Create compiler

  puts Rainbow('Watching source files...').yellow

  compiler.watch do |modified|
    modified.each do |file|
      filename = File.basename(file) # Changed file name

      puts "#{Rainbow(filename).cyan} changed."
    end
  end
end
