---
date: 2015-09-24 10:00:00
title: Open Redirect in LinkedIn and Yahoo
category: security
---

This all started as any other day. As Pentesters, we come across many types of platforms and frameworks and because of that there is a need on trying to keep up with the developers.

This time, Vitor Oliveira ([@r0t1v](https://twitter.com/r0t1v)) had to test a Node.js application. After some research he had found a great website with node.js vulnerabilities, [https://nodesecurity.io](https://nodesecurity.io). Since the client webapp was using express.js, the next thing was to look for vulnerabilities that express.js had.

After a while he found a open-redirect vulnerability that had been disclosed by Pierre-Élie Fauché ([https://nodesecurity.io/advisories/serve-static-open-redirect](https://nodesecurity.io/advisories/serve-static-open-redirect)).


> "When using serve-static middleware version < 1.7.2 and it’s configured to mount at the root it creates an open redirect on the site.
>
> For example: If a user visits http://example.com//www.google.com/%2e%2e they will be redirected to //www.google.com/%2e%2e, which some browsers interpret as http://www.google.com/%2e%2e."

**Note:** This vulnerability doesn't work in Google Chrome but works in Firefox and Opera.

Testing in the client app, and indeed was vulnerable.

After a couple of days, me ([@fjreis](https://twitter.com/fjreis)) and Fábio Pires ([@fabiopirespt](https://twitter.com/fabiopirespt)) joined Vitor and decided to search for the top websites that were using express.js. **Yahoo** and the **mobile website from Linkedin** were two of the websites that we have found.


![image1]({{ site.url }}{{ site.baseurl }}/images/posts/2015/09/open-redirect/LinkedIn_logo.jpeg){: .align-center}

We started with Linkedin mobile website: **[https://touch.www.linkedin.com/](https://touch.www.linkedin.com/)**

Issuing the request in burp suite we found that it was not working with two slashes (as Pierre describes in his vulnerability), so we tested with 4 slashes and this is what we got:

* Request 

![image2]({{ site.url }}{{ site.baseurl }}/images/posts/2015/09/open-redirect/LinkedIn_request.png)

* Response

![image3]({{ site.url }}{{ site.baseurl }}/images/posts/2015/09/open-redirect/LinkedIn_response.png)

Open redirect, yey!

## Proof of Concept

* **Android:** [https://vimeo.com/126193891](https://vimeo.com/126193891)
* **iOS:** [https://vimeo.com/126193892](https://vimeo.com/126193892)

## Report timeline

* April 28, 2015 - **Bug reported to Linkedin**
* April 28, 2015 - Confirmation from Linkedin’s security team
* May 28, 2015 - Pinged Linkedin team
* May 28, 2015 - **Bug fixed**
* September 24, 2015 - **Public disclosure**

<br/>
<br/>

![image4]({{ site.url }}{{ site.baseurl }}/images/posts/2015/09/open-redirect/Yahoo_logo.gif){: .align-center}

Now the story with **Yahoo** is more fun. We found two websites from **Yahoo** using **express.js**:

* [developer.yahoo.com](developer.yahoo.com)
* [publish.yahoo.com](publish.yahoo.com)

## developer.yahoo.com

* Request

![image5]({{ site.url }}{{ site.baseurl }}/images/posts/2015/09/open-redirect/Yahoo_developer_request.png)

* Response

![image6]({{ site.url }}{{ site.baseurl }}/images/posts/2015/09/open-redirect/Yahoo_developer_response.png)

## publish.yahoo.com

* Request

![image7]({{ site.url }}{{ site.baseurl }}/images/posts/2015/09/open-redirect/Yahoo_publish_request.png)

* Response

![image8]({{ site.url }}{{ site.baseurl }}/images/posts/2015/09/open-redirect/Yahoo_publish_response.png)

## Proof of Concept

* **Android:** [https://vimeo.com/126305222](https://vimeo.com/126305222)
* **iOS:** [https://vimeo.com/126320994](https://vimeo.com/126320994)
* **Android:** [https://vimeo.com/126305223](https://vimeo.com/126305223)

## Report timeline

* May 28, 2015 - **Bug reported to Yahoo**
* May 28, 2015 - **Yahoo’s security team** tells to report in **HackerOne**
* May 28, 2015 - **Bug reported to HackerOne**
* May 28, 2015 - **Response from HackerOne:** *"Thank you for your submission to Yahoo! We are aware of this functionality on our site and it is working as designed. Open redirects have been out of scope since January 1st, 2014. Please continue to send us vulnerability reports!"*
* September 24, 2015 - **Public disclosure**

Both websites **are still vulnerable**