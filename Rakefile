require 'fileutils'

# Dir.chdir "Notebooks"
SOURCE_FILES = Rake::FileList.new("Notebooks/*.ipynb")
# puts SOURCE_FILES

task :default => :all 
task :all => [:html, :pdf, :slides]
task :clean => [:clean_md,:clean_slides,:clean_html,:clean_pdf]
task :html => SOURCE_FILES.ext("html")
task :slides => SOURCE_FILES.ext("slides.html")
task :pdf => SOURCE_FILES.ext("pdf")
task :md => SOURCE_FILES.ext("md")
# task :offline => :slides

task :clean_md do
	sh "rm -rf Notebooks/*.md"
	sh "rm -rf Markdown/*.md"
end
task :clean_html do
	sh "rm -rf Notebooks/*.html"
	sh "rm -rf Html/*.html"
	sh "rm -rf index.html"
end

task :clean_slides do
	sh "rm -rf Notebooks/*.slides.html"
	sh "rm -rf Slides/*.html"
end

task  :clean_pdf do
	sh "rm -rf Notebooks/*.pdf"
	sh "rm -rf Pdfs/*.pdf"
end



rule ".html" => ".ipynb" do |t|
	puts "#{t.source}"
	sh "jupyter-nbconvert --to html #{t.source}"
	if "#{t.name}" == "Notebooks/Index.html"
		FileUtils.mv("#{t.name}", "index.html")
	else
		FileUtils.mv("#{t.name}", "Html/#{File.basename(t.name)}")
	end
end

# special rule for .slides.html file ending!
rule( /\.slides\.html$/ => [
    proc {|task_name| task_name.sub(/\.slides\.html$/, '.ipynb') }
]) do |t|
	sh "jupyter-nbconvert --to slides #{t.source} --reveal-prefix=reveal.js"
	# sh "jupyter-nbconvert --to slides #{t.source} "
	FileUtils.cp("#{t.name}", File.join("Slides",File.basename("#{t.name}",".slides.html")+".html"))
	sh "rm -rf #{t.name}"
end



rule ".pdf" => ".ipynb" do |t|
	# exclude those from bibtex citations
	exclude = ["BasicIntroduction","Index","PlotsJL"]
	bn = File.basename("#{t.source}")
	b  = File.basename("#{t.source}",".ipynb")
	dir = File.dirname("#{t.name}")
	# puts "#{dir}"
	# sh "jupyter-nbconvert --to pdf #{t.source}"
	sh "jupyter nbconvert #{t.source} --to latex --template assets/templates/citations.tplx --output-dir=Pdfs"
	if !(exclude.include?b)
		Dir.chdir "Pdfs"
		sh "xelatex #{bn.ext('tex')}"
		sh "bibtex #{bn.ext('aux')}"
		sh "xelatex #{bn.ext('tex')}"
		sh "xelatex #{bn.ext('tex')}"
		sh "xelatex #{bn.ext('tex')}"
		sh "rm -rf *.tex *.aux *.synctex.gz *.nav *.dvi *.bbl *.blg *.out *.log"
		Dir.chdir ".."
	end
end

rule ".md" => ".ipynb" do |t|
	sh "jupyter-nbconvert --to markdown #{t.source}"
	FileUtils.mv("#{t.name}", "../Markdown/#{t.name}")
end
