# CS6250-Project-5-BGP-Hijacking-Attacks-solved

Email US: jarviscodinghub@gmail.com
https://jarviscodinghub.com/assignment/cs6250-project-5-bgp-hijacking-attacks-solved/

In this project, using an interactive Mininet demo [1], we will explore some of the vulnerabilities of
Border Gateway Protocol (BGP). In particular, we will see how BGP is vulnerable to abuse and
manipulation through a class of attacks called BGP hijacking attacks. A malicious Autonomous System
(AS) can mount these attacks through false BGP announcements from a rogue AS, causing victim ASes to
route their traffic bound for another AS through the malicious AS. This attack succeeds because the
false advertisement exploits BGP routing behavior by advertising a shorter path to reach a particular
prefix, which causes victim ASes to attempt to use the newly advertised (and seemingly better!) route.

Instructions
Part 1: Background reading, resources and example BGP router
configurations
A.Browse this paper as a reference for subsequent tasks and for some important background on
Prefix Hijack Attacks.
B.Refer to this resource on configuring a BGP router with Quagga.
C.Check out the following example configurations: Example 1 and Example 2
D.Project Intro Presentation Video Link and Slides from CS6250 in Spring 2019 (there Project 7)

Part 2: Interactive Demonstration using a Mininet Topology and simulated
prefixes/paths
The demo creates the network topology shown below, consisting of four ASes and their peering
relationships. AS4 is the malicious AS that will mount the attack. Once again, we will be simulating this
network in Mininet, however there are some important distinctions to make from our previous projects.

In this set up, each container is not a host, but an entire autonomous system. Each AS runs a routing
daemon (quagga), communicates with other ASes using BGP (bgpd), and configures its own isolated set
of routing entries in the kernel (zebra). Each AS has an IP address, which is the IP address of its border
router.

NOTE: In this topology solid lines indicate peering relationships and the dotted boxes indicate the
prefix advertised by that AS.

1. First, download and unzip the Project-5 files (modify permissions if necessary).
2. Next, in the Project-5 directory, start the demo using the following command:
o sudo python bgp.py
3. After loading the topology, the Mininet CLI should be visible. Keep this terminal open
throughout the experiment.

4. Start another terminal and navigate to the Project-5 directory. We will use this terminal to
start a remote session with AS1’s routing daemon:
o ./connect.sh
5. This script will start quagga, which will require access verification. The password is:
o en
6. Next, use the following commands to start the admin shell and view the routing table
entries for AS1:
o en
o You will be prompted for the password again, retype en
o sh ip bgp

7. You should see output very much like the screen grab below. In particular, notice that
AS1 has chosen the path via AS2 and AS3 to reach the prefix 13.0.0.0/8:
9.Next, let’s verify that network traffic is traversing this path. Open a third terminal and navigate to
the Project-5 directory. In this terminal you will start a script that continuously makes web requests
from a host within AS1 to a web server in AS3:
./website.sh

10.Leave this terminal running as well, and open a fourth terminal, also in the Project-5
directory. Now, we will start a rogue AS (AS4) that will connect directly to AS1 and advertise the
same 13.0.0.0/8 prefix. This will allow AS4 to hijack the prefix due to the shorter AS Path Length:
./start_rogue.sh

11.Return to the third terminal window and observe the continuous web requests. After the BGP
routing tables converge on this simple network, you should eventually see the attacker start
responding to requests from AS1, rather than AS3.

12.Additionally, return to the second terminal and rerun the command to print the routing table. You
may need to repeat the steps to establish the remote session if it closes due to inactivity. You
should now see the fraudulent advertisement for the 13.0.0.0/8 prefix in the routing table, in addition
to the longer unused path to the legitimate owner.

13.Finally, let’s stop the attack by switching to the fourth terminal and using the following command:
./stop_rogue.sh

14.You should notice a fairly quick re-convergence to the original legitimate route in the third
terminal window, which should now be delivering the original traffic. Additionally, you can check the
BGP routing table again to see the original path is being traversed.

Part 3: Creating a more complex topology and attack scenario
As demonstrated in Part 2, network virtualization can be very useful in demonstrating and analyzing
network attacks that would otherwise require a large amount of physical hardware to accomplish. In
Part 3, you are tasked with replicating a different topology and attack scenario to demonstrate the
effects of a different instance of a Prefix Hijack Attack.

1. To start, we recommend making a working copy of the code provided to you in the
Project-5 directory. You will likely find this project to be more approachable if you spend
time exploring the demo code and fully understanding how each part works rather than
immediately trying to edit the code.

2. Next, refer to the referenced paper in Part 1A, and locate Figure 1.

3. Edit the working copy of the demo code you just made to reconstruct the topology in Figure
1. When complete, you should be able to use the commands from Part 2 to explore the
routing tables generated by each border router. For our purposes, you can assume:

a. All links to be bidirectional peering links.
b. Each AS advertises a single prefix: AS1: 1.0.0.0/8, AS2: 2.0.0.0/8, AS3: 3.0.0.0/8,
AS4: 4.0.0.0/8, AS5: 5.0.0.0/8, AS6: 1.0.0.0/8 (Note: We highly recommend using
these prefix values in your configuration to simplify grading and for consistency in
communication and discussion in Piazza. However, you may use any valid prefix
values in your configuration.)
c. The number of hosts in each AS is the same as in the provided code.

4. Do not change passwords in zebra and conf files. If you change the passwords, the
auto-grader will fail resulting in 0 for the assignment.
——–

5. Next, locate Figure 2 in the referenced paper. Draw a topology map using any drawing tool of
your choice. You may hand-draw your topology with pencil and paper and scan or
photograph your drawing. All configuration values drawn on the map must be legible. Save
your topology diagram in PDF format with the name fig2_topo.pdf. You must use this
filename as part of your submission to receive credit for your diagram.

