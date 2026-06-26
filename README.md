# ace-hl7-sharding-examples
Parallel processing of HL7 messages using message data to trigger multiple threads

Relies on the App Connect Health HL7 v2.7 DFDL models

Tests can use Perf Harness (see https://github.com/ot4i/perf-harness) with arguments of the form:
```
JMSPerfHarness -tc mqjava.Requestor -nt 1 -ss 1 -sc ResponseTimeStats -wi 5 -rl 60 -mf PATH/TO/ADT-A03-patient8.txt -jb ACEv13_QM -iq DEMO.IN -oq DEMO.REPLY -jt mqb -rt 100 -tx false -rf 3
```
to run a maximum of 100 msgs/sec through the flow with patient number 8. Multiple patients can be
processed at the same time by sending the appropriate message (patient0-patient9) file.

# Flows

The first stage detects the patient number and sends the message to the appropriate thread in the mapping 
flow. This allows the mapping side of the solution (which uses the ESQL SLEEP call to simulate significant
processing) to work in parallel when working on different patients:

## Stage 1 (fan out) flow
![stage1 flow](/images/InputToFanOut.png)

## Stage 2 (multi-threaded mapping)
![stage2 flow](/images/MultiThreadMapping.png)

## Stage 3 (distribute to downstream)
![stage3 flow](/images/OutputToDownstream.png)


## Stats output

The fan-out flow produces statistics (using the Scheduler node) to show how many message have been processed:
```
2026-06-25 23:16:48.464984: BIP8099I: Shard: 0  -  message count: 0
2026-06-25 23:16:48.465080: BIP8099I: Shard: 1  -  message count: 0
2026-06-25 23:16:48.465112: BIP8099I: Shard: 2  -  message count: 0
2026-06-25 23:16:48.465136: BIP8099I: Shard: 3  -  message count: 0
2026-06-25 23:16:48.465160: BIP8099I: Shard: 4  -  message count: 0
2026-06-25 23:16:48.465172: BIP8099I: Shard: 5  -  message count: 0
2026-06-25 23:16:48.465184: BIP8099I: Shard: 6  -  message count: 0
2026-06-25 23:16:48.465208: BIP8099I: Shard: 7  -  message count: 0
2026-06-25 23:16:48.465232: BIP8099I: Shard: 8  -  message count: 108
2026-06-25 23:16:48.465264: BIP8099I: Shard: 9  -  message count: 0
```
