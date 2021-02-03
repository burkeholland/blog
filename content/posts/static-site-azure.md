---
title: "This is how to easily deploy a static site to Azure"
date: 2019-05-30T08:36:49-06:00
draft: false
---

Over the weekend I bought my first new vehicle ever: A red moped. That was the only thing available at my price point.

![](https://miro.medium.com/max/1000/1*igNXrT_-JvgxJSeJ745Owg.jpeg)

What a glorious thing it is! The wind whips over your body at a stunning 35 mph and you are alone with your thoughts. You ponder the finer points of the universe: existential questions like, “Do I look stupid on this thing”, “How many bugs can you eat before you die” and “How many ways can I deploy a static site to Azure”?

## What’s a static site, moped boy?

Good question. But can we not call me “moped boy”? I mean it’s got 150 CC’s. I can do like 55\. Downhill with the wind.

Frameworks like PHP, ASP.NET and Django assemble pages on the fly. When a site is requested, the server performs some queries or operations, assembles the markup and sends a “page” back to you.

![](https://miro.medium.com/max/600/1*69aS6OhjDU3RBeSRqXiaFA.png)

A “static site”, is any site that is not using a server-side framework. It is just HTML, CSS and JavaScript. These days most static sites are some form of single page application built with Angular, React or Vue (alphabetical order, breathe deeply). I think I’m supposed to include Svelte in that list now too.

![](https://miro.medium.com/max/600/1*sar590LUigydRl6LkIqU7Q.png)

I’m not sure why I drew my servers in such a bad mood. I think it’s because that’s how I would feel if I was a server. Thankless job. Nobody cares until you go down and then everyone is mad you.

“So this article is about how to host an index.html file? Really?”.

NO. I mean, kind of. Actually, yes.

## Azure Storage

[Azure Storage](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-static-website?WT.mc_id=personal-medium-buhollan) is specifically designed to serve files as quickly as possible. Static sites are just files. And because God loves you and wants you to be happy, there is VS Code. And because Microsoft loves you, there is an [Azure Storage extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage&WT.mc_id=personal-medium-buhollan).

Once it’s installed, you create a storage account. That’s pretty straightforward and quick. Short enough to fit in this GIF.

![](https://miro.medium.com/max/1280/1*Py8u-X-STzMXvL_YKOE1Iw.gif)

[Jeff Hollan](https://twitter.com/jeffhollan) from the Azure Functions team had a cool tweet the other day about naming temporary Resource Groups with “deleteme” and then having a PowerShell function that deletes them every day. This way you can play around and not end up with a bill you weren’t expecting.

<div class="twitter-tweet twitter-tweet-rendered" style="display: flex; max-width: 550px; width: 100%; margin-top: 10px; margin-bottom: 10px;"><iframe id="twitter-widget-0" scrolling="no" frameborder="0" allowtransparency="true" allowfullscreen="true" class="" style="position: static; visibility: visible; width: 550px; height: 580px; display: block; flex-grow: 1;" title="Twitter Tweet" src="https://platform.twitter.com/embed/index.html?dnt=false&amp;embedId=twitter-widget-0&amp;frame=false&amp;hideCard=false&amp;hideThread=false&amp;id=1132774507821748224&amp;lang=en&amp;origin=https%3A%2F%2Fburkeholland.github.io%2Fposts%2Fstatic-site-azure%2F&amp;theme=light&amp;widgetsVersion=ed20a2b%3A1601588405575&amp;width=550px" data-tweet-id="1132774507821748224"></iframe></div>

The account will get created and show up in the sidebar under the Storage Extension.

![](https://miro.medium.com/max/1392/1*6IXOSL6kZcJ3GMizLC3u7g.png)

Azure Storage is for serving files, so we need to tell it that we want it to serve our files like a web server. To do that, open the Command Palette and select “Configure static website”.

![](https://miro.medium.com/max/1280/1*OB5z_9Wv3mgy2XMrkN76dw.gif)

It asks what the “index.html” page is. That’s usually just “index.html”. Then it asks for a 404 page. Since we have a single page application, we’re going to direct all traffic back to the “index.html” page and handle routing ourselves.

Now we just need to right-click our “dist” or “build” or whatever folder contains the build assets that Webpack made via whatever it is that Webpack does (nobody but [Sean Larkin](https://twitter.com/TheLarkInn) knows) and select “Deploy To Static Website”. In this case, I’m deploying a site that is written in Vue.

![](https://miro.medium.com/max/1281/1*yGN41vTumZskHeRHOoKYpA.png)

And that’s it. The extension will give you a prompt to browse the website. You will marvel at your own productivity and consider having pizza for a third night in a row. Go ahead. Treat yourself.

![](https://miro.medium.com/max/1392/1*153NnRgd61tH9U4URyjCNg.png)

Azure Storage is the preferred method for hosting a static site. Here’s why…

*   It’s Simple
*   It’s Fast
*   It’s Cheap — 20 cents per gig of storage per month / 8 cents per gig outbound data transfer, but you get the first 5 gigs every month free.
*   It automatically scales
*   IT’S SERVERLESS. I used a buzzword. You have to be convinced now.

## Azure App Service

[App Service](https://docs.microsoft.com/en-us/azure/app-service/?WT.mc_id=personal-medium-buhollan) is Azure’s Platform as a Service or “PaaS”. It’s different from Storage because it provides a runtime for your server-side projects. If you had an ASP.NET application, you would run it in App Service. You can’t run it in Azure Storage because Storage doesn’t know how to run applications.

You can host a static site in App Service, but because it’s designed for much more powerful server-side workflows, We need to sort of “finesse” things a bit.

Kid gloves, people.

I’m going to do everything via VS Code because that’s how I want to do everything in my life. I would happily take a VS Code extension for Outlook. Free startup idea right there.

![](https://miro.medium.com/max/2416/1*Gvs5rpNLh3I6hTMApKe4PQ.png)

When you set up a new App Service instance, you can choose from either Linux or Windows hosting in App Service, so I’m going to cover how to publish your static site to both. And of course, we’re going to do it all from VS Code with the [App Service extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice&WT.mc_id=personal-medium-buhollan).

### Linux

Let’s set up the new site via the VS Code extension.

![](https://miro.medium.com/max/2560/1*YDJJsQI1h55O-yHkZUFQ_A.gif)

Note that I selected “LTS” as my Node version. LTS on Azure means that you get whatever the highest Node version that Azure supports is. Whenever Azure supports a newer version, your project will automatically get updated.

App Service for Linux does not come with a baked-in web server. So you can’t just chuck your static site in there. Azure is gonna be like, “I don’t know what you want from me”. And you’ll be like, “I want you to run this site”. And Azure will be like “I don’t know how”. And you’ll feel like nobody understands you.

To fix that, we need to send a web server up with our files. A simple way to do this is to use the [serve](https://www.npmjs.com/package/serve) package from npm.

You also need some way to tell Azure to start “serve”. You could do that by putting a second `package.json` file in your “dist” folder. But two package JSON’s is kinda ew. Not very ew, but definitely slightly ew.

Instead, we can leverage the fact that Azure bakes [pm2](http://pm2.keymetrics.io/) into every Node.js app in App Service. pm2 is a process manager for Node, and you can start apps with it. That means that if we include the right kind of file in our project, Azure will see it and use pm2 to start our project. Here’s a video that explains pm2 in a bit more detail just in case you’re new to the pm2 crew.

<iframe width="560" height="315" src="https://www.youtube.com/embed/hYdSfH-0tVI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen=""></iframe>

The file pm2 looks for is called ecoysystem.config.js. In that file, tell pm2 to start “serve”. Passing the -s flag makes “serve” route every request it can’t find back to index.html. We want that because we have a SPA and we are handling all the routing on the client.

    module.exports = {
      apps: [
        {
          script: "npx serve -s"
        }
      ]
    };

I’m also using `npx` because that keeps me from having to ssh into my instance and install “serve” globally.

Drop this file in the “dist” folder. Then deploy the “dist” folder to Azure. Boom — there is your static site. It took me 2 years here at Microsoft to figure out that simple solution. If Vue is your jam, I created a [simple Github repo](https://github.com/burkeholland/dead-simple-vue-azure) just for you.

Now I realize that you probably don’t wanna right-click and deploy for the rest of your natural life, so instead of doing that, you can use App Service’s ability to build your project for you. Building With App Service for Linux

If you enable Local Git or GitHub deployment on your site and then check in, Azure will pull the code, run npm install, and then automatically run npm run build. In this case, you would want the ecosystem.config.js file to be at the root and point to the “dist” folder that gets created by the build step.

    module.exports = {
      apps: [
        {
          script: "npx serve dist -s"
        }
      ]
    };

Now your build is taking place on Azure. All you need to do is check your code into Git — Which, let’s be honest, is hard enough as it is. It’s nice that the rest of this “just works”.

> Note that at the time of this writing, you need to choose Node 10.14.1 in order for the automatic builds to kick in. This should be fixed in LTS shortly (like in a couple of weeks) so I’m writing it that way to keep it fresh.

### Windows

On Windows, your site kind of just works. Kind of. This is because Windows for Node.js apps in App Service includes Internet Information Services (IIS), which is perfectly fine with serving up some static files. Here’s a Windows instance of my app where all I did was deploy the “dist” folder.

![](https://miro.medium.com/max/2784/1*tYRiq9ZPaojmrAossVw7HA.png)

Amazing! But — there is a problem here. You can’t deep link to your app. So if you tried to go the theurlist.com/build2019 (which is a valid route), you would get this…

![](https://miro.medium.com/max/2784/1*YNFHYjl3YdFVaoDU5lWwow.png)

This is because IIS is looking for “theurlist.com/build2019/index.html” — which does not exist. We need to tell IIS to just route any and all traffic to “index.html”. To do that, we need to add a web.config to the “dist” folder. Inside, define a single routing rule to catch all traffic.

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <rewrite>
          <rules>
            <rule name="Vue" stopProcessing="true">
              <match url=".*" />
              <conditions logicalGrouping="MatchAll">
                <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
              </conditions>
              <action type="Rewrite" url="/" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>

As before, push the “dist” folder into Azure. Note that the automatic builds that we have on Linux do not work for Windows.

### Azure Web App for Containers

Azure App Service actually uses containers under the covers for every site — even when you are just deploying your files. For that reason, you can also just deploy a container to App Service.

Make sure you have [Docker](https://www.docker.com/) installed. Docker doesn’t work very well if it isn’t installed.

Now is also a good time to tell you to….wait for it….install the [Docker extension for VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker&WT.mc_id=ignite2019-urlist-buhollan). Come on. You knew that was coming.

First, we need to add a `Dockerfile` to our project. You can just drop that in the root.

![](https://miro.medium.com/max/2784/1*yvq3BMrKuf-aMG1arlvlTg.png)

To run the site in a container, we need a web server. Since we’re on a Node kick, we’ll just keep working with “serve”.

> Check out [this gist](https://gist.github.com/willurd/5720255) I found that shows you a bunch of different web servers that you can get running for static sites in about one line. Any of these would work in Docker.

Our Docker file with “serve” looks like this…

    FROM node:alpine

    # Copy in dist

    COPY ./dist /app

    # Install serve

    RUN npm i -g serve

    # Start it up

    CMD serve /app -s

Build it with VS Code by selecting “Docker: Build Image” from the Command Palette.

![](https://miro.medium.com/max/2784/1*dFnsJSaDEpvtB0ZyZ3R85A.png)

The image will then show up in VS Code under the “Docker” explorer.

![](https://miro.medium.com/max/2784/1*9hyAvTUWZXs9RzYHygnStA.png)

Now push it to Docker Hub. To do that, expand the node and right-click the “latest” image and select select “tag”. You then need to enter your Docker Hub username first. The “latest” tag just means that this is the latest version of this image. Right-click it again and select “Push”. This should push it to Docker Hub. If it fails or says “Access Denied”, make sure you are signed into Docker Hub via the terminal…

    $ docker login

Now that the image is up in Docker Hub, we can pull it into Azure. We need a site to do that. We could configure that through the portal, but that’s…not very exciting, so let’s do with the [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&WT.mc_id=personal-medium-buhollan). Also, this article is getting long and my fingers are tired. Your eyes are probably bleeding.

Create a Resource Group…

    $ az group create -n deleteme-vue-docker

Create a Service Plan. Remember — this is where we choose how much resources we want from Azure. In other words, this is where you spend money.

    $ az appservice plan create -n vuedocker -g deleteme-vue-docker — is-linux -l “East US” — sku S1

Create the new instance and pull in the container from Docker Hub…

    $ az webapp create -n vue-docker -g deleteme-vue-docker -p vuedocker -i burkeholland/frontend-vue-typescript:latest

After that command finishes, if you scroll back up through the CLI output, you’ll see the URL of your site…

![](https://miro.medium.com/max/2784/1*Kf0GREvsy7b0Meo6bcXopA.png)

> Disclaimer: It’s a good idea to use a more full-featured web server in production than just “serve”. The reason for this is that “serve” is fairly simple and does not have some of the advanced features you might want in the future. This would be things like gzip or brottli compression, max worker connections, MIME type mappings and more. For that, I would recommend using [nginx](https://hub.docker.com/_/nginx/). It’s not nearly as simple to setup, but it’s got all the bells and whistles.

## And…..DONE

Are you still with me? How many people did we lose along the way? Who died of dysentery?

For those who are still awake, alert, alive and enthusiastic, let’s recap what we now know about hosting static sites in Azure.

*   Preferred: Azure Storage
*   Azure App Service for Linux with an `ecosystem.config.js` file
*   Azure App Service for Windows with a `web.config`
*   Azure App Service with a container

Choose your own destiny. Here are some additional links you might find helpful in your static site quest. Godspeed.