6. Continue to adapt the code in your working copy to simulate this hijack scenario. When
complete, you should be able to use the commands from Part 2 to start a Rogue AS and
demonstrate a similar change in routing table information as was shown in Part 2.

7. Finally, create a compressed file (zip format) named Part3.zip containing your entire attack
demonstration. You must include all of the files necessary to run your demo in an empty
directory – do NOT assume that we will provide any of the files necessary to run your
demonstration for grading purposes. Include your fig2_topomap.pdf file in your Part3.zip.

Part 3 Configuration Debugging Tips
• When viewing the BGP Tables note the “Status codes”. Give your topology enough
time to converge before recreating the hijack simulation portion. It may take a minute
or so for your topology to fully converge. You may continue to check the BGP Tables
to determine whether the topology has converged

• The order that you set up your peering links using addLink() matters. In previous
projects, we manually selected which port on the switch to use. There is an optional
parameter to the addLink() call which allows you to specify which switch port to use.
In this project, you will not use those options. Therefore, the order of the links
matters.

• Some of the commands in the boilerplate code may not be necessary to complete
Part 3. Some of it is there just so that you know it exists.
• Check for more descriptive errors in the /logs directory. See the zebra files for the
location of additional log files.

• Run “links” on the Mininet CLI terminal to see if all links are connected and OK OK.
• Run “net” on the Mininet CLI terminal to see if your ethernet links are connected as
you expect.
• Run “ifconfig -a” on all routers and hosts to ensure that all IP addresses are assigned
correctly.

• Run “sh ip bgp” and “sh ip bgp summary” on all routers.
• The command pingall may not work and that is fine.
• The website.sh may sometimes hang intermittently. If this happens restart the
simulation. We are aware of this issue, and we keep this in mind as we grade your
submission. You will not lose points if website.sh hangs so long as we are eventually
able to run the simulation.

• Watch the Intro presentation and read through the additional debugging tips on
the intro slides.

Part 4 (Optional Extra Credit) – Design and implement a countermeasure to
the attack from Part 3
This part of the project is optional, but it is worth extra credit if you complete it. Your task here is to
design and implement a countermeasure to the attack demonstrated in Part 3. We recommend you
start by creating a complete copy of the code you produced in Part 3, and paste it to a fresh working
directory.

Next, design and implement a countermeasure to the attack from Part 3. When complete,
you should be able to use the commands from Part 2 to launch the simulation, and start a Rogue AS
that mounts a Prefix Hijack attack as in Part 3. In this case, the attack should fail and you should be
able to observe the victim AS routing table maintain (or revert back to) it’s original state before the
attack commences.

The paper referenced in Part 1A describes some example countermeasures, and you can implement
/ modify them as required for this project. You are also free to explore other methods; this Part is
open ended. The first stipulation is that the solution you implement be applicable in the general
case, meaning it is not a hard-coded defense.

Your defense should work regardless of which AS is
attacked, which AS mounts the attack, and what prefix is targeted. The second is that the
countermeasure must be demonstrable on the course VM. It is permissible to use additional
libraries in the development of your countermeasure; however, they must be documented so the
grader can install them prior to grading your code.

As was done in Part 3, create a compressed file (zip format) named Part4.zip containing your
entire countermeasure demonstration. You must include all of the files necessary to run your demo
in an empty directory – do NOT assume that we will provide any of the files necessary to run your
demonstration for grading purposes. Additionally, you should provide a supplementary document
(PDF format) named Countermeasure.pdf.

This document should provide the following:
1. A brief summary of how your solution counters the attack
2. A list of files you modified from Part 3 or created in order to implement the countermeasure
3. A brief description of what is changed in each file, (or the purpose of newly created files)
including how it functions as a part of the larger system.

4. Instructions for demonstrating the countermeasure, including instructions for installing
required software / libraries.
5. A brief closing containing any additional information the grader may need to reproduce your
countermeasure and contact information (if different than your GT student email address) in
case the grade has questions.

What to Turn In
For this project you need to turn in the Part3.zip file you created in Part 3. Include your topology
diagram fig2_topo.pdf in Part3.zip

If you chose to pursue the extra credit, also turn in Part4.zip file and Countermeasure.pdf files you
created in Part 4. Please upload Part3.zip and Part4.zip and Countermeasure.pdf directly into
canvas, there is no need to zip these three files into another zip. So please make sure you submit
these three files on canvas directly.

What you can and cannot share
While discussion of the project in general is always permitted on Piazza, you are not permitted
to share your code generated for Part 3 or Part 4. You may quote snippets of the unmodified
skeleton code provided to you when discussing the Project. You may not share yout topology
diagram you created in Part 3 Step 5.

Rubric (out of 150 points)
5 pts Submission for turning in all the correct demo files with the correct names, and
significant effort has been made towards completing the project.
5 pts Fig 2 Topo

Diagram
For turning in the correctly named Topology diagram file: fig2_topo.pdf
with legible configuration values.
140 pts Attack

Demo
for accurately recreating the topology, links, router configuration, and attack
per the instructions. Partial credit is available for this rubric item.
50 pts Extra Credit For correctly designing and implementing a countermeasure to the attack
from Part 3. Submissions MUST include both the code and documentation –
extra credit will not be considered for code without accompanying
documentation.

Some partial credit may be provided for thorough
Countermeasures.pdf identifying a viable solution without accompanying
code or with non-working code if the documentation acknowledges the lack
of code or the failing code.
[1] This Project inspired by a Mininet Demo originally presented at SIGCOMM 2014.
