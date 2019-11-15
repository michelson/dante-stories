# Step by Step - Install & Configuration

## System dependencies

* PostgreSQL
* Elasticsearch
* Redis
* Ruby version 2.6.5
* Rails

# Lets Get Started!

**1. Install Homebrew**

Homebrew allows us to install and compile software packages easily from source.

Run the following command in the Terminal. When it asks you to install Xcode CommandLine Tools, say yes

```ruby -e “$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)”```

**2. Instal Ruby**

```
brew install rbenv ruby-build
```

Add rbenv to bash so that it loads every time you open a Terminal

```
echo ‘if which rbenv > /dev/null; then eval “$(rbenv init -)”; fi’ >> ~/.bash_profile
source ~/.bash_profile
```

Install Ruby 2.6.5 which is required by the app:

```
rbenv install 2.6.5
rbenv global 2.6.5
ruby -v
```

**3. Install PostgreSQL**

```
brew install postgresql
```

Make sure to follow those instructions of the build notes.

If your installing PostgreSQL for the first time create a database:

```
initdb /usr/local/var/postgres -E utf8
```

Lunchy is a helpful gem that will allow you to easily start and stop Postgres.

```
gem install lunchy
```

Change the version number of PostgreSQL to the version that you installed:

```
mkdir -p ~/Library/LaunchAgents
cp /usr/local/Cellar/postgresql/0.0.0/homebrew.mxcl.postgresql.plist ~/Library/LaunchAgents/
```

To Stop PostgreSQL run:
```
lunchy stop postgres
```

We want it to start PostgreSQL, so instead run:
```
lunchy start postgres
```


**4. Install Elasticsearch**

Where would we be without search? The app uses ElasticSearch 1.7.3 which is a search server - quite simply it’s going to help you find posts and users in the app.

```
brew install elasticsearch17
```

Start Elasticsearch:
```
elasticsearch --config=/usr/local/opt/elasticsearch17/config/elasticsearch.yml
```

Lets check it is running by visiting [http://localhost:9200](http://localhost:9200) in your browser. You should get something like this:

```
{
  “status” : 200,
  “name” : “Atalanta”,
  “cluster_name” : “elasticsearch_kenhibino”,
  “version” : {
    “number” : “1.7.3”,
    “build_hash” : “05d4530971ef0ea46d0f4fa6ee64dbc8df659682”,
    “build_timestamp” : “2015-10-15T09:14:17Z”,
    “build_snapshot” : false,
    “lucene_version” : “4.10.4”
  },
  “tagline” : “You Know, for Search”
}
```

**5. Install Redis**

Using Homebrew install Redis and then start the server:

```
brew install redis
redis-server
```

**6. Install ImageMagick or GraphicsMagick**

```
brew install imagemagick
```
or
```
brew install graphicsmagick
```

**7. Clone the app**

Browse to where you want the app to live and clone app:

```
git clone https://github.com/kenny-hibino/stories.git
```

Change directory into the stories folder
```
cd stories
```

**8. Install the app**

First install all the required gems:
```
bundle install
```

Next, Set up environment variables:

You have to set `SECRET_KEY_BASE` and `STORIES_DATABASE_*` at least. (Using `rake secret` create a value to set)

```
STORIES_DATABASE_HOST=db_host
STORIES_DATABASE_PORT=db_port
STORIES_DATABASE_NAME=db_name
STORIES_DATABASE_USER=db_user
STORIES_DATABASE_PASSWORD=db_password
```

Also have to set the following values

```
MAILER_ADDRESS=localhost
MAILER_PORT=[25|465|587]
MAILER_AUTH=[:plain|:cram-md5|...]
MAILER_USER=user
MAILER_PASS=password
MAILER_DOMAIN=example.com
MAILER_ENABLE_TLS=[true|false]
```

As appropriate, set variables for Oauth2 login

The callback addresses to be needed are as below:

```
Facebook: https://example.com/users/auth/facebook/callback
Twitter:  https://example.com/users/auth/twitter/callback
Google:   https://example.com/users/auth/google-oatuh2/callback
```

Then Execute Sidekiq, ElasticSearch and Mailer
```
bundle exec sidekiq -q elastic search -q mailer -c 3
```

Set up and migrate the database:
```
rake db:setup
rake db:migrate
```

Create Index with elasticsearch
```
rake elasticsearch:reindex
```

Lets run the App:
```
rails server
```

Then browse to [http://localhost:3000](http://localhost:3000) to view the app in all its glory. Wait where is that “Hello World!” moment? Time to create the admin account so we can create some posts…

**9. Update a user as an Admin account**

Fist, sign up your account and then,

Enter the rails console:

```
rails console
```

Update a user account replacing your email:

```
User.update(email: ‘your@email.com’, role: ‘admin’)
```

Close the console:

```
exit
```

You can now...
- Write new posts
- Feature tags by clicking on the “feature” button when on a tag pages





## Still to come - Things you may want to cover:

* Elasticsearch configuration

* OAuth configuration

* How to run the test suite

* Services (job queues, cache servers, search engines, etc.)

* Deployment instructions


Please feel free to use a different markup language if you do not plan to run
<tt>rake doc:app</tt>.
