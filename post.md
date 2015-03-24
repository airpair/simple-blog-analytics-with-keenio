## Introduction

Nowadays most blog platforms provide some kind of analytics data.

In my case I am using google's blogger ([mancusoa74's blog](http://mancusoa74.blogspot.it/ "mancusoa74's blog"))
  and as expected the stats are very good and detailed.
I also have other web sites which I normally track with Google Analytics. 

Google Analytics provides any possible info you can imagine about your pages. This is very good!

However, for me, such an amount of information is overwhelming. 
In most of the cases, I don't need all such details. I just care about number of visitors and sessions.

Last but not least each platform provides his own dashboard, and I would like to have a unique consolidated dashboard view for all my blogs and web site.

## Image Beacon

[Keen IO](https://keen.io/ "Keen.io") provides the necessary tool, which is called [Image Beacon](https://keen.io/docs/data-collection/image-beacon/ "Image Beacon")

The concept is not really new, but still very effective. The idea is to embed a small (very small) transparent image in your HTML content, and every time someone hits your page, the transparent image is requested by the browser.

The URL of the image is pointing to Keen IO's service. When Keen IO receives a request for such particularly crafted URL, it will add an event to your selected collection (see Keen IO [documentation](https://keen.io/docs/ "Documentation"))  and send back to the browser the transparent image.

This way you can easily track any content you have on your blog, or any other type of web site.

Simple, quick and effective!!!

Let's now see an example:

Let's assume your projectID is PROJECT_ID. Your write api key is API_KEY and your collection is COLLECTION

You construct the URL of the beacon image in this way:

```
https://api.keen.io/3.0/projects/PROJECT_ID/events/COLLECTION?api_key=API_KEY&data=BEACON_DATA

```

You are wondering what BEACON_DATA is?

Simple! it is the event you want to record in your collection when your page/post is hit.

An example of BEACON_DATA is:

```javascript,linenums=true
{
  "post_id": 1234,
  "post_title": "my blog post",
  "hit": 1
}
```

However you cannot just put this JSON in the above URL. 

Keen IO API requires that you value the data parameter with the base64 representation of the above JSON chunck.

In this case the base64 representation is:

```
ewogICJwb3N0X2lkIjogMTIzNCwKICAicG9zdF90aXRsZSI6ICJteSBibG9nIHBvc3QiLAogICJoaXQiOiAxCn0K
```

It might look complex but it is not.

Now just add this image to your HTML content

```
<img src="https://api.keen.io/3.0/projects/PROJECT_ID/events/COLLECTION?api_key=API_KEY&data=ewogICJwb3N0X2lkIjogMTIzNCwKICAicG9zdF90aXRsZSI6ICJteSBibG9nIHBvc3QiLAogICJoaXQiOiAxCn0K"></img >
```

After that, every time your page will be hit a new event will be added to your Keen IO COLLECTION.

## Auto Collection & Data Enrichment

I found very interesting and useful also the [Data Enrichment](https://keen.io/docs/data-collection/data-enrichment/ "Data Enrichment")  and the [Auto Collection](https://keen.io/docs/data-collection/auto-collection/ "Auto Collection") functionalities.

Using those two very useful features, let's see another possible BEACON_DATA chunk:

```javascript,linenums=true
{
    "keen" : {
        "addons" : [
            {
                "name" : "keen:ip_to_geo",
                "input" : {
                    "ip" : "ip_address"
                },
                "output" : "ip_geo_info"
            }
        ]
    },
    "ip_address" : "${keen.ip}",
    "user_agent" : "${keen.user_agent}",
    "hit": 1,
    "post_id": 1234,
    "post": "your post title"
}

```
As in the previous example let's calculate the base64 of this BEACON_DATA chunk:

```
ewogICAgImtlZW4iIDogewogICAgICAgICJhZGRvbnMiIDogWwogICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAibmFtZSIgOiAia2VlbjppcF90b19nZW8iLAogICAgICAgICAgICAgICAgImlucHV0IiA6IHsKICAgICAgICAgICAgICAgICAgICAiaXAiIDogImlwX2FkZHJlc3MiCiAgICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgICAgIm91dHB1dCIgOiAiaXBfZ2VvX2luZm8iCiAgICAgICAgICAgIH0KICAgICAgICBdCiAgICB9LAogICAgImlwX2FkZHJlc3MiIDogIiR7a2Vlbi5pcH0iLAogICAgInVzZXJfYWdlbnQiIDogIiR7a2Vlbi51c2VyX2FnZW50fSIsCiAgICAiaGl0IjogMSwKICAgICJwb3N0X2lkIjogMTIzNCwKICAgICJwb3N0IjogInlvdXIgcG9zdCB0aXRsZSIKfQo=
```

<img src="https://api.keen.io/3.0/projects/54bae2f990e4bd31acbe257d/events/trackme_airpair?api_key=8f78c2ac53f8fb11725b1262d0af9f339334d67581241b3d86469524b8cfc038091f8b1f9b2fd84f7e1a551900e87cc1f72cb9f835778aed02b3246b2f38a1f610fab167fda89e7cf3f906eac95dd4285135e72194e9e16e2bef7f40227c4ba98683419496c294396aeec368021a354e&data=ewogICAgImtlZW4iIDogewogICAgICAgICJhZGRvbnMiIDogWwogICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAibmFtZSIgOiAia2VlbjppcF90b19nZW8iLAogICAgICAgICAgICAgICAgImlucHV0IiA6IHsKICAgICAgICAgICAgICAgICAgICAiaXAiIDogImlwX2FkZHJlc3MiCiAgICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgICAgIm91dHB1dCIgOiAiaXBfZ2VvX2luZm8iCiAgICAgICAgICAgIH0KICAgICAgICBdCiAgICB9LAogICAgImlwX2FkZHJlc3MiIDogIiR7a2Vlbi5pcH0iLAogICAgInVzZXJfYWdlbnQiIDogIiR7a2Vlbi51c2VyX2FnZW50fSIsCiAgICAiaGl0IjogMSwKICAgICJwb3N0X2lkIjogMSwKICAgICJwb3N0IjogInNpbXBsZS1ibG9nLWFuYWx5dGljcy13aXRoLWtlZW5pbyIKfQo="></img>

and again this is the final image tag to add to yours HTML content 


```
<img src="https://api.keen.io/3.0/projects/PROJECT_ID/events/COLLECTION?api_key=API_KEY&data=ewogICAgImtlZW4iIDogewogICAgICAgICJhZGRvbnMiIDogWwogICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAibmFtZSIgOiAia2VlbjppcF90b19nZW8iLAogICAgICAgICAgICAgICAgImlucHV0IiA6IHsKICAgICAgICAgICAgICAgICAgICAiaXAiIDogImlwX2FkZHJlc3MiCiAgICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgICAgIm91dHB1dCIgOiAiaXBfZ2VvX2luZm8iCiAgICAgICAgICAgIH0KICAgICAgICBdCiAgICB9LAogICAgImlwX2FkZHJlc3MiIDogIiR7a2Vlbi5pcH0iLAogICAgInVzZXJfYWdlbnQiIDogIiR7a2Vlbi51c2VyX2FnZW50fSIsCiAgICAiaGl0IjogMSwKICAgICJwb3N0X2lkIjogMTIzNCwKICAgICJwb3N0IjogInlvdXIgcG9zdCB0aXRsZSIKfQo="></img >
```

***NOTE: in real scenario you might want to minify your JSON in order to remove white spaces. The resulting base64 chunk and image beacon source URL will be shorter***



This is an example of an event which is created using this URL

```javascript,linenums=true
{
    "hit": 1,
    "keen": {
        "timestamp": "2015-01-23T19:57:58.922Z",
        "created_at": "2015-01-23T19:57:58.922Z",
        "id": "54c2c7c6a6662c1527843321"
    },
    "user_agent": "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/40.0.2214.91 Safari/537.36",
    "post_id": 1234,
    "post": "your post title"
    "ip_geo_info": {
        "province": "Turin",
        "city": "San Benigno Canavese",
        "postal_code": null,
        "continent": "Europe",
        "country": "Italy"
    },
    "ip_address": "A.B.C.D"
}    
```

You can now use Keen.IO's [Data visualization SDK ](https://keen.io/docs/data-visualization/ "Data Visualization")
to represent those events on a dashboard.

![](http://i.imgur.com/FGfg7sg.png)

Pretty cool,eh? :) I love it!!! :)  


## Image beacon simple tool

As the people who know me know that I am lazy, I have created a real simple bash script which creates the IMG tag for me. 

You can find it on [Github](https://github.com/mancusoa74/keen_beacon "Git Hub").

The repository contains to files:
* data_beacon.json
* keen_beacon.sh

Use it in this way:
* edit your data_beacon.json as you like
* edit (only first time) keen_beacon.sh and insert your PROJECT_ID, API_KEY and COLLECTION
* execute ./keen_beacon.sh

This will generate an output similar to this (it depends on content of data_beacon.json):


```
https://api.keen.io/3.0/projects/PUT YOUR PROJECT ID HERE/events/PUT YOUR COLLECTION HERE?api_key=PUT YOUR API KET HERE&data=ewogICAgImtlZW4iIDogewogICAgICAgICJhZGRvbnMiIDogWwogICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAibmFtZSIgOiAia2VlbjppcF90b19nZW8iLAogICAgICAgICAgICAgICAgImlucHV0IiA6IHsKICAgICAgICAgICAgICAgICAgICAiaXAiIDogImlwX2FkZHJlc3MiCiAgICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgICAgIm91dHB1dCIgOiAiaXBfZ2VvX2luZm8iCiAgICAgICAgICAgIH0KICAgICAgICBdCiAgICB9LAogICAgImlwX2FkZHJlc3MiIDogIiR7a2Vlbi5pcH0iLAogICAgInVzZXJfYWdlbnQiIDogIiR7a2Vlbi51c2VyX2FnZW50fSIsCiAgICAiaGl0IjogMSwKICAgICJwb3N0X2lkIjogMTIzNCwKICAgICJwb3N0IjogInlvdXIgcG9zdCB0aXRsZSIKfQo=


<img src="https://api.keen.io/3.0/projects/PUT YOUR PROJECT ID HERE/events/PUT YOUR COLLECTION HERE?api_key=PUT YOUR API KET HERE&data=ewogICAgImtlZW4iIDogewogICAgICAgICJhZGRvbnMiIDogWwogICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAibmFtZSIgOiAia2VlbjppcF90b19nZW8iLAogICAgICAgICAgICAgICAgImlucHV0IiA6IHsKICAgICAgICAgICAgICAgICAgICAiaXAiIDogImlwX2FkZHJlc3MiCiAgICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgICAgIm91dHB1dCIgOiAiaXBfZ2VvX2luZm8iCiAgICAgICAgICAgIH0KICAgICAgICBdCiAgICB9LAogICAgImlwX2FkZHJlc3MiIDogIiR7a2Vlbi5pcH0iLAogICAgInVzZXJfYWdlbnQiIDogIiR7a2Vlbi51c2VyX2FnZW50fSIsCiAgICAiaGl0IjogMSwKICAgICJwb3N0X2lkIjogMTIzNCwKICAgICJwb3N0IjogInlvdXIgcG9zdCB0aXRsZSIKfQo="> < /img >
```

That's really all about it.

Hope this is useful. Let me know if you have ideas to improve it.

take care!!!

x