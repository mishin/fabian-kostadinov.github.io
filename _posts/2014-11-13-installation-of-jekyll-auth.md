---
layout: post
title: Installation of Jekyll-Auth
tags: [jekyll-auth]
---
These are (hopefully) complete installation instructions for Jekyll-Auth. The next figure shows the conceptual workflow of Jekyll-Auth in combination with a repository on GitHub.com. This is how Jekyll-Auth works.<!--more-->

![Jekyll-Auth Workflow](/public/img/2014-11-13-jekyll-auth.jpg "Jekyll-Auth Workflow")

* On GitHub.com, there exists an organization _foo-organization_ containing a team _foo-team_ and a repository _foo-repository_. _foo-repository_ is private, therefore only members of _foo-team_ can view or modify its contents.
* Although the repository contains a complete Jekyll-enabled website, GitHub pages is not used to host any of this content. Instead, all users only work on the <code>master</code> branch (or any other branch except a GitHub pages <code>gh-pages</code> branch). 
* The website's content is not accessible publicly. Instead, on Heroku an app is running - it's a Jekyll server enhanced with Jekyll-Auth functionality.
* There are currently two users pushing and pulling to _foo-repository_ on GitHub.com: Anna and Bob. Both users are members of _foo-organization_ and of _foo-team_.
* Bob is an ordinary GitHub user. He works on a local clone of _foo-repository_. Whenever he has changes, he simply pushes them to the remote repository.
* Anna is also a GitHub user, hence she has her own local clone of _foo-repository_. However, unlike Bob, she is also responsible for hosting the website's content so that it is really accessible _as_ a fully functional website (and not only as files) to all members of _foo-team_. In her local version of the repository, besides having a local copy of all files in _foo-repository_, she additionally has an (installed and configured) version of Jekyll-Auth. Anna has two responsibilities. Like Bob she can work on the website's content. But at certain predefined points in time, she can also update the hosted website running on Jekyll as a Heroku app.
* The hosted website is accessible under the URL <code>https://my-foo-heroku.herokuapp.com</code>. Whenever a user wants to access this site, Heroku redirects him to a GitHub authorization page, where he must provide his GitHub username and password. Heroku also sends Client ID, Client Secret as well as organization ID and/or team ID (e.g. "@foo-organization/foo-team") to GitHub. GitHub now tries to authenticate the user, and if he is a member of _foo-organization_ and _foo-team_ and is allowed to access _foo-repository_, then he is granted access to the website's content. In the example, Charles is no organization/team member and is denied access to the website.
* Access rights to files and folders are specified inside the _foo-repository_'s _&#95;config.yml_ file.

#Installation instructions

