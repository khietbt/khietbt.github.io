---
layout: post
title: "Run Jekyll at local"
date: 2023-12-05 11:43:01 +0700
categories: jekyll local
---

# Install rbenv on Ubuntu

Install my beloved version manager

```shell
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'eval "$(~/.rbenv/bin/rbenv init - bash)"' >> ~/.bashrc
```

# Install ruby 3.0.6

Open a new terminal and run below lines:

```shell
git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build
sudo apt install  zlib1g-dev # <-- this is required for building openssl
rbenv install 3.0.6
rbenv global 3.0.6
```

# Update everything

```bash
gem update --system
gem install bundle
```

# Run Jekyll locally

```shell
cd ${JEKYLL_DIR}
bundle install
bundle exec
bundle exec jekyll serve
```
Finally, please visit http://localhost:4000 to see the page.