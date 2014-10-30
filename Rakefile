desc "Delete _site/"
task :delete do
  puts "\## Deleting _site/"
  status = system("rm -r _site")
  puts status ? "Success" : "Failed"
end

desc "Preview _site/"
task :preview do
  puts "\n## Opening _site/ in browser"
  status = system("open http://0.0.0.0:4000/")
  puts status ? "Success" : "Failed"
end

desc "Build _site/"
task :build do
  sh "jekyll build"
end

desc "Serve _site/"
task :serve => [:local] do
  sh "jekyll serve --watch"
end

desc "Change URL to localhost/"
task :local do
  puts "\n## Change URL to localhost"
	sh "sed -i .bak 's,https://clburlison.com,http://localhost:4000,g' ./_config.yml; rm ./_config.yml.bak"
end

desc "Change URL to remote/"
task :remote do
  puts "\n## Change URL to localhost"
	sh "sed -i .bak 's,http://localhost:4000,https://clburlison.com,g' ./_config.yml; rm ./_config.yml.bak"
end

desc "Commit _site/"
task :commit => [:delete] do
  puts "\n## Staging modified files"
  status = system("git add -A")
  puts status ? "Success" : "Failed"
  puts "\n## Committing a site build at #{Time.now.utc}"
  message = "Build site at #{Time.now.utc}"
  status = system("git commit -m \"#{message}\"")
  puts status ? "Success" : "Failed"
  puts "\n## Pushing commits to remote"
  status = system("git push origin source")
  puts status ? "Success" : "Failed"
end

desc "Deploy _site/ to master branch"
task :deploy => [:build]  do
  puts "\n## Deleting master branch"
  status = system("git branch -D master")
  puts status ? "Success" : "Failed"
  puts "\n## Creating new master branch and switching to it"
  status = system("git checkout -b master")
  puts status ? "Success" : "Failed"
  puts "\n## Forcing the _site subdirectory to be project root"
  status = system("git filter-branch --subdirectory-filter _site/ -f")
  puts status ? "Success" : "Failed"
  puts "\n## Switching back to source branch"
  status = system("git checkout source")
  puts "\n## Pushing all branches to origin"
  status = system("git push --all origin")
  puts status ? "Success" : "Failed"
end

desc "Commit and deploy _site/"
task :push => [:remote, :build, :commit, :deploy] do
end