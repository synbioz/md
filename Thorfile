#!/usr/bin/env ruby

require 'rubygems'
require 'thor'
require 'redcarpet'
require 'liquid'

class ::MD < Thor # :: is used to escape Thor::Sandbox
  FORMATS = %w{html pdf}
  DEFAULT_PATH = File.expand_path("~/.config/md/themes/default")
  DEFAULT_LAYOUT = File.join(DEFAULT_PATH, "layout.html")
  DEFAULT_HTML_CSS = File.join(DEFAULT_PATH, "css", "html.css")
  DEFAULT_PDF_CSS = File.join(DEFAULT_PATH, "css", "pdf.css")
  DEFAULT_OUTPUT_PATH = File.expand_path("output")

  module Config
    MarkdownDefaults = {
      fenced_code_blocks: true,
      no_intra_emphasis: true
    }

    class Renderer < Redcarpet::Render::HTML; end

    private_constant :MarkdownDefaults
    private_constant :Renderer

    class << self
      def markdown_options(options = {})
        MarkdownDefaults.update(options)
      end

      def renderer(&block)
        return Renderer unless block_given?

        Renderer.class_eval(&block)
      end
    end
  end

  namespace 'md'
  default_task :generate

  desc 'setup', 'Create config files.'
  def setup
    FileUtils.mkdir_p File.expand_path("~/.config/md/themes/default/css")
    FileUtils.mkdir_p File.expand_path("~/.config/md/themes/default/images")
    FileUtils.cp "layout.html", DEFAULT_PATH
    FileUtils.cp_r "css/", DEFAULT_PATH
    FileUtils.cp_r "images/", DEFAULT_PATH
  end

  desc 'generate MD_FILENAME', 'Generate HTML or PDF from md.'
  method_option :format, :type => :string, :aliases => "-f", :desc => "Output format (html, pdf)", :default => "html"
  method_option :output, :type => :string, :aliases => "-o", :desc => "Output file"
  def generate(md_file)
    ensure_layout_presence(options)
    apply_mdfile!

    begin
      if FORMATS.include?(options[:format])
          if FORMATS.include?(options[:output])
            self.send("generate_#{options[:format]}", md_file, DEFAULT_OUTPUT_PATH)
          else
            self.send("generate_#{options[:format]}", md_file, options[:output])
          end
      else
        raise "Unknown format"
      end
    rescue
      puts "An exception occured: #{$!}"
    end
  end

  desc 'watch', 'Add guardfile to regenerate PDF when md changed'
  def watch
    guardfile_contents = File.read(__FILE__).split(/^__END__$/).last

    open('Guardfile', 'w+') do |f|
      f.write(guardfile_contents)
    end

    %x{guard}
  end

  private

  def apply_mdfile!
    Pathname.new(Dir.pwd).ascend do |dir|
      file = dir.join('Mdfile')
      next unless file.exist?

      break Config.module_eval(file.read)
    end
  end

  def ensure_layout_presence(options)
    raise "layout.html not found." unless File.exists?(DEFAULT_LAYOUT)
    raise "html.css not found." if !File.exists?(DEFAULT_HTML_CSS) && options[:format] == "html"
    raise "pdf.css not found." if !File.exists?(DEFAULT_PDF_CSS) && options[:format] == "pdf"
  end

  def generate_html(md_file, output)
    renderer = Redcarpet::Markdown.new(Config.renderer, Config.markdown_options)
    filename = File.basename(md_file, '.*')

    doc = File.read(md_file)
    template = Liquid::Template.parse(File.read(DEFAULT_LAYOUT))

    output_path = File.expand_path(output);

    unless Dir.exists?(output_path)
      FileUtils.mkdir_p output_path;
    end 

    File.open(output_path+"/"+"#{filename}.html", 'w') do |f|
      f.write(template.render('content' => renderer.render(doc), 'title' => filename, 'stylesheet' => DEFAULT_HTML_CSS))
    end
  end

  def generate_pdf(md_file, output)
    generate_html(md_file, output)
    filename = File.basename(md_file, '.*')

    output_path = File.expand_path(output);
    html_path = output_path+"/"+"#{filename}.html"

    unless Dir.exists?(output_path)
      FileUtils.mkdir_p output_path;
    end 

    pdf_path = output_path+"/"+"#{filename}.pdf"
    
    command = pdf_command + " -o #{pdf_path} #{html_path}"

    system(command)

    # Remove temp html file
    FileUtils.rm(File.expand_path(html_path));
  end

  def pdf_command
    command = `which prince`.chomp
    raise "Prince has not been found in your path." if command.empty?
    command << " -s #{DEFAULT_PDF_CSS}"
    command << " --input=html"
  end

  class Theme < Thor
    RESERVED_WORDS = ["default"]
    namespace 'md:theme'
    argument :name

    desc 'default', 'Change default theme.'
    def default
      base_path     = "~/.config/md/themes/#{name}"
      default_path  = "~/.config/md/themes/default"
      if Dir.exists?(File.expand_path(base_path))
        %x{rm -f #{default_path}}
        %x{ln -s #{base_path} #{default_path}}
      else
        puts "Theme not found."
      end
    end

    desc 'create', 'Create a new theme.'
    def create
      raise ArgumentError, "#{name} is a reserved word." if RESERVED_WORDS.include?(name)
      base_path = "~/.config/md/themes/#{name}"

      if Dir.exists?(File.expand_path(base_path))
        puts "Theme #{name} already exists."
      else
        %x{mkdir -p #{base_path}/{css,images}}
        %x{touch #{base_path}/layout.html}
        puts "#{name} theme created."
      end
    end

    desc 'delete', 'Delete an existing theme.'
    def delete
      raise ArgumentError, "#{name} is a reserved word." if RESERVED_WORDS.include?(name)
      base_path = "~/.config/md/themes/#{name}"

      if Dir.exists?(File.expand_path(base_path))
        %x{rm -fr #{base_path}}
        puts "#{name} theme deleted."
      else
        puts "Theme #{name} does'nt exists."
      end
    end
  end
end

__END__
# Automatically generated Guardfile
require 'guard/guard'

module ::Guard
  class MD < ::Guard::Guard
    def run_all
    end

    def run_on_changes(paths)
      puts "Rebuilding #{paths.inspect}"
      %x{thor md:generate "#{paths.first}" -f pdf}
    end
  end
end

guard :md do
  watch(/(.*)\.md/)
end
