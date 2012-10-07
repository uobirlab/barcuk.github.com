---
layout: default
title: Our Robots
---

This page gives a brief example of how to move a mobile robot in simulation using Java. It assumes you have installed Ubuntu and ROS (including rosjava) as described on the [getting started page](./startup.html). These instructions are derived in part from the [rosjava getting started guide](http://docs.rosjava.googlecode.com/hg/rosjava_core/html/getting_started.html), show you might want to read them too. The following assumes you are going to use Eclipse to write your code, and that you're familiar enough with Java to fill in the gaps where necessary. If one or both of these assumptions are invalid and you get stuck, don't be afraid to ask for help. 

## Writing the code

1. Create the package you're going to work in: `roscreate-pkg java_example`. 

1. Change directory into it: `roscd java_example`. If this doesn't work then you need to make sure the pacakge was created in your ROS_PACKAGE_PATH . One way to do this is described under the "Creating a sandbox directory for new packages" section on [Overlays](http://www.ros.org/wiki/fuerte/Installation/Overlays).

1. Remove the unnecesary files: `rm Makefile CMakeLists.txt`

1. Make the directory for storing the Java source: `mkdir -p src/main/java`

1. Paste the following file as `build.gradle` in the `java_example` directory:

{% highlight groovy %}
apply plugin: 'java'

// The Maven plugin is only required if your package is used as a library.
apply plugin: 'maven'

// The Application plugin and mainClassName attribute are only required if
// your package is used as a binary.
apply plugin: 'application'
mainClassName = 'org.ros.RosRun'

// uncomment to create an eclipse project using 'gradle eclipse'
apply plugin: 'eclipse'

sourceCompatibility = 1.6
targetCompatibility = 1.6

repositories {
  mavenLocal()
  maven {
    url 'http://robotbrains.hideho.org/nexus/content/groups/ros-public'
  }
}

version = '0.0.0-SNAPSHOT'
group = 'ros.my_stack'

dependencies {
  compile 'ros.rosjava_core:rosjava:0.0.0-SNAPSHOT'
}

{% endhighlight %}


1. As this example is going to use messages from other packages to communicate with the robot, add these as dependencies in `manifest.xml` before `</package>`. 

{% highlight xml %}
  <!-- LaserScan comes from here -->
  <depend package="sensor_msgs"/>
  <!-- Twist, used for sending commands to the robot base, comes from here -->
  <depend package="geometry_msgs"/>
{% endhighlight %}

1. Generate an Eclipse project by running `gradle eclipse`.

1. Open Eclipse them import the project via `File > Import > General > Existing Projects into Workspace`, selecting the `java_example` directory from the file select dialogue.

1. In this project create a new class called `barcuk.java_example.LaserDriver` that has `org.ros.node.AbstractNodeMain` as its superclass. 

1. Provide a default name for this node:

{% highlight java %}
	@Override
	public GraphName getDefaultNodeName() {
		return GraphName.of("laser_driver");
	}
{% endhighlight %}

1. Add the following code to control the robot. You will also need to add the necessary imports for the pasted code. If Eclipse can't find the message types (`LaserScan` and `Twist`) you should run `gradle build` in the `java_examples` directory then refresh your project in Eclipse. 

{% highlight java %}
	@Override
	public void onStart(ConnectedNode _node) {

		// Use a logger instead of the usual System.out.println to get send
		// output to ROS.
		//
		// This is final so we can use it in the anonymous inner class below
		final Log logger = _node.getLog();

		// Create an object used to publish Twist messages to the cmd_vel topic
		// in order to control the robot
		final Publisher<Twist> publisher = _node.newPublisher("cmd_vel", Twist._TYPE);

		// Subscribe to laser scans published on the base_scan topic
		Subscriber<LaserScan> subscriber = _node.newSubscriber("base_scan",
				LaserScan._TYPE);

		// Add a listener that is called each time a new laser scan is received.
		subscriber.addMessageListener(new MessageListener<LaserScan>() {
			@Override
			public void onNewMessage(LaserScan _scan) {
				float[] ranges = _scan.getRanges();
				float maxRange = Float.MIN_VALUE;
				for (int i = 0; i < ranges.length; i++) {
					if (ranges[i] > maxRange) {
						maxRange = ranges[i];
					}
				}
				logger.info("The longest range I found was: " + maxRange);
				logger.info("I hope this is less than: " + _scan.getRangeMax());
			
				//create a new Twist message
				Twist twist = publisher.newMessage();
				//set the desired forward velocity of the robot to 0.2 m/s.
				Vector3 linear = twist.getLinear();
				linear.setX(0.2);
				twist.setLinear(linear);
				//publish method to robot
				publisher.publish(twist);
			}

		});
	}
{% endhighlight %}	

1. Compile and install your code: `gradle installApp`
## Running the code

It's probably easiest to do this in three separate terminals.

1. Start the ROS master: `roscore`

1. Start the simulation: 
{% highlight bash %}
roscd stage
./bin/stageros world/willow-erratic.world
{% endhighlight %}	

1. Run your node:
{% highlight bash %}
roscd java_example
./build/install/java_example/bin/java_example barcuk.java_example.LaserDriver
{% endhighlight %}	
