
# sidekiq-worker-killer
[![Gem Version](https://badge.fury.io/rb/sidekiq-worker-killer.svg)](https://badge.fury.io/rb/sidekiq-worker-killer)
[![CircleCI](https://circleci.com/gh/klaxit/sidekiq-worker-killer.svg?style=shield&circle-token=:circle-token)](https://circleci.com/gh/klaxit/sidekiq-worker-killer)

[Sidekiq](https://github.com/mperham/sidekiq) is probably the best background processing framework today. At the same time, memory leaks are very hard to tackle in Ruby and we often find ourselves with growing memory consumption. Instead of spending herculean effort fixing leaks, why not kill your processes when they got to be too large?

Highly inspired by [Gitlab Sidekiq MemoryKiller](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/lib/gitlab/sidekiq_middleware/shutdown.rb) and [Noxa Sidekiq killer](https://github.com/Noxa/sidekiq-killer).

quick-refs: [install](#install) | [usage](#usage) | [available options](#available-options) | [development](#development)

## Install
Use [Bundler](http://bundler.io/)
```ruby
gem "sidekiq-worker-killer"
```

## Usage

Add this to your Sidekiq configuration.

```ruby
require 'sidekiq/worker_killer'

Sidekiq.configure_server do |config|
  config.server_middleware do |chain|
    chain.add Sidekiq::WorkerKiller, max_rss: 480
  end
end
```

## Available options

The following options can be overrided.

| Option | Defaults | Description |
| ------- | ------- | ----------- |
| max_rss | 0 MB (disabled) | max RSS in megabytes. Above this, shutdown will be triggered. |
| grace_time | 900 seconds | when shutdown is triggered, the Sidekiq process will not accept new job and wait at most 15 minutes for running jobs to finish. If Float::INFINITY specified, will wait forever  |
| shutdown_wait | 30 seconds | when the grace time expires, still running jobs get 30 seconds to terminate. After that, kill signal is triggered.  |
| kill_signal | SIGKILL | Signal to use kill Sidekiq process if it doesn't terminate.  |
| gc | true | Try to run garbage collection before Sidekiq process terminate in case of max_rss exceeded  |

## Development

Pull Requests are very welcome!

There are tasks that may help you along the way in a makefile:

```bash
make console # Loads the whole stack in an IRB session.
make test # Run tests.
make lint # Run rubocop linter.
```
Please make sure that you have tested your code carefully before opening a PR, and make sure as well that you have no style issues.

## Authors

See the list of [contributors](https://github.com/klaxit/sidekiq-worker-killer/contributors) who participated in this project.

## License

Please see LICENSE
