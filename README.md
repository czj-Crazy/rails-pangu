[![CircleCI](https://circleci.com/gh/paiyou-network/rails-pangu.svg?style=svg)](https://circleci.com/gh/paiyou-network/rails-pangu)
[![All Contributors](https://img.shields.io/badge/all_contributors-5-orange.svg?style=flat-square)](#contributors)

# Rails-pangu
[中文文档 Chinese document](/README.CN.md)

`Rails-pangu` is a **Rails 6(API Only)** boilerplate which follows cutting-edge solutions already adopted by the industry, notablly, [Devise](https://github.com/plataformatec/devise), [JWT(JSON Web Tokens)](https://jwt.io/), Postgres, Redis, Docker, Rspec, RuboCop, [CircleCI](https://circleci.com/) and so on. It is a solid production-ready starting point for your new backend projects.

**Mixing all these solutions and letting them work perfectly is not easy. Here is an example**:

[Devise](https://github.com/plataformatec/devise) is a flexible and almost a standard authentication solution for Rails, while [JWT](https://jwt.io/)(JSON Web Tokens) is a JSON-based open standard (RFC 7519) for creating access tokens, which is a distributed and faster solution than authentication through databases.

There is always a strong need to bring these two beautiful solutions together into rails for better user authentication, however, there's no single satisfying article or project which demonstrates how to incorporate both with Rails 6(API Only), which becomes the main reason for the birth of `Rails-pangu`.

Thanks to lots of the close solutions that gave hints to this `Rails-pangu`, for example, this article: [Rails 5 API + JWT setup in minutes (using Devise)](https://medium.com/@mazik.wyry/rails-5-api-jwt-setup-in-minutes-using-devise-71670fd4ed03). However, these solutions have common problems:

- Blurry explanation

- Buggy

- Lots of legacy code that are not usable for current version of frameworks and gems

At the same time, we saw couple of other repos doing the same work, but one big issue for these repos is that they are started with **Rails <= 5.0**, which is far different from **Rails 6**, and contributes to the final decision to "reinvent the wheel again".

<img src="https://res.paiyou.co/pangu.jpg" width="300" align="middle" />

> Pangu opened up the whole new world in Chinese mythology. In the myth, Pangu created the Earth and the Sky with a swing of his giant axe and kept them seperated by standing between them.
> Just like pangu, `Rails-pangu` aims at being a foundational code base which eliminates those tedious research and experimental work for your new Rails projects.

## Features

#### 🚀 Rails 6

As explained above, quite different from `rails 5`,`rails 6` is adapted to the future trend.

#### 🚀 Rails API only

It is common to use frontend js libraries like `react`and`vuejs` to replace `rails view` in modern web development.

#### 🚀 [Devise](https://github.com/plataformatec/devise)

Quoted from its homepage:

>Based on Warden, Devise is a flexible authentication solution for Rails. It:
>
> - Is Rack based;
> - Is a complete MVC solution based on Rails engines;
> - Allows you to have multiple models signed in at the same time;
> - Is based on a modularity concept: use only what you really need.

To our best of knowledge, `devise` provides a full, industry-standard,and easy-to-ingrate way of all kinds of authentication. Damn, it's awesome!

#### 🚀 JWT

JSON Web Tokens is the currently well-received solution that is cross-field certified. 

#### 🚀 [devise-jwt](https://github.com/waiting-for-dev/devise-jwt)

While there are lots of solutions which link `devise` to `jwt`, this repo is better implemented from our point of view.

We implemented a devise-jwt blacklist policy leveraging the power of `redis` in [app/models/jwt_blacklist.rb](https://github.com/paiyou-network/rails-devise-jwt/blob/master/app/models/jwt_blacklist.rb).

#### 🚀 Postgres
We use postgres as default database. Sqlite3 will die out sooner or later when there exists  traffic congestion in one web server.

#### 🚀 Rspec
Ruby's behavior-driven testing framework makes TDD productive and interesting.

#### 🚀 RuboCop
A Ruby static code analyzer and formatter, based on the community Ruby style guide. https://docs.rubocop.org

#### 🚀 [CircleCI](https://circleci.com/)
CircleCI is the leading continuous integration and delivery platform for teams looking to shorten the distance between idea and delivery.
In this project, we leverage CircleCI to test `Rails-pangu`'s code base with both `rspec` and `RuboCop`

#### 🚀 [Factory Bot](https://github.com/thoughtbot/factory_bothttps://github.com/thoughtbot/factory_bot)
A library for setting up Ruby objects as test data

#### 🚀 Docker

Docker is a standard containerize solution which is almost employed in every team worldwide. As a result, a `Dockerfile` and related scripts are provided to help generate a docker image for this project.

The provided docker building solution contains the following optimizations: 

* Docker building acceleration

  When a project grows, hundreds or even thousands of versions of different gems will be tried or used. Even a small change in `Gemfile` can cause re-bundling for every `Gem` while building a `Ruby` based docker image, in which case, most of the docker image building time is wasted for bundling a large number of stable gems, such as `rails`, `pg`... To solve this issue, we utilized a trick which accelerates the docker building process by bundling two rounds for `Gemfile` files, and generates two layers of docker image: 

  * Build the first layer for `Gemfile.core`, which is for stability or core `Gem`s, such as `rails`, `pg`.
  * Build the second layer for `Gemfile`,  which is for variability or non-core `Gem`s. For example, `Gem`is written or forked by yourself. Inside `Rails-pangu`, we demonstrate an example by putting `mailgun` gem inside `Gemfile`. 

  Though, this process generates extra one layer of docker image which makes the image lager a little bit(hundreds of `KB`s). It makes sense due to the fact that in this way **time is much more limited than disk space**

  The following lines of `Dockerfile` demonstrates this docker building process: 

```dockerfile
COPY Gemfile.core .
RUN echo 'gem: --no-document' >> ~/.gemrc && \
    cp ~/.gemrc /etc/gemrc && \
    chmod +r /etc/gemrc && \
    bundle install --gemfile Gemfile.core -j16 --binstubs=$BUNDLE_PATH/bin

COPY Gemfile .
COPY Gemfile.lock .
RUN bundle install --gemfile Gemfile -j16 --binstubs=$BUNDLE_PATH/bin
```

* Bundling `Gem`s acceleration (For developers in China only)

  By default, we mirror gem source `https://rubygems.org` to `https://gems.ruby-china.com`, which boosts the bundling speed largely for developers in China. 

  

#### 🚀 Docker Compose

A `docker-compose.yml` is attached to helping quickly stick postgres and web server together.

#### 🚀 [Puma](https://github.com/puma/puma)

Puma is a simple, fast, threaded, and highly concurrent HTTP 1.1 server for Ruby/Rack applications in development and production.

#### 🚀 Redis

Is there any web project not using `redis` as a faster and sometimes easier way of storing data? Well, if there is,  just replace it!

#### 🚀 Mailgun

Mailgun is an online service that provides a set of APIs for sending, receiving, tracking and storing email.



## Build, Run, Test

#### Build

Run `bin/build.sh` to build the docker image `rails-devise-jwt`.



#### Run

After building the image, please run: `docker-compose up -d`

Then, initialize the database with `docker-compose exec server rails db:create`



#### Test

```bash
docker-compose exec server rspec
```



The following environment varialbes are required in order to run or test (check `docker-cmpose.yml`):

- `DEVISE_JWT_SECRET_KEY`: You generate this by running `rails secret`
- `DATABASE_URL`: Postgres database url for database connection
- `REDIS_URL`: Redis database url for database connection



## CORS

We allow CORS and expose the Authorization header by default. If you want to disable it, you can comment out the contents of the `config/cors.rb` file.



## Add script to cron job

You can add cron job in `bin/gen_cronjobs.rb`. An example is as follows.

```ruby
puts [
  "59 * * * * ruby script",
  "*/10 * * * * ruby script"
].map { |job|
  *schedule, cmd = job.split(' ')
  "#{schedule.join(' ')} cd /usr/src/app; rails runner \"Util.run_once('#{cmd}')\""
}.join("\n")
```

If you want to run bash script, you can replace `cd /usr/src/app; rails runner \"Util.run_once('#{cmd}')\"` as your custom cmd.



## Create a role

In addition to the default role we provide, we also allow developers to create their custom role. There is a document about how to [create a new role](https://github.com/paiyou-network/rails-pangu/wiki/Create-a-role)

## Blacklist

#### Default blacklist with redis

The capability of redis to get access to memory bank is so strong that it is a pleasant choice to fulfill `blacklist`. In `jwt_blacklist` record, we implement blacklist with redis. By setting `redis` expiration time equal to `jwt token`'s, this token can be automatically deleted from redis when the token expires.

```ruby
  def self.jwt_revoked?(payload, user)
    # Check if in the blacklist
    $redis.get("user_blacklist:#{user.id}:#{payload['jti']}").present?
  end

  def self.revoke_jwt(payload, user)
    # REVOKE JWT
    expiration = payload['exp'] - payload['iat']
    $redis.setex("user_blacklist:#{user.id}:#{payload['jti']}", expiration, payload['jti'])
  end
```

#### Custom blacklist

You can also implement blacklist by your own strategies. You just need to rewrite two methods: `jwt_revoked?` and `revoke_jwt` in `jwt_blacklist.rb`, both of them accepting as parameters the JWT payload and the `user` record, in this order.

```ruby
  def self.jwt_revoked?(payload, user)
    # Does something to check whether the JWT token is revoked for given user
  end

  def self.revoke_jwt(payload, user)
    # Does something to revoke the JWT token for given user
  end
```

#### Dispatch requests

You can config `dispatch_requests` in `devise.rb`. When config it, you need to tell which requests will dispatch tokens for the user that has been previously authenticated (usually through some other warden strategy, such as one requiring username and email parameters). To configure it, the request path should be added to `dispatch_requests`.

```ruby
  jwt.dispatch_requests = [['POST', %r{^users/sign_in$}]]

```

#### Revocation requests

You can config `dispatch_requests` in `devise.rb`. When configing it, you need to add the request path to revocation_requests, and then tell which requests will revoke incoming JWT tokens.

```ruby
  jwt.revocation_requests = [['DELETE', %r{^users/sign_out$}]]
```

#### Jwt payload

`user` records may also implement a jwt_payload method, which gives it a chance to add something to the JWT payload.

```ruby
  def jwt_payloads
    # { 'foo' => 'bar' }
  end
```

#### Jwt dispatch

You can add a hook method `on_jwt_dispatch` on the `user` record. It will execute when a token dispatching for that user instance, and it takes token and payload as parameters. This method will call when you access the routes which are in dispatch_requests.

```ruby
  def on_jwt_dispatch(token, payload)
    # do_something(token, payload)
  end
```

## MailGun

#### Configure mailgun environment

Before using mailgun, you should register a mailgun account on [Mailgun Official website](https://www.mailgun.com/). Having it done, you only need to add these environment config in docker-compose.yml.

```yml
MAILGUN_EMAIL_FROM: [send_template_from_name]
MAILGUN_DOMAIN: [your_domain_on_mailgun]
MAILGUN_PRIVATE_API_KEY: [your-api-key]
```

#### Send_email

You only need to call `UtilMailgun.send_email(from_name, to_name, subject, text)` to send mail.

#### Send_template

If you want to send a email template, you should first set up your custom email template on mailgun. Then you can call `UtilMailgun.send_template(template_name, recipient_or_receipients, subject, variables = {})`   to send the mail template. `recipient_or_receipients` parameter allows an email or an array of emails.


## Projects using rails-pangu

* **[LINGTI](https://lingti.io)**  (https://lingti.io/): Game booster which helps you get better, faster, smoother performance from your PC. It is now popular among game players playing [MTGA](https://magic.wizards.com/en/mtgarena), [MTGO](https://magic.wizards.com/en/mtgo), [LOL](https://lol.qq.com/main.shtml), [World of Warcraft](https://worldofwarcraft.com/), [PUBG](https://www.pubg.com/), [Dota2](https://www.dota2.com.cn/index.htm), [CSGO](https://www.csgo.com.cn/index.html), etc.
* **[eSheep](https://esheep.io)**  (https://esheep.io/): Network booster which helps global users access better entertainment content from China.

[<img src="https://assets.lingti.paiyou.co/ed568fbe.png" width="150" align="middle" />](https://lingti.io) [<img src="https://res.paiyou.co/44920709.png" width="200" align="middle" />](https://lingti.io)

## License

Code and documentation copyright 2019 the [Rails-pangu Authors](https://github.com/paiyou-network/rails-pangu/graphs/contributors) and [Paiyou Network](https://paiyou.co/) Code released under the [MIT License](https://github.com/paiyou-network/rails-pangu/blob/master/LICENSE). 

## Contributors ✨

Thanks goes to these wonderful people ([emoji key](https://allcontributors.org/docs/en/emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore -->
<table>
  <tr>
    <td align="center"><a href="https://paiyou.co/"><img src="https://avatars2.githubusercontent.com/u/3121413?v=4" width="100px;" alt="hophacker"/><br /><sub><b>hophacker</b></sub></a><br /><a href="https://github.com/paiyou-network/rails-pangu/commits?author=hophacker" title="Code">💻</a> <a href="https://github.com/paiyou-network/rails-pangu/commits?author=hophacker" title="Documentation">📖</a> <a href="#infra-hophacker" title="Infrastructure (Hosting, Build-Tools, etc)">🚇</a></td>
    <td align="center"><a href="https://github.com/zhcalvin"><img src="https://avatars3.githubusercontent.com/u/5792099?v=4" width="100px;" alt="Jiawei Li"/><br /><sub><b>Jiawei Li</b></sub></a><br /><a href="https://github.com/paiyou-network/rails-pangu/commits?author=zhcalvin" title="Code">💻</a> <a href="https://github.com/paiyou-network/rails-pangu/commits?author=zhcalvin" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/caibiwsq"><img src="https://avatars0.githubusercontent.com/u/37767017?v=4" width="100px;" alt="caibiwsq"/><br /><sub><b>caibiwsq</b></sub></a><br /><a href="https://github.com/paiyou-network/rails-pangu/commits?author=caibiwsq" title="Code">💻</a> <a href="https://github.com/paiyou-network/rails-pangu/commits?author=caibiwsq" title="Documentation">📖</a></td>
    <td align="center"><a href="http://blog.cloud-mes.com/"><img src="https://avatars3.githubusercontent.com/u/1131536?v=4" width="100px;" alt="Eric Guo"/><br /><sub><b>Eric Guo</b></sub></a><br /><a href="https://github.com/paiyou-network/rails-pangu/commits?author=Eric-Guo" title="Code">💻</a> <a href="https://github.com/paiyou-network/rails-pangu/commits?author=Eric-Guo" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/alen9968"><img src="https://avatars2.githubusercontent.com/u/38801833?v=4" width="100px;" alt="张学财"/><br /><sub><b>张学财</b></sub></a><br /><a href="https://github.com/paiyou-network/rails-pangu/commits?author=alen9968" title="Code">💻</a> <a href="https://github.com/paiyou-network/rails-pangu/commits?author=alen9968" title="Documentation">📖</a></td>
  </tr>
</table>

<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!
