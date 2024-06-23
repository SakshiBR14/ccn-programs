# ccn-programs
1) ns2 programs    
Implement an Ethernet LAN using n nodes and set multiple traffic nodes   
and  plot congestion window for different source /destination.  
   
set ns [new Simulator]     
set tf [open lab3.tr w]      
$ns trace-all $tf     
set nf [open lab3.nam w]     
$ns namtrace-all $nf      

set n0 [$ns node]      
$n0 color "magenta"   
$n0 label "src1"   
set n1 [$ns node]   
$n1 color "red"     
set n2 [$ns node]   
$n2 color "magenta"   
$n2 label "src2"  
set n3 [$ns node]  
$n3 color "blue"  
$n3 label "dest2"  
set n4 [$ns node]    
$n4 shape square  
set n5 [$ns node]    
$n5 color "blue"    
$n5 label "dest1"    

$ns make-lan "$n0 $n1 $n2 $n3 $n4" 50Mb 100ms LL Queue/DropTail   
Mac/802_3    

$ns duplex-link $n4 $n5 1Mb 1ms DropTail   

$ns duplex-link-op $n4 $n5 orient right   

set tcp0 [new Agent/TCP]   
$ns attach-agent $n0 $tcp0   

set ftp0 [new Application/FTP]   
$ftp0 attach-agent $tcp0   
$ftp0 set packetSize_ 500   
$ftp0 set interval_ 0.0001  

set sink0 [new Agent/TCPSink]   
$ns attach-agent $n5 $sink0   

$ns connect $tcp0 $sink0

set tcp1 [new Agent/TCP]
$ns attach-agent $n2 $tcp1

set ftp1 [new Application/FTP]
$ftp1 attach-agent $tcp1   
$ftp1 set packetSize_ 600   
$ftp1 set interval_ 0.001   

set sink1 [new Agent/TCPSink] $ns   
attach-agent $n3 $sink1   
  
"sink1" $ns connect $tcp1 $sink1   
set file1 [open file1.tr w]    
$tcp0 attach $file1   
set file2 [open file2.tr w]   
$tcp1 attach $file2   
$tcp0 trace cwnd_   
$tcp1 trace cwnd_   

proc finish { } {   
global ns nf tf   
$ns flush-trace   
close $tf   
close $nf   
exec nam lab3.nam &   
exit 0   
}   
  
$ns at 0.1 "$ftp0 start" $ns  
at 5 "$ftp0 stop"  
$ns at 7 "$ftp0 start"   
$ns at 0.2 "$ftp1 start"   
$ns at 8 "$ftp1 stop"   
$ns at 14 "$ftp0 stop"   
$ns at 10 "$ftp1 start"   
$ns at 15 "$ftp1 stop"   
   
