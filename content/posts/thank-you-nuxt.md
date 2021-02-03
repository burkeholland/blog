---
title: "Deploying a Nuxt App to Azure"
date: 2019-06-05T17:04:42-06:00
draft: false
---

<summary>Summary: Nuxt apps with “server-side rendering” require a Node server, so you need to deploy them to Azure App Service. If you are using Nuxt apps in SPA mode, you can (and should) deploy them to Azure Storage.

Check out the links below for more details…

<figure><img class="azure-icon" src="/media/Icon-compute-35-App-Services.svg" /><a href="https://code.visualstudio.com/tutorials/app-service-extension/getting-started?WT.mc_id=personal-blog-buhollan">Deploy to Azure App Service</a></figure>

<figure><img class="azure-icon" src="/media/Icon-storage-86-Storage-Accounts.svg"/><a href="https://code.visualstudio.com/tutorials/static-website/getting-started?WT.mc_id=personal-blog-buhollan">Deploy a static website to Azure</a></figure>

</summary>

<p></p>

Ariana Grande once said, “One taught me patience. And one taught me pain”. She was talking about her ex’s. I’m here today to talk to you about Nuxt’s.

Nuxt’s? Nuxt’is? Nuxt’ises?

## What is Nuxt anyway?

[Nuxt](https://nuxtjs.org/) is “the VueJS framework”. That’s what it says on their website and why would they lie.

It’s kinda hard to nail down exactly what Nuxt does, because it does so much. It will automatically setup routing based on your folder structure. It will automatically do code splitting for static sites. It will automatically cause someone to write a blog post with a few too many uses of the word “automatically:“.

But it’s probably best known as the way that you do server-side rendering with Vue.

## Server-side Vue

Vue is a JavaScript framework. Which means that the code you write is executed in the browser at runtime. But a few years ago, someone smart realized that we can execute JavaScript on the server with Node, so we should be able to run and compile JavaScript frameworks on the server. And they called it, “Isomorphic JavaScript”.

I still don’t know what that means. Fortunately, it was renamed to “Universal JavaScript”. Vue’s primary value proposition is that it enables developers to build these “Universal Applications”. It does a lot more than that, but that’s primarily what it’s known for. Kind of like John Krasinski is Jack Ryan, but in a much more real sense, he is and always will be [Jim Halpert](https://en.wikipedia.org/wiki/Jim_Halpert).

It’s hard to understand Universal JavaScript without seeing something in action, so let’s build something pointless.

## A pointless demo

My sample app is a single page with one XMLHTTPRequest (XHR) which pulls some fake images in from the JSON placeholder API. I was expecting better fake images than this, but I suppose you can’t complain about free. Like when my sister-in-law rented a margarita machine and then made them too weak. “Listen, thanks for the free alcohol, but I am disappointed.”

![](https://cdn-images-1.medium.com/max/1600/0*7gaMmL0HlfVX9vZf.png)

Here’s the entirety of the code used to make this page.

    <template>
      <div class="section">
        <h1 class="is-size-1">Photos</h1>
        <div class="columns is-multiline">
          <div
            v-for="photo in photos"
            :key="photo.id"
            class="column is-one-quarter">
            <div class="card photo">
              <div class="card-image">
                <figure class="image">
                  <img :src="photo.url" alt="photo.title" />
                </figure>
              </div>
              <div class="card-content">
                <div class="content">
                  <p>{{ photo.title }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </template>
    <script>
    export default {
      async asyncData({ $axios }) {
        const { data } = await $axios.get(
          'https://jsonplaceholder.typicode.com/photos?_limit=100'
        )
        return { photos: data }
      }
    }
    </script>
    <style>
    .card.photo {
      height: 100%;
    }
    </style>

Not super exciting, but enough to test out Nuxt’s value proposition of rendering apps on the server. On line 29 there is an HTTP call to the endpoint made with axios. Even though this code is in the component, Nuxt is going to make that call and compile this template on the server. So the page we get will already have all of the markup in it.

But how do we know for sure that Nuxt is actually doing this? What if it’s just lying to us? How can I ever trust anyone again after [left-pad](https://www.theregister.co.uk/2016/03/23/npm_left_pad_chaos/)?

The easiest way is to just right-click the page and “View Source”. View Source shows you what the page looks like when it was requested from the server. That is before any JavaScript has a chance to mutilate it. I mean mutate it.

![](https://miro.medium.com/max/2784/0*quP9zI62tcajdDpr.png)

Yep - all of the markup is definitely there. And it ain’t pretty either. But that’s OK because it’s minified or something. And smaller is always better on the web. [OR IS IT](https://css-tricks.com/how-to-increase-your-page-size-by-1500-with-webpack-and-vue/).

You can also open the dev tools and just look for an XHR. It will be conspicuously absent.

![](https://miro.medium.com/max/2784/0*FVNiYW--Kj67z5PS.png)

I didn’t have too much trouble getting this far. Nuxt is….different than a standard Vue application, but it’s not a brick to teeth. It’s relatively easy to just scaffold out a new app and then read the first paragraph of the “Getting Started” page and assume that you know everything already.

Then I went to deploy it.

## Deploying Nuxt apps

Since Nuxt is rendering things on the server, Nuxt apps require a server. A node server to put too fine a point on it. I’m using Azure, so that means [AppService](https://code.visualstudio.com/tutorials/app-service-extension/getting-started?WT.mc_id=personal-blog-buhollan). Vue apps that are single-page applications can be hosted on [Azure Storage](https://code.visualstudio.com/tutorials/static-website/getting-started?WT.mc_id=personal-blog-buhollan).

Before the app can be run, it has to be built. Ideally, we want this to happen on the server. The preferred workflow is…

*   Make a change
*   Check into Github
*   Azure pulls in code automatically
*   Azure performs build
*   Azure starts the site

This workflow lets you focus on writing code, not deploying things. You just check them into source control and your site is magically updated. This is kind of like the simplest possible form of DevOps.

To set up a workflow like this in Azure, I need to first decide if I’m more of a Windows guy, or more of a Linux guy. Azure supports both operating systems, and the one that you choose matters a whole lot. Just wait.

Let’s start with Linux. It’s important that we start with Linux because Linux is the preferred way to run Node applications in Azure. I bolded that statement because it’s important. It means that the effort to improve the Node experience in Azure is focused around Linux.

## Deploying to Linux

Running a Nuxt application on AppService for Linux “just works” thanks to a new build tool in Azure called “Oryx”. In fact, Oryx just rolled out this month. Welcome to the world, Oryx! Sorry about 2019.

![](https://miro.medium.com/max/2600/0*FsNsKCMuU_6r6Tfe.png)

All it takes to deploy and build this thing on Azure, is to configure the site for Git or GitHub deployment and check the project into the repo.

![](https://miro.medium.com/max/1392/0*cDgYe1sVFNHR13Zm.png)

Oryx will pull in the code, run an npm install and then execute npm run build. The Git logs will show that a Nuxt build did occur.

![](https://miro.medium.com/max/2600/0*ey-IoljeCrcp32l1.png)

Browse to the site and…

![](https://miro.medium.com/max/2600/0*lyVy7wWpY044J3dx.png)

It takes a LONG time for Azure to return this error and I’ve been working with Azure long enough to know that when this happens, it’s probably because Azure cannot bind to a port or address. That happens if the app is trying to bind to some hard-coded address (like localhost) or some hard-coded port (like 3000).

A lot of hosting providers have this issue. This included sites on Heroku and anywhere else that uses containers to host apps like Azure does. To fix it, the Nuxt docs say to include a section in the nuxt.config.js that binds to 0.0.0.0.

    server: {
      host: '0.0.0.0'
    }

And…

![](https://miro.medium.com/max/2600/0*fFFcsLVb3AdMJJNO.png)

Excellent! Not too bad. The host setting is really the only thing that might trip you up here.

**Bonus tip**: you can speed up the whole build process quite a bit by using yarn instead of npm. Azure caches the top yarn modules so yarn install is going to be enormously faster than npm install.

That’s Linux. Now let’s talk about Windows.

## Deploying To Windows

Deploying to Windows is quite a bit more….interesting. There is no Oryx build tool for Windows. Instead, you get something called “Kudu”.

By default, Kudu doesn’t build anything. You have to include a .deployment file with the right setting to get it to perform a build. That just happens to be the SCM_DO_BUILD_DURING_DEPLOYMENT setting.

    [config]
    SCM_DO_BUILD_DURING_DEPLOYMENT=true

The problem is that your definition of a build and Kudu’s definition of a build are probably not the same. When I say build, I mean npm install and then npm run build. Kudu’s definition stops at npm install. But since Kudu is already running an npm install, we can trick it into also running the build command by just making it a postinstall hook.

    "postinstall": "npm run build"

It works! Wait. Kind of.

![](https://miro.medium.com/max/1392/0*9ySqMlUopJGE9oJI.png)

Well, it worked in so much as Kudu did run the postinstall hook, but the build fails. And it fails because function is an unexpected token. Anytime you see “unexpected token” as an error, the problem is likely because your Node version is wrong. I scrolled back up through the logs and saw this…

![](https://miro.medium.com/max/1392/0*_uED5o2xWwhIL83N.png)

My guess is Node 6.9.1 doesn’t support async functions.

![](https://miro.medium.com/max/1392/0*whhi1gg9RW2I-iHd.png)

Nope. Looks like version 7.10.1 is the minimum for that. To stay safe, let’s choose the highest supported version of Node in Azure, which at the time of this writing, is 10.15.2\. The most reliable way to set the `WEBSITE_DEFAULT_NODE_VERSION` in Azure is to do it via an App Setting.

![](https://miro.medium.com/max/1392/0*Pnf75E2ErbA9rKCX.png)

SUCCESS!

![](https://miro.medium.com/max/1392/0*OMHwK7RnSCs6BRO0.png)

Now we’ll just browse to the site….

![](https://miro.medium.com/max/1392/0*omyORxUG6LZq6tI3.png)

What’s the word when something should work, but it doesn’t work and you don’t know why? Oh yeah, “programming”.

Scrolling back up through the output reveals this…

![](https://miro.medium.com/max/1392/0*CW88bWnlqBMRU3wQ.png)

Well, it’s not an invalid startup command, but Kudu is expecting something in the format of node entry-point. There is an interesting line below that one that says…

“Missing server.js/app.js files, web.config is not generated”

What’s happening here is Windows uses IISNode to run the application. IISNode requires a web.config to set the entry point and root directory. If you have an app.js or serverjs file in the root of your app, Kudu will use that file to automatically generate a web.config.

So I added a server.js file to the root of my project that just required in my actual startup file.

    require('./server/index.js');

Redeploy and…

![](https://miro.medium.com/max/1392/0*bWHIKki05mhHVZXV.png)

Not terrible, but not terribly intuitive either. You don’t know what you don’t know. And if you didn’t know, now you know.

## Look what you taught me

I’ll be honest, figuring out the Windows part of this thing was no fun. Patience and pain. That said, I do feel like I understand Azure AppService for Windows way more than I ever thought that I would. At least enough to write a blog post.

Oryx is new, and it’s a much-welcomed improvement. It works the way that I would expect it to, which is kinda all we’re really asking for as developers. It’s like the Dyson guy used to say…

> <div>I just think that things should work</div>
> 
> <cite>-The Dyson Vacuum Cleaner Guy</cite>

[Sample application from this article](https://github.com/burkeholland/nuxt-appservice-windows)

