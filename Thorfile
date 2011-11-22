#!/usr/bin/env ruby

require 'rubygems'
require 'thor'
require 'maruku'
require 'liquid'

class MD < Thor
  namespace 'md'
  default_task :setup
  map "-p" => "pdf"

  desc 'setup', 'Create config files.'
  def setup
    puts "setup"
  end

  desc 'generate', 'Generate html from md.'
  def html
    puts "html"
  end

  desc 'generate', 'Generate pdf from md.'
  def pdf
    puts "pdf"
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
