##StickyFingers Plugin

[![Build Status](https://travis-ci.org/cwru-robotics/cwru_stickyfingers.svg?branch=master)](https://travis-ci.org/cwru-robotics/cwru_stickyfingers)

**What Is StickyFingers?**

	StickyFingers is a ROS plugin meant to assist in grasping objects or representing a magnetic, suction, or adhesive gripping element. It makes one link in a Gazebo model “sticky”- when enabled by a ROS message, if that link comes into contact with an object that object will become “stuck” to the link and follow its position until the disable message is sent.
	
**Employing StickyFingers**

To add a StickyFingers plugin to a link in your robot, add the following material to the *model* definition containing the link you want to make sticky:
~~~~
<plugin name="_[NAME 1]_" filename="libsticky_fingers.so">
	<capacity>_[##]_</capacity>
	<link>_[LINK NAME]_</link>
</plugin>
~~~~
_`[NAME 1]`_ can be anything.
_`[##]`_ is the maximum mass the gripper should be able to lift- anything above this value, and any static objects, will not attach to the gripper.
_`[LINK NAME]`_ is the *fully qualified Gazebot name* of the link you want to make sticky. This can be found through the "models" subsection of the "world" tab in the Gazebo interface- select the link in question, and look at the "name" field.

An example of this structure can be found in `world/blocks_on_table.world`.

Note that in a URDF file, like any plugin definition you will need to include the stickyfingers plugin in *unnamed* Gazebo tags, in the main robot definition (and inside the macro, if one is used) outside of all of the links:
~~~~
<robot name="some_name" xmlns:xacro="http://www.ros.org/wiki/xacro">
	<xacro:macro name="something" params="something_else">
		<link name="some_link">
			... 
		</link>
		
		<gazebo>
			<plugin name="[NAME 1]" filename="libsticky_fingers.so">
				<capacity>[##]</capacity>
				<link>parent::other_parent::some_link</link>
			</plugin>
         	</gazebo>
         </xacro:macro>
</robot>
~~~~

**Controlling StickyFingers**


On startup, every StickyFingers link offers an action server that communicates with StickyControl messages:
~~~~
bool sticky_status
­­­---
bool new_status
~~~~
The messages are on topics named `sticky_finger/_[NAME 1]_`- for instance, if we had a finger named `ftip_sticky`, we would call the action server `sticky_finger/ftip_sticky`. The console will display the messages used by each sticky finger in the simulation whenever Gazebo starts up.

A `True` sticky_status will enable the finger, a `False` sticky_status will disable a finger and cause it to drop whatever it is holding.
A stand-alone executable to produce these messages is included as `finger_control_dummy_node`.
