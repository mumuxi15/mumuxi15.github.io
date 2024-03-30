How to run

```
bundle exec jekyll serve
```

search 

```
grep -rni "Aletbar" *
```

```
RubyGems Environment:
  - RUBYGEMS VERSION: 3.5.4
  - RUBY VERSION: 3.3.0 (2023-12-25 patchlevel 0) [x86_64-darwin21]
  - INSTALLATION DIRECTORY: /usr/local/lib/ruby/gems/3.3.0
  - USER INSTALLATION DIRECTORY: /Users/mumuxi/.gem/ruby/3.3.0
  - RUBY EXECUTABLE: /usr/local/opt/ruby/bin/ruby
  - GIT EXECUTABLE: /usr/bin/git
  - EXECUTABLE DIRECTORY: /usr/local/lib/ruby/gems/3.3.0/bin
  - SPEC CACHE DIRECTORY: /Users/mumuxi/.gem/specs
  - SYSTEM CONFIGURATION DIRECTORY: /usr/local/Cellar/ruby/3.3.0/etc
  - RUBYGEMS PLATFORMS:
     - ruby
     - x86_64-darwin-21
  - GEM PATHS:
     - /usr/local/lib/ruby/gems/3.3.0
     - /Users/mumuxi/.gem/ruby/3.3.0
     - /usr/local/Cellar/ruby/3.3.0/lib/ruby/gems/3.3.0
  - GEM CONFIGURATION:

```

```
echo 'export PATH="/usr/local/opt/ruby@3.2/bin:$PATH"' >> /Users/mumuxi/.bash_profile
gem which jekyll
gem install jekyll

bundle install
bundle exec jekyll serve

```

