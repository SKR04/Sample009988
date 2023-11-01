Certainly! To simulate the network scenario you described using Tcl with ns-2 (Network Simulator 2), you can use the following code:

```tcl
# Create a new simulation object
set ns [new Simulator]

# Define constants
set node_count 10
set distance_between_nodes 150
set max_packet_size 1460
set max_buffer_size 50
set data_rate 4 ;# packets per second
set simulation_time 60 ;# seconds

# Create nodes
for {set i 0} {$i < $node_count} {incr i} {
    set node($i) [$ns node]
}

# Create links between nodes
for {set i 0} {$i < $node_count-1} {incr i} {
    $ns duplex-link $node($i) $node([expr $i+1]) 10Mb 10ms DropTail
}

# Set propagation delay based on distance
for {set i 0} {$i < $node_count-1} {incr i} {
    set distance [expr $distance_between_nodes / 150000000] ;# Convert meters to seconds
    $ns rtproto DV
    $ns delay $node($i) $node([expr $i+1]) $distance
}

# Define traffic source and sink
set source [new Agent/Null]
$ns attach-agent $node(0) $source
set sink [new Agent/Null]
$ns attach-agent $node($node_count-1) $sink

# Set up traffic generation
set traffic [new Application/Traffic/CBR]
$traffic attach-agent $source
$traffic set type_ CBR
$traffic set packetSize_ $max_packet_size
$traffic set rate_ $data_rate
$traffic set interval_ [expr 1.0 / $data_rate]

# Create a TCP connection between source and destination
set tcp [new Agent/TCP]
$ns attach-agent $node(0) $tcp
$ns connect $tcp $sink
$tcp set fid_ 1

# Create a traffic source for the TCP connection
set tcpsrc [new Application/Traffic/CBR]
$tcpsrc attach-agent $source
$tcpsrc set type_ CBR
$tcpsrc set packetSize_ $max_packet_size
$tcpsrc set rate_ $data_rate
$tcpsrc set interval_ [expr 1.0 / $data_rate]
$ns at 0.1 "$tcpsrc start"

# Set up tracing
$ns trace-all $ns

# Run the simulation
$ns run

# Clean up
$ns delete
```

This code sets up a simulation with 10 nodes connected in a series. It generates traffic from the first node to the last node at a rate of 4 packets per second. The simulation runs for 60 seconds. The nodes are connected by links with 10Mbps bandwidth and 10ms delay.

Please make sure you have ns-2 installed on your system and the necessary Tcl libraries available. Keep in mind that this is a simplified simulation and may require further customization based on your specific requirements.
