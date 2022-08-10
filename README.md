# My Personal Website

## Develop locally

**Install ruby & dependencies:**

```
sudo apt install ruby-full build-essential zlib1g-dev
```

**Export GEM paths (put in .zshrc/bash/fish):**

```
export GEM_HOME="$HOME/gems"
export PATH="$HOME/gems/bin:$PATH"
```

**Install jekyll, bundler & other plugins:**

```
gem install jekyll bundler jemoji
```

**Start Jekyll and website:**
- Website will be available at: [localhost:4000](localhost:4000)

```
cd antontofft.com
bundle exec jekyll serve --livereload
```
