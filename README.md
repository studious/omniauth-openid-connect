# OmniAuth::OpenIDConnect

OpenID Connect strategy for OmniAuth
[![Gem Version](https://badge.fury.io/rb/omniauth-openid-connect.png)](http://badge.fury.io/rb/omniauth-openid-connect)
[![Build Status](https://travis-ci.org/jjbohn/omniauth-openid-connect.png?branch=master)](https://travis-ci.org/jjbohn/omniauth-openid-connect)
[![Coverage Status](https://coveralls.io/repos/jjbohn/omniauth-openid-connect/badge.png?branch=master)](https://coveralls.io/r/jjbohn/omniauth-openid-connect?branch=master)
[![Code Climate](https://codeclimate.com/github/jjbohn/omniauth-openid-connect.png)](https://codeclimate.com/github/jjbohn/omniauth-openid-connect)

## Installation

Add this line to your application's Gemfile:

    gem 'omniauth-openid-connect'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install omniauth-openid-connect

## Usage

Example configuration for Hydra (there is no userinfo endpoint)
```ruby
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :openid_connect, {
    name: :hydra,
    scope: [:openid],
    response_type: :code,
    issuer: 'http://hydra.test',
    discovery: true,
    client_options: {
      port: 443,
      scheme: "https",
      host: "myprovider.com",
      identifier: ENV["OP_CLIENT_ID"],
      secret: ENV["OP_SECRET_KEY"],
      redirect_uri: "http://myapp.com/auth/hydra/callback",
      authorization_endpoint: '/oauth2/auth',
      token_endpoint: '/oauth2/token',
      jwks_uri: '/.well-known/jwks.json'
    }
  }
end
```

The auth endpoint becomes

    /auth/:name

To turn off HTTPS or cancel SSL Verification when certs aren't set add the
following and change client_options scheme and port

```ruby
SWD.url_builder = URI::HTTP
OpenIDConnect.http_config do |config|
  config.ssl_config.verify_mode = OpenSSL::SSL::VERIFY_NONE
end
OpenIDConnect.debug!
```
Configuration details:
  * `name` is arbitrary, I recommend using the name of your provider. The name
  configuration exists because you could be using multiple OpenID Connect
  providers in a single app.
  * Although `response_type` is an available option, currently, only `:code`
  is valid. There are plans to bring in implicit flow and hybrid flow at some
  point, but it hasn't come up yet for me. Those flows aren't best practive for
  server side web apps anyway and are designed more for native/mobile apps.
  * If you want to pass `state` paramete by yourself. You can set Proc Object.  
  e.g. `state: Proc.new{ SecureRandom.hex(32) }`
  * `nonce` is optional. If don't want to pass "nonce" parameter to provider, You should specify
  `false` to `send_nonce` option. (default true)
  * Support for other client authentication methods. If don't specified
  `:client_auth_method` option, automatically set `:basic`.
  * Use "OpenID Connect Discovery", You should specify `true` to `discovery` option. (default false)
  * In "OpenID Connect Discovery", generally provider should have Webfinger endpoint.
  If provider does not have Webfinger endpoint, You can specify "Issuer" to option.  
  e.g. `issuer: "https://myprovider.com"`  
  It means to get configuration from "https://myprovider.com/.well-known/openid-configuration".

For the full low down on OpenID Connect, please check out
[the spec](http://openid.net/specs/openid-connect-core-1_0.html).

## Contributing

1. Fork it ( http://github.com/jjbohn/omniauth-openid-connect/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
