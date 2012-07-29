---
layout: post
title: "Ant Javadoc and GitHub"
date: 2012-04-16 20:29
comments: true
categories: 
---

During our [Systems Design Project](http://www.inf.ed.ac.uk/teaching/courses/sdp/) 
course at Edinburgh we hit the problem of a large team coding on separate parts
of a system and not having instant access to each other like we would in
a development office environment.

We wanted to create a easy, automated way to share documentation about code
which was being worked on in separate branches. The code was already being
documented by JavaDoc and some of use were generating it adhoc to look at other
peoples stuff.

I had used Github pages service before (this site is hosted there). Since our
code was already hosted there this would create an easy platform to view the
generated JavaDoc as a web page, independent of the branch it was on.

Ant was being used to build the code so it was a simple task to add an ant task
into generate the JavaDoc and then call a script that would upload it Github
pages.

### Ant Task


    <target name="javadocgenerate"> 
        <javadoc packagenames="uk.ac.ed.inf.sdp2012.group7.*"
            sourcepath="src" 
            destdir="../pcdoc" 
            author="true" 
            version="false"
            windowtitle="Group 7 PC code"> 
        </javadoc> 
        <exec executable="/bin/bash"> 
            <arg value="publish.sh"/> 
        </exec> 
    </target>

### Bash Script

    #!/bin/sh
    # Push javadoc files to a website hosted by github <http://pages.github.com/>.
    # Before executing this script, generate the javadoc files into pcdoc
    #
    #David Fraser

    # find out the current branch so we know where to switch back
    OLD_BRANCH=`git branch --no-color | sed -e '/^[^*]/d' -e 's/* \(.*\)/\1/'`

    git checkout gh-pages || exit $?

    # Clear out the old files: (files which will be served)
    rm -rf javadoc/* 

    # Replace them with new files and commit them:
    cp -pr pcdoc/* javadoc \
    && git add javadoc \
    && git commit -a -m "generated javadoc"

    #Remove the generated doc
    rm -rf pcdoc/*

    git push origin gh-pages || exit $?

    # Switch back to the old branch
    git checkout $OLD_BRANCH || exit $?


All the user has to do is call ‘ant javadocgenerate’ and any JavaDoc they have
added to the code will be generate and put online. It does take 5 or 10 minutes
for the docs to appear online but Github notify you when the page is built.

### Links

[Github pages](http://help.github.com/pages)  
[Ant JavaDoc](http://ant.apache.org/manual/Tasks/javadoc.html)  

[Example from SDP](http://sdpgroup7.github.com/code/javadoc/overview-tree.html)  

