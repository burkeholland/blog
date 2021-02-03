---
title: "How to configure a root domain in an Azure Static Web App"
date: 2020-05-06T14:55:57-06:00
draft: false
---

[Azure Static Web Apps](https://docs.microsoft.com/en-us/azure/static-web-apps/?WT.mc_id=staticwebapps-docs-cxa) is a new Azure service for hosting static web applications. Azure is very literal when they name things. Want Storage? You need Azure Storage. Want a Serverless function? Azure Functions. Need to host a static web app? **AZURE STATIC WEB APPS**.

Static Web Apps is currently in preview, which means not all features that need to be there are quite there yet. One of the features that is currently [AFK](https://www.grammarly.com/blog/afk-meaning/) is support for root domains. You can have [https://www.mystaticsite.com](https://www.mystaticsite.com), but you cannot have [https://mystaticsite.com](https://mystaticsite.com).

OR CAN YOU?

If you click on those links, you’ll see that they both work. While root domains aren’t officially supported in Azure Static Web Apps, you can make it work with a little bit of DNS trickery and the help of Cloudflare. In this article, I’ll show you how to get a root domain configured for your Static Web App while we’re waiting for official root domain support to arrive.

### Setting up Cloudflare

Cloudflare is a service that is often placed in front of web sites to provide additional features and security, such as protection against denial of service attacks. Cloudflare has a powerful DNS and page rules engine that you can use to get root domain support for your site.

First, you need to setup Cloudlfare and make it your DNS provider.

Sign up for a [Cloudflare acccount](https://cloudflare.com). They have a free tier, and everything we’re doing here is covered under that free offering.

Enter your site url as the name.

![cloudflare new site screen](/media/static-site-root-domain/cloudflare-new-site.png)

Select the Free tier. I put a few extra arrows so you know for sure which one it is.

![cloudflare tier selection screen](/media/static-site-root-domain/free-account.png)

Since Cloudflare is going to be your new DNS provider, it tries to query your site to see if it can determine what your current DNS records are. This is so you don’t have to copy them all over manually from whatever you current DNS provider is. Click the “continue” button at the bottom of this screen.

![cloudflare DNS preview screen](/media/static-site-root-domain/cloudflare-continue.png)

Now Cloudflare is going to give you two new nameservers that it wants you to use instead of your current nameservers. This requires logging in to your DNS provider and changing the nameserver. Every provider is different (like snowflakes and Nickelback songs). For this example, we’ll use mine, which is “Namecheap”.

Go to your domain registrar and log in. Go to wherever it is in your dashboard that allows you to manage your domain. There is usually a “Manage” button or option that you need to click on.

From your domain dashboard, find the “nameservers” section. By default, this will be your domain registrars default DNS. You’ll need to change it to custom DNS and enter the nameservers that Cloudflare gave you.

![cloudflare custom nameserver settings](/media/static-site-root-domain/custom-dns.png)

Make sure to save your changes. I always make this mistake because the “save” button in Namecheap is so very, very small and not at all like a save button. I mean, is it just me or is this the smallest save button you have ever seen?

![namecheap tiny save button](/media/static-site-root-domain/tiny-save.png)

What I’m trying to say is don’t forget to save and “saving” might require you to be a highly trained Army sniper.

#### Configuring CNAMES

What you’ve just done is said that Cloudflare is now managing your DNS. From here on out, all DNS changes we make will be in Cloudflare.

You’ll need two CNAMEs in Cloudflare DNS. One of them is going to be the “www” which points to the auto-generated domain name that Azure Static Web Apps gives you. The other is going to be a CNAME that points the root domain at the www. This works because Cloudflare supports something called “[CNAME flattening](https://support.cloudflare.com/hc/en-us/articles/200169056-Understand-and-configure-CNAME-Flattening)”. Simply put, it’s going to “flatten” any request to mystaticsite.com over to www.mystaticsite.com.

![cloudflare dns dashboard showing two custom CNAME's](/media/static-site-root-domain/two-cnames.png)

**IMPORTANT - DON’T SKIP THIS**: Those orange cloud icons on the right-hand side under “proxy status” are important. For the “www” CNAME, we only want Cloudlfare to act as DNS, so **click on the orange cloud** to toggle it to grey (DNS only). For the root domain CNAME, we want to proxy all requests through Cloudflare, so leave that one orange.

At this point, your root domain will work. Kind of. If you navigate to mystaticsite.com (or whatever your domain name is), you will get back the www.mystaticsite.com and your site will load. Awesome!

![site kind of working with cloudflare configuration](/media/static-site-root-domain/kinda-working.png)

However, if you try and navigate to mystaticsite.com/some/path, the url won’t resolve. That’s because Cloudflare doesn’t recognize the path you just passed in as matching the root CNAME. In order to handle this, we need to setup a page redirect rule.

Click on the “Page Rules” icon in the Cloudflare navigation bar.

![cloudflare page rules icon](/media/static-site-root-domain/page-rules-icon.png)

Create a new page rule. Make sure you select the type as “Forwarding URL”. You’ll then need to add in the url you want to match and specify where you want that URL redirected to. The syntax is important. The URL matcher “*” says match yourdomain.com/any/path and redirect it to www.yourdomain.com. The $1 appends anything after the .com to the redirect. So if you went to yourdomain.com/any/path you will get redirected to www.yourdomain.com/any/path.

![cloudflare page rules configuration screen](/media/static-site-root-domain/page-rules-screen.png)

And now - you can select any path you like on your root domain and you will get back the right page in your app.

## When will root domains be officially supported?

Hopefully sooner than later. Rest easy knowing that the team is keenly aware that root domain support is an essential feature. The change required to support it is bigger than just Static Web Apps, and there is already a plan in place to make that change. It’s a priority for the service, so no need to worry that it will stay like this forever.

## More Azure Static Apps Resources

*   [Static Web Apps Docs](https://docs.microsoft.com/en-us/azure/static-web-apps/?WT.mc_id=staticwebapps-docs-cxa)
*   [Create and publishing an Angular, React, Svelte or Vue JavaScript app and API](https://docs.microsoft.com/en-us/learn/modules/publish-app-service-static-web-app-api/?WT.mc_id=staticwebapps-frameworks-cxa)
*   [Creating and publishing an app with Gatsby](https://docs.microsoft.com/en-us/learn/modules/create-deploy-static-webapp-gatsby-app-service/?WT.mc_id=staticwebapps-sitegen-cxa)
*   [Give us your feedback!](https://github.com/Azure/static-web-apps)