---
layout: default
title: Quick Start
---

This tutorial will take you through the steps of up a virtualized
operating system for programming with ROS and writing a basic
controller which moves a wheeled robot about its environment.

## Set up Virtualbox ##

Install **Virtualbox** and the **Virtualbox extension pack**. You can
download it [here] [virtualbox].

Next, download the Ubuntu disk image. You can get it from
[Jeremiah's server] [vdi1] or from within the
[school of computer science] [vdi2]. The second will likely be much
quicker if you're in the school of computer science. The download is
~7gb, so be patient. Once that is finished, start Virtualbox and click
on the **new machine** icon.

![Create a new machine]({{root_url}}/images/tutorials/quick_start/vb_new_machine.png)

Now, you can click through the installer. I used the name "Ubuntu
10.10", but you may use whatever you want. You may have to select the
operating system manually if you use an alternative name.

When you get to the section titled "**Virtual Hard Disk**", select the
use existing hard disk radio button and select the previously
downloaded disk image.


![Choose disk image]({{root_url}}/images/tutorials/quick_start/vb_choose_vdi.png)

You're almost done now. Before you start the machine, click on the
settings button next to the new machine icon. There are some settings
worth changing. If you have the memory, you probably want to increase
the amount of memory. For running in simulation, you need to enable
the 3D acceleration of video. I usually bump the memory up to 20mb as well.

![Virtualbox settings]({{root_url}}/images/tutorials/quick_start/vb_settings.png)

Okay, now you're ready! Start up the virtual machine and you will be
greeted with a login screen. The password is **hacker**.

![Startup]({{root_url}}/images/tutorials/quick_start/vb_startup.png)

Onward!

## Download the BARC repository ##

We're going to download the repository into our ROS workspace. You can
find this at ``~/ros_workspace``. To verify that ROS will be able to find it,
let's check our ``ROS_PACKAGE_PATH``. This is list of places where ROS
will look for the programs we write. Open a terminal (CTRL-ALT-T
shortcut) and verify this:

```
hacker@barc:~$ echo $ROS_PACKAGE_PATH
/home/hacker/ros_workspace:/opt/ros/electric/stacks
```

And so there it is.

We can now move to this directory and checkout the repository. This
will ask you to verify the certificate. If you select permanently, be
careful about sharing your disk image with others, because your
password may be stored on the system.

```
hacker@barc:~$ cd ros
hacker@barc:~/ros$ svn checkout https://codex.cs.bham.ac.uk/svn/nah/robotclub/trunk barc
```

Now, let's test the system. Open another terminal and start the ROS
master server:

```
hacker@barc:~$ roscore
```

Back in your original terminal, you can make sure that the simulated
environment works by running:

```
hacker@barc:~/ros/barc$ roslaunch barc_stage pioneer_stage.launch
```

You should see the ground floor of the computer science building with
a robot in the center. Play around with the views, for example turning
on the data and going into perspective mode.

![Stage Simlator]({{root_url}}/images/tutorials/quick_start/stage.png)

Almost there!

## Set up rosjava ##

The version of rosjava in the Ubuntu repository is out of date and
broken, so it have to install it from their repository. First, you
have to remove the old version and then you can install the current
version.

```
hacker@barc:~$ sudo apt-get remove ros-electric-rosjava-core
hacker@barc:~$ cd ros/sources
hacker@barc:~/ros/sources$ hg clone https://rosjava.googlecode.com/hg/ rosjava_core
hacker@barc:~/ros/sources$ rospack profile && rosstack profile
hacker@barc:~/ros/sources$ rosmake rosjava
```

Okay, that's done now. Towards greatness!

## Writing your first piece of robot code ##
### Create the project ###

Now comes time for the real fun! You're going to write a simple
Braitenberg machine that avoids walls. It does this rather simply, by
splitting its laser scan in half and using each half to calculate the
excitation to the wheels.

```
  hacker@barc:~/ros/barc/barc_stage$ cd ~/ros
  hacker@barc:~/ros$ roscreate-pkg braitenberg rosjava std_msgs sensor_msgs geometry_msgs nav_msgs
```

Now you have to edit some files for `rosjava` to work correctly.

First, change into the new Braitenberg directory and then edit the
`Makefile`:

````
hacker@barc:~/ros$ cd braitenberg/
hacker@barc:~/ros/braitenberg$ gedit Makefile &
```

You have to replace the line in the `Makefile` with this snippet:

```include $(shell rospack find rosjava_bootstrap)/rosjava.mk```

Now, you have to add the ```build.xml``` file. You can create and open
the file by doing:

```
  hacker@barc:~/ros/braitenberg$ touch build.xml
  hacker@barc:~/ros/braitenberg$ gedit build.xml &
```

Now paste this into the file:

```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project name="." default="default">

    <property file="ros.properties" />
    <property name="build" location="build" />

    <property name="dist" location="dist" />
    <property name="build" location="build" />
    <property name="src" location="src" />

    <path id="classpath">
      <pathelement path="${ros.compile.classpath}" />
    </path>

    <echo message="${toString:classpath}" />

    <target name="default" depends="init, compile" />

    <target name="init">
      <fail unless="ros.compile.classpath" message="ros.properties is missing.  Please type 'rosmake' first "/>
      <mkdir dir="${build}" />
      <mkdir dir="${dist}" />
    </target>

    <target name="compile" depends="init">

      <javac destdir="${build}">
        <classpath refid="classpath" />
        <src path="${src}" />
      </javac>
    </target>

    <target name="clean">
      <delete dir="${build}" />
      <delete dir="${dist}" />
    </target>

    <!-- required entry point -->
    <target name="test" />

  </project>
```

Okay, now you can make the package. The reason you build the package
for a Java project is because `rosmake` needs to build the jars for
each message type you depend on, as listed in your `manifest.xml`
file. After the initial call to `rosmake`, you can just use `ant` to
build your project. This saves a lot of time.


```
    hacker@barc:~/ros/braitenberg$ rosmake
```

As part of the build process, `rosmake` generates Eclipse project
files and some other system specific files. When you start working
with the repository, you'll want to make sure you set the `svn:ignore`
properties to exclude these files from being submitted.

### Coding the Braitenberg machine ###

To get started, we first have to install Netbeans (or whatever editor
you prefer).

```
  hacker@barc:~/ros/braitenberg$ sudo apt-get install netbeans
```

Once that is installed, start Netbeans. Once Netbeans is started,
navigate to `File > Import project > Eclipse project`. Choose to
import the project ignoring the project dependencies. Make the input
and output folders the braitenberg package.

![]({{root_url}}/images/tutorials/quick_start/netbeans_import.png)

Create a package called `barc`. Inside the package, make a Java file
named `BraitnebergMover`. Now make the class implement
`NodeMain`. This is the interface that specifies node behavior and
create the empty implementations of the required methods. Your class
should look like this:

```java
  package barc;

  import org.ros.node.NodeConfiguration;
  import org.ros.node.NodeMain;

  public class BraitenbergMover implements NodeMain {

      public void main(NodeConfiguration nc) throws Exception {
          throw new UnsupportedOperationException("Not supported yet.");
      }

      public void shutdown() {
          throw new UnsupportedOperationException("Not supported yet.");
      }

  }
```

Now that you have the skeleton, it's time to flesh it out a
bit. First, we need some fields for the class. We need a field of type
`Node`, which will be the way this class communicates to the ROS
XML-RPC server. You also need to create a `Publisher` of type `Twist`
will which publish our movement messages to subscribing nodes. The
publisher will publish on the `cmd_vel` topic, which is listened to by
other noes which control the robot.

After the fields are initialized, you need to subscribe to messages
published to `base_scan` of type `LaseScan`. This is the laser data
that the robot will use to guide its movement. Inside the subscriber
method, make an anonymous class to call the callback method `move`.

Finally, you need to implement the `shutdown` method. This is simply
involves calling `node.shutdown()`. When you're done, the changed code
should like this:

```java
  private Node node;
  private Publisher pub;

  public void main(NodeConfiguration nc) throws Exception {
      node = new DefaultNodeFactory().newNode("braitenberg", nc);
      pub = node.newPublisher("cmd_vel", "geometry_msgs/Twist");

      node.newSubscriber("base_scan", "sensor_msgs/LaserScan", new MessageListener<LaserScan>() {
          @Override
          public void onNewMessage(LaserScan scan) {
              move(scan);
          }
      });
  }

  public void shutdown() {
      node.shutdown();
  }
```

The interesting part of the code is creating the `move` method. This
is where you get to tell the robot how to move.

This is a very basic way to move the robot. Essentially, each half of
the lase scan is summed into its own variable. Then the difference
between these two halves is used to calculate the rotation in the
z-axis. Also, a slight forward motion is always applied just to keep
the robot going along. The method looks like this:

```java
  private void move(LaserScan scan) {
          double left = 0.0, right = 0.0;
  
          // get space on each side
          for (int i = 0; i < scan.ranges.length; i++) {
              double range = (scan.ranges[i] == 0.0) ? scan.range_max : scan.ranges[i];
              if (i < scan.ranges.length / 2) {
                  left += range;
              } else {
                  right += range;
              }
          }
  
          // publish a twist command to move towards the area of greatest space
          Twist move = new Twist();
          move.linear.x = 0.1;
          move.angular.z = (right - left) / (scan.ranges.length) * 0.5;
          pub.publish(move);
  }
```

So, in the end, your code should look like this:


```java
  package barc;
  
  import org.ros.internal.node.DefaultNode;
  import org.ros.message.MessageListener;
  import org.ros.message.geometry_msgs.Twist;
  import org.ros.message.sensor_msgs.LaserScan;
  import org.ros.node.DefaultNodeFactory;
  import org.ros.node.Node;
  import org.ros.node.NodeConfiguration;
  import org.ros.node.NodeMain;
  import org.ros.node.topic.Publisher;
  
  public class BraitenbergMover implements NodeMain {
  
      private Node node;
      private Publisher<Twist> pub;
  
      public void main(NodeConfiguration nc) throws Exception {
          node = new DefaultNodeFactory().newNode("braitenberg", nc);
          pub = node.newPublisher("cmd_vel", "geometry_msgs/Twist");
  
          node.newSubscriber("base_scan", "sensor_msgs/LaserScan", new MessageListener<LaserScan>() {
  
                  @Override
                      public void onNewMessage(LaserScan scan) {
                      move(scan);
                  }
              });
      }
  
      private void move(LaserScan scan) {
          double left = 0.0, right = 0.0;
  
          // get space on each side
          for (int i = 0; i < scan.ranges.length; i++) {
              double range = (scan.ranges[i] == 0.0) ? scan.range_max : scan.ranges[i];
              if (i < scan.ranges.length / 2) {
                  left += range;
              } else {
                  right += range;
              }
          }
  
          // publish a twist command to move towards the area of greatest space
          Twist move = new Twist();
          move.linear.x = 0.1;
          move.angular.z = (right - left) / (scan.ranges.length) * 0.5;
          pub.publish(move);
          System.out.println("Published: " + move);
      }
  
      public void shutdown() {
          node.shutdown();
      }
  }
```

### Running it ###

To get your code running, you will need to start `roscore` if it's not
already running and then start the simulator, both in separate
terminals. Then, once that is done, you can start your program.

```sh
  hacker@barc:~$ roscore
  hacker@barc:~$ roslaunch barc_stage pioneer_stage.launch 
  hacker@barc:~/ros/barc/barc_stage$ roscd braitenberg/
  hacker@barc:~/ros/braitenberg$ ant
  hacker@barc:~/ros/braitenberg$ rosrun rosjava_bootstrap run.py braitenberg barc.BraitenbergMover
```

And with any luck, you should not see your robot moving about its
environment and avoiding obstacles. You can experiment to find better
ways to move!

If you have any trouble that you cannot resolve yourself (i.e., Google), please post
to the mailing list! There are a number of us there who can help.


[virtualbox]: https://www.virtualbox.org/wiki/Downloads "Virtualbox"
[vdi1]: http://jeremiahvia.com/barc/Ubuntu%2010.10.vdi
[vdi2]: http://www.cs.bham.ac.uk/research/projects/poplog/robot-club/Ubuntu-10.10.vdi
