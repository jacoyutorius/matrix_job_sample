[![Ruby on Rails CI](https://github.com/jacoyutorius/matrix_job_sample/actions/workflows/rubyonrails.yml/badge.svg)](https://github.com/jacoyutorius/matrix_job_sample/actions/workflows/rubyonrails.yml)

# 複数の言語・フレームワークバージョンについて Github Actionsでテストする

[https://github.com/jacoyutorius/matrix_job_sample](https://github.com/jacoyutorius/matrix_job_sample)

```yaml
# This workflow uses actions that are not certified by GitHub.  They are
# provided by a third-party and are governed by separate terms of service,
# privacy policy, and support documentation.
#
# This workflow will install a prebuilt Ruby version, install dependencies, and
# run tests and linters.
name: "Ruby on Rails CI"
on:
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]
jobs:
  lint:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        ruby-version: ['3.0', '3.1']
        gemfile: [ rails6, rails7 ]
    env: # $BUNDLE_GEMFILE must be set at the job level, so it is set for all steps
      BUNDLE_GEMFILE: ${{ github.workspace }}/gemfiles/${{ matrix.ruby-version }}/${{ matrix.gemfile }}.gemfile
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Set up Ruby
        # To automatically get bug fixes and new Ruby versions for ruby/setup-ruby,
        # change this to (see https://github.com/ruby/setup-ruby#versioning):
        # uses: ruby/setup-ruby@v1
        uses: ruby/setup-ruby@0a29871fe2b0200a17a4497bae54fe5df0d973aa # v1.115.3
        with:
          ruby-version: ${{ matrix.ruby-version }}
          bundler-cache: true # runs 'bundle install' and caches installed gems automatically
      - name: Lint Ruby files
        run: bundle exec rubocop --parallel
```

`matrix`  配下に指定されたパラメータの組み合わせでジョブが実施される。

```bash
jobs:
  lint:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        ruby-version: ['3.0', '3.1']
        gemfile: [ rails6, rails7 ]
```

[https://github.com/jacoyutorius/matrix_job_sample/actions/runs/3070984267](https://github.com/jacoyutorius/matrix_job_sample/actions/runs/3070984267)

## memo

- Actionsで使う用のGemfileを /gemfiles 配下に用意する必要がある。
    - ここのGemfileでは `ruby-version` は指定しない。
        - [https://github.com/jacoyutorius/matrix_job_sample/actions/runs/3070974205/jobs/4961239983#step:3:29](https://github.com/jacoyutorius/matrix_job_sample/actions/runs/3070974205/jobs/4961239983#step:3:29)
        
        ```bash
        bundle install
          /opt/hostedtoolcache/Ruby/3.0.4/x64/bin/bundle config --local path /home/runner/work/matrix_job_sample/matrix_job_sample/vendor/bundle
          /opt/hostedtoolcache/Ruby/3.0.4/x64/bin/bundle lock
          Fetching gem metadata from https://rubygems.org/..........
          Could not find gem 'Ruby (~> 3.0.3.0)' in the local ruby installation.
          The source contains the following versions of 'Ruby': 3.0.4.208
          Took   3.33 seconds
        ```
        

## gemfiles

```bash
$ tree gemfiles
gemfiles
├── 3.0
│   ├── rails6.gemfile
│   └── rails7.gemfile
└── 3.1
    ├── rails6.gemfile
    └── rails7.gemfile

2 directories, 4 files
```
