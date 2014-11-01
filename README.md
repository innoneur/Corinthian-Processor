Corinthian Processor
====================
**Version: 0.0.1**

Corinthian Processor (CP) is a processor for trivial packets emitted by 
micro System On Chip (SoC) units.CP processes real-time feeds i.e. 
the packets have a time context, and pours in real-time.

*This document describes the system and its nuts/bolts. The document also
explains the motivation behind the reaches of a system.*

Corinthian Processor is based on [Apache Storm](https://storm.apache.org/) and is written in Java.

##1. Purpose

CP is a plug-n-play system designed to be plugged in to multiple SoCs
These are http streams coming in realtime but can be customized to 
any other realtime feed APIs like MQTT.

##2. Internals

Stream - Where the realtime hose is fitted , it gets realtime data in
JSON format.Packets will be inherently JSON unless specified otherwise.
	
#####Spout Message Q 
- Minimum feed of 25,000  packets in a minute
- Packet Size - Max 2000 bytes 
	
#####Step 0 (Collect by two traits)
Make a set by `nid, type, datetime, s0..25 = value`
```
nid = nodeId
did = domainId
type = {raw,head,mesh}
datetime = {unix timestamp}
s0..25 = {parameter name}
```
`value` is for each parameter name
			
#####Aggregate hourly
```
For each s0..25, by nid, type in {raw}
sum(value(s0..25))
numberOf(s0..25)
average( use the above two )
```

#####OffLoader(Type RDBMS)
```
CCYY-MM-DD HH
nid
did
parameter name
sum
numberOf
avg	
```

#####Stats
```
nid
type = {raw,head,mesh}
number of packets
CCYY-MM-DD HH
```

##3.  Formats

A typical format looks like below
- Has four parameters `s0` thru `s3`
- Packets would be interspersed with different `nid`'s

```
{
	 "type":"raw",
	 "s1":"1023",
	 "did":"1084807054",
	 "datetime":"1414834710",
	 "s3":"1023",
	 "type":"data",
	 "nid":"20480",
	 "s0":"396",
	 "s2":"1023"
}
```
    
##4.  Measurement - QC
- Spout must never go down
- Stats stage is the sink - packets can be discarded
- Aggregator and OffLoader work on Batched Sets prepared by hour 	 


##Contributions
- Content by @ajijohn
