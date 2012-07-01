<script src="/syntaxhighlighter/scripts/shCore.js"         type="text/javascript"></script>
<script src="/syntaxhighlighter/scripts/shBrushBash.js"    type="text/javascript"></script>
<script src="/syntaxhighlighter/scripts/shBrushPlain.js"    type="text/javascript"></script>
<script src="/syntaxhighlighter/scripts/shBrushRuby.js"    type="text/javascript"></script>
<link  href="/syntaxhighlighter/styles/shCore.css"         rel="stylesheet"></link>
<link  href="/syntaxhighlighter/styles/shThemeMidnight.css" rel="stylesheet" type="text/css" />
<script type="text/javascript">
  SyntaxHighlighter.defaults['toolbar'] = true;
  SyntaxHighlighter.config.space=' ';
  SyntaxHighlighter.all()
</script>


What is databasedotcom-oauth2?
------------------------------
* an extension of the [databasedotcom](https://rubygems.org/gems/databasedotcom) gem that simplifies authentication and authorization with salesforce.com for Ruby web apps via OAuth 2.0
* a Ruby gem intended to run as Rack Middleware
* an alternative to using [OmniAuth](http://www.omniauth.org/) and the corresponding [omniauth-salesforce](https://rubygems.org/gems/omniauth-salesforce) gem.

When and why should I use it instead of OmniAuth?
---------------------------------------------------------------
Many Ruby web apps integrated with salesforce.com need more than just identification, they also need to __interact__ with salesforce.com via the databasedotcom gem.  Both OmniAuth and databasedotcom-oauth2 provide identification; however, databasedotcom-oauth2 makes the interaction part easier. 

Specifically, databasedotcom-oauth2:

* allows multiple saleforce.com endpoints (production, sandbox, etc.)
* supports configuration of scope, display, and immediate OAuth 2.0 parameters
* supports My Domain
* maintains an encrypted OAuth 2.0 token in whatever session store you choose (Cookie, Pool, etc)
* materializes a databasedotcom client upon each request (using the token in session)
* provides a mixin for your app containing utility methods like unauthenticated?, client, etc.

Demos
-------

**<a href="https://db-oauth2-sinatra-basic.herokuapp.com" target="_blank">Simple example using Sinatra</a>**&nbsp;&nbsp;<a href="https://github.com/richardvanhook/databasedotcom-oauth2-sinatra-basic" target="_blank">view source on github</a>

**<a href="https://db-oauth2-sinatra-jqm.herokuapp.com" target="_blank">In-depth configuration with JQuery Mobile</a>**&nbsp;&nbsp;<a href="https://github.com/richardvanhook/databasedotcom-oauth2-sinatra-jqm" target="_blank">view source on github</a>

Usage
-------

### Required 

`:token_encryption_key` & `:endpoints` are required.  databasedotcom-oauth2 encrypts oauth2 token using `:token_encryption_key` and stores it in rack.session for further use.  `:endpoints` defines the server endpoints to be available; multiple can be specified but at least one is required.  

<pre class="brush: ruby">
  use Databasedotcom::OAuth2::WebServerFlow, 
    :token_encryption_key => TOKEN_ENCRYPTION_KEY,
    :endpoints            => {"login.salesforce.com" => {:key => CLIENT_ID, :secret => CLIENT_SECRET}}
</pre>  

```ruby
```

### Multiple Endpoints 

```ruby
use Databasedotcom::OAuth2::WebServerFlow, 
  :endpoints            => {"login.salesforce.com" => {:key => CLIENT_ID1, :secret => CLIENT_SECRET1},
                            "test.salesforce.com"  => {:key => CLIENT_ID2, :secret => CLIENT_SECRET2}}
```
### Authentication Options
```ruby
use Databasedotcom::OAuth2::WebServerFlow, 
  :scope                => "full",  #default is "id api refresh_token"
  :display              => "touch", #default is "page"
  :immediate            => true     #default is false
  :scope_override       => true,    #default is false
  :display_override     => true,    #default is false
  :immediate_override   => true,    #default is false
```

Required Configuration Parameters
-----------------------------------

* **`:endpoints`**

    Hash of endpoint names to client credentials.  Only one entry is needed for salesforce.com environment like production, sandbox, or pre-release.  Separate entries are not necessary for My Domain.

    Example:
    ```ruby
    <pre class="brush: ruby">
    :endpoints => {"login.salesforce.com" => {:key => "replace me", :secret => "replace me"}
                   "test.salesforce.com"  => {:key => "replace me", :secret => "replace me"}}
     ```

### `:token_encryption_key`

It's uber important that `:token_encryption_key` is sufficiently strong.  To generate a sufficiently strong key, run following:

    $ ruby -ropenssl -rbase64 -e "puts Base64.strict_encode64(OpenSSL::Random.random_bytes(16).to_str)"

Then, in your code, decrypt prior using:

```ruby
Base64.strict_decode64(TOKEN_ENCRYPTION_KEY)
```
Optional Configuration Parameters
-----------------------------------

### `:api_version`
### `:debugging`
### `:display`
### `:display_override`
### `:immediate`
### `:immediate_override`
### `:on_failure`
### `:path_prefix`
### `:scope`
### `:scope_override`

## Resources

* [Article: Digging Deeper into OAuth 2.0 on Force.com](http://wiki.developerforce.com/index.php/Digging_Deeper_into_OAuth_2.0_on_Force.com)