web: bundle exec passenger start -p $PORT --max-pool-size 3
worker: bundle exec sidekiq -q elasticsearch -q mailer -c 3
