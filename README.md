How to run

```
git clone git@ #the project
rbenv global 3.4.3
ruby -v # check ruby version
bundle exec jekyll serve
```

search 

```
grep -rni "Aletbar" *
```

Install ruby (version 3.3 is not compatible with jekyll)

```
brew install ruby@3.2
echo 'export PATH="/usr/local/opt/ruby@3.2/bin:$PATH"' >> /Users/mumuxi/.bash_profile
gem install jekyll
bundle update
bundle install
bundle exec jekyll serve

```