_Step 1:_ Make sure you have a [Heroku account](http://www.heroku.com). A free one will be sufficient for most needs.

----

_Step 2:_ Make sure you have [Heroku Toolbelt](https://toolbelt.heroku.com/) installed. You will probably need to use your Heroku login information. When I first tried to <code>bundle install</code> Jekyll-Auth (see below), it worked, but I received this warning message:
{% highlight console linenos %}
Your bundle is complete!  
Use `bundle show [gemname]` to see where a bundled gem is installed.  
Post-install message from heroku:  
 !    The `heroku` gem has been deprecated and replaced with the Heroku Toolbelt.  
 !    Download and install from: https://toolbelt.heroku.com  
 !    For API access, see: https://github.com/heroku/heroku.rb
{% endhighlight %}
If this shows up, you need to first uninstall Heroku gem: <code>gem uninstall heroku</code>. Heroku gem is deprecated and it will interfere with your Heroku Toolbelt installation, so make sure you actually uninstalled it.

This is a step-by-step installation instruction.

----

_Step 3:_ If you've installed Heroku Toolbelt, you will probably have to recreate SSH keys, otherwise your local Heroku Toolbelt will not be able to push files to the remote server. Create a key <code>ssh-keygen -t rsa</code>, then add the key to Heroku <code>heroku keys:add</code>. Make sure that you __do not mistakenly publish your private RSA key file__ together with the rest of the website!

----

_Step 4:_ Use Heroku Toolbelt to [create a new Heroku app](https://devcenter.heroku.com/articles/creating-apps): <code>heroku create my-new-cool-heroku-app</code>. Your website will be available at <code>https://my-new-cool-heroku-app.herokuapp.com</code>, and it will have a git account to push to called <code>git@heroku.com:my-new-cool-heroku-app.git</code>.

----

_Step 5:_ The Heroku app will access the GitHub account to perform an authorization check for every user. If the user is registered with the corresponding GitHub account, she will also be allowed to access the Heroku app. Hence, the Heroku app must be registered with GitHub. Upon registration, you will receive a OAuth2 Client ID and Client Secret which will be needed at a later step.

Login to your GitHub account and go to <code>https://github.com/settings/applications/new</code>. Enter the following information:

* _Application name:_ Any meaningful name for this application.
* _Homepage URL:_ The link to your heroku app received in step 3, e.g. <code>https://my-new-cool-heroku-app.herokuapp.com</code>.
* _Application description:_ A textual description.
* _Authorization callback URL:_ Same as homepage url + <code>/auth/github/callback</code> appended, e.g. <code>https://my-new-cool-heroku-app.herokuapp.com/auth/github/callback</code>

Attention: The correct Heroku URL necessarily starts with <code>https://...</code> and not with <code>http://...</code>.

You will be given a Client ID and a Client Secret, that is a shorter and a longer string of numbers and letters. We will need them later on, so you better write them down. In case you want to know what they are useful for, here's a short excerpt from [OAuth's API description](https://developer.github.com/v3/oauth/):
<blockquote>OAuth2 is a protocol that lets external apps request authorization to private details in a user's GitHub account without getting their password. This is preferred over Basic Authentication because tokens can be limited to specific types of data, and can be revoked by users at any time.

All developers need to register their application before getting started. A registered OAuth application is assigned a unique Client ID and Client Secret. The Client Secret should not be shared.</blockquote>

----

_Step 6:_ Make sure you have Ruby installed. Jekyll-Auth depends on Ruby (and other stuff).

----

_Step 7:_ Make sure you have [Ruby's bundler](http://bundler.io/) installed.


----

_Step 8:_ Create a local clone of the [Jekyll-Auth repository available on GitHub](https://github.com/benbalter/jekyll-auth).

----

_Step 9:_ Navigate to your local clone. There should be a <code>Gemfile</code> in your repository's directory. Change this file so that it looks like this:
{% highlight Ruby %}
source "https://rubygems.org"

gem 'jekyll-auth'
{% endhighlight %}

----

_Step 10:_ Then run <code>bundle install</code>. You might see a warning that <code>DL is deprecated, please use Fiddle</code>, which you can safely ignore.

----

_Step 11a:_ Still inside your local clone's directory, you can now run <code>jekyll-auth new</code> to create a new Heroku app. Follow all these steps.

----

_Step 11b:_
{% highlight console %}
...
Would you like to set up Heroku now? (Y/n)
{% endhighlight %}
Type <code>Y</code> and hit <code>Enter</code>.

----

_Step 11c:_
{% highlight console %}
If you already created an app, enter it's name
otherwise, hit enter, and we'll get you set up with one.
Heroku App name?
{% endhighlight %}
We have already created a Heroku app in step 3 with the name _my-new-cool-heroku-app_. Type <code>my-new-cool-heroku-app</code> and hit <code>Enter</code>.

----

_Step 11d:_
{% highlight console %}
...
Git remote heroku added
Awesome. Let's teach Heroku about our GitHub app.
What's your GitHub Client ID?
{% endhighlight %}
Here we need to enter the GitHub OAuth Client ID.

----

_Step 11e:_
{% highlight console %}
...
What's your GitHub Client Secret?
{% endhighlight %}
Then enter the GitHub Client Secret.

---

_Step 11f:_
{% highlight console %}
...
What's your GitHub Team ID?
{% endhighlight %}
Enter the GitHub Team ID. Be aware that you _cannot_ use a private (paid or unpaid) account's username, it _must_ be a team created with an organizational account.

----

_Step 12:_
Inside your local clone of Jekyll-Auth, create a file named <code>.env</code>. Put the following lines into this file providing your own OAuth2 Client ID, Client Secret and the GitHub Team ID.
{% highlight console %}
GITHUB_CLIENT_SECRET=abcdefghijklmnopqrstuvwxyz0123456789
GITHUB_CLIENT_ID=qwertyuiop0001
GITHUB_TEAM_ID=@foo-organization/foo-team
{% endhighlight %}
I'm not entirely sure, but you will probably have to put an @ at the beginning of the organization/team ID as in the example given.

----

_Step 13:_
We are not yet ready to push our local clone of Jekyll-Auth to the remote Heroku server. We still need to add the Gemfile.lock to the repository:
{% highlight console %}
git add -f Gemfile.lock
git commit -m "Added Gemfile.lock"
{% endhighlight %}
Be aware that we use the <code>-f</code> parameter to enforce adding this file. If you do not provide the parameter git might refuse to add the file to the repository because it is actually ignored in <code>.gitignore</code>.

----

_Step 14:_
In the _&#95;config.yml_ file you can specify which directories and files are accessible without authentication and which are not. By default, all access requires authorization except for the _drafts_ directory. You can actually use regular expressions to specify the files and directories. The following denies access to all parts of the site except for the _drafts_ directory.
{% highlight yaml %}
jekyll_auth:
  whitelist:
    - drafts?
{% endhighlight %}
Using regexes, you can also reverse the logic, allowing access to everything except _drafts_:
{% highlight yaml %}
jekyll_auth:
  whitelist:
    - "^((?!draft).)*$"
{% endhighlight %}

----

_Step 15:_
Now we are finally ready to push everything to the remote Heroku server: <code>git push heroku master</code>.

----

_Step 16:_
Open a browser and navigate to the Heroku URL <code>https://my-new-cool-herokuapp.herokuapp.com</code>. You should be automatically redirected to a GitHub page asking for authorization: <code>Authorize application - my-cool-new-heroku-app by @my-team-id would like permission to access your account</code>. You can click on <code>Authorize application</code>.

----

Hope this helped. I ran into a [few problems which I discussed here](https://github.com/benbalter/jekyll-auth/issues/36).