$ns at 16 "finish"  
$ns run   
AWK:   
BEGIN {   
}    
{    
if($6=="cwnd_")    
printf("%f\t%f\t\n",$1,$7);   
}   
END {   
} 
DV:
set ns [new Simulator]
$ns rtproto DV
$ns color 1 Blue
set nf [open sample.nam w]
$ns namtrace-all $nf
set tr [open sample.tr w]
$ns trace-all $tr
proc finish {} {
global ns nf tr
$ns flush-trace
close $nf
close $tr
exec nam sample.nam &
exit 0
}
set n0 [$ns node]
set n1 [$ns node]
set n2 [$ns node]
set n3 [$ns node]
$ns duplex-link $n0 $n1 10Mb 10ms DropTail
$ns duplex-link $n1 $n2 10Mb 10ms DropTail
$ns duplex-link $n2 $n3 10Mb 10ms DropTail
$ns duplex-link $n3 $n0 10Mb 10ms DropTail
$ns duplex-link $n0 $n2 10Mb 10ms DropTail
$ns duplex-link $n1 $n3 10Mb 10ms DropTail
#$ns duplex-link-op $n0 $n1 orient Right
#$ns duplex-link-op $n0 $n1 orient down
#$ns duplex-link-op $n0 $n1 orient left
#$ns duplex-link-op $n0 $n1 orient up
#$ns duplex-link-op $n0 $n1 orient right-down
#$ns duplex-link-op $n0 $n1 orient left-down
$ns cost $n0 $n1 1
$ns cost $n1 $n2 4
$ns cost $n2 $n3 1
$ns cost $n3 $n0 1
$ns cost $n0 $n2 2
$ns cost $n1 $n3 1
set tcp [new Agent/TCP]
$tcp set class_ 1
$ns attach-agent $n1 $tcp
set sink [new Agent/TCPSink]
$ns attach-agent $n2 $sink
$ns connect $tcp $sink
$tcp set fid_ 1
set ftp [new Application/FTP]
$ftp attach-agent $tcp
$ftp set type_ FTP
$ns at 1.0 "$ftp start"
$ns rtmodel-at 3.0 down $n1 $n2
$ns rtmodel-at 5.0 up $n1 $n2
$ns at 10 "finish"
$ns run
-----------------------------------------------------------------------------------------------------------------
Link state routing:
#Create a simulator object
set ns [new Simulator]
$ns rtproto LS
#Define different colors for data flows (for NAM)
$ns color 1 Blue
#Open the NAM trace file
set nf [open sample.nam w]
$ns namtrace-all $nf
set tr [open sample.tr w]
$ns trace-all $tr
#Define a 'finish' procedure
proc finish {} {
global ns nf tr
$ns flush-trace
#Close the NAM trace file
close $nf
close $tr
#Execute NAM on the trace file
exec nam sample.nam &
exit 0
}
#Create four nodes
set n0 [$ns node]
set n1 [$ns node]
set n2 [$ns node]
set n3 [$ns node]
#Create links between the nodes
$ns duplex-link $n0 $n1 10Mb 10ms DropTail
$ns duplex-link $n1 $n2 10Mb 10ms DropTail
$ns duplex-link $n2 $n3 10Mb 10ms DropTail
$ns duplex-link $n3 $n0 10Mb 10ms DropTail
$ns duplex-link $n0 $n2 10Mb 10ms DropTail
#$ns duplex-link $n1 $n3 10Mb 10ms DropTail
#Give node position (for NAM)
#$ns duplex-link-op $n0 $n1 orient right
#$ns duplex-link-op $n1 $n2 orient down
#$ns duplex-link-op $n2 $n3 orient left
#$ns duplex-link-op $n3 $n0 orient up
#$ns duplex-link-op $n0 $n2 orient right-down
#$ns duplex-link-op $n1 $n3 orient left-down
#Setup a TCP connection
set tcp [new Agent/TCP]
$tcp set class_ 1
$ns attach-agent $n1 $tcp
set sink [new Agent/TCPSink]
$ns attach-agent $n2 $sink
$ns connect $tcp $sink
$tcp set fid_ 1
#Setup a FTP over TCP connection
set ftp [new Application/FTP]
$ftp attach-agent $tcp
$ftp set type_ FTP
#Schedule events for the CBR and FTP agents
$ns at 1.0 "$ftp start"
$ns rtmodel-at 3.0 down $n1 $n2
$ns rtmodel-at 5.0 up $n1 $n2
#Call the finish procedure after 5 seconds of simulation time
$ns at 10 "finish"
#Run the simulation
$ns run
-------------------------------------------------------------------------------------------------
NODE POINT TO POINT
#Create a simulator object
set ns [ new Simulator ]
#Open the nam trace file
set tf [ open lab1.tr w ]
$ns trace-all $tf
#Open the nam trace file
set nf [ open lab1.nam w ]
$ns namtrace-all $nf
#Define a 'finish' procedure
proc finish { } {
global ns nf tf
$ns flush-trace
exec nam lab1.nam &
close $tf
close $nf
exit 0
}
#Creating nodes
set n0 [$ns node]
set n1 [$ns node]
set n2 [$ns node]
set n3 [$ns node]
#Define different colors and labels for data flows
$ns color 1 "red"
$ns color 2 "blue"
$n0 label "Source/udp0"
$n1 label "Source/udp1"
$n2 label "Router"
$n3 label "Destination/Null"
#Create link between nodes
$ns duplex-link $n0 $n2 100Mb 300ms DropTail
$ns duplex-link $n1 $n2 100Mb 300ms DropTail
$ns duplex-link $n2 $n3 1Mb 300ms DropTail
#Set queue size of links
$ns set queue-limit $n0 $n2 50
$ns set queue-limit $n1 $n2 50
$ns set queue-limit $n2 $n3 5
#Setup a UDP connection
set udp0 [new Agent/UDP]
$ns attach-agent $n0 $udp0
# Create a CBR traffic source and attach it to udp0
set cbr0 [new Application/Traffic/CBR]
$cbr0 set packetSize_ 500
$cbr0 set interval_ 0.005
$cbr0 attach-agent $udp0
#Create a UDP agent and attach it to node n1
set udp1 [new Agent/UDP]
$udp1 set class_ 2
$ns attach-agent $n1 $udp1
# Create a CBR traffic source and attach it to udp1
set cbr1 [new Application/Traffic/CBR]
$cbr1 set packetSize_ 500
$cbr1 set interval_ 0.005
$cbr1 attach-agent $udp1
#Create a Null agent (a traffic sink) and attach it to node n3
set null0 [new Agent/Null]
$ns attach-agent $n3 $null0
#Connect the traffic sources with the traffic sink
$ns connect $udp0 $null0
$ns connect $udp1 $null0
#Schedule events for the CBR agents
$ns at 0.5 "$cbr0 start"
$ns at 1.0 "$cbr1 start"
$ns at 4.0 "$cbr1 stop"
$ns at 4.5 "$cbr0 stop"
#Call the finish procedure after 5 seconds of simulation time
$ns at 5.0 "finish"
#Run the simulation
$ns run
#AWK:
BEGIN{
count=0;
}
-----------------------------------------------------------------------------------------------------
TRANSMISSION PING
set ns [new Simulator]
set tf [open lab2.tr w]
$ns trace-all $tf
set nf [open lab2.nam w]
$ns namtrace-all $nf
set n0 [$ns node]
set n1 [$ns node]
set n2 [$ns node]
set n3 [$ns node]
set n4 [$ns node]
set n5 [$ns node]
set n6 [$ns node]
$n0 label "Ping0"
$n4 label "Ping4"
$n5 label "Ping5"
$n6 label "Ping6"
$n2 label "Router"
$ns color 1 "red"
$ns color 2 "green"
$ns duplex-link $n0 $n2 100Mb 300ms DropTail
$ns duplex-link $n1 $n2 1Mb 300ms DropTail
$ns duplex-link $n3 $n2 1Mb 300ms DropTail
$ns duplex-link $n5 $n2 100Mb 300ms DropTail
$ns duplex-link $n2 $n4 1Mb 300ms DropTail
$ns duplex-link $n2 $n6 1Mb 300ms DropTail
$ns queue-limit $n0 $n2 5
$ns queue-limit $n2 $n4 3
$ns queue-limit $n2 $n6 2
$ns queue-limit $n5 $n2 5
#The below code is used to connect between the ping agents to the node n0,
#n4 , n5 and n6.
set ping0 [new Agent/Ping]
$ns attach-agent $n0 $ping0
set ping4 [new Agent/Ping]
$ns attach-agent $n4 $ping4
set ping5 [new Agent/Ping]
$ns attach-agent $n5 $ping5
set ping6 [new Agent/Ping]
$ns attach-agent $n6 $ping6
$ping0 set packetSize_ 50000
$ping0 set interval_ 0.0001
$ping5 set packetSize_ 60000
$ping5 set interval_ 0.00001
$ping0 set class_ 1
$ping5 set class_ 2
$ns connect $ping0 $ping4
$ns connect $ping5 $ping6
#Define a 'recv' function for the class 'Agent/Ping'
#The below function is executed when the ping agent receives a reply from the destination
Agent/Ping instproc recv {from rtt} {
$self instvar node_
puts " The node [$node_ id] received an reply from $from with round trip
time of $rtt"
}
proc finish {} {
global ns nf tf
exec nam lab2.nam &
$ns flush-trace
close $tf
close $nf
exit 0
}
#Schedule events
$ns at 0.1 "$ping0 send"
$ns at 0.2 "$ping0 send"
$ns at 0.3 "$ping0 send"
$ns at 0.4 "$ping0 send"
$ns at 0.5 "$ping0 send"
$ns at 0.6 "$ping0 send"
$ns at 0.7 "$ping0 send"
$ns at 0.8 "$ping0 send"
$ns at 0.9 "$ping0 send"
$ns at 1.0 "$ping0 send"
$ns at 1.1 "$ping0 send"
$ns at 1.2 "$ping0 send"
$ns at 1.3 "$ping0 send"
$ns at 1.4 "$ping0 send"
$ns at 1.5 "$ping0 send"
$ns at 1.6 "$ping0 send"
$ns at 1.7 "$ping0 send"
$ns at 1.8 "$ping0 send"
$ns at 0.1 "$ping5 send"
$ns at 0.2 "$ping5 send"
$ns at 0.3 "$ping5 send"
$ns at 0.4 "$ping5 send"
$ns at 0.5 "$ping5 send"
$ns at 0.6 "$ping5 send"
$ns at 0.7 "$ping5 send"
$ns at 0.8 "$ping5 send"
$ns at 0.9 "$ping5 send"
$ns at 1.0 "$ping5 send"
$ns at 1.1 "$ping5 send"
$ns at 1.2 "$ping5 send"
$ns at 1.3 "$ping5 send"
$ns at 1.4 "$ping5 send"
$ns at 1.5 "$ping5 send"
$ns at 1.6 "$ping5 send"
$ns at 1.7 "$ping5 send"
$ns at 1.8 "$ping5 send"
$ns at 5.0 "finish"
$ns run
#AWK:
BEGIN{
count=0;
}
{
if($1=="d")
count++;
}
END{
printf("The Total no of Packets Drop is :%d\n\n",
count); }
----------------------------------------------------------------------------------------------------------
ETHERNET LAN
#Make a NS simulator
set ns [new Simulator]
set tf [open lab3.tr w]
$ns trace-all $tf
set nf [open lab3.nam w]
$ns namtrace-all $nf
# Create the nodes,color and label
set n0 [$ns node]
$n0 color "magenta"
$n0 label "src1"
set n1 [$ns node]
$n1 color "red"
set n2 [$ns node]
$n2 color "magenta"
$n2 label "src2"
set n3 [$ns node]
$n3 color "blue"
$n3 label "dest2"
set n4 [$ns node]
$n4 shape square
set n5 [$ns node]
$n5 color "blue"
$n5 label "dest1"
#Creates a lan from a set of nodes given by <nodelist>. Bandwidth, delay
#characteristics along with the link-layer, Interface queue, Mac layer and
#channel type for the lan also needs to be defined.
$ns make-lan "$n0 $n1 $n2 $n3 $n4" 50Mb 100ms LL Queue/DropTail Mac/802_3
# Create the link
$ns duplex-link $n4 $n5 1Mb 1ms DropTail
# Create the node position
$ns duplex-link-op $n4 $n5 orient right
# Add a TCP sending module to node n0
set tcp0 [new Agent/TCP]
$ns attach-agent $n0 $tcp0
# Setup a FTP traffic generator on "tcp0"
set ftp0 [new Application/FTP]
$ftp0 attach-agent $tcp0
$ftp0 set packetSize_ 500
$ftp0 set interval_ 0.0001
# Add a TCP receiving module to node n5
set sink0 [new Agent/TCPSink]
$ns attach-agent $n5 $sink0
# Direct traffic from "tcp0" to "sink1"
$ns connect $tcp0 $sink0
# Add a TCP sending module to node n2
set tcp1 [new Agent/TCP]
$ns attach-agent $n2 $tcp1
# Setup a FTP traffic generator on "tcp1"
set ftp1 [new Application/FTP]
$ftp1 attach-agent $tcp1
$ftp1 set packetSize_ 600
$ftp1 set interval_ 0.001
# Add a TCP receiving module to node n3
set sink1 [new Agent/TCPSink]
$ns attach-agent $n3 $sink1
# Direct traffic from "tcp1" to "sink1"
$ns connect $tcp1 $sink1
set file1 [open file1.tr w]
$tcp0 attach $file1
set file2 [open file2.tr w]
$tcp1 attach $file2
$tcp0 trace cwnd_
$tcp1 trace cwnd_
# Define a 'finish' procedure
proc finish { } {
global ns nf tf
$ns flush-trace
close $tf
close $nf
exec nam lab3.nam &
exit 0
}
# Schedule start/stop times
$ns at 0.1 "$ftp0 start"
$ns at 5 "$ftp0 stop"
$ns at 7 "$ftp0 start"
$ns at 0.2 "$ftp1 start"
$ns at 8 "$ftp1 stop"
$ns at 14 "$ftp0 stop"
$ns at 10 "$ftp1 start"
$ns at 15 "$ftp1 stop"
# Set simulation end time
$ns at 16 "finish"
$ns run
#AWK:
BEGIN {
}
{
if($6=="cwnd_")
printf("%f\t%f\t\n",$1,$7);
}
END {
}
--------------------------------------------------------------------------------------------------------
AODV
# A 100-node example for ad-hoc simulation with AODV
# Define options
set val(chan) Channel/WirelessChannel ;# channel type
set val(prop) Propagation/TwoRayGround ;# radio-propagation model
set val(netif) Phy/WirelessPhy ;# network interface type
set val(mac) Mac/802_11 ;# MAC type
set val(ifq) Queue/DropTail/PriQueue ;# interface queue type
set val(ll) LL ;# link layer type
set val(ant) Antenna/OmniAntenna ;# antenna model
set val(ifqlen) 50 ;# max packet in ifq
set val(nn) 12 ;# number of mobilenodes
set val(rp) AODV ;# routing protocol
set val(x) 500 ;# X dimension of topography
set val(y) 400 ;# Y dimension of topography
set val(stop) 150 ;# time of simulation end
set ns [new Simulator]
set tracefd [open testAODV.tr w]
set windowVsTime2 [open win.tr w]
set namtrace [open testAODV.nam w]
$ns trace-all $tracefd
$ns namtrace-all-wireless $namtrace $val(x) $val(y)
# set up topography object
set topo [new Topography]
$topo load_flatgrid $val(x) $val(y)
create-god $val(nn)
#
# Create nn mobilenodes [$val(nn)] and attach them to the channel.
#
# configure the nodes
$ns node-config -adhocRouting $val(rp) \
-llType $val(ll) \
-macType $val(mac) \
-ifqType $val(ifq) \
-ifqLen $val(ifqlen) \
-antType $val(ant) \
-propType $val(prop) \
-phyType $val(netif) \
-channelType $val(chan) \
-topoInstance $topo \
-agentTrace ON \
-routerTrace ON \
-macTrace OFF \
-movementTrace ON
for {set i 0} {$i < $val(nn) } { incr i } {
set node_($i) [$ns node]
$node_($i) set X_ [ expr 10+round(rand()*480) ]
$node_($i) set Y_ [ expr 10+round(rand()*380) ]
$node_($i) set Z_ 0.0
}
for {set i 0} {$i < $val(nn) } { incr i } {
$ns at [ expr 15+round(rand()*60) ] "$node_($i) setdest [ expr 10+round(rand()*480) ] [ expr
10+round(rand()*380) ] [ expr 2+round(rand()*15) ]"
}
# Generation of movements
$ns at 10.0 "$node_(0) setdest 250.0 250.0 3.0"
$ns at 15.0 "$node_(1) setdest 45.0 285.0 5.0"
$ns at 70.0 "$node_(2) setdest 480.0 300.0 5.0"
$ns at 20.0 "$node_(3) setdest 200.0 200.0 5.0"
$ns at 25.0 "$node_(4) setdest 50.0 50.0 10.0"
$ns at 60.0 "$node_(5) setdest 150.0 70.0 2.0"
$ns at 90.0 "$node_(6) setdest 380.0 150.0 8.0"
$ns at 42.0 "$node_(7) setdest 200.0 100.0 15.0"
$ns at 55.0 "$node_(8) setdest 50.0 275.0 5.0"
$ns at 19.0 "$node_(9) setdest 250.0 250.0 7.0"
$ns at 90.0 "$node_(10) setdest 150.0 150.0 20.0"
# Set a TCP connection between node_(2) and node_(8)
set tcp [new Agent/TCP/Newreno]
$tcp set class_ 2
set sink [new Agent/TCPSink]
$ns attach-agent $node_(2) $tcp
$ns attach-agent $node_(8) $sink
$ns connect $tcp $sink
set ftp [new Application/FTP]
$ftp attach-agent $tcp
$ns at 10.0 "$ftp start"
set tcp [new Agent/TCP/Newreno]
$tcp set class_ 2
set sink [new Agent/TCPSink]
$ns attach-agent $node_(5) $tcp
$ns attach-agent $node_(0) $sink
$ns connect $tcp $sink
set ftp [new Application/FTP]
$ftp attach-agent $tcp
$ns at 10.0 "$ftp start"
# Printing the window size
proc plotWindow {tcpSource file} {
global ns
set time 0.01
set now [$ns now]
set cwnd [$tcpSource set cwnd_]
puts $file "$now $cwnd"
$ns at [expr $now+$time] "plotWindow $tcpSource $file" }
$ns at 10.1 "plotWindow $tcp $windowVsTime2"
# Define node initial position in nam
for {set i 0} {$i < $val(nn)} { incr i } {
# 30 defines the node size for nam
$ns initial_node_pos $node_($i) 30
}
# Telling nodes when the simulation ends
for {set i 0} {$i < $val(nn) } { incr i } {
$ns at $val(stop) "$node_($i) reset";
}
# ending nam and the simulation
$ns at $val(stop) "$ns nam-end-wireless $val(stop)"
$ns at $val(stop) "stop"
$ns at 150 "puts \"end simulation\" ; $ns halt"
proc stop {} {
global ns tracefd namtrace
$ns flush-trace
close $tracefd
close $namtrace
}
$ns run
------------------------------------------------------------------------------------------
SLIDING WINDOW
#include<stdio.h>
#include<dos.h>
#include<stdlib.h>
int n,r;
struct frame
{
char ack;
int data;
}
frm[10];
int sender(void);
void recvack(void);
void resend_sr(void);
void resend_gb(void);
void goback(void);
void selective(void);
int main ()
{
int c;
do
{
printf("\n\n 1. selective repeat ARQ\n 2. goback arq\n 3.exit from he option\n");
printf("\n enter your choice");
scanf("%d",&c);
switch(c)
{
case 1: selective();
break;
case 2: goback();
break;
case 3:exit(0);
break;
}
}
while(c>=4);
return 0;
}
void goback()
{
sender();
recvack();
resend_gb();
printf("\n all frames sent successfully\n");
getch();
}
void selective()
{
sender();
recvack();
resend_sr();
printf("\n all frames sent successfully\n");
}
int sender()
{
int i;
printf("\n enter the number of frames to be sent");
scanf("%d",&n);
for(i=1;i<=n;i++)
{
printf("\n enter the data of frames[%d]",i);
scanf("%d",&frm[i].data);
frm[i].ack='y';
}
return 0;
}
void recvack()
{
int i;
rand();
r=rand()%n;
frm[r].ack='n';
for(i=1;i<=n;i++);
{
if (frm[i].ack=='n')
printf("\n the frame number %d is not received \n,r");
}
}
void resend_sr()
{
printf("\n resending frame %d",r);
sleep(2);
frm[r].ack='y';
printf("\n the received frame is %d", frm[r].data);
}
void resend_gb()
{
int i;
printf("\n resending fro.frame %d",r);
for(i=1;i<=n;i++);
{
sleep(2);
frm[i].ack='y';
printf("\n received data of frame %d is %d",i ,frm[i].data);
}
}
---------------------------------------------------------------------------------------------------
crc
#include<stdio.h>
#include<string.h>
#include<conio.h>
int main()
{
int i,j,kl,ml,f=0;
char input[100],k[30],t[30],q[100],r[30],k1[30];
printf("\n Enter Data :");
scanf("%s",input);
printf("\n Enter key :");
scanf("%s",k);
kl=strlen(k);
ml=strlen(input);
strcpy(k1,k);
for(i=0;i<kl-1;i++)
input[ml+i]='0';
for(i=0;i<kl;i++)
t[i]=input[i];
for(i=0;i<ml;i++)
{
q[i]=t[0];
if(q[i]=='0')
{
for(j=0;j<kl;j++)
k[j]='0';
}
else
for(j=0;j<kl;j++)
k[j]=k1[j];
for(j=kl-1;j>0;j--)
{
if(t[j]==k[j])
r[j-1]='0';
else
r[j-1]='1';
}
r[kl-1]=input[i+kl];
strcpy(t,r);
}
strcpy(r,t);
printf("\n quotient is");
for(i=0;i<ml;i++)
printf("%c",q[i]);
printf("\n remainder is");
for(i=0;i<kl-1;i++)
printf("%c",r[i]);
printf("\n final data is :");
for(i=0;i<ml;i++)
printf("%c",input[i]);
for(i=0;i<kl-1;i++)
printf("%c",r[i]);
printf("\n");
char t1[20];
printf("enter received data : ");
scanf("%s",t1);
for(i=0;i<kl;i++)
t[i]=t1[i];
for(i=0;i<ml;i++)
{
q[i]=t[0];
if(q[i]=='0')
for(j=0;j<kl;j++)
k[j]='0';
else
for(j=0;j<kl;j++)
k[j]=k1[j];
for(j=kl-1;j>0;j--)
{
if(t[j]==k[j])
r[j-1]='0';
else
r[j-1]='1';
}
r[kl-1]=t1[i+kl];
strcpy(t,r);
}
strcpy(r,t);
printf("\n quotient is : ");
for(i=0;i<ml;i++)
printf("%c",q[i]);
printf("\n remainder is :");
for(i=0;i<kl-1;i++)
printf("%c",r[i]);
f=0;
for(i=0;i<kl-1;i++)
{
if(r[i]=='1')
{
f=1;
break;
}
else
f=0;
}
if(f==0)
printf("\n no error");
else
printf("\n error is detected");
getch();
return 0;
}
----------------------------------------------------------------------------------
HAMMING
#include <stdio.h>
#include <stdlib.h>
#include<string.h>
int main()
{
int data[10],rec[10],i,c,c1,c2,c3;
printf("Enter 4 bits of data one by one \n");
scanf("%d%d%d%d",&data[3],&data[5],&data[6],&data[7]);
data[1]=data[3]^data[5]^data[7];
printf("parity bit at p1,%d\n", data[1]);
data[2]=data[3]^data[5]^data[7];
printf("parity bit at p2,%d\n", data[2]);
data[4]=data[5]^data[6]^data[7];
printf("parity bit at p4,%d\n", data[4]);
printf("\n Encoded data is \n");
for (i=7; i>0; i--)
printf("%d", data[i]);
printf("\n");
printf("Enter the received data bits one by one :");
for(i=0;i<7;i++)
scanf("%d",&rec[i]);
c1=rec[6]^rec[4]^rec[2]^rec[0];
c2=rec[5]^rec[4]^rec[2]^rec[0];
c3=rec[3]^rec[2]^rec[1]^rec[0];
c=c3*4+c2*2+c1;
if(c==0)
printf("No error while transmission of data \n");
else
{
printf("error on position %d",c);
printf("\n data received: ");
for (i=0;i<7;i++)
printf("%d", rec[i]);
printf("correct message is : ");
if (rec[7-c]==0)
rec[7-c]=1;
else
rec[7-c]=0;
for (i=0;i<7;i++)
printf("%d",rec[i]);
}
} 
