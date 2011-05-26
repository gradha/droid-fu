# Just looking for the API docs?

You can find them [here](http://kaeppler.github.com/droid-fu).

---

# Droid-Fu - for your Android needs

Droid-Fu is an open-source effort aiming to collect and bundle solutions to common concerns in the development of applications for the [Google Android platform](http://developer.android.com/index.html). Currently it mostly contains code which I pulled out of [Qype's Android app](http://www.qype.co.uk/go-mobile), and which I believe may prove useful to other developers. This is _very_ much a work-in-progress, and both content and APIs are still in flux. There is not much documentation (yet), and I intend to change that. For now, you may want to read all related articles [on my weblog](http://en.wordpress.com/tag/droid-fu/). You can also find me on [Twitter](http://twitter.com/twoofour).

There are no releases yet, we pretty much work directly with the sources. If you want to stay up-to-date, we suggest you check out the project sources, pull frequently, and compile your app against them.

## What does it offer?

Droid-Fu offers both support classes meant to be used alongside existing Android code, as well as self-contained, ready-to-be-used components like new adapters and widgets.

The areas tackled by Droid-Fu include:

  * application life-cycle helpers
  * support classes for handling Intents and diagnostics
  * better support for background tasks
  * super-easy and robust HTTP messaging
  * powerful caching of Objects, HTTP responses, and remote images
  * custom adapters and views

I suggest you read [this introductory article](http://brainflush.wordpress.com/2009/11/16/introducing-droid-fu-for-android-betteractivity-betterservice-and-betterasynctask/), and anything that follows.

## How do I install it?

Droid-Fu is deployed as a JAR. Just drop it in your app's lib folder and add it to the classpath. Alternatively, you can of course simply compile against the sources.

### Getting the JAR

If you don't want to compile against the sources, then I'm afraid you'll have to roll the JAR yourself. It's a little elaborate, but don't run off scared.
Droid-Fu employs a managed build process, driven by the wonderful [Maven build system](http://maven.apache.org) system.
This means you have to install Git to get the sources, and Maven 3 to build them.
Droid-Fu is built against the latest Android APIs (it's backwards compatible down to 1.5 though), so you must have the proper Android JARs installed as Maven artifacts, too.

The following steps summarize how to do all that.

#### Step 1: Make sure you have the latest Android library JARs

You only need to perform this step when Google releases a new platform version of the Android library files, which means every now and then.
Update your SDK files to the latest version like so:

    $ android update sdk --no-ui
    
It's important that you repeat this command until you see the message "There is nothing to install or update." That's because the tools update themselves,
so it may take several iterations of this command until everything is fully updated.

#### Step 2: Install the Android JARs to your local Maven repository

Droid-Fu must be compiled against the android.jar and maps.jar library files. Since the build is driven by Maven, you must provide these JARs as artifacts to Maven during the compile stage, otherwise the build will fail.
We can do this with the [maven-android SDK deployer](http://github.com/mosabua/maven-android-sdk-deployer).

    $ git clone https://github.com/mosabua/maven-android-sdk-deployer.git
    $ cd maven-android-sdk-deployer
    $ mvn install

(requires `ANDROID_HOME` to point to your SDK home)

This will install all JAR files from `$ANDROID_HOME/platforms` and `$ANDROID_HOME/add-ons` as Maven artifacts.

#### Step 3: Getting the source code

This is simple:

    $ git clone git://github.com/kaeppler/droid-fu.git

Alternatively, you can simply download the archived source code from the master branch [here](http://github.com/kaeppler/droid-fu/archives/master).

#### Step 4: Build and install the Droid-Fu JAR

To build and install the Droid-Fu JAR as a Maven artifact, run this from the directory where your checked out the sources:

    $ mvn install

This will build the JAR and place it under the `target` directory (and also install it as a Maven artifact).
To make your life easier, I included a switch which lets you deploy the JAR directly to your application's lib folder:

    $ mvn install -DcopyTo=/path/to/your/apps/lib/folder

This will additionally copy it to the given folder.

If you want a JavaDoc JAR to get inline docs in Eclipse, do this:

    $ mvn javadoc:jar

This will create a JavaDoc JAR under `target`.

## How do I use it?

1.  If your Android app is Maven managed, declare a dependency to `com.github.droidfu:droid-fu:1.0-SNAPSHOT`. If not, just put the JAR in your libs/ folder and add it to your application's classpath.

1.  If you haven't yet created an [Application](file:///home/matthias/devel/frameworks/android-sdk/docs/reference/android/app/Application.html) class for your app, create a new class and let it inherit from `com.github.droidfu.DroidFuApplication`. Otherwise, just alter your app class to include said inheritance relation.

1.  If you had to create a new app class in the previous step, modify your `AndroidManifest.xml` and change the `application` element so that its `android:name` attribute points to the name of the new class, e.g.:

        <?xml version="1.0" encoding="utf-8"?>
        <manifest xmlns:android="http://schemas.android.com/apk/res/android" ...>
            <application android:name="MyApplication" ...>
                ...
            </application>
            ...
        </manifest>

## Alternative source checkout install

If you prefer to avoid maven and/or binary jar files, you might want to try a different approach where the source code from github is checkout out as a submodule inside your own github project in a separate subdirectory. The advantage is you don't have to mess with additional software installation if you already have Eclipse (which seems required to do Android work anyway). The disadvantage is that the checkout process is slightly more complicated and opening the project in Eclipse doesn't compile straight out of the box because you have to tweak build paths to get it working.

Having said that, if you have a github project, these would be the steps to perform:

1. At the top of your project run:

        $ git submodule init
        $ git submodule add git://github.com/gradha/droid-fu.git external/droid-fu
        $ cd external/droid-fu
        $ git checkout source_install

1. The previous commands initialised your git repository to contain a `.gitmodules` file which is used to track the external git repositories. The `add` command created an external dependency to this git repository. The last checkout changed from the `master` to the `source_install` branch which contains some changes to make this type of integration work better.

1. If you run a `git status` command at the root you should see changes in the `.gitmodules` and `external/droid-fu` directory. You can add them and commit them. This will fix the external repository checkout to that version forever. If the external repository gets changed, you will need to run a `git pull` inside the module and commit the pull at the root of your project. These, and other related tips can be found on your git-submodule man page. Please check it out.

1. Placing the `droid-fu` subdirectory inside an `external` directory makes it more obvious that it is not part of the original tree, and also allows adding more external checkouts in case you need them.

1. While all this is fine for git, now you need to tell Eclipse to look for this new external directory in your project. Open Eclipse and open the properties of your project. In the "Java Build Path" section you should see that Eclipse contains by default a `project/gen` and `project/src` entries. Click on the "Add Folder" button and add the path `external/droid-fu/src/main/java`.

1. Done! Now you can `import com.github.droidfu.whatever` and it will work. If you are not using the `source_install` branch of the `gradha` fork, just change the commands to check out any other repo. Even if you don't plan to contribute code upstream, it is recommended that in your project you check out a fork under your control. If for some reason the upstream project was deleted you won't loose your fork (or so I've heard). It also makes more obvious updating the repo to a specific version.


## How is it licensed?

Droid-Fu is free and open source and may be used under the terms of the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0).

