weibo_2_example
===============

A example for [weibo_2](https://github.com/simsicon/weibo_2).

## Basic Usage

The [example](https://github.com/simsicon/weibo_2_example) written with sinatra shows how to ask for oauth2 permission, get the token and send status with picture. It should cover basic usage in all ruby apps. You can run your own demo!

```bash
$ KEY=change_this_to_your_key SECRET=change_this_to_your_secret REDIR_URI=change_this_to_your_redir_uri ruby example.rb
```
It should work.


1.  How to get the token?

    OAuth2 is simpler than its first version. In order to get the access token, you first need to get an Authorization Code through a callback request. Now use the following code to get the token.

    ```ruby
    WeiboOAuth2::Config.api_key = YOUR_KEY
    WeiboOAuth2::Config.api_secret = YOUR_SECRET
    WeiboOAuth2::Config.redirect_uri = YOUR_CALLBACK_URL   
    ```

    If you are developing in your localhost, you can set YOUR_CALLBACK_URL as 'http://127.0.0.1/callback' something. Then set your weibo app account's callback URL as this URL too. Weibo will call the URL using GET method, which will then enable you to retrieve the authorization code.
    
    ```ruby
    client = WeiboOAuth2::Client.new  
    ```
    
    Or you can pass the key and secret to new a client if you did not set WeiboOAuth2::Config
    
    Redirect to this URL. If user grants you permission, then you will get the authorization code.
    
    ```ruby
    client.authorize_url
    ```
    
    In your callback handling method, you should add something like the following, 
    
    ```ruby
    client.auth_code.get_token(params[:code])
    ```
    
    which will give permission to your client.
    
2.  How to post a status with picture? (or call other interfaces)
    
    Simply update a status
        
    ```ruby
    client.statuses.update(params[:status])
    ```
    
    Upload a picture.
        
    ```ruby
    tmpfile = params[:file].delete(:tempfile)
    File.open(tmpfile.path, 'rb'){|pic| client.statuses.upload(params[:status], pic, params[:file])}
    ```

    pass params[:file] into upload method as options could help weibo_2 to build post body, useful options as:
    *   filename, filename with extension of the uploading file, example 'pic.jpg'
    *   type, mime type of the uploading file, example 'image/jpeg'