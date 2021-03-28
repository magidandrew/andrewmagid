---
layout: post
title: How to use Processing 4 in external Java IDE (and why you might want to)
categories: Setup
excerpt: The errors with using Processing 3 on macOS, what worked, and what didn't
---

## Why To Use a Java IDE 
Beyond syntax completion, workspace customizability, and other common creature comforts of advanced IDEs, the Processing IDE traded functionality for simplicity which made writing "good code" so much more difficult. Sure, the **omission of access modifiers** like `public` or `private` no longer makes us concerned with OOP principles and let's us focus code actually is doing. However, this also makes complex programs so much more difficult to write. We **trade Java classes for `.pde` files** which can be unintuitively created by opening a "new tab" in the IDE. The lack of organization begs for **spaghetti code** as we continue to add methods and custom data structures and **buggy libraries** attempt to replace some of the fundamental libraries of Java (why do we need a Processing GUI library when we have swing?). Did I also mention it doesn't autosave your work? (something I don't wish upon anyone, even my nemesis).

The Processing library makes graphics in Java easy. Daniel Shiffman's [coding train](https://www.youtube.com/channel/UCvjgXvBlbQiydffZU7m1_aw) and the [comprehensive documentation](https://processing.org/reference/) make it approachable and effortless to pick up. But if the Processing IDE is going to translate all my code into Java source anyway, I might as well just do it myself.

## Processing 3 Issues on Mac
When trying to recreate Daniel Shiffman's [Perlin Noise Landscape Generator](https://thecodingtrain.com/CodingChallenges/011-perlinnoiseterrain.html), I noticed that Processing 3 would throw out the following error:

```
0   AppKit                              0x00007fff22da747f -[NSWindow(NSWindow_Theme) _postWindowNeedsToResetDragMarginsUnlessPostingDisabled] + 352
1   AppKit                              0x00007fff22d92121 -[NSWindow _initContent:styleMask:backing:defer:contentView:] + 1375
2   AppKit                              0x00007fff22d91bbb -[NSWindow initWithContentRect:styleMask:backing:defer:] + 42
3   libnativewindow_macosx.jnilib       0x000000012d30c3fe Java_jogamp_nativewindow_macosx_OSXUtil_CreateNSWindow0 + 398
4   ???                                 0x000000010efd9a88 0x0 + 4546468488   
```

I realized this would happen with sketches that use the `P3D` renderer. This is an issue fixed with Processing 4, so I had to upgrade to the new version.

# How To Use Processing 4 in Intellij on macOS

### Adding the Processing Library to Intellij
1. Download [Processing 4.app](https://processing.org/download/) (I used the 3.0 release)
1. Create an Intellij Project **using SDK 11**
1. Open Project Structure `command ⌘`+`;` and go to Modules
1. Click the `+` button and `Add Library`. 
1. Select the folder at `Processing4.app/Contents/Java/core/library`

### Starting A Project
Feel free to use the template below. Keep in mind that you have to `@Override` the `setup()`, `settings()`, and `draw()` methods.
```java
import processing.core.PApplet;

public class Main extends PApplet {

    public void settings(){
        size(600,600);
    }

    public void setup(){}
    public void draw(){}

    public static void main(String[] args) {
        PApplet.main("Main");
    }
}
```
### Walkthrough
![Walkthrough](/gifs/ProcessingIntellijSetup.gif)

## What Didn't Work/Tips
- Building Processing 4 from source. I could not get `ant` to build the repository, even though my `java_home` and `Java -version` both pointed to Java 11. If you have any ideas on a fix, please send me an email.

```
BUILD FAILED
/Users/andrewmagid/Documents/processing4/build/build.xml:65: Unsupported Java version: 15.0.2. To build, make sure that Java 11 is installed, and that JAVA_HOME points at the JDK that you want to use. For instance, on macOS, set it with:
export JAVA_HOME="`/usr/libexec/java_home -v 11`"

Total time: 0 seconds
```

- Processing 3 requires Java 8. Using SDK Java9 or higher, there are issues running anything at all. [StackOverflow Post](https://stackoverflow.com/questions/47343612/cannot-compile-when-importing-processing-library-into-eclipse/48292239#48292239)