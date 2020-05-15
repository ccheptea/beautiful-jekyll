---
layout: post
published: false
title: Angular meets Elixir/Phoenix
---
# Elixir/Phoenix + Angular

I recently started working on an Elixir/Phoenix based project. Well, I'm too rusty for that, I said to myself. After many years of OOP I was about to delve into the realm of functional programming.  But, against common misconceptions, it turned out to be a very fun experience and I literally feel I'm in the safe zone now (pun intended). 

I got so excited with this new discovery that I decided to use Elixir/Phoenix for another project of my own. The thing is, I already had the frontend somewhat prototyped in Angular 9, so all I had to do is create a Phoenix/Elixir app that serves an Angular frontend. 

So, let's see how we can make things work.

### 1. Create the Elixir/Phoenix project

Open you terminal and navigate to your working directory and create a new Phoenix project. We will also specify it doesn't add webpack, that will be taken care of by Angular.

    $ mix phx.new --no-webpack

[Optional] You can navigate to `priv/static/` and remove all files in there. Here is the place our built Angular sources will live.

### 2. Create the Angular project

In the root folder of you Phoenix project create the Angular project. Let's call it `frontend`.

    ng new frontend

For simplicity's sake, lets move `angular.json` and `package.json` from the `/frontend` folder to the root folder of our project. The reason we're doing this is to allow us to run `npm` and `ng` (angular-cli) commands straight from the root folder.

Now we need to update the `angular.json` file such that each path reflects the new location, in our case we have to add the `frontend/` prefix. Check the following gist as a reference.

[angular.json](https://gist.github.com/ccheptea/f117fa7073444934ffcfe687188f9ffd)

{% gist f117fa7073444934ffcfe687188f9ffd %}

Note: If Angular initialized a git repo and installed the default node_modules (it most likely did) you can safely delete them as they will now be available in the root folder. To do that navigate to `frontend` and run `rm -rf node_modules` and `rm -rf .git`. Also, don't forget to add `node_modules` to you main `.gitignore` file.

### 
