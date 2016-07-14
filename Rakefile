require 'rubygems'
require 'bundler/setup'

require 'systemu'
require 'nwn/all'
require 'fileutils'


MODULE    = FileList["module/*.mod"]
TMP_GFFS  = FileList["tmp/*"]
GFFS      = FileList["src/**/*.*"].exclude(/n[cs]s$|\.yml$/)
YMLS      = FileList["src/**/*.yml"].exclude(/n[cs]s$/)

rule '.yml' => ->(f){ source_for_yml(f) } do |t|
  system "nwn-gff", "-i", "#{t.source}", "-o", "#{t.name}"
  # FileUtils.rm "#{t.source}"
end

def source_for_yml(yml_file)
  GFFS.detect{|f| f == yml_file.sub(/\.yml$/, '')}
end

# Circle dependency! (.yml.gff => .yml)
# rule '.gff' => ->(f) { source_for_gff(f) } do |t|
#   system "nwn-gff", "-i", "#{t.name}".sub(/\.gff$/, ''), "-o", "#{t.source}".sub(/\.yml\.gff$/, '')
#   # FileUtils.rm "#{t.source}"
# end

# def source_for_gff(gff_file)
#   YMLS.detect{|f| f == gff_file.sub(/\.gff$/, '')}
# end


directory "tmp"
directory "src"

namespace :main do
  desc 'Clean tmp folder'
  task :clean do
    FileUtils.rm_r Dir.glob('tmp/*')
  end

  desc 'Extract module'
  task :extract => ["tmp", MODULE] do
    Dir.chdir("tmp") do
      system "nwn-erf", "-x", "-f", "../"+MODULE[0]
    end
  end

  desc 'Move to src'
  task :move_sources => ["src", "extract"] do
    TMP_GFFS.each do |file|
      ext = File.extname(file).delete('.')
      srcdir = 'src/'+ext
      FileUtils.mkdir_p(srcdir)
      FileUtils.mv Dir.glob('tmp/*.'+ext), srcdir
    end
  end

  desc 'gff to yaml'
  multitask :yml =>  GFFS.inject(GFFS.class.new) {|res, fn| res << fn + '.yml' } # GFFS.map {|p| p << '.yml' }

  desc 'yaml to gff'
  # multitask :gff =>  YMLS.sub(/\.yml$/, '')
  multitask :gff =>  YMLS.inject(YMLS.class.new) {|res, fn| res << fn + '.gff' } # YMLS.map {|p| p << '.yml' }

  desc 'Pack module'
  task :pack do
  end
end