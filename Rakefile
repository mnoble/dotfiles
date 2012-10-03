require "rake"

class Link
  include Rake::DSL

  def initialize(target, source)
    @target = target
    @source = source
  end

  def create
    log { File.symlink(@source, @target) }
  rescue Exception
    print "#{@target} already exists. Overwrite? [yn]: "
    STDIN.gets.chomp == "y" and rm_rf(@target) and retry
  end

  def log
    print "#{@target}: "
    yield
    puts "done"
  end

  class << self
    include Rake::DSL

    def symlinks
      destinations.zip(sources)
    end

    def sources
      FileList["**/*.symlink"].pathmap(File.expand_path("%p"))
    end

    def destinations
      sources.pathmap(File.expand_path("~/.%n"))
    end
  end
end

Link.symlinks do |target, source|
  file target => source do
    Link.new(target, source).create
  end
end

task :default => Link.destinations
