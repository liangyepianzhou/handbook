<a id="_xq0ee1vnpz4o"></a>Exactly Once Delivery and Transactional Messaging in Kafka

<a id="_i4ub5zye01nh"></a>The definitive design\.

# <a id="_pxd1n1pvutgj"></a>__Update History__

### <a id="_7mkuz62dyql"></a>2017\.02\.22

- Message key and value are no longer optional fields as indicated through attributes\.

### <a id="_yrbjdxvtr013"></a>2017\.02\.15

- Removed BeginTxnRequest in favor of allowing the transaction to be started implicitly upon receiving the first AddPartitionToTxnRequest\.
- Moved the transaction timeout that was previously passed in BeginTxnRequest into InitPidRequest\.
- Re\-added MessageSetSize field to ProduceRequest\. Although it is not strictly necessary, it makes parsing the request much easier\.
- Clarified the semantics of transactionalId expiration\. It is based on the age of the most recent transaction, not on the time the InitPIDRequest was received\. 
- Updated the doc to indicate that there will be only one PID snapshot file\. There was no good reason to keep two\.
- Added a section on metrics\.

### <a id="_4vwxtw8qa5ci"></a>2017\.02\.13

- Changed MaxTimestampDelta to MaxTimestamp in the MessageSet\.
- Added TransactionTimeoutMs in BeginTxnRequest\. Also added a new error : InvalidTransactionTimeout
- Added configurations for the transaction state log topic\.

### <a id="_i8usz7jt7ms6"></a>2017\.02\.07

- Remove message\-level CRC and deprecate client checksum APIs\.
- Individual message timestamps are variable length and relative to an initial timestamp stored at the message set\.

### <a id="_pcbwemhd5rbm"></a>2017\.02\.03

- Added TransactionalId to the [ProduceRequest](#_mkk6ckzh7jxl) and added section to describe authorization of produce requests using the ProducerTransactionalId resource\.
- The [AddPartitionsToTxn](#_r6klddrx9ibz) API requires Topic Write permission for all topics included in the request\.
- The [WriteTxnMarker](#_jtpvkrldhb7) API requires ClusterAction permission on the Cluster\.
- GroupCoordinatorRequest renamed to FindCoordinatorRequest since there is no group for transactional producers\.

### <a id="_vanysvwh06z8"></a>2017\.02\.01

- Changed AppID to TransactionalId\.
- Added notes on [client compatibility](#_u76s202ebj43) and [third\-party authorizers](#_tiigsrv40pcn) to migration notes\.
- InitPIDRequest renamed to InitPidRequest\.

### <a id="_9me0nxchj3gz"></a>2017\.01\.27

- Provided default values for added client / broker configs\.

### <a id="_bfgd93xaigrf"></a>2017\.01\.26

- Added section on transaction coordinator [authorization](#_3q05uee3vqcq)\.
- AddOffsetCommitsToTxn RPC renamed to AddOffsetsToTxn\.
- UpdateTxn RPC renamed to WriteTxnMarker\.

### <a id="_2eud57xy692z"></a>2017\.01\.25

- Added a Future work section for feedback which should be incorporated in version 2\.

### <a id="_2n0rz78mn9a0"></a>2017\.01\.24

- Reworked the schema of transaction [control messages](#_mylukj7bg1rf) so that we could include the coordinator epoch, which is needed to fence zombie transaction coordinators\.
- Included the schema for the [PID snapshot file](#_vjqzk4b5fdhg)\.
- Added producer config to [enable idempotence](#qomrk2mk882)\.

### <a id="_cczdozou5271"></a>2017\.01\.20

- The GroupType field in [GroupCoordinatorRequest](#_97qeo7mkx9jx) was renamed to CoordinatorType\.
- Added version 2 of [ListOffsetRequest](#_d2pvxax47irz) to give the client a way to find a partition’s LSO in order to support seekToEnd\(\) in READ\_COMMITTED\.
- LSO has been added to the [FetchResponse](#_p3hvy6kufvd1) to be able to compute consumer lag in READ\_COMMITTED\.

### <a id="_o5trbd2fetr"></a>2017\.01\.19

- Incorporated [abort index proposal](https://docs.google.com/document/d/1Rlqizmk7QCDe8qAnVW5e5X8rGvn6m2DCR3JR2yqwVjc/edit#heading=h.8gn43c4xxueb)\.
- The NoPidMapping error code was changed to InvalidPidMapping to also express the case where the PID passed in a request is different from that stored in the transaction coordinator\.
- Idempotent producers that do not need transactions can send InitPidRequest to any broker with a null TransactionalId in order to be assigned a PID\.
- Slight modification to handling of [EndTxnRequest](#aqrq1dgbix3a) for the edge case in which the coordinator fails after completing the transaction successfully, but before returning the response of the request to the client\.

### <a id="_4w2kfdo7rwah"></a>2017\.01\.17

- Added a discussion on TransactionalIds and PIDs in the rejected alternatives\.

### <a id="_1y6lo9t7x9fm"></a>2017\.01\.05

- Added a motivation subsection about how this proposal is guided by the streams usecase for transactions\.
- Updated the ‘Transactional Guarantees’ to include the guarantees for the consumer\. Previously we only discussed guarantees for the producer\. 
- Changed the consumer config from ‘fetch\.mode’, to ‘isolation\.level’, to be more inline with database terminology\. The values were correspondingly changed as well\.
- Miscellaneous other changes based on KIP feedback\. 

### <a id="_av9hpc24qnps"></a>2016\.11\.29

- Update transaction maker messages to use attribute fields for all controller flags\.
- Incorporated motivation, guarantees, and data flow sections from the [unified KIP doc](https://cwiki.apache.org/confluence/display/KAFKA/KIP-98+-+Exactly+Once+Delivery+and+Transactional+Messaging)\.

### <a id="_8pmtm5camx2t"></a>2016\.11\.25

- Merge TxnCoordinatorRequest into GroupCoordinatorRequest with bumped up version and a newly added field\.
- Update the send function procedure with non\-transactional producer\.

### <a id="_73lthv2inwb4"></a>2016\.11\.23

- GetPIDRequest changed to [InitPidRequest](#_z99xar1h2enr) and handling is updated to ensure that pending transactions are completed before returning\.
- TransactionalId was added to the transaction coordinator APIs\. This makes it easier to check whether the client is contacting the right transaction coordinator\.
- Changed ProducerNotRecognized error to NoPIDMapping\.
- New client configuration transactional\.id and the TransactionalId parameter was removed from the initialize API, which was renamed to initTransactions, to emphasize the fact that it is only needed when using transactions\.

### <a id="_f85qnygt6fhu"></a>2016\.11\.22

- *Incorporated Idempotent Producer KIP *[*doc*](https://docs.google.com/document/d/1RhldVRJN7xgRSRwJN-pHsuLyg6_PQw6w9IBtNCYRC2c/edit#)*, including the message format changes\.*
- *Add init API to the producer\.*

### <a id="_1sgtf08i5b5t"></a>2016\.11\.21

- *Added SendOffsets API to the producer\.*
- *Added TxnOffsetCommitRequest into the PRC protocol to not pollute the original OffsetCommitRequest\.*
- *Augment AddOffsetsToTxnResponse with the consumer coordinator information\.*

### <a id="_e8uompjdxlrf"></a>2016\.11\.19

- *Collapsed CommitTxnRequest and AbortTxnRequest with UpdateTxnRequest, rename PrepareTxnRequest to EndTxnRequest\.*
- *Added a discussion *[*section*](#kix.uu5bwrue4nmm)* about fencing zombie writer WITHOUT requiring that same TransactionalIds always map to same PIDs\.*
- *Updated transaction status message format on the transaction topic\.*
- *Added producer\-side exception handling description\.*
- *Updated logic in TransactionalId expiration, and also updated GetPIDRequest with TransactionalId\.*
- *Added a discussion section on committing offsets within a transaction\.*
- *Added a discussion section on updating log compaction for transactional messages\. *

This document serves to describe the detailed implementation design of [KIP\-98: Exactly Once Delivery and Transactional Messaging](https://cwiki.apache.org/confluence/display/KAFKA/KIP-98+-+Exactly+Once+Delivery+and+Transactional+Messaging)\. Readers are highly recommended to read the KIP proposal itself before continue reading on this doc\.

[Update History](#_pxd1n1pvutgj)

[2017\.02\.13](#_4vwxtw8qa5ci)

[2017\.02\.07](#_i8usz7jt7ms6)

[2017\.02\.03](#_pcbwemhd5rbm)

[2017\.02\.01](#_vanysvwh06z8)

[2017\.01\.27](#_9me0nxchj3gz)

[2017\.01\.26](#_bfgd93xaigrf)

[2017\.01\.25](#_2eud57xy692z)

[2017\.01\.24](#_2n0rz78mn9a0)

[2017\.01\.20](#_cczdozou5271)

[2017\.01\.19](#_o5trbd2fetr)

[2017\.01\.17](#_4w2kfdo7rwah)

[2017\.01\.05](#_1y6lo9t7x9fm)

[2016\.11\.29](#_av9hpc24qnps)

[2016\.11\.25](#_8pmtm5camx2t)

[2016\.11\.23](#_73lthv2inwb4)

[2016\.11\.22](#_f85qnygt6fhu)

[2016\.11\.21](#_1sgtf08i5b5t)

[2016\.11\.19](#_e8uompjdxlrf)

[Motivation](#_rbxg0at9106a)

[A little bit about Transactions and Streams\.](#_dc15d4ckm8e8)

[Summary of Guarantees](#_8chv2811xe3p)

[Idempotent Producer Guarantees](#_p4239g4vpvr7)

[Transactional Guarantees](#_mmbff48qbcw6)

[Design Overview](#_m6x1258qinxn)

[Key Concepts](#_f0eotchjto1f)

[Transactional Messaging](#_o8gioa9dcbnm)

[Offset Commits in Transactions](#_cjy2azqx9su6)

[Control Messages for Transactions](#_scppt9di2ipj)

[Producer Identifiers and Idempotency](#_azlgwswrwqxk)

[Data Flow](#_k3ikkiat5ahm)

[1\. Finding a transaction coordinator \-\- the FindCoordinatorRequest](#_6u8uypxxs15g)

[2\. Getting a producer Id \-\- the InitPidRequest](#_dinbsctebcle)

[2\.1 When a TransactionalId is specified](#_o6wvubfhzaqo)

[2\.2 When a TransactionalId is not specified](#_6562cfctwipz)

[3\. Starting a Transaction \-\- the beginTransaction API](#_z9nopnb9z4o1)

[4\. The consume\-transform\-produce loop](#_9nlwbd1bifn0)

[4\.1 AddPartitionsToTxnRequest](#_dyen7q27r3i2)

[4\.2 ProduceRequest](#_cckv7jc1fsy3)

[4\.3 AddOffsetsToTxnRequest](#_ggt0dw5tk47r)

[4\.4 TxnOffsetCommitRequest](#_4fiwu5vphzq9)

[5\. Committing or Aborting a Transaction](#_rhcuthaoajrw)

[5\.1 EndTxnRequest](#_rr7z72vuy251)

[5\.2 WriteTxnMarkerRequest](#_bgy58z5pyzw7)

[5\.3 Writing the final Commit or Abort Message](#_leodiv40s4w1)

[Transactional Producer](#_dz78fc8xlsd5)

[Public APIs](#_9f3l35ifpyku)

[Error Handling](#_8y3tle2c2qbm)

[Added Configurations](#_78p7cgjqcbnx)

[Transaction Coordinator](#_kcryyiqr0svu)

[Transaction Log](#_lzhx7x34k9er)

[Transaction Coordinator Startup](#_bg39k6ipxugn)

[Transaction Coordinator Request Handling](#_mcphg8e8gg24)

[Coordinator\-side Transaction Expiration](#_tqy9gm4d8r26)

[Coordinator TransactionalId Expiration](#_n3o7f9yqymnk)

[Added Broker Configurations](#_iixbdsg65d7k)

[Authorization](#_3q05uee3vqcq)

[Broker](#_pabpm8thuoag)

[Transaction Markers](#_mylukj7bg1rf)

[Last Stable Offset Tracking](#_8nl3bhqx81oo)

[Aborted Transaction Index](#_g0jqni2vcxg4)

[Compacting Transactional Data](#_ft6h1rabna7a)

[PID\-Sequence Management](#_kivcfzwo3zm1)

[PID Snapshots](#_vjqzk4b5fdhg)

[PID Expiration](#_loujdamc9ptj)

[Coordinator\-Broker request handling](#_m9yg71bk9idd)

[Client\-Broker request handling](#_d42xl2uz6r8j)

[Authorization](#_cbb5tvp30uqe)

[Consumer Coordinator](#_qv0jmz7iwucq)

[Client\-Coordinator request handling](#_yct0oc2nyy0h)

[Consumer Coordinator Startup](#_ul06rr7fcm7w)

[Consumer](#_od2aaa53rbv)

[Added Configurations and Consume Protocol](#_wvdrakld4019)

[Message Format](#_pau6n6buvoee)

[Message Set Fields](#_tp1lvfionrzv)

[Message Fields](#_c3oktixktv3b)

[Space Comparison](#_fzysbytkejm4)

[Client API Changes](#_mo0ibmx05lcn)

[Migration Plan](#_4q83oxy4ofov)

[Client Compatibility](#_u76s202ebj43)

[Third\-party Authorizers](#_tiigsrv40pcn)

[RPC Protocol Summary](#_68og8h96rdei)

[FetchRequest](#_p3hvy6kufvd1)

[ProduceRequest](#_mkk6ckzh7jxl)

[ListOffsetRequest](#_d2pvxax47irz)

[FindCoordinatorRequest](#_97qeo7mkx9jx)

[InitPidRequest](#_z99xar1h2enr)

[AddPartitionsToTxnRequest](#_r6klddrx9ibz)

[AddOffsetsToTxnRequest](#_cqecubxziwie)

[EndTxnRequest](#_2dnhvqqi7mr7)

[WriteTxnMarkerRequest](#_jtpvkrldhb7)

[TxnOffsetCommitRequest](#_5695qbm2hne)

[Future Work](#_hapfg5juqkyc)

[Recovering from correlated hard failures\.](#_oc95ehvigl7v)

[Producer HeartBeat](#_u5fw38sut3ef)

[Update Producer Interceptor](#_3ie86d5xx5xx)

[Rejected Alternatives](#_eze4c49k73f6)

[On TransactionalIds and PIDs](#_lizhp2urqn59)

# <a id="_rbxg0at9106a"></a>Motivation

This document outlines a proposal for strengthening the message delivery semantics of Kafka\. This builds on significant work which has been done previously, specifically, [here](https://cwiki.apache.org/confluence/display/KAFKA/Idempotent+Producer) and [here](https://cwiki.apache.org/confluence/display/KAFKA/Transactional+Messaging+in+Kafka)\.

Kafka currently provides at least once semantics, viz\. When tuned for reliability, users are guaranteed that every message write will be persisted at least once, without data loss\. Duplicates may occur in the stream due to producer retries\. For instance, the broker may crash between committing a message and sending an acknowledgment to the producer, causing the producer to retry and thus resulting in a duplicate message in the stream\.

Users of messaging systems greatly benefit from the more stringent idempotent producer semantics, viz\. Every message write will be persisted exactly once, without duplicates and without data loss \-\- even in the event of client retries or broker failures\. These stronger semantics not only make writing applications easier, they expand the space of applications which can use a given messaging system\.

However, idempotent producers don’t provide guarantees for writes across multiple TopicPartitions\. For this, one needs stronger transactional guarantees, ie\. the ability to write to several TopicPartitions atomically\. By atomically, we mean the ability to commit a set of messages across TopicPartitions as a unit: either all messages are committed, or none of them are\.

Stream processing applications, which are a pipelines of ‘consume\-transform\-produce’ tasks, absolutely require transactional guarantees when duplicate processing of the stream is unacceptable\. As such, adding transactional guarantees to Kafka \-\-a streaming platform\-\- makes it much more useful not just for stream processing, but a variety of other applications\.

## <a id="_dc15d4ckm8e8"></a>A little bit about Transactions and Streams\. 

In the previous section, we mentioned the main motivation for transactions is to enable exactly once processing in Kafka Streams\. It is worth digging into this use case a little more, as it motivates many of the tradeoffs in our design\. 

Recall that data transformation using Kafka Streams typically happens through multiple stream processors, each of which is connected by Kafka topics\. This setup is known as a stream topology and is basically a DAG where the stream processors are nodes and the connecting Kafka topics are vertices\. This pattern is typical of all streaming architectures\. You can read more about the Kafka streams architecture [here](https://kafka.apache.org/documentation/streams)\.

As such, a transaction for Kafka streams would essentially encompass the input messages, the updates to the local state store, and the output messages\. Including input offsets in a transaction motivates adding the sendOffsets API to the Producer interface, described below\. Further details will be presented in a separate KIP\. 

Further, stream topologies can get pretty deep \-\-10 stages is not uncommon\. If output messages are only materialized on transaction commits, then a topology which is N stages deep will take N x T to process its input, where T is the average time of a single transaction\. So Kafka Streams requires speculative execution, where output messages can be read by downstream processors even before they are committed\. Otherwise transactions would not be an option for serious streaming applications\. This motivates the ‘read uncommitted’ consumer mode described later\. 

These are two specific instances where we chose to optimize for the streams use case\. As the reader works through this document we encourage him/her to keep this use case in mind as it motivated large elements of the proposal\. 

# <a id="_8chv2811xe3p"></a>Summary of Guarantees

## <a id="_p4239g4vpvr7"></a>Idempotent Producer Guarantees

To implement idempotent producer semantics, we introduce the concepts of a *producer id, *henceforth called the *PID*, and *sequence numbers* for Kafka messages\. Every new producer will be assigned a unique *PID* during initialization\. The PID assignment is completely transparent to users and is never exposed by clients\.

For a given PID, sequence numbers will start from zero and be monotonically increasing, with one sequence number per topic partition produced to\. The sequence number will be incremented for every message sent by the producer\. Similarly, the broker will increment the sequence number associated with the PID \-> topic partition pair for every message it commits for that topic partition\.  Finally, the broker will reject a message from a producer unless its sequence number is exactly one greater than the last committed message from that PID \-> topic partition pair\. 

This ensures that, even though a producer must retry requests upon failures, every message will be persisted in the log exactly once\. Further, since each new instance of a producer is assigned a new, unique, PID, we can only guarantee idempotent production within a single producer session\. These semantics have been discussed previously in [this document](https://cwiki.apache.org/confluence/display/KAFKA/Idempotent+Producer)\.

## <a id="_mmbff48qbcw6"></a>Transactional Guarantees

As mentioned in the Motivation section, transactional guarantees enable applications to batch consumed and produced messages into a single atomic unit\. 

In particular, a ‘batch’ of messages in a transaction can be consumed from and written to multiple partitions, and are ‘atomic’ in the sense that writes will fail or succeed as a single unit\. Consumers may or may not consume these messages atomically, depending on their configuration\. This has been previously discussed [here](https://cwiki.apache.org/confluence/display/KAFKA/Transactional+Messaging+in+Kafka)\.

Additionally, stateful applications will also be able to ensure continuity across multiple sessions of the application\. In other words, Kafka can guarantee idempotent production and transaction recovery across application bounces\.

To achieve this, we require that the application provides a unique id which is stable across all sessions of the application\. For the rest of this document, we refer to such an id as the *TransactionalId*\. While there may be a 1\-1 mapping between an TransactionalId and the internal PID, the main difference is the the TransactionalId is provided by users, and is what enables idempotent guarantees across producers sessions described below\.

When provided with such an *TransactionalId*, Kafka will guarantee:

1. Idempotent production across application sessions\. This is achieved by fencing off old generations when a new instance with the same TransactionalId comes online\.
2. Transaction recovery across application sessions\. If an application instance dies, the next instance can be guaranteed that any unfinished transactions have been completed \(whether aborted or committed\), leaving the new instance in a clean state prior to resuming work\.

Note that the transactional guarantees mentioned here are from the point of view of the producer\. On the consumer side, the guarantees are a bit weaker\. In particular, we cannot guarantee that all the messages of a committed transaction will be consumed all together\. This is for several reasons:

1. For compacted topics, some messages of a transaction maybe overwritten by newer versions\.
2. Transactions may straddle log segments\. Hence when old segments are deleted, we may lose some messages in the first part of a transaction\.
3. Consumers may seek to arbitrary points within a transaction, hence missing some of the initial messages\.
4. Consumer may not consume from all the partitions which participated in a transaction\. Hence they will never be able to read all the messages that comprised the transaction\.

# <a id="_m6x1258qinxn"></a>Design Overview

In this section, we will present only a very high level overview of the key concepts and data flow of transactions\. Further sections flesh these concepts out in detail\.

## <a id="_f0eotchjto1f"></a>Key Concepts

### <a id="_o8gioa9dcbnm"></a>Transactional Messaging

The first part of the design is to enable producers to send a group of messages as a single transaction that either succeeds or fails atomically\. In order to achieve this, we introduce a new server\-side module called __transaction coordinator__, to manage transactions of messages sent by producers, and commit / abort the appends of these messages as a whole\. The transaction coordinator maintains a __transaction log__, which is stored as an internal topic \(we call it the __transaction topic__\) to persist transaction status for recovery\. Similar to the “offsets log” which maintains consumer offsets and group state in the internal \_\_consumer\_offsets topic, producers do not read or write directly to the transaction topic\. Instead they talk to their transaction coordinator who is the leader broker of the hosted partition of the topic\. The coordinator can then append the new state of the indicated transactions to its owned transaction topic partition\.

We will talk about how the transaction coordinator manages the transaction status from producer requests and persist it in the transaction log in the [Transaction Coordinator](#_n3o7f9yqymnk) section\.

### <a id="_cjy2azqx9su6"></a>Offset Commits in Transactions

Many applications talking to Kafka include both consumers and producers, where the applications consume messages from input Kafka topics and produce new messages to output Kafka topics\. To achieve “*exactly once*” messaging, we need to make the committing of the consumer offsets part of the producer transactions in order to achieve atomicity\. Otherwise, if there is a failure between committing the producer transaction and committing the consumer offsets, data duplicates or data loss will incur upon failover depending on the ordering of these two operations: if committing producer transaction executes first, then upon recovery the input messages will be re\-consumed since offsets were not committed, hence *data duplicates*; if committing consumer offsets executes first, then upon recover the output messages that are failed to commit will not be re\-send again, hence *data loss*\.

Therefore, we want to guarantee that for each message consumed from the input topics, the resulting message\(s\) from processing this message will be reflected in the output topics exactly once, even under failures\. In order to support this guarantee, we need to include the consumer’s offset commits in the producer’s transaction\.

We will talk about how to enhance the consumer coordinator that takes care of the offset commits to be transaction\-aware in the [Consumer Coordinator](#_qv0jmz7iwucq) section\.

### <a id="_scppt9di2ipj"></a>Control Messages for Transactions

For messages appended to Kafka log partitions, in order to indicate whether they are committed or aborted, a special type of message called [control message](https://issues.apache.org/jira/browse/KAFKA-1639) will be used \(some of the motivations are already discussed in [KAFKA\-1639](https://issues.apache.org/jira/browse/KAFKA-1639)\)\. Control messages do not contain application data in the value payload and should not be exposed to applications\. It is only used for internal communication between brokers and clients\. For producer transactions, we will introduce a set of [__transaction markers__](#_mylukj7bg1rf) implemented as control messages, such that the consumer client can interpret them to determine whether any given message has been committed or aborted\. And based on the transaction status, the consumer client can then determine whether and when to return these messages to the application\.

We will talk about how transaction markers are managed in the [Broker](#_pabpm8thuoag) and [Consumer](#_od2aaa53rbv) sections\.

### <a id="_azlgwswrwqxk"></a>Producer Identifiers and Idempotency

Within a transaction, we also need to make sure that there is no duplicate messages generated by the producer\. To achieve this, we are going to add __sequence numbers__ to messages to allow the brokers to de\-duplicate messages per producer and topic partition\. For each topic partition that is written to, the producer maintains a sequence number counter and assigns the next number in the sequence for each new message\. The broker verifies that the next message produced has been assigned the next number and otherwise returns an error\. In addition, since the sequence number is per producer and topic partition, we also need to uniquely identify a producer across multiple sessions \(i\.e\. when the producer fails and recreates, etc\)\. Hence we introduce a new __TransactionalId__ to distinguish producers, along with an __epoch number__ so that zombie writers with the same TransactionalId can be fenced\.

At any given point in time, a producer can only have one ongoing transaction, so we can distinguish messages that belong to different transactions by their respective TransactionalId\. Producers with the same TransactionalId will talk to the same transaction coordinator which also keeps track of their TransactionalIds in addition to managing their transaction status\. 

We will talk about how transactional producers can interact with the transaction coordinators in the [Transactional Producer](#_dz78fc8xlsd5) section\.

## <a id="_k3ikkiat5ahm"></a>Data Flow

![Kafka Transactions Data Flow.png](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAtAAAAIiCAYAAADhDf+OAAByrElEQVR42uy9D7xVdZ3vvYDNOQc4wAEOekyyo5LyKBkaTtqlBi819EQjFfrSLj1Rl9tDE3dkknGsaKLkMTQzMnOwBxMn80EHfeimhVd84lHHHC4ZNurFCSecyMGiJEVFU9t3ffb+feF3Fmufs8/Z/9af9/v1+r7O2f/W/q211+/3e6/f+v0JAgAASArLOAQAAAAAANWzm0MAAAAAAIBAAwAAAAAg0AAAAAAACDQAAAAAAAINAAAAAIBAAwAAAAAg0BwCAAAAAAAEGgAAAAAAgQYAAAAAQKABAAAAABBoAAAAAAAEGgAAAAAAgeYQAAAAAAAg0AAAAAAACDQAAAAAAAINAAAAAIBAAwAAAAAg0AAAAAAACDSHAAAAAAAAgQYAAAAAQKABAAAAABBoAAAAAAAEGgAAAAAAgQYAAAAAQKA5BAAAAAAAzRXojjBmh3FBGFM5pAAAAACAQFdmXhh7wtgSxs1h7HV/CxxaAAAAAECg+6LW5v1hzPGe6wxjZxjLOLQAAAAAgED3ZVUYG2OenxHGdPf/tKDcOr3HifU89/xZYWwKY0MYB8J4NIxZ7rWZYWwPY1cYO8KY656/KiLm/uMHXHr2ulgQlFvCD7rXejzp3+L2e4eXHgAAAACAhgv05jBW9vN6wUnwYvdYUr3PCfbsMIqewK5womvbvcD9P8c9Fusj3+c/1n6sc9+p73s1ONwyLlG/1P3/aEx6ejkNAAAAACAJAq2W5L2R59Y5WZZA7/Gen+2lZZ0TXbUuT60gzHECfZb7f1pk2ytdSJgPuu+y2BrQ3QQAAAAAmiTQVzmJjSKRXRSRYmO1k9noa/5ja0VWy/EBJ+qFKgS61/3fG9n2Su8793uP/ecBAAAAABou0DOckE7znpPoqo/xmqDc71gtvl3e62rxXTKAQEtqp7j/NShR3Symu22u9j5z5yAFWul5NTjcH1osD8ot5QAAAAAADRdok1MJ7lXu/4eC8uC8Tvf6OvecWpTXBuWBhJ0DCPRaJ9pqxV7lPqO5pucH5S4hS8O41v0/GIEOXDq3u/Sscdvo5jQAAAAAgGYJtFBL9KVOUjX7RXQO6HnutcWeWPe49wYxj/X5C9xnJMt+C/Zc73tmuAjcY9t2Z2Tb/vv89CxFngEAAACgFQINAAAAAIBAAwAAAAAAAg0AAAAAgEADAAAAACDQAAAAAAAINAAAAAAAAg0AAAAAgED3g5bq1iqAG4PyIiqPhjEnKC+gopUJ/eW9NR/zrjD2BOUVCqd629jhvl+fnz/A85onepN7fp/bls3hPMu9d7dL151uO0P5fgAAAACAugv07DCKTpoDJ7ZaLbDLhS3vrcVLtNKfLcuthVS2u/83B+XFUkyA76zw/Cb3/xoXwpYLX+7+13fMda9d4NI2233/niq/fxOnAgAAAAA0UqD3eI9XenJr29R7VgTlZbwXeSG5VWuyluJWy++lQd9VAis9L7TC4FluOzvc9+rxrsj7dtXw/QAAAAAADRHo3RGBXhkj0FeF8YD3uoVaqdVyvDCMDUG5S8ZW91yl59V6rJbmjU6Mt7pt6Xu2R9K33T1/7RC+HwAAAACgZQK92AmsIXFdG0ZHGKuDw10r9Ni6ffjPd7rne4Nyq/I8b1ub3XeqH/SB4HB/aP09OMjv978HAAAAAKBlAi0xVd/oDU5m1Z3CBhiucY8Xu//VnUItwNdWeN4GBy5yErzbSbB9/073eLsT6lkDfH+l7wEAAAAAqLtAqw/xAu/xjKBvP+IF7j2iw0mvJNdvQS64x3p+SVBuHbbn58c83+keq/uG+j33BuVBjAX32syg3CVD37s3ONya3N/3x30PAAAAAEDdBTpJSJAPBoenrZMs7wloTQYAAAAABLoiall+KDg8D/Q0flYAAAAAQKABAAAAABBoAAAAAAAEGgAAAAAAgQYAAAAAAAQaAAAAAACBBgAAAABAoAEAAAAAEGgAAAAAAAQaAAAAAACBBgAAAAAABBoAAAAAAIEGAAAAAECgAQAAAAAQaAAAAAAABBoAAAAAAIEGAAAAAAAEGgAAAAAAgQYAAAAAQKABAAAAABBoAAAAAAAEGgAAAAAAgQYAAAAAAAQaAAAAAACBBgAAAABAoAEAAAAAEGgAAAAAgOTTE8bTYewL4wUkGgAAAABgYB4Ko+jiKQ4HAAAAAED/LAjjJSfQ6zgcAAAAAAD9UwjjgIuFHA4AAAAAgIH5ZlBugZ7OoQAAAAAAGJjeoNwCDQAAAABppvuoo58c09m5j2h8jGxre5Hj0JyYOKl7C7kbAAAAGsIbphxX3PHLA0QTYtuufRyHJsW48V37yd0AAACAQBMEAg0AAAAINEEg0AAAAIBAEwQCDQAAAAh01mLdbT8sXvixJcVzz19YXLh4aXHjlm3IJwINAAAAgEDHxQc/vKjYNXFS8ZOf/lzxsq9dXzzvI4uLbe0dxW/cdDsCikADAAAAINB+XH7NDcWx48YX7334yT7PX/TZy4qTJh9d/Mkv9vd5/gc/frziDBp6r16Pe03b3/rIU0NKoz774M5nKj6uNn36/uj+INAAAAAACPSgYubZ7yx13YibZs7vxrHmhltLQq1jJOFWa7W/DXX90Gt6j+KWu+47JLunnHZG8ehjji09/5Yzzjwk6/qMvx21fmtb1qVE733H7PeUPqsW8ZVfua74rjnvLX2PHkv+K6Vv6SVf6JM+tbKPKBSKb591DgINAAAAgEAPPSSdEtf+3vP9+39WEtYb77in9PjubU+URNU+J0GdevIpxfsf+1Xp8bwPXVg8Z+77S/9LZCXKti29tmL1NVUJdPiTHJLkS754ZemxdSv5zKqrS99prc5Knz5j0q702Xu1zZNPPa2U7kot5Ag0AAAAAAJdVajv80ACffHnLz+i5Vbiq9ZgE9RKIqxWY+tf/Z3v/ajPNgYS6NFjxvQZ5Kjt+I/t95Jc9554Uuk5i3e/7wOlvtyWPu0DfaABAAAAEOiaQ90kfIn1+zNLaNWqrNf9VuSo7PYn0PZYAq5WYnXHuO7mTVUJtP979PdY21BLuj7rx8c/dfGh9A10kYBAAwAAACDQVYUGC76x94QjBtd99frvloRXA+/U5UL9mP3XNdXd3HPPG1Cg1e1j09aHD/Wr1ues60VUoNUtYygCrfSpi4afPn3vrZsfRKARaAAAAECg6xuazULdH9RCLClVH2F1u1D3CRuIpz7FGpgnwZUEqwVZr1tLcn8Crecl3+p/rO+SNKt7hV77xEV/UxJfCba6dygdQxFoS58uBpQ+bUvdPXQRgEAj0AAAAIBA1z3UyqyWYQmsjoG6dfgzXCg0q4YkW69LiE1OrTXaF1SJtZ4zQVdfZLVy67MSaJuFQ6+pFVvPn37m2aXvtM+p9dhauKt5rPRpxg5tSy3cNlDR0meyj0ADAAAAINAEgUADAAAAAk0QCDQAAAAg0ASBQAMAAAACTRAINAAAACDQBIFAAwAAACDQBAINAAAAUEfGd00oTYNGEFkJLXOOQAMAAEDDoAWaoAUaAAAAAIEmEGgEGgAAABBogkCgAQAAAIEmCAQaAAAAEGiCQKABAAAAgSYIBBoAAAAAgSYQaAAAAAAEmkCgEWgAAABAoAkCgQYAAAAEmiAQaAAAAECgCQKBBgAAAASaIBBoAAAAAASaQKABAAAAEGgCgUagAQAAAIEmCAQaAAAAEGiCQKABAAAAgSYIBBoAAAAQaIJAoAEAAAAQaAKBBgAAAECgCQQagQYAAAAEmiAQaAAAAECgCQKBBgAAAASaIBBoAAAAQKAJAoEGAAAAQKAJBBoAAAAAgSYQaAQaAAAAEOh6xvfv/1lR+z7z7Hc29HtOP/Ps4iVfvLL0/3e+96PSd9697Ym6bX/brn3FNTfceujxV6//bpGLIgQaAAAAEOi6x8c/dXHxjb0nFMNDUFx32w8b9j06vp/89OdK/2/a+nDx3PMXFrc+8lTdtn/eRxb3uQi48Y57St+BQCPQAAAAgEDXLX7yi/3Fo485tiS2J596WnHuuec1RaAbEZLlRreiI9AAAAAAORfob9x0e6nleeOWbaXuFW3tHcV7H36yz3su+uxlpZD8dk2cVBw7bnxJVv33LVy8tLjyK9cVL/zYktLrep9ahB/c+UysQN+6+cGS7PrbuPyaG4qnnHZGcUShUOw98aTSd/qiv/SSLxSnnnxKKY3a/rvf94FS9xNL46TJR5e+W9vV9q+7eVMfodY2tI/ahvZZfz+z6uo++6r3f2vDnaULiUr7gUADAAAA5FigJaGSVv2v7hSSV19crWV39JgxpT7Mt9x1X6lrhARXn5OUmnjqPefMfX9JxiWvEtp3zXlvrECrq4gk9gc/frz0eMXqa0qP1Z1Er0ls/bR8dMmyktBe9rXrS69fcd1Npe0rTdZdQ/8rXXqPxFx/tU37fsm90njx5y8vbUN/9R3atr1H75c0670SaX2/npO8I9AAAAAAORdoE2a/FVYCHD0OJtD3P/arQ8+phVdiKck0gdbnNJAvrnV7IIFWN5Jof+VPXPQ3xQ9+eFHpf/1VC3f0dbVGV+rC4Qu0Bivqf0mzvw2TaBvMqPdEu7Fom2+fdQ4CDQAAAJB3gTZ5VKuyRFZhLcGSX19M/ZZkC7XUmhBLMtXVITorhralbfYn0Ca3mjVjoFk2JO56n3XnCLwW5v4EWi3W+t+6fPgzkOh5vW4CHW1tTnvfagQaAAAAEOg6hbo7SBjjwhdmCWTcbBY6XurqYAIdN0DQF9JKAq0IBpgBRNKsLhwSfomzWonfMfs9VQu0/R/t323frdctvfY/Ag0AAACAQB8KzcMcVGj1VZ9giap1r5BARrswqO+zuk9YlwgJ5rwPXdjnPZJV/zsqCbS6kuj/aBcNtTare4le13cpHX4XEXXhqFago91Jol1RrMUdgQYAAABAoGNDfYrVousLqYXmaA7cgD4TSAmsCbUN+vMlW4Kp7fktvJJlfc76TvfXB1oDEqOSrlZmTa1nsu8vkqJ0x3XhsAGRUYHWLBpKX7SbiT6j522WDQQaAAAAAIE+IiSLGhRoA/Ti4i1nnFma5UItzTaIUI8l1ZJQybNagP2BdhJRHUM9r9bowOv/PJBAS5Jtpg+9R7OD6Ds0SFGyrM8qNA2dZsaQPEuutQ2TX3UV0WObLSQ6C4dauLVNdU/Rd+ivHvvCjEADAAAAINCxS3dLIKPdGfzQtHB6jwTXBFJT00mM9dgfZGgCrefVXUPyKznXNvz3SH6tn7O2q+37M3soXeo+oplA1LfaT58GGtprEnhtR63d2oYNDJRI67HSoa4Zimi/bD2nbdt36LH/ut4ffU4t31GpRqABAAAAgvwu5V2PFf5MoDleCDQAAAAg0Ag0Ao1AAwAAACDQ1Yf6HGup7v7eo9ejKxgSCDQAAAAg0ASBQAMAAAAg0AQCDQAAAIBAEwQCDQAAAAg0QSDQAAAAgEATBAINAAAACDRBINAAAACAQBMEAg0AAACAQBMINAAAAAACTRAINAAAACDQBIFAAwAAAAJNEAg0AAAAINAEgUADAAAAAk0QCDQAAAAAAk0g0AAAAAAINEEg0AAAAIBAEwQCDQAAAAg0QSDQAAAAgEATBAINAAAACDRBINAAAAAACDSBQAMAAAAg0ASBQAMAAAACTRAINAAAACSG8V0TipJogshKXPLFKxFoAAAAaBy0QBO0QAMAAAAg0ETOo72j46Xw9J5GDgcAAAAEmiCqiFGjRr8Qnt57kWgAAABAoAmi+i4cy8LYFUYPOR0AAAAQaIKorg+0JHonEg0AAAAINEFUP4jwUiQaAAAAEOg6xt3bnij+4MeP94l6f4e2+ZNf7EduWzcLx5owHgijk1wPAAAACHSNoWMQHoo+0TVxUvGr13+3bt+hbTZCzIlBTWOHRAMAAAACXS+Bvuxr1/d5buklXyi2tXcUH9z5DAKdrXmg1zmJ7iD3AwAAAAJdR4G+/7FflaT3xjvuKW595KniR5csK678ynXFd8x+T/E73/tR6T36zLvmvLcUes3//C133Vece+55pde+teHOPgKtbW3a+vCh9+qza2649dBjfac++/ZZ55RE3rp+6O+K1deUtnnO3PcXv3HT7Yc+o8/rtYWLlxbf/b4PlNKMQMdSCGOjiwIlAAAAACDQdRJodd8YUSiURFriO3rMmOLMs99Z/OSnP1eSX/3tPfGkkrgq9P/HP3Vx6bN6fey48cXPrLq6JM/6nC/Q+r51t/3w0Hede/7C0vZMnvVZSbU+e/qZZxcv/NiS0mv6e8ppZxSvu3lT6Tu1ncuvuaH0mj6vFvPzPrL40LYQ6Iog0QAAAIBA1yrQEmCJrkKSKnm+6LOXHRoA6AuwWoIl1JJd24Zapa3Lh1qYJcX+IMVqBVqtxybiiu/f/7NSy7JEXmnauGXbodeuuO6m4tSTTzkk0NoHum9UvZS3xHlLGBsoBQAAAACBHoJAf/DDi0qt0Aq1Pktc/Rk0JMDRx9t27Yvt56yuF9FWYA1KrEagJcHR1nATdG1fn7WYNPnoQ+nS5yX/yHPVAi00mFD9oddQEgAAAAACXWMXjugUdL5Aq3+xHqtl2Z5Ty7M9F21FVqjFuhqBVuu3359a29V7b938YGn76h4SN+UeAj0kgUaiAQAAAIFuhkAr1HVCA/zs8SVfvLL4xt4TDg0u1Os2g4cG+wVeFw69z75P79H3m0Cr+4cGCdp21cdZrdLqNqIWZ3XnsNf0GfWRRqBrEmiTaC20sooSAQAAABDoBgm0ulRIaNVdQ8Kr/61PtGRXs2hIlPX35FNPK71uAi3ZVn9pfVbv0cweJtDq6/yWM84stUSrJdvfrlqiNcDQZv5QtxCbEQSBrkmgRY+T6GWUCgAAAIBAV7ESYX/zPUuI4+ZwVh9oya0i2h9aoW4X9pq+w1+JUH2sJcTqDiJpVkSnwdPr0XRZl47oa/r8vQ8/iTwPXaCRaAAAAECgCQR6CPSGsTeMRZQOAAAAgEATCHR1TAtjTxgLKCEAAAAAgSaGFOq3nSOBRqIBAACgoiAsGt814YUJk7oPNDpGjR7zcjO+Jynfm7U0Dh8+/PVmH5Pw3HxxyNuYOOmJOuSRs8KQiM+muAAAAACtwrYjjMVN/M7dLdrX3Sn4PUjjkSwMyn2Rp7R4v2e5dMyi2AAAAMg3q8PYlBNJRE7Tm0bNhqFZMXqQaAAAAGi1DKhvZzcCjUCnII3LnUR3t3j/1Rd6X1Du+gQAAAA5osuJ0LwcCRhymv40apltLbfdmQCJ3oNEAwAA5Iubw7g2ZwKGnGYjjUmS6L1INAAAQD5Qxb+zhQKCQJPGWtDA140uCi1Oi/pm7wpa3zcbAAAAGohmMlD/zZk5FDDkNDtpTJpEJ2GAIwAAADSILWGsyKmAIafZSqPEWV051iYgLZci0QAAANlELWUPBa1vsUOgSWO96HQSvSYBaUlK32wAAACoE9ODcteN3hwLGHKazTRKWNX6uyIBaUGiAQAAMkIrVhtEoEljM+lxEr0sAWlZ5yS6g6IHAAAgvVwVlAdb5V3AkNNsp1ESvScBF4pJGuAIAAAAQ2C2E57uBKUJgSaNjWKak+gFSDQAAAAMBVttcC4ChpzmKI2SaPX3n9fidEicN4exgaIIAAAgPbRytUEEmjS2kllBeZXAWS1OR5JmCQEAAIABuCAoD6pK4kAmBJo0ItEAAACQKGy1wRkIGHKaY4EW6gutPtHTEiDRj4axiuIJAAAgmWwNyiujIWDIad4FOkkSnaSp9gAAAMBjeVC+XZzkkf8INGlsNkvD2BW0fqltJBoAACBhqMtGUlYbRKBJY9JY5uS11RKtLlbqm72IIgsAAKC1aLDgjpRUygg0aWwVq1w+afVS20mZrxoAACDXJG21QQSaNCYVzYbxABINAACQb2a7irg7JelFoEljq1kfxpag9WMFZoax3+Xhaple4flOt53uAAAAAPqly8nznBSlGYEmja0mSUttD2a+ao1zOBjz/NSg3KquwZJbBynkAAAAuUPLBKdtgQYEmjQmRaLvDJKx1HY1Et3hhH97zGtzXQTuYvraAAAAAGLRaoOPBslcbRCBJo1pIEmrBM4PyrPoVJqvWuMczgrKLcz9cXMY8ygeAQAAjiTpqw0i0KQxTRL9kBPUVlNp0Re1KtsqhpUEWi3qa8NYSfEIAAAQX1GqEl2ekPT0BuU+l9XOr5sXgZ5S4Zjo95sVxLc07m7i7zW7nwuwzn5e253BPJWkBU7iJHpxUB74qNgbI/s6pza49wEAAEAMy51AJ2G1QbWM3ekqbvXNnN5EkZ0XVG41tEFirRI/fX+lebk3O1G7Oeb1ZqRRrZTqsrCyQvo0MHVTULklc3dG81WSJFppqLRyot8CvcZd6Fzq8t9KF/MpJgEAAA6TtNUGFwblGQACJ9HVyEc9BExTdan/9/qY13qdpO5rofitdmmICqr6q9u8v9OcqDY7jVvdMarU11bHdEUOBTpw5/LeIBlzM1daOXFG5P8u91vO9mIaRSUAAMBh+XrUSWvSsH6kzWqBttbbOIGe7kRoa4vETwKzKqjcwhu459VCPacFaZQgquVyoxP9SvuQR4G2C5ukLHCyPEjG8uMAAACpxaQnaXQ5Wa1WOGoVsCVOQGdXEGijFQKtY/GAE/g1ToA6KlwMzXQXHc1Oo5+enQh0LLbAyayE5PskrJwIAACQOtRSuccJWpLocZX7YESjVgHb68RZXSTUT3R+ggR6WnB4sNcOd2z82+nqerK0nzQ1Oo0a2LjC/a9+2o8i0BUZzAInSDQAAEDCSPJqg+ucKJo0VjN4qV4CNjs43AKtVtUZCRBoH78Lx1zvfy0hrUFfGsy3ugVp1F0MLbSh/tdL3HM2xzACfeSFq1qipycgLWudRHcEAAAAUJXwXJXQtKkFuteLalrI6yVgatm1abu6nZT6XNpi8dOxsBbDTu/YFJysTm+hnE4Jhn43Y3fO8l+luZmbTZKWHwcAAEg0GjCoFt4stTqxkAppTBta9XOvu/BAogEAABJMr6u0Z2RsvxBo0phGKk0r1wqJVv//DRSRAAAAR1aSSVptEIEmjXkX6CA4PK1cd4vToS5B6g+9hqISAACgb0W9NaP7hkCTxjSTlBkxkGgAAAAPW21wSkb3D4EmjUh0/SRaUxGu4icBAIA8o8GCukV8QYb3EYEmjVkgKYP5elyZsYyfBAAA8orm58364CAEmjRmgSTNiIFEAwBAbtFiG0lcbRCBJo0IdGWJVleOtQlIi7p8adaeRfwsAACQF7qdPM/Owb4i0KQxSyRpMN80V44s4GcBAIA8kOTVBhFo0ohADyzRGsy3AokGAABoDrrlmrXVBhFo0pg3ktQPeWYY+4N83NECAIAc0huUp6ybnqN9RqBJY5YlWq2/ixOQlllBuU/0LH4WAADIEjYAKW8j5xFo0phlktSFAokGAIDMcWkYW3K43wg0acyDREtc5yUgLfOD8l2uafwsAACQdrK+2iACTRrzTpJaf9UavgeJBgCANGOrDeZ1lDwCTRqRaCQaAABgUGi1wZtzvP8INGnME0kSV4232BWUBzsCAACkhnlOQLpyfAwQaNKIRLdWonci0QAAkBYGu9qgZumY08/rqgBnINDIKWlMBUuC5LT+LkeiAQAgLWwKBrfa4Mowtvbz+mb3HgQaOSWN6SBJrb9aelzTaHbyswAAQFLRwgpabbBQ5fvPCmN1PwKtivhaBBo5JY2pY5UrC5Igrkg0AAAklt5gcKsNqjJTa3VHBYHWdtYG5a4gCDRy2gx6XNp0Hr/g/j4e5Gf5+SyL61qXFn5LAABIDGpxfigY3GqD69z7JchqqYoOPFJlN8+9Z32Qjn6MSuPTnoD9ooXi91jCZMGWf7Y0PpXQ31Bdhv45jGIYv3fiBUNH+XxLUP1dqUaWURtdFPhZAAAgCaxw4jEYJMYrXUj+5kdet9ckz2qhTstAwn9y8qX41yaL39Pue59OqPht9Y7N0wn9/TSTxE6Xxv1hzCR7V2R5MPDdoSSJKxINAACJQYKhRRRqWW3Q78IRvb0/O0hXFw4J2EthvB7G9U3+3l848ftdQsVPaXzWpfG7Cf39JFbPhPGHMB4le1fk2qD6Ab46pneGsSEhv+/mhKQFAAByivo25nm1wUoV9IEwngtjYZO/9zdO3B9J8LFRt4iDTT42g2W1E+glnM4VmTLIi1uVFeqWtSYh5VZS0gIAADlErVDrOQxH8HdBuZV1epO/V+L3asLF7+oWHZvB0Osugpi1oX8GI9C+uF6VgLQj0QAA0BJstcG8SYb2d+HwQuHK8eO7No7vmvDTzrFjfzWms3OfxegxY54Nhg37o/9cM8L73t82+7uHkMZ9SY729vbnk5q2seO6Hh87duzngtYPEh2sQAsNJtVdq2UJycvqprOqnhttHzV6R9LPbyJ/ccwb3ng/2gLQerTaoPo9z8rJ/vaMGtX5XydNPuqnbW3tf5h1zp+9dNFnLyte9rXri+tu+2Fx09aHiw/ufKa445cHDsXSS77Q53Gz4qvXf7cl3zuYaNWxyUJsfeSp4hXX3VScNee9r08++ph9xx5/0ltTJtBJk+i6p0WywrlKJC26j+o5iLoAtB4NCFqdg/3sOmbKcdd0jBr1yrwFH/7DmhtuLW7bta+qwqra9+UxODb1iYs/f3lxQvfkl3t6e3tbKJ9DnR1nqrsIT8L4ibpKNAJNINAAEIf61w5mtcE0Ujiq59i/HdM57sU/P2/h6/c+/CQFMJFYiX7DlOP2pDQ/au73PQmR6ClO6Bch0AQCDQD1Rq1G+4IjFz3JEj2hkPz8T/7Dn762ccs2Cl4i8fHO/zj3j+FJe0lK85tasDXfdhK6g9VF6BFoAoEGgD6tskF5tcGlWd3BMV1db50wqfvAJz/9OQpcIjVx4x33FN/Ye0KaBwjNCpIzpqJmiUagCQQaAHw0WGhzVnfupFNPu3B814RXNECLwpZIU9z/2K+K4bn725RnwTmuJToJ0xvOdGmZjUATCHT9KU1lFZQHUq1va+t4sL1j1O4w9hJEMyIspH/ePmrUN5vUanNWUPtqg4nlxGnT3tE1cdKrasmjoCXSGOPGd+3PQFZc4Fp/k9BFbMit4gg0gUAfiUbqLikURm4dUSgcPP3Ms5/+5Kc/9+yXrr7+tUpTWRFEo1qcvnHT7cX/8peXvB6K30ujRo/+R3d+NupicVcY87Moz8dOnTplwqTug5phg3OLQKBbzgUJuliPSnQHAk0g0IOjS7evQ3F+4U/fM+/pr63b8BrTMBFJkukLPrbk5ZEj23/doEpnbRjrstqv+9jjep9mXmICgU4Umk5uZwMbBQaDGg40cPqyMP6tGolGoAkEujxoatnIkW2/m/O/n7uPqayIJMdfLF/x62EjRmwL6judVaZXG5zxtrNuOWfu+zl/CAQ6eSx3Et3d4nT0hvFYGM+F8TvXQo5AEwh0f9011K/51NPe9lumsiLSEie++eT/GZTnaa4HttrgWVntuqF+z9+//2ecOwQCnUzWhPFACy/gp7vBhOqXXQzjtaA8ExECTSDQlTJNW1v70/956fLnOOhEmuLrN/7Dg+H5u6FO+UCrDa7KaNeNYMbMs+5ZuHgp5w2BQCPR/aEZOR53LdCS6BcGGuSIQBN5Fei57e2j9n/52m8f5IATaYv7Ht3zWzfgr1Y01/P2IKOrDR439ZRTx3dNeF39xzlvCAQ68Wx00cryaLHrwvEHJ/UINIFA+41S7aNG/56prIg0h+uzXAvTXNeNzK42+PZZ59zx0SXLOF8IBDolg32TINEdo0c/OmLEiBeHDRv2envHqGcqTTHa0THq9TdMOa5IEM2Id7/vAy0X6B7NYqAZNiiMiRwLdMG1PC/NcGUcvPFNx3OhTCDQ6ZNodeVY26oEaJVSHetb7rqvNG0t5x2RhJBEt1KgC21t7Q8vWfaZA/wYRM4FepXr+5xZjj/5tJMndR/1R84VAoFOHZ1Oote0UqAJAoE+zMq3v/Oc/fwQRM4F2lYb7MlyDTzrnD+74YMfXsS5QiDQ6ZXoR8NYgUATRGsFukf9npnKisi5QHe6z83Peu0748yzH7v8mhs4VwgEOr3oIl9zRC9DoAkEukUCPXJk+7Xnf2Qx09UReRfodUF2Vxvsw4knn7J33W0/5FwhEOj0S7TmZ16MQBMIdPMFemp7x6gXmMqKyLlAq9V5V5DR1QajHH3MsS9xx4lAoDPBNCfRCxBoAoFurkCv/PDHljzPD0DkWKB7ggyvNhhHx6jRrz+48xnOFQKBzo5Eqwybh0ATCHSTBHpMZ+fPmcqKyLlAa8aNlXmqbTvHjmMGDgKBztjYYCfRsxBoAoFuvED3ju7sJDMQeRZozfX8UJDR1QYrUW1hQxAINBKNQBMI9JEse98HzmfqOiKvAm23PafmrZZFoAkEOrMscH2ipyHQBALdIIEe3Tn2dqayInIq0Gpx3hHGkjzWsAg0gUAj0Qg0gUAPkXHjx/+EqayInAr06iDjqw0i0AQCnWvUOLArqPOiUAg0gUCrBXrMmH9nKisihwJt/QS7EWiCQKAzjBZZ2VlPiUagCQRa97ALI19mKisiZwJtqw3Oy3OtikATCHRuWOW6q3Ui0AQCXSeBbmvvOMiBJ3Im0OvDWJv3GhWBJhDoXLEmjAfqIdEINIFAl/pAdzEDB5EngV4Q5Gi1QQSaQKDBY10YW4Iap+xEoAkEGoEm8iXQU4KcrTaIQBMINPi9NsPY6KKAQBMINAJNEP0JtLU2bw5jBXUoAk0g0DmXaM0+tAGBJhBoBJogKgn0iWGoQv2/ghyuNohAEwg0VBhI/YDrF41AEwg0Ak0QRwj098LQ/6+F8THqTQSaQKChj0RfhUATCDQCTRC+QP8yjBedQB90/Z87qTcRaAKBhhKaG1pzRC9DoAkEGoEmCBPo58L4g5PnvwvqvBoXAk0QCHQeJRqBJhBoBJrIaNz78JMS6NfdYBnEGYEmEGiozFR3h24BAk0g0Ag0keO45a77rAsHINAEAg0DMy2MPdVINAJNINAINJGPaewAgSYQaBiYGUF51qJZCDSBQCPQBAINCDSBQEN1zHLdOUyiOxFoAoFGoAkEGhBoAoGG/pnjWqI/EcavwuhAoAkEGoEmEGhAoAkEGvrniqA8f76mAl2GQBMINAJNINCAQBMINFRmSlCeArToYp/fCo1AEwg0Ak0g0Ag0QSDQcCQnB+WVCl8N42W/FRqBJhBoBJpAoBFogkCgYWCRPtQXGoEmEGgEmkCgEWiCQKBhYLTYyjQEmkCgEWgCgUagOU8IBBoGCQJNINAINIFAI9AEgUADAk0g0Ag0QSDQCDSBQAMCTSDQCDRBINAINEEg0Ag0QSDQBIFAI9D1i01bHy6uu+2HFeM73/tRJvZT+3Lvw0+SzxFoBJogEGiCQKAR6Nri3PMX2sIPsZHWfb7sa9cXP7Pqav/cLj1HPkegEWiCQKAJAoFGoOsWP/jx45kRTf1Wn/z052iBRqARaIJAoAkCgUagWyPQEtAHdz5TvOK6m4rfuOn24k9+sb/0/K2bHyy9X/GtDXceet4+s23XvlI3EL2+5oZbS4/97eozeu3ya244QnD1Xn2XbV/pi6bLtq103f/Yrw6ladLko0ut6/o/TqC17a9e/93SZ6+7eVOfdKtbi0LvV7oqfTcCDQg0gUAj0ASBQCPQFQVaz79j9nuKY8eNL44eM6YkoPM+dGHp8bvf94HiOXPfX2xr7yi+a857+3xm7rnnlQrpmWe/s/T6W84485Cs6jWJrrZz+plnl16XUOu179//s+LRxxxb7D3xpJIIn3zqacURhUJJlPW6tqHP2/efctoZxa6Jk0rCvHDx0tK29L36P9qFQ3Ksbb+x94RDadD2tz7y1KFuLdqenle6tR1t78Y77kGgAYEmEGgEmiAQaAS6eoF++6xzSuIqeZZM6rmNW7Ydeo/kVs9ZS6//GT22z6jV+O5tTxzx+fM+srj40SXLSv9Lqn3ZVmhbknjr4yyplQybUOt1E/hoFw5/vyTLep9tWy3XEvUPfnjRIYGObnvqyaeUnkegAYEmEGgEmiAQaAS6aoFWdwb/OesyYZ/VoD29z7o76P+VX7nuiO2oW4Y+K0mV9Korhb8tC3UZse4WallWK7VahPWcWrzV8uy/X+Ju26kk0GrZ1v/q0uF/VmlXy7oJtOQ9OtjSvhuBBgSaQKARaIJAoBHoqgQ6Kp0rVl9zqGuF5FP/RwU6ui3/OfU9lqjqOW1DMm3T5kmG1SKsrhaBmw1E/5vE6u/HP3Vx1YMI7Xu1D34aLST1er6SLCPQEEMHAk0g0Ag0QSDQCHTVAq3WaEmvWphNRqNyOpBA+y3H2p71O7buGhJybdNalX2J1evq5hFNv/VTriTQasnW/za40L8YUIs4Ag2DYF8Yd4RxKgJNINAINIFAAwI9oECrv3L0eCy95AtVC7S2pQGA/swY6sqh19VlQ3J+8ecvP/Sa+iGrn7JJrORYLdL+rB4aMCgJ1/8aIBgn0NqOJN36Wsf1r86xQK9wZUB/sSuMrVXExjDWDxDrwlhZRSwNY9EAsTCM2VXE9DB6B4gpVRYJe4PyfOmvh/HLMP66Uqs0Ak0g0Ag0gUAj0Aj0oS4Pkla9X0KtgXZ67pa77htQoCW+er/iki9eWZJlHV8bqKdBfhJd9U1W67C6ekiK1SrtD/zT83rPhR9bUmpBVrcQvS4Z1vZMlP20aDo9Cbq6iGjbNruItUrnWKC7qpDL3ipFdX4V0ruoSoFeU4WMr69S7HdUcZGwO+hncSEvfuf9/7L7+9swloRRQKAJBBqBJhBoyIFAS0rVahvt3mAtvnH9hiWhkkuJqKT4os9edqgfc9y2/Of0fWq11ucV6sZhM2NoAKG2peclx5J3zYqhz1ursz7vv8f/Lg0W1HM2jV00Lfpfr+uzn7job/q0hEuwo+If9xxdOHKPSfSWMJaHMcddhNACTSDQCDSBQENeBJrIZyDQQ6aLQYQEAo1AEwQCjUATCDQ0AASaQKARaAKBRqAJAoEGBJpAoJMh0OqTZ/38+GH7hvpDanoqf0qrVg2ysv6XWkmtlWlBoBFogkCgEWiCyK1Aa+CMLRZgoZHmNpI97+KsQUoaXW/HxkbP2yplTRbMQzMRROeZrTU0n27c4C0EGoEmCAQagSYIBNoLjVyXEGokubVmqmVT0yppZa/oqPc8hUbja05XTV31rQ13Hhqdr5H/XRMnlZbybaVAa87aegpvELPSGwKNQBMEAo1AEwQC7YWmTVLLqqZQiptOSvOSRif7V6u0JEtTNPnPb33kqdJn1L1AXR0U/gID0W4Q/pRNep9E3QQ17jn7X3+j368pq/RcXItwpfTq+5VeWzksmlaF5obVxUVclxZNJyXh3LhlW5/903dVSosdG72u4+W/posWvS4h9lv+bZv+vLYmuX4XDm1P4X9HXBp0HCyNfvcPm3/3iutu6pM2/a/3apv+9uy30F8d/3p1+0Gg8y3QOp80j7Lyl6JV3cmUNyyfKU+1uusWAg0INIFAJ0ig1fosafJlNirFvnipW4ekWgsHSCw1f6kJrv7XsrY6GAq1XqtbiImrJEuf1cIDatXV5637QXT53bjnArdggrah7erzWrxACwvosX2f7Yv+6nssvbpQULcLS68+pxZkfS6osHCDPmcrg8VV9H6Fqu4P2pb2Ty3W+l61EPv7o0UZlEYdR2v1908ipc+6iUik1dKt7Wh7+tzcc8+r2IXDFlnQc3Y81Epugi/5tbsK2i+lRcdEc8vatrRtf4U0HV+9R/uk15UWe78Jtxab0F9t178AQqAR6MGGxNnOXZ2jOu90bvnlTLPCyh//XK/X3RmVq5XKFQQ6cfQEfRddidIdlOdf1kqFXQg0QeREoCWkEqZq3isZPf3Msw+1QkrMJGjWeq1KTlJolYzkUtvWQgF6rApDK4VFhdNaWKsRaKXBvt9kUtsxYdb21Gpsgqylce39aslSeiWF9rqtGqbviGut1fY+/qmLqxp8qX2371ZIjq31WhWm5NOOhXW/UPolyXYSSR70foXSo8/Y8ZVA6Pj1J9B6zVZB0/E3KddjLRah38OkX9vTBYR+07guHHb81T/eXldadEx0rE0qdIz1ONrCj0Aj0IMJXWArv+gc9+8G6XxWPo3eCWumQNe7BdrfNpF4gV4QlFcYrCTQDwXlxUu06uEWBLr5d4miobKkUXeLVNdxNwqBLoXkqprES47iWmAkZZI+Ezi1Gvmvq0XalsWVrKlFSS2YUVmtVqDVYh4V8GiLsa1Wpver37L/ugTW0miCPVCf4GoG6Uks1Uocd2LoNUmoJCDaTUTfrwsBe68vCSbY/rGSiPcn0LrA8LfvL/2rz/qSa4Mj/d/f37aOVXR7km5JvS4U7BhbizR9oBHoWkJ5IXq++flL+dZvhVbeVvmjMiHuDpoqUS3NrXPVP+9NhnX+6zXlM9uuZF3b1N/+BFrb1nfqwlkXykpDtEuWHivfK3/qot3KMW1D32t5zdKuNOhiWt+vss3fJ/2v79Q2tE/qToVAN42rwlgYlJcG74i81unE2diFQDf/IrdSqNGu0t31oYZ13Wz1WCEEOgECrcJfYlfp9qgJX5zg+idTVNZ8gTOBVquqKkG9X9+pCtPkq1qB9oVY3x098Pp+VVj9ZSz7jN5raasUEn5rwe3v+MTtu/8dSlPcSeJ/LjqjhirSuM+oFb2/LhyVtq/fWBW3ChXrAqMW6UoCXen4RC9S6l2QIND5E2hJpc4l/w5Of3lOZYcu5HSBrjsoupD2L+T0vMqYd7/vA6Xz3b875Hc98s9/vde2qS5W1n0krguHPqPv1ef1Of1VWGUtAVealE7loaknn1JKj8RX0q3ta3vKY3qv9l8XD9qGvl95TGkxUVZZZ92/7DsR6KZxZxjXum4a22Mk2rg0jNUIdPMFWo1zNmZCoQta5RG7a41AI9ANEWgV0DoZKrVoqAJQRaPWD70vOqBHJ6oqhmoE2h+sppNQFZt9d7UC7Z+0/Qm0pVfCrc/7oYF31Qq05DmuZdkG8Kli1qA73Xb2u0L4sqnXdKES11VGlaW1gkcFWq1WqkSjn9HxHopAq0VZadB2NSBRLXBRsfePsQqluFlGJBYINAJdz9D5GHfHKC50YamLSL9lSS3Uyis6p5UflS/9wb0qK/ScWqLtvLW8by2//mf0nPJlfwKtfGB3h2wwtnUPs3zv37nR+9VlLq4Lh57XNv19UvokzPqsVdp2By5uwDMC3RTWRFqcjZVhrAujgEA3X6Ar3SU2ia7ndLwINALdJ9Qa4vct9m+RWqGt11RBRFuIJNcmjgMJtCqk6EA9tdLYSPvoiW4DHIci0JZe3e70X1elZpVYNQKt1iJ9rz/Yz5dfVdq6Vatbrvo+vwKUYOs5vWYXKtHZNSS01sc5KtD23boY8G9LDzSIsL8W7mg/Ul3EVBJoHTuJin9e2O9k/cYRaAS6ngJdzbmkssofS+B3bVIrtPJl3AA9ncs6p+28tbECVi5EL4Al1f0JdHRsRDT/2l09pU1lqS7ErbyJCrRei86EZHlNed4q7VbMO49Al8TYWp1vDmNGTBePS9NQduRNoPV8tFyxO9TK3yonVCdavW3duiTe8hW95l+IVxJofU4X9go/j2q7ytfyJDVG+VMF+9szB9L3qowwT5ADqKxTWvSayq8s9r1OtUDrh5PIqRDXySXhU8urWmT0o1lFoJNDwquTQBWCDRq01uuBBNoGLEqM9XmdGNqeWoX0HWptUcuNTiZ9h80KMRSBtvQqffqr79P367FVnNUItE1lZ7d81cKkxzYLic2yoRYh3abVxYieUyuYWpz02FqL9HmlV/0ilXn1/Toe1ncyblEUyYDeo88o9BsNVaD1W+r9kgz7/XQB4LeMa5+UTr1HGVX7oH3VY/t+bc+msEOgEeh6zUoRHeMQnf7SyoFKiwcFbiadSvnayre489a6WsSN+6gk0NE0+M+p0rUZb5SnrHtGJYEOKswCZN/pd5VDoJvO4jC2hrHJdeWwgYVXOZk+6F63QKATINCqo1QWKA/64xOsb7TyZeAWRVNjl/K7OYcupiW8qh/1ul8uRQVaZZPe7/uA3d3Xd6sRTdvSd+p/myjAdxZ5gr+QnfzB7qQpTdYoYN0uo+MtEOgWCrR/paQfUyIoiYor0HWC6DW9Tz+qf3Um8Y621Oo5myVDJ7ReV2UV93mdVHpOr0ngdfWlCsdOFv3vn3g6QaMtqtq+3xfST68E0q809V5LWzUZ1fomqjJUWqJXphJOO4aK6NWo9l9XkLb/SrufEfQ4OiBPn9F29H5lIBNfOw7+Zyodf3tO36WWNtuWLga0D9qeXdXqN1f6rEDSZ/QdNuBS27ILAr0W/U0QaAS6ljthyqv9TbepSk7lUzTfK5/pdeX3Sl2PVPHo/I0TaJ3H0QHFdrdnsAJtjQHaF+Uvf9rMSgJtaYt2dbMubgh0yym4AYOpJqsCbQ1nFqrnVE5IYH2pNYFWqP5T3rSFyEyC/W5kNjbB797lC3QledbndMdLZYJ1GbULa0m53y9babd+3Nqe6lNrkFMDlj8+zSYWkBMg0AkSaIJISiDQ+RRo3eGIa4m1O2Q2cE7yrNZk/+LUVlPVRZ0qRlWcfsWlu07WHSJOoG2WHP8zukM2FIGO6yJiU2xWEmgbuOhXlqokrV83Ag0I9OBn4dCFrD8Nqwl0tAyV2Or5uAkD7ELauo1ZXpRoW6twVNLVcBVEpoC1UNevwFuAzQQ6OkGDrf3gl0nWrSNrXbkQaIJAoBHoGkNyrMpEd3okm5Jmia3k0vopSpLVMqMWGmttskWVrDuVWoVsCkl13ZK82tiHOIG22732GQmtWpCG2oVDaVMFqDSpZdkWV7KLABvfoFZvG0+giwSb4ca6x9kdMgQaEOjKAq28HZ0sQIJrs375F+XW2lvNxbs/cN+6QlpetFl6oq3ENqZCr+mv3zKu0Hf7cm3dTOIGS1sXE5Vn+qy1ltMHGoEmCAQagY4duCuJlUSqAlJfwGgFpVukklO9R5WnP9DWhNjGcqjlyBYrss9aS3H0M2rJtmkn1UJkQhz9jMZBRPv++89J8k2E1eKkridKo99NQ/ultPsLT+l1+4xfWer/auajR6CBPtB9u6ZKQHVB6wt0dLyD36pcSfCiAm13lOK+355XC7KtzBwNa7W2C/dKd+V0kS2Bt1Z1CXu9u00i0ASBQCPQBIFAAwJ9KGzNg/4EWhezlQYx68Jan7cxEibQdmFvEwr4F7zW/asa0e1PoP0xHrpTZWJe77mtEWiCQKARaIJAoAGB7jMVpC+ccQJtXbTiBh/b+AmbtjI6C4fuVEmg/TEM9p7oNLp2p0p9p02u4wRaLecahBw3fa51S0GgEWiCQKARaIJAoBHoIQu0hFj/+yF5tWnpon2g46a5tEVXfOmV5Grsgk23W2keaJtv2kRe4zDU8q3P+YOJ9Rk9p3TZbFaVWqD1vRqH4c/2pf/1+bhVjxFoBJogEGgEmkCgAYGueRYOyWZ0yrdKAq1uEjYjhk1Bp/81E44/hiJOoG2lUb8rh+Tb5nbWAGH7P24e6LgyXWMm9N2BmzXE0hP9PAKNQBMEAo1AEwg0INCDWr/CVvLzQwP0JKBxS97rdVv8LS40kNBmzNB7oyv/qSVaz/urDpswR7et71dfaX970TSphTo6DZ6FBiOrX7Z9XoMK4/YJgUagCQKBRqAJBBoQaCJHgUATBAKNQBMEAo1AEwQCTRAINAJNEAg0Ak0QCDRBINAINEEg0Ag0QSDQBIFAI9AEgUADAk0g0Ag0gUADAk0g0IBAEwg0Ak0QCDQCTRAINAJNEAg0QSDQCDRBINAINEEg0ASBQCPQBIFAI9AEgUATBAKNQBMEAo1AEwQCTRAINAJNEAg0INAEAo1AEwg0INAEAg0INIFAI9AEgUAj0ASBQCPQBIFAEwQCjUATBAKNQBMEAk0QCDQCTRAINAJNEAg0QSDQCDRBINAINEEg0ASBQCPQBIFAAwJNINAINIFAAwJNINCAQBMINAJNEAg0Ak0QCDQCTRAINEEg0Ag0QSDQCDRBINAEgUAj0ASBQCPQBIFAEwQCjUATBAKNQBMEAk0QCDQCTRAINCDQBAKNQBMINCDQBAINCDSBQCPQBIFAI9AEYfHgzmcQaASaQKARaIJAoBFogqg2Nm19OBToCXvJ3Qg0gUAj0ASBQCPQBFFFrLvth8VJRx39KLkbgSYQaASaIBBoBJogqojLvnZ9sefYKXeRuxFoAoFGoAkCgUagCaKKuOizlxW7Jkz6BrkbgSYQaASaIBBoBJogqoh3/Omc58NTeyG5G4EmEGgEmiAQaASaIAaIbbv2FUeObHslPLU7yd0INIFAI9AEMYT4x517X0CgEWgiP7HmhluLXRMnbSdnI9AEAo1AE8QQ4x/ueejfwtN7J1UdAk3kI9577vm6aF5CzkagCQQagSaIobdG7QhP761UdQg0kf249+Eni21t7S+Fp3UPORuBJhBoBJoghhhL//oLD4an93qqOgSayH7M/fMFzw0vFL5ErkagCQQagSaIGmL+BR+5Lzy9V1PVIdBEtmPjlm3F9lGjfx+e0l3kagSaQKARaIKoIY6Z8qZ/CpjOCoEmMh9vOf3M/eHpvIwcjUATCDQCTRA1xD/9fN+B8NRWMJ0VAk1kOP7z0uXPtbV1qLtWgRyNQBMINAJNEDXE315xrVqft1DNIdBEduPL1377YHt7x78zcBCBJggEmiDqEG+eNl39n5nOCoEmMho33v7fX2tvH6WuG9PJyQg0QSDQBFFj/Pft//Lvw4YN+z2tUgMzvmvC65wzRNriq//3LQfb2jqeDU/hueRiBJogEGiCqEOcdMr0B8LTeiVV3MB0TZz0kpY/5rwh0hKL/+slvx0xorA3PH1nkIMRaILwY3zXhJJEDxRTTz7lOQSaILy45c77tPKgKlems6qCN5049ada/phzh0h6fP/+nxVPe9uf/LJQGLmdu0sINEHEhXy2qRkBgSayEpMmH/XTgOmsqqZ99Ohz3zDlTbRCE4mN+x/7VfH9C/7TnlCcf+vuLDHbBgJNEAg0QdQr3vP+D2rZ7q1UsIPuxnHHe889/wUkmkhSfHvj5pfe98EL/iUUZ41nWEOrMwJNEAg0QdQ5LvnSVzRt3W4q2SHR2d7e8f91T+559sq/+/uDd297IlvnxhevJI8k+Lg+uPOZUveM62/5/u9WXH7NU/9h9ruf0ADBYcOH/8y1OE8liyLQBIFAE0Sd4+9u/m8/HzZs2DMB01nVygXhcbw3jJfV6tfeMWpvhfj1AK8nKsL9eS0J6QiP2XNpOWbNOq4jCoUDw4cPfzE893a5u0c3uy5YvWRHBJogEi/QYUH2AgeeSGN88arrHg4r4F8HTGfVTOaFsTlF6d1NOtgfQKAJBLr+At3e8Rv6PxJpi3PP/4iW790TMJ1Vs1kdpGuaQASa/QEEmkCg609bW/tdX1v3/7zCwSfSEN+776fPHXf8iT8JT92HAvo8twId99mIHgJNVkCgCSLXAh0yr2vipF/TCk0kezqrPS+/c87c/+G6bDCdVWvoCONAGJ2IHgJNdkCgCSLvAi3Wv/M/zv03JJpIWqy77Ye/e8+8Dz4SivPvAqazajWzXQs0oodwItAINHUUgUCrRWnYsGF3d03q/s0V1930fNamsiKSHzad1bc23PnS56/4xp63nfXOn44c2SZp3hEwnVVSuNRdxCB6CCcCjUBTdxEItEdpKqvhw4e/kqapqoh0h01nFca/BkxnlWQ0+8Z8RA/hRKARaISNQKDrT1cYi8NYHpT7TAJANtjv8jeih3Ai0Ah0zcKz9K//trjiy19H/ggE2nFWGNvD2ORaEQEg/Wi6wJ2IHsKJQEO9BPptb59V7HnDFOSPQKAdS8NY6ypcFbLMlgCQfixfI3oIJwKdc4YPH/70iBGFPbVGuJ3nw839ccSIEXvqsb2sR3i89nMcKkehMHJ72vPWtcHhlmf1Z11EcQOQejakNC8j0OwPJNsXduEJ5Ds4LM2z3f9zgvIsCgCQbvYG6RzUiUCzP5BsX7gYTyDfQRn1Qen2HitjzOewAKSWXifQVDhUfFTk0AhfwBPId7lHi1zsizy3wF1lAkA60e3VDVQ4VHxU5NAgX8ATyHe5Z15QnivWR4MI1cdpNocHIJVo8GBaZ9RBoNkfSL4v4Anku9yjuZ/jVipbEpSntQOA9KHp62ZQ4VDxUZFDA30BTyDf5Zr1QXkRlShaUGVviithgLyihVPSPLcmAs3+QHJ9YRGeQL6DMhoIMLOfq831HCKAVKGBPZupcKj4qMihCb6AJ5DvcsvBMDorvKaWLA0Y6OUwAaSGq8K4lAqHio+KHJrgC3gC+S6XTAsGXup3dRDfRxoAkslDQboH9iDQ7A8kj+n9+AKeQL7LHZqGZqABAJq2JjpPNAAkE7UOHQjKfROpcKj4qMihGb6AJ5DvcsfKMFZV8b617r0AkGxmB+UWaCocKj4qcmimL+AJ5LtcscldVQ7E1KDcx6mTQwaQ+EpuNRUOFR8VOTTZF/AE8l2uUH+m6VW+d2OQ3oUZAPKCZt9I+/K6CDT7A+n0BTyBfJcL1EdSI2oLVb5f8zzuHcT7AaC5KG+q/3MXFQ4VHxU51JFO5wt4AvkOgvJcjjsG+Rm1bi3i0AEkElVej1LhUPFRkUMLfQFPIN9lHp3gNw/yM7ODgae9A4DWoFuna6lwqPioyKGFvoAnkO8yjxZbWD6Ez20P0t/HEiCLqP/hBVQ4VHxU5FBn1gzSF/AE8l2m0W2WeUP4nDLFQxw+gMShvoe9VDhUfFTk0GJfwBPId5mvbKcM8bO7gnSvdAaQZLQowaJBVljTWlxQLwgq93ssuAp4IOaEMReBZn8gkb7QgyeQ76C8WtD+Gj6/qMoKEQAGL88POZHUogUrBpEnN7QozboQ1+DFuEUUel1ZcSDyfKd7zR+tr9vEXQg0+wOZ8AU8gXyXSWYHtd1e6XBXpDM4lAB1ZWpwuNVGFdcW979aZ9cH5X7OZ8V8Tq8taUF6Cy5NCyoI9HQnyn4lstB9Rgu+bA8OLzu+PmEVDgINUC6PHsATyHdQZmkY19a4DY3438ChBGgYmlFjsZPU9e5vT4XKbGeLKioNLJrnKtmVVVYiuhhQC7S6nWx1gt3rfR6BZn8gOSyrwRfwBPJdJivmpTVuQxWglu2cyuEEqCsS5XURIV3gntsQUyBLqve3KK37nNzrVq3miZ1VRSWy0r1/pSfQS7zPItDsD2TDF/AE8l3meKifim4wrAxqb8kGgL7yLEn2B+SpG8QDrjKK6yOsEe93tii9vS40fd4al0bdup3RTyWyy9uXXe7zNweH+0OnWaC7gsatwqauO7Nd9FR4fUFwuEsMFTnUyxfOwhMQaCizP6jPcr/W8tXDIQWoCxe4PLXVhaRUfaF3uIpIrdDR1Qb1nktbnO5pweF5X7tj0nOV9/88d5Gg55a6C4RLE1jh+Omw/pz2u8yoUB7uDBo3leBOdw6sjPl+teDrTsDioHJ/VSpyGAoHavSFvHsC+S5D9LqKoF6o8l7FYQVoKB39iJkG4p2VoX1NokDPjFwExF1ASFx3RX4nzaaiuwm13vHTRYZa8aYE8S3MuijpdP8/5P1PRQ61+sIePAGBhsMFbT1v96pv076gPi3aADA4JEoHgsq37alw6pOOpU5MN7jojPkd1HVjqyfQGkClmUZmu89OqyEtmrlkp5N4XTDNqFAW6/vXU5FDnahX97A8ewL5LkNcGvTfkjIU1H9xOYcWoOnMddJEhdPYdEwPDg+EWuYiDl+gO9zvY/I9u4a0dHnSrllM1lZ4n75TUx/OoiKHhPnC+px6AvkuQ0QHKNUDtYbsDbLVCgaQBtQfdjUVTsPTMSs43Ho2tx+p8AVaXS5021rda9bUKNALg8OtzrODIwdlrfQEW+fDBVTkUCdfWIgnINBQZkfQmPliNwXlASwA0Dy2OqFLAr1BfGt4jysfFMtSKtA2SE+tv2rhVYt0d4xI+wKt/VVjxXy3rVp+J/XB3u5EWX2tp3qCE7jvudPJ8wMBfaChPjxaZ1/IoyeQ7zKC+ui92qArwNlBeQBNgcMM0LT8XOsI+XqmZUOFyuJSTx63BIdH49vAyK4UCLSVcRJV68ssSV0Qec9cT167PYGe5qS7FrTdWRE5nhK5UJnZTxlMRQ6DzdMH6+wLafCEme5i2SLKBa6suzmoblwD+S4jTA+OnAKrnjwQHJ7KCgAai7oG7EhIWlY4edzdT2Ws9NoMESqL1Fq7MjhynlkWUmF/oPXMaJAvJN0TLnUXvr3BkTMfdbv0F9zFqz9lZId7nXyXUdRasrGB25+foAodIOvUssRuPVGLzZoBKgu1Mqs/8BZX8ahimuriqiD580Aj0JA3LmiQLyTdEzYGhxd/id418iW5x138B+79aq1Wq/Q68l02WR30XR64EeiKdTaHGqDhbAriB4s1m82u0lEFciA4chEVf+7iJe519SV+wJVJayPlEgLN/kAyfGFFAz1hTkL3Wxf06iqlFnhNHRk3nkDPbfX2YZ77O9WTavJdBivcRt86WRRkb1otgCSieVWTtrqXX1lYt4xrXblg/aTnOmme755bj0CzP5A47mygLyTZE/w+3yqbZkde73EX//7z64LDLdfbyXfZRD/ktAZ/hyrEPUFjZvoAgDLTElowXxXzv1prVrsK2VrMe13ltNY9txCBZn8gcefL1AZ6wu6EesJOJ8kqt9Sa3OUe9zq53u4aAfR4invNuqTYgjHku4yhk+Fgk75LfTM3ccgBGsbioPKKc4gewolAQ62+cCCnnqA7Z7biqC1IpL9qjY/O0GGNBOrqov7PaoFeFfRtxSbfZQCbR7QZ6OTZ28CrV4C8o8J7CQKNcCLQ0CCJ7M8X1IJc6W52r/v8QFPV5cUTyHcZwBYC6A+1ag3UL2lVUF3Ll67E1nHYARpWKE/L8L6RDvYHWusL64bgAfqc+gKvcC4xkETnwRPIdxlA00z1twrYVHc1uHWAq9L9VQp0l3tvD4ceoK70uLxFhUPFR0UOjeDafnxB3Rk2VPCANZ40q0uDLeyz2Mny/Bx6AvkuA2j+1UpLyRacOC/pR6DVJ0q3dJZ7GafgMow60Gt1oTuDvlO+6LWrOPQAdUWD7rI8xgCBZn+gtWyt4AudruyZGvTfkNbjfEHdNDRt5VL3GXnIjBjpXkN5BkmmvymvVrkTfHY/Ar3eXT0u8jLObHclamwO+s5LO8V9bxeHH6BurHEXslQ4VHxU5NAoX4hbVW+dE+DefgRar2maNxuAJ4nWLDuaiWdHcOS0cFn3BPJdyukOjpxaxTjLiW/Qj0AvCA73U1oUyTjqh6nbM2pp3uNej4r3Cn4CgLqxI+i79DUVDhUfFTnUi55+fMFmn1A/Z911jnbJmOYcwh+foa4cy5xYXxXEL7SWZU8g36WcuUH/Lcv73I+sPtAHg77ruwfus3vde/ReTW+jlmetvqM5E5cGh/tFRQV6uvtsBz8DQM3Y9FKFDO8jAs3+QGt9YcsA7+kNDjekdQeHu2rKB9TYttJFrxNodeNY7F6P6xoyLcOeQL5LObr6u7aK982OiLZauaK3VfwWaL+Pc5e7Il0Us91NTrIBoHEXw1Q4VHxU5FAry4OB+yR3BIdbmTuCw3fEZkeiy70+371HrdtTKmwzq55Avks5uiKsZs5Ym0DceCA48lbxAk+aNShgh6vQNzlJX15hu5LrAj8FQE1ovMJKBJqKj/2BBtGqOeaz6gnku5SzPWh9n0lJ9gX8FAA156O5CDQVH/sDDfSFmXgC+Q7KqM9kZ4vToP7SO/gpAIZMR0LyMgLNcYXscrCFZUwWPYF8l2KmJugHVMaYy08CMCTOyslFKALN/kBrUL/mXQnwhHnkO0gC6rx/Z0LSorkgt/KTAAwJjWRfk4P9RKDZH2idL7R6kaaseQL5LsVobsXVCUlLwV3dnsXPAjBoVLEtQKCp+NgfaBArE+ALWfME8l2K0YTnSeqUvyzI9jLEAI1if1B5NVFEj4qPihzq4QtJuEhfmiFPIN+lmEeDI9eebyUaCKUJ06fx0wBUjRYk2pmTfUWg2R9oDTtdWYMnkO9yj05CjahN2ryK6laynp8HoGqW5CjPINDsD+ALWfEE8l1KUcvzowlMl1Yn0pLgPfxEAFWh5XAXIdBUfOwPNNAXkjTLT1Y8gXyXUtT3eUNC07YmyMeMAgD1KoSn5WhfSQf7A81lUQJ9IQueQL5LKVpy+9KEpq3HXV128TMB9EtvUO4PmKeLBdLB/kDzfWF5Aj1hf8o9gXyXUjT/8/wEp0/9mzRtToGfCqAiupOUp5lrEGj2B5pzYa4p67a4c+OlMJ5x/ycpDjiJ3p3SeDXFaU9CPBCUZ2Vpmid2uC/b4zJJUjkjjH8M43nKMoAj8rCxNihP/4hAI5zsD9SDpcNHjPjN+z504UM33n734w88tufgpMlHF3/w48eLO355IDGxbde+4oovf714wpunFcd0jk1U2qqNN0w5LpXpTkJsfeSpV79w5bVPv3naqbuHFwr/HJRX1m443WE8F8aTYbw/SGYL75SgPOL3j2EUw3gHZRpAnzysVpd7w/hVGP8JgUY42R+oA6vGjhv/r3f+4z8/6ySl2Dl2XHHKm44vXv2tW4o/+cX+RMjTrZsfLIbpLI4aNVp+UBw2bFjpOQQ6n/HJiz/3+/b2jt8ETRpU+rIT09fDeCqhEr3CE+gvUa4B9GGPyxu6BfiCe9yZ0X1VoajVx3Qb+Q/u7yNB35b4ZqXj6aA8PuOFjEin+o/+3h1XNVr8SwuOKySDmcNHjHj+7m1PvOzLyegxnUVX1hQnH31MYiT6xjvuKU7snlwc2dZWStuyz65CoHMcSy/525c6Okb9j2b47C6XIX4SlPsZJ5UVLp0PULYB9GGTkx7lj38LkrWiaCPQmIj/6fZ3ZwvLrR+bTGSo1Xav2x9dmKwia+WWjUv+6rP/GhWTU95yeul873nDG4uf/PTnknYbv/iW088stUCf/a45CHTO45S3nvHvQROmdL01jFfC+HWQ/BGsK1wLCQD0zRc2eGZtDvZX/dt+50Tv2RaWW1rK+EWXjnUZObY3BuU7GS8GzHyUW0IJfWHjlm1HSMmHP/7JkqB2TZhUvP+xXyVSnM77yOJSl440SN69Dz8ZXoxMKR59zLGlNB8/9WTkt05x2de+pTKs4dMtfspVACtTkrdVaU2niAM4xLyg3BVLiyHl5Zb7d4Jyq3sru3Tp9uDzLhZm5Lhe4OqDb5Ot8suIQuFAnJR8/du3lVqgk9b6HNel4+5tT6RC9I47fuqhbjFvCv9Hfut3R2LkyLanGp1XbBBSmlobmM4OoG8efiFo0sjjhDA1IeXWN13ll5WL+h53QUDrc45p7xi1t5KUqKU0qa3PaYzPX/GNkjyHFy3FCz76fyYmXZ9ZdXXqj63O42bkl8UUGblnGYcg1czJ4T7PTEAaeoPy/LNZ4gKyU27pnfKm42+YfPQxr6pPblx0TZhYrPQaMbRQt5jhw4cXJ0yclJg0SeqTeKxOPvW04sLFS6uaaaVZAg3A1FUtrriGFwpXjhs//p9Gj+n8zZjOzn1Jj7a2thdIY+e+kW1tL6bh9yKGFqPHjPnN2PFdPxs1asxfBRm++3nqW9+2evyEiX9Q9wz1f35w5zOJak285ItXJrals9a0zTrnz0rCGtfvvFWh9CTtOOvOx5obbi1+/FMXFzUf+bvf94FSP3IEmtZUBDqn58DYsWMvHtM57sUlf/WZV5NYcaV51Hgz0qhFHPIuEGlOazV9KS+/5gbN7PBa9+Sjnx7b3X1y1gr/k6e/9TsnnfKWPyZtgZS0lDe1pu32e7cXR48ek6h9SqJAR2VaLdE69pXK4MEKdO+oMWOuntQ9+eHOseN+N2581/48xvDhw1/P675zzCrvj/JEeNW6U8KalJakcV1dV5940v/2SpIrLgSafWFKrnJc9NnLihO6J7987NSpU7Iiz8e/+c3vGtM59vWkD7rLskCX5y7+AgI9hDj3/IXFiz9/eW0CPXzkyL8c3dl5YMmnP/t6mlqxKLw5Zs3aH78laWL3ZC020upBdzNHjR79h7SMFkeg2RfK4LJEH/um4/8tK9053jzt1EfScMcg6wLdrDtZg9mnNDTsqC9074knDV2ghxcKlx93wtQDaWzFovDmmLVif1QJdk2YqHkie1pVcU3uOWZrmm91I9DsS17L4Hf86bv/OOmoo/4iCwLd3t7xapL63uZVoJO4T2lwSqWx0vGvRqBntrW1v5TWVixOZo5Zq/ZHEq0uHa1qSRo5su2VNFRcCDT7QhncN3Qn6w3HHvejLAj06DFjXuWc4nxPq0BrOXktRDMkgR5RKPy/F39+9ctIIALNMRv8/pzxJ+94LmjCcp9xdIwa/QrnDGlEoNM5sHDc+K59WRDo7qN6DnJOcb6nVaD7O/4DCvTwESNeSnMrFiczx6yV+3P5NeteGzFy5D+0ouKaMKn7AOcMaUSg0xkamIxAc05l9XzXfMvVzLWcaoEuFEa+gAAi0Byzoe1Ps5b7RKBJIwKNQCPQCHRaYubZ7yyuu+2H2RZoTfSOACLQzThmmndRGare/e01Y0y9RyAP5hwYMaKwB4FGoNkXjjsCne5zSvVItNuBFtrw65dGLEOOQCPQua1oBhLCK667qeqBJR9dsqz4ne/9qDQ3YTUZNSl9jAY6Zlr9511z3ltc+ZXriufMfX9pX+v13TfecU9x09aHi5d97fq6HQ8EGkmqNo2qJLT6mkLziequRNL3RflPZcwnLvqbuk5Zqlu2Cm1Tx0EDdKrJkyofEGgEOgl5ua29o/j9+392SKhHjxlzSAT1d+rJp7S0LExLv+JcC7QKvkoFq55fsfqa4rc23FlV4iRNKiC/cdPtxc+surqqzzTiKq9RJ7MqzkrH6byPLK44x+ARUxvNfk/pb7XybO9NQ+H0ljPOLJ1Tdm7pnND/Oo+0GpD+2mNNCH/hx5aUzhcdP507qoztvZq6TaH3qKAzgVafK70fgUagm5lG5X9dJKss0Lk699zzEr0vWsZWoi85uO7mTaUL2np9h1rrFDomqjzt4mKw5RgCnS2B1oxFulizx7fcdd+hfKI8o3JdjUdWR7RSoJUfbFGSr17/3eLpZ55del5jxZR39LgVabNGKNV7b591TqnOq9f36/dRvq3WzxDoAQRaLRSVdn7ehy4sSYtOJknOQIJpQqgfv791x/2TWK2JaajYJXISN53MEmUVAhJh7bdCrTE6kaKfUyuz1mFXZlAFo8zxxt4TSp+3vzqpVbAosyjz6ljbZ+yiRO/Vb2HbVSFlv5ve26zJ1Qc6ZnHHQJW39lP/66/SrffpWKrg+uCHF5UKVBVYOpannHZG6bEKOL9y1rli/9MCHR/Kq4NdqUq/SfTuiv1ezTrGcefNUFpFdbGv/KTtTZp8dOmCrpptVyvQlud0oaf8r3NS+Vb5V/lUZabKQStXdU7rWOpcVh7Vb6P32t2Z/j5f6/FWeRV3i1rfYWnSfuj7VNZof/RXYdKjsk6PdRyVRpU1lm6FvkOf02v6X99h29c2VB/oglj/66IXgc62QOv31Tlv9ZHKI5XnemwNR6rTVO4n4W6Szmc9Vh3k16n1KpMGmzY1xul77QJDDUcmuzpmSrNNBqG0Km/peOpCRa/p/cqDcgWVNfqr/Cp/0Tb1WO/Rb6R6uR77pN+8lR7XUoFWIaoTWz+OX4DqNYmw/YjRFj+9XwWlCkZJok4+FaAmhvpRtR19zgpj/dg6URXapj6vH9K/FWpdG7R9a71MWgu0teToBFZlHc1wOoH1mip0HUNrZfYrLL81xpaYtFuiOiYSAW1DhY/fWm1CqfToOOqv/V5JEOjoVbsyqY6RFUzaL2U2O1b+RZSeswsx22e7QEGgq+sjrgpB55xVYCowdUGmY6cCVPlO+UuFst2+tONsd5ysa1ErBFq/rwp75X2lV3lJ5489trsa1oVCadY5pscqa5Qn7I6a7ZeVYToWfgU0FIFWGanyTH91nHRcrYKTZFq+tdYsa43Te1SmqaJTPtdxtorH/7zl71pav2xf4rYhSbdFefS/jpvSYWWWhMf/PWwb2g+l2/KnCXS0BVrnmn4D23ftp9UjftmHQGdXoCVuOreUV3UuWLlu9ZTOo2ruVjRDoO28lnf4F8itEmiVUboYjWvoNEHWBbfykrqYKO06rsq3KluU17QPKp/0XrmY7avunun3UcOV6olqGjkR6AEE2qREf22dcP2AqpSssNOBtkJWGUEiZyeXKitribVMYZ+zSkQ/sn5w/fB6n04SK2T1vdqentd79L+2px+4kbd5ahFoib/dnvJb++yYWAGhfdO+2H7oc3Ys/eOkbVursz5jx01h/apNBqwPpgm5CgClIykCrd/VWs+0T5IN7Ye1aOrc0u9fq0DXa9qcLAm0ZEXni4TMzhvlOVVmkjcVsjqGOm8kkTreyr92nHV+WverVgm0Jrq3Ck1pUVr1WOm2u2HaNz2n15RuXbhLXCWGKsMsv/kCrc9b/lMlE+22NtgWaL/8tPPXF2Adc/21MlXSr3Tr97DfxxfRuM/Xerx1TP07U/o+fa81TOiYqfyy39rvhmG/h/21i9aBBFrbVx7XY/1+Os+swUG/CwKdfYHWb656QOeXzgcr160O0OtJEWiVIyo/7OK61QKt9OjY+c/Z8bSyzBqkrPXc6kW/O4UdX2u8tPxqZWI9y3cE2kmMHQQdfJ1Qfgu0rnL0w+lgqXD3Ty4T46hA60pHz6li0Pb0175Hgm7fq+3pM9bKpAK3Xn1c6yVPqniVfu2nKnmlWyen35oVl+Ekk9oXE5doy7OJtfZdBYwKHFW6+l9CZLKuluhoXyhtU2lKUrcXVdh2nqhyNlHR/qliNbGzc826v9hzuoAyedBfPbaWd2sB0wVDvVrdsyTQOhckmDo2di76BaXOV8tvvhBaoWoFcr373A9GoO2vlROSMJUvVoHof6VNoYrPrwz8MixOoK0bWpwI10OgrUVLIm8NBOoCYXf3dB7rN1LZp7ygvN3f52s93voeG9Cr75K4W+OHpUnlVy0CrW2rfFPe1PZURukzyud2oabPap90biLQ2RdouxjXeaH6wC7Urf7TuWdjYVpVr+s8tXLQxF6P/TrWv7vczLSpAc261KnMUnkuJ7AuFyoblE6/3Ksk0NGy0RfoejVQ5l6gdQLprwpbXTWqILS+cjqplBGiAwlV2eo1ZQQVjFGBtr48+jFVcesH13Z1QujE1AlhlbffGqTnrbVsoH7XzR7cZC3pSrNu30ZvgVS6JaJ91/vthLUuK/bX+ib5Mq7//c9oG9F+qvqMtXAxICzfAq3zRfnVJMfkxfreSpxMoO0WvvK1Lo6tULW7Jf5dklYLtMoXpUnlj/XBtXygc38wAm1dJfR8NC9VOz1jdKyByi6//FIZoXxrQiohVj61blzWVUKf0f/9fb4ex1tljL7PuutYmvVctByy88Qvy+yvnrcuNNZ1RmFll6Vd77Pt+8dY+2R3GRHo7Aq0jadQHW5dEawVVH6gMkdlUiMnD0j7PNDWvdXGJlhe0/86vnYHyQRfYm2NUDrmdscu+h7rTqk8rdetu1atYQP+cynQ+mHUSmr9CnVgrdBUgaqWBH/wmt/SqExiV0X+vIp2FacTQduzH1ffpe3ZZ1T46rFfgVjloe01enWboWa0RlyZDjZUQfn9qxHofAu0dXHwzw/laQmnWixUINsUgGrF1WM7j61Q1edt8Go9z/FqjrFVtvbXCnxdRFtlovSrTLH90QW89ZG28kbpN0mz/bLjYyPb41q/GnFe+xUZeZR5oJnGjnMqi+e73600t/NAS4YbOTKWk5ljhkA3fxq7aEtytJU2L+fMQPvNQiqkFYFGoDnfEWgWUqHwbukx050IuyNhYV1RrEVQrYT2mn8HotmTx2ddoKN3j9RK69/Oz0s+G2i/EWjSikDnS6Bt8R+bxEDhd8O0/+PqMX1mKAspIdAIdG4rGvXtkpDYoEfdSvZvE9utZBvkpP6P1l1Fma3aBWnSfMx0m139oWyuZ2Usm5RehY8/uE236/W6zRRhmbGZ3VJYSAVJGkwadZ7q4lDnuFUc1rJt00vaLDwKlQl2UajP1nM1wGr2Rd3zbNyGlUlWDqmLj17TX8uP6iduaax2ZVUEGoFOY162qTyVT3X+27zQ0YG02p7VYzaRgMqAofQhrjZtQ13ltL+F8RDo+OOv49VRi0BrVgNJi01LZPM4Wj8+m/3ArzBMGtVnuZWrCTaz8Fb/SWUcf0Uym3PRz3CWyWwwgB1Lfb7RU/O1+phpkIgNIvIzlQaS6Jj4Am1ioWNis4lo4NtgFwJBoLORz4ZajihP1rqQUDVpVJlnlaYt+mPzsas8sFk6bEpQKx90bit9ugBvxrlt+6LvVrc8m2HFyiQb5e+PyFfDgP7X+/3ZEpI2kBuBTr5A2+qXKusVNg5D9YJ1FVVoZimbV93OM13w1XoXsppzSnWOzXAVnV3HVhSODmy2O3o2A5Tyctyc8rWmTT5mixUpryp/avyGzSnvp0uv6fj5KxbGzSPd6O5xSVkpeSjHXw0KI0e2PTnkpbxtyiSbNUNhq+BZ30GbMcPmKNXJb1OM1Ws0Z5IFWleENrWen+G0/7bYSVyGs3ln7USrZX7XNBwzu5iItkDb6kpxAm3HzC7KbBsIdP0E2iozFbQ6viqk1YoanfGhEQVhNcdYd2pUkNliJUqH0mojzu280eIBek2Sp7DV9Wotg6pJo8TAusT4eVzHUQMTfYH2+1urFdryvT9VYKPPaQmCdaWKTmdo09fZa35eVHml8k75tdHLlSPQ2RJoSZzdifXziPKHWnwtb/h5xGRbF3X6v5YpHKs9p1TuWTr9Fmj7W6k+9x/787zXM23Kt8p70XKkP8/QhbF5huS70Re+WRLoj//Fp18MT++1QxJoFez+Qhb2vH4AW3jBX8DCXlfFZqPZo1PRZVGgbYU0y3Cag9aurqOVaqUM1+gVmJJwzPzCxS64tN/W8l5JoK2LR9zxQ6AHJ9BqDdV0R9ELFH92CFVS+mtTqalys9+sFcfYvte/OPXvTMTlLQm3XdQOpTVosGmUTFo5p1YquyCx1rVKAm0NEdY61Oh+/rYv1pKs79Oy5kqvLk786UbjBNp/bJUyAo1AVyPQaiW1fOjfpVGDnMqlOIE2wbYFuPx81qhzylZntTLHuh364yTiyhxJvp+vBlufV1vOmJjKwWzJbpU11XiG0tjM1YnTLND6Ddva2l8KT+9pQxJo/UB2wlsrlQpZW12wkkBba5FVXnFT3WVJoNVCZhcMcQsqVDqxrTXfrs6zLtBW4UbPl+gx8m8bS978hXMaXWnnoQVa55ok2uY89u8uKc+r9cVfSMUK6VYItCotu+3oV2b6ay22lSoNa5WJrgraiPNAabGuInEXeZUEWsfUVguNXtg08py2Y1dp5bE4gbaLFttPBBqBHuxCKnbu2N0iW0jMnw2nP4FW+RRdNKwR3SSsfhqoPrcumUqT5NbGNcmBGtECbS3wOj76X3/V5SVa9kTLIH9RtWY2QqVVoFUvvumEN78wvFD40oAnfyWB1slthWfcQa8k0KqAbZCJVXhZFmi7iq4mw9mgA1WWdmvK+hSmZcWeoR4zHSObYSPuGNn+686H9am3FSitdbSZ82xnRaB1MaJCzI6dPbbfw7rT2PgGHW87j/3b9K0QaL9ssS4/1VQaes1aZWpdnrbalQhtbvqBBFqtVHqs27F+a5Av4c1ogbaZBgYSaBvQq4YT6zbjt2Ij0Ah0tQJtYxn68wlfoHWO2mrG9bhLM9g+0Db4t1JdZfWUrdDst7YPVvSr7cLR39SbcWWhGqFsRUUdf9tGM0L1SaPLiXqWN6oPdbw6x45/aVxX1+erOvkrCbQ2ZP1lBhJoHST9r0rYVits1m3JJBTetvxl3Ipklaa9iRtgl2WBVmtiLQIseWIWjtq7cOii1j+O/QmfWhl1XtvAsmYfY7UGWR/mau/uKL0qh2x1Qd1FswqkUeeBjdj383p0EKTKBeVxy//+aHq93oxbq7YvatFTa5mtHhg3rsNei5tSUi1sjb7gR6CzJdBqSbZZXuL6z/uDCG2sg8off8aXWscJVHtOKa1DXaxN+Xwog/UG04XDFpOKvu4vOqXyUF6mOtcaofzuKc0IG++UhrzYfdTRxQkTJz191NE9/23AbhvVCLQqAqt44k4mva5Q4W+3gP0lphvVapVEgdbxGWrnfB1Dv2Un6/NAD3X7zAPdmEGEcYW9rRKqVhUV2npPI1r/BzrGfmuQv3JpXPqVThXWqnRNnq2fcS3TRFZ7HtgMA0P5DqUxuoR4I89pHddafk+/UkagEehqBLrWi0T/IrVZd5aH8h02gUIj0qaLiVru6KusbPRdrvQKdM/BIZ38/U1j589lPJTm++hIfuanZe7sLO4P09g1doaLWhZ4acbIffIo5UmeBXpM59hXqtlfNTINVeBq+Wwazqlq02ZjJgYbuuCtZTB1lgVad9cmTT7qxboLNDJIINDJFejRY8YczPoxVsE/1NupagmqdXVFBJq0NqrSHjtu/LNZEOiuCRN/l4YuiCzlfQCBjgndoeye3PMsAs3JzDHLkUCHFfBv0tx3PksrEXK8Oe6DrbQ7x457OgsCfewbe///NAyCR6CbG+oGk4b90qDPE9487a4hnfwjR7Y9hwAi0Byzoe2PBLZVAj1p8lF3p3n2FgSafclrGfxf/vKS18eO6/r7IBucpVvgSV95GIFGoOPSOGr06D8MauCgz/ARI36R9hkgOJk5Zq3aH7UkDS8Uft6qimti9+QXkl5xIdDsC2Vw30q7rb3j4JAr7QQysfuo69868+3PJ7ksQqARaD80cPv4E096afyECVfWcu5v+NzlX38GCUSgOWaD35/z/49PPDXgcp8NpHNc1zfP+JN3vJRGiUag2Ze8lcGqtI87YeqBqhZpSBeF4SNG/H3n2HHPrfjy1w/UOvYAgUagGxWanvOiz3zp6dGdncqHl9TcitUxavQzaW3F4mRORthy5nk6B37w4OOvhZXG8y1uSSq0dXR8d9z4rgNfuPKbryex4kKg2Ze8l8GqtMMy8qVRYzqfr0elnWDmhLF52LBhL4dl47Pq3paUCNP1apLSk5a0DT1GPD1s2PDnkpSm4cOHPxfG4+HxXl/Pevuq3qkn/QKJRqCJ6s6Bu7c98eqESd2/DPPOyqRUXIVC4Z6wcHilUBj5+/aOUXuTHmE6n0t6GkMReC0NxzIrxzuNaR0oRhQKB9ra2v+l3pU2DJplpA0a0oqlzN3e3vGbv/7Clb9MUysWranZjEYv1DBUgf7RI7tf+OTyFU+EFfxvwzyznKKDShcAACCxt1+IfMXw4cOfbfH374+7/RPmj0dpSQIAAACApNETxm85DAAAAAAA1aFZLYphTOVQAAAAAAAMjGa2eDmMBRwKAAAAAID++fMwXgvKLdDrORwAAAAAAP3zYyfPit0cDgAAAACAymjw4Kth7A3jORcAAAAAAFAFtD4DAAAAACDQAAAAAAAINAAAAEDT6ApjSRgrw7g0jOkcEkCgAQAAAOLRsuH7w9jsBFpTlh0MYxmHBhBoAAAAgL70OHleEnl+XlCehYEV6ACBBgAAAPBQd41dFV6bH0a3J9qbgnLLtIT7qjAK7jW1VGvJ541Ounc7ATfUqr3XvfZAGNPc81rdboP3vikRWdvtxF6fPeDSujjyOKiQvtWR9K12+7knjA5+dgQaAAAAYKiou8bNVbxv+/9q73yh2liiOHxFRAQiAhGBiEBEIBAIBCLnIBAIRAWiAoGoQCAQiAoEoqICgaioQFQgEBUIBKKiAoFAIBAViApEREREBaLv3dffnL0ZNn+goY/C952zh+xOsjskszvf3p0/kuSqZPVMEp0E+TZIs6+39XpeAlbTun/mRK/X/l2+hGM07NekHYmfknL/7KLWPa8TWr8N+z0L+ZvU+tuQn66EfZGfHIEGAAAA+B0+2fBpmmclqxNhW2o3nQT1ax8R9s96VHjLisiz3UOgW9n6bLbe0H799YzWffHI9WXI3yk/NQINAADPAneBsn5aDdX5f4JxPM2u/cH8wpjZsyIinDOvH7dVIlFRdneGiPCq/YoI+zZvRvH6HgLdGGG9ZUXTkbhchfwd8FMj0AAA8CxY61M3tTKPeCz8ifaHMexnijr278UjyT/0I+Z3RR3Jb4pAV7NC2h1BoH0/zfB6y4qmF7lANx8o0GURcm/GMYNAI9AAAIBAjxm8Av7Dmzd4hHg+iKxvu7CiI55Hc1PHvJrSP44g0N4u+iYI+pLEvKq0ttKq2t9DBDrlb1/5830dW9FBkYKOQAMAwMsT6DfyDPcQfwK+EtKWtc3Tv2dp3izVn9D/KPGHeXlM14rmq3PyqLbdHcjA+355tPpS+Ti3IrCYR6BXQp7igAyzcrIbpb2jCDwNJlRQOip4qcBMhvc0JcltLQdWRHw3bfBoGmnf37MCUdF+brXPbbs7CsfUiOtJ+jtajkL+N63o8AgINAAAPH+BnpRfJE94bcWoY025TisIaseKflbXQXSbJceJgbmJ4DDJgfw4W1o/0L7TU/FtK/poNUJ+p7M8LUnSff8nVrT5npJPzVEMABBoeNkV4U6fZeUv+R/8qVctVMRrj3CMnXAMAK4bwwW6JgHd0/ZKeJ+P0vXFioEHfPEn17uh3nsz5HxMAr0ioc3zdxkEej+kVZSv2Uygt+xun7RZXV8Old9VrgMACDSAs60Kxhd/PHkR1tf/kv/hzHqjRnuPcIy8CRvAS+ZVibQmgb4N6wv2a46IrpadILU3ktK4bId6b21Egc77c6V8XIdj7ZTUqy27OyBDv+amPnzwB/3PtxLtOsUAAIEGMFVCg4Z0yiuMySFS6ell0ZrqgM9VlFa7xz6vrXfIzRx/BDs1IL1mvU3mRhXoKevtPJ3nc4IiBc+UOZ0T+XnjUdzLcN4thPPab25/6LMeaT7OPjsTzrH7CHRZBHpdwYAk0PvZ9cfz0bThEegdvWchnM8z2jdNUwEQaIC+At1QGTxWRbOmiudclZYvbVWOJpE9U0X0XRVVrLzeWtFBp229Ue6dkOafO7Tise+09ps68XxWhebvudX2V9YbjfLPetQo9gFZyKT4nRWzq55Y/7Flo0AvWjFcZ0fHqARxPrWi49QB5zA8Uy51XZjTuZEGLdgO56yfx/NBPLvanuaoWA5pnbA+TKD9GF+tGAThuw1uA93WMSoS3/NwfRvUBrqjG+xzK5qXVHUt2qYIACDQAIME+qe2T6kyObHeXujvrZgsqaX3p/HmZ7U+Jbn01/Ugoufhc+2QVs8q31Mrxn2tqPKMj3tTpRcFekf7TxHr16oQJ4MU74bjtSXhgwR6UhKwqu2+74vwvX0M4l+RYHAOw3PEz4cjnQ8/Vc63rbet87q2d0ukeFkS3pV4b4W0rwPOxSTcV1bMN5FG4ejqPN4N+TjQ/q50TTkN15l80IU0MkhXeUs33M2w//ymGQD+IoFetqKdatldcBqZ5cBopwXjEej8UW1VZcvF9VOQ1iTCkba2V/X6yMo740xomVF6R5+rKQ/T4b01K6LF/QT6wu62474K8pvv84v1j3olgV4NlbaFY6bH1t0g/aYKGIEG+P9IdSW8QNYV1VhQJeZ3Vvs2vM3eICp2/2kw60PuCOHPCfSBykOjRJDnJSip8j7MpIMexPAQgY54m8M0rutnLVGgr0vKcBLcpq5nvu1WZTmNb38iAfVIz14Q75SHyoBzpEygy9pGR0nO2zWPItDDOizl+5xGoAEQaPjzeEXVkTTPquI5VyX3Ox1Uruz+PcpbVARPRqDPVAbel/yOE0GSo0B7lNAfO/kj5bd8rfAbAt3NbqY3RhTomvUOjTenfS3ouGfhxr5iRQS6Ktmeya6NG0ME2ve3meXlJuThIQKdbh4imzpWykv8bl5x3QT4X1mw3r4P8ELIox3+ehxDSz1kSCYE+ukI9IFuqPw3uezzubrKy7QEZFFSMmt3I2gA9xFoF9s3QYKvQzkcJNCpXfMrlcV5CXRDx72UZKfJpfy4qXOi3wh6pDuN/PEtyO61bgrr2TXT8/hdx/Fz4J3Wq78h0KnD0q5ez0uo0/expQDFihWzmnHdBAB4BJZUcXR0Id4MkYu2KpxrVSA/tO1QFc2xPpfaFabIY+x93tb70qP+r6oMUsWShmRJsxy+H1GgV63oSe9/X4e0po7TUQWyZwz5Mk6Bnsgq/XyILpfkMytmcKqqfBypgkegoYy9EoGsl5SXFV2zrlWmvE3+SbhBO8zef2jFDGP+3nMrZhpbDmX6MMj4hoR3NaR/ULoLamz7vxGEOh8HeiPLa97muZ79/0sDbi7Se6cl8ymv+YQPfsxTHW8TgQYAGD8NSXGqJGastyd4WQQ6VXBvdYFO7Zm/BPl+L4GqKX3fil7yeeQln6Lyxno7wZQJ9KwiR62QFnvMn1sxF31Dsk4bpPEJ9JWEom69w/HU9fdKv0XatmBFu+iWbm4AYPy42Mcpft9wwwoAMH42S2TGH2UejyDQKbKxbnebY3T0voaWNJTUZIlAH0q2h01RGQV6z+5GmdJg5U3tv5pVKgj0+AR6Wd//JyuizCsS5SUrOk2kUToq+s0OVG52+FoBHk2g/ZzdVSAjjm0LAABjomzqyCjNgwTapcij0JcS1gsrGsr/lBTn02TWSwR61Ckqo0CXTY2Z/hd/303J/4RAj0+gAeDpsqBrszeXmubrAAAYP2URaI9cfB5BoD3yOBUk2KOR6XG+Rz1iW76qxLZSItD+qD9FngdNURkF2tOPsvQUgW7Y3XFjdxBoBBoAAABgHLhsPrQNdJr9KzWV8Ef0qemHR5TPJLEuzd4e+Vsm0Ev6bD5FZZzhq59A92sDnSLgJxJsl/xF/U8INAINAAAAMBbiKBzejCKOW+oiHdsaHwa5nrBiGs2O9U5P6WkfldaVFDfDflLaivVOUdlWWtkkK/PWGy1Po3CkkTbiKByT2s83Sf0nBBqBBgAAAID+7FrvUGsu03t8LQg0AAAAAJTjTU28GceaZNqj1DN8LQg0AAAAAJTj0WefTMAjz94bnZ7oCDQAAAAAAAINAAAAAPDU2OQrAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAIBH5R8aXKzJI8U4JgAAAABJRU5ErkJggg==)

In the diagram above, the sharp edged boxes represent distinct machines\. The rounded boxes at the bottom represent Kafka topic partitions, and the diagonally rounded boxes represent logical entities which run inside brokers\.

Each arrow represents either an RPC, or a write to a Kafka topic\. These operations occur in the sequence indicated by the numbers next to each arrow\. The sections below are numbered to match the operations in the diagram above, and describe the operation in question\.

### <a id="_6u8uypxxs15g"></a>1\. Finding a transaction coordinator \-\- the [FindCoordinatorRequest](#_97qeo7mkx9jx)

Since the transaction coordinator is at the center assigning PIDs and managing transactions,the first thing a producer has to do is issue a *FindCoordinatorRequest* \(previously known as *GroupCoordinatorRequest*, but renamed for general usage\) to any broker to discover the location of its coordinator\. Note that if no TransactionalId is specified in the configuration, this step can be skipped\.

### <a id="_dinbsctebcle"></a>2\. Getting a producer Id \-\- the [InitPidRequest](#_z99xar1h2enr)

The producer must send an InitPidRequest to get idempotent delivery or to use transactions\. Which semantics are allowed depends on whether or not the [transactional\.id](#g2xsf9n49puh) configuration is provided or not\.

#### <a id="_o6wvubfhzaqo"></a>2\.1 When a TransactionalId is specified

After discovering the location of its coordinator, the next step is to retrieve the producer’s *PID*\. This is achieved by sending an *InitPidRequest* to the transaction coordinator\. 

The TransactionalId is passed in the *InitPidRequest* along with the transaction timeout, and the mapping to the corresponding PID is logged in the transaction log in step 2a\. This enables us to return the same PID for the TransactionalId to future instances of the producer, and hence enables recovering or aborting previously incomplete transactions\. 

In addition to returning the PID, the *InitPidRequest* performs the following tasks:

1. Bumps up the epoch of the PID, so that any previous zombie instance of the producer is fenced off and cannot move forward with its transaction\.
2. Recovers \(rolls forward or rolls back\) any transaction left incomplete by the previous instance of the producer\.

The handling of the *InitPidRequest* is synchronous\. Once it returns, the producer can send data and start new transactions\.

#### <a id="_6562cfctwipz"></a>2\.2 When a TransactionalId is not specified

If no TransactionalId is specified in the configuration, the *InitPidRequest* can be sent to any broker\. A fresh PID is assigned, and the producer only enjoys idempotent semantics and transactional semantics within a single session\. 

### <a id="_z9nopnb9z4o1"></a>3\. Starting a Transaction \-\- the beginTransaction API

The new KafkaProducer will have a beginTransaction\(\) method which has to be called to signal the start of a new transaction\. The producer records local state indicating that the transaction has begun, but the transaction won’t begin from the coordinator’s perspective until the first record is sent\.

### <a id="_9nlwbd1bifn0"></a>4\. The consume\-transform\-produce loop

In this stage, the producer begins to consume\-transform\-produce the messages that comprise the transaction\. This is a long phase and is potentially comprised of multiple requests\.

#### <a id="_dyen7q27r3i2"></a>4\.1 [AddPartitionsToTxnRequest](#_r6klddrx9ibz)

The producer sends this request to the transaction coordinator the first time a new TopicPartition is written to as part of a transaction\. The addition of this *TopicPartition* to the transaction is logged by the coordinator in step 4\.1a\. We need this information so that we can write the commit or abort markers to each TopicPartition \(see section 5\.2 for details\)\. If this is the first partition added to the transaction, the coordinator will also start the transaction timer\.

#### <a id="_cckv7jc1fsy3"></a>4\.2 [ProduceRequest](#_mkk6ckzh7jxl)

The producer writes a bunch of messages to the user’s TopicPartitions through one or more *ProduceRequests *\(fired from the *send* method of the producer\)\. These requests include the PID, epoch, and sequence number as denoted in 4\.2a\.

#### <a id="_ggt0dw5tk47r"></a>4\.3 [AddOffsetsToTxnRequest](#_cqecubxziwie)

The producer has a new sendOffsets API method, which enables the batching of consumed and produced messages\. This method takes a map of the offsets to commit and a *groupId* argument, which corresponds to the name of the associated consumer group\.

The sendOffsets* *method* *sends an *AddOffsetsToTxnRequests* with the groupId to the transaction coordinator, from which it can deduce the TopicPartition for this consumer group in the internal \_\_consumer\_offsets topic\. The transaction coordinator logs the addition of this topic partition to the transaction log in step 4\.3a\.

#### <a id="_4fiwu5vphzq9"></a>4\.4 [TxnOffsetCommitRequest](#_5695qbm2hne)

Also as part of sendOffsets*, *the producer will send a *TxnOffsetCommitRequest* to the consumer coordinator to persist the offsets in the \_\_consumer\_offsets topic \(step 4\.4a\)\. The consumer coordinator validates that the producer is allowed to make this request \(and is not a zombie\) by using the PID and producer epoch which are sent as part of this request\. 

The consumed offsets are not visible externally until the transaction is committed, the process for which we will discuss now\.

### <a id="_rhcuthaoajrw"></a>5\. Committing or Aborting a Transaction

Once the data has been written, the user must call the new commitTransaction* *or abortTransaction methods of the KafkaProducer\. These methods will begin the process of committing or aborting the transaction respectively\. 

#### <a id="_rr7z72vuy251"></a>5\.1 [EndTxnRequest](#_2dnhvqqi7mr7)

When a producer is finished with a transaction, the newly introduced KafkaProducer\.commitTranaction* *or KafkaProducer\.abortTransaction must be called\. The former makes the data produced in step 4 above available to downstream consumers\. The latter effectively erases the produced data from the log: it will never be accessible to the user \(at the READ\_COMMITTED isolation level\), ie\. downstream consumers will read and discard the aborted messages\.

Regardless of which producer method is called, the producer issues an *EndTxnRequest* to the transaction coordinator, with a field indicating whether the transaction is to be committed or aborted\. Upon receiving this request, the coordinator:

1. Writes a *PREPARE\_COMMIT *or *PREPARE\_ABORT* message to the transaction log\. \(step 5\.1a\)
2. Begins the process of writing the command messages known as COMMIT \(or ABORT\) markers to the user logs through the *WriteTxnMarkerRequest\. *\(see section 5\.2 below\)\.
3. Finally writes the *COMMITTED* \(or *ABORTED\)* message to transaction log\. \(see 5\.3 below\)\.

#### <a id="_bgy58z5pyzw7"></a>5\.2 [WriteTxnMarkerRequest](#_jtpvkrldhb7)

This request is issued by the transaction coordinator to the leader of each TopicPartition which is part of the transaction\. Upon receiving this request, each broker will write a *COMMIT\(PID\) *or *ABORT\(PID\)* control message to the log\. \(step 5\.2a\)

This message indicates to consumers whether messages with the given PID should be delivered or dropped\. As such, the broker will not return messages which have a PID \(meaning these messages are part of a transaction\) until it reads a corresponding *COMMIT *or *ABORT* message of that PID, at which point it will deliver or skip the messages respectively\. In addition, in order to maintain offset ordering in message delivery, brokers would maintain an offset called last stable offset \(LSO\) below which all transactional messages have either been committed or aborted\.

Note that, if the \_\_consumer\_offsets topic is one of the TopicPartitions in the transaction, the commit \(or abort\) marker is also written to the log, and the consumer coordinator is notified that it needs to materialize these offsets in the case of a commit or ignore them in the case of an abort \(step 5\.2a on the left\)\.

#### <a id="_leodiv40s4w1"></a>5\.3 Writing the final Commit or Abort Message

After all the commit or abort markers are written the data logs, the transaction coordinator writes the final *COMMITTED *or *ABORTED* message to the transaction log, indicating that the transaction is complete \(step 5\.3 in the diagram\)\. At this point, most of the messages pertaining to the transaction in the transaction log can be removed\. 

We only need to retain the PID of the completed transaction along with a timestamp, so we can eventually remove the TransactionalId\->PID mapping for the producer\. See the Expiring PIDs section below\.

In the rest of this design doc we will provide a detailed description of the above data flow along with the proposed changes on different modules\.

# <a id="_dz78fc8xlsd5"></a>__Transactional Producer__

Transactional Producer requires a user\-provided __TransactionalId__ during initialization in order to generate transactions\. This guarantees atomicity within the transaction and at the same time fences duplicate messages from zombie writers as long as they are sending transactions\.

## <a id="_9f3l35ifpyku"></a>Public APIs

We first introduce a set of new public APIs to the KafkaProducer class, and describe how these APIs will be implemented\.

/\* initialize the producer as a transactional producer \*/

initTransactions\(\)

<a id="ypgqs5sy98rn"></a>The following steps will be taken when initTransactions\(\) is called:

1. If no TransactionalId has been provided in configuration, skip to step 3\.
2. Send a [FindCoordinatorRequest](#_97qeo7mkx9jx) with the configured TransactionalId and with CoordinatorType encoded as “transaction” to a random broker\. Block for the corresponding response, which will return the assigned transaction coordinator for this producer\.
3. Send an [InitPidRequest](#_z99xar1h2enr) to the transaction coordinator or to a random broker if no TransactionalId was provided in configuration\. Block for the corresponding response to get the returned PID\.

/\* start a transaction to produce messages \*/

beginTransaction\(\)  

<a id="w4ko2ygwszpn"></a>The following steps are executed on the producer when beginTransaction is called:

1. Check if the producer is transactional \(i\.e\. init has been called\), if not throw an exception \(we omit this step in the rest of the APIs, but they all need to execute it\)\.
2. Check whether a transaction has already been started\. If so, raise an exception\.

/\* send offsets for a given consumer group within this transaction \*/

sendOffsetsToTransaction\(

            Map<TopicPartition, OffsetAndMetadata> offsets, 

            String consumerGroupId\)  

<a id="9soew3kapduv"></a>The following steps are executed on the producer when sendOffsetsToTransaction is called:

1. Check if it is currently within a transaction, if not throw an exception; otherwise proceed to the next step\.
2. Check if this function has ever been called for the given groupId within this transaction\. If not then send an [AddOffsetsToTxnRequest](#_cqecubxziwie) to the transaction coordinator, block until the corresponding response is received; otherwise proceed to the next step\.
3. Send a [TxnOffsetCommitRequest](#_5695qbm2hne) to the coordinator return from the response in the previous step, block until the corresponding response is received\.

/\* commit the transaction with its produced messages \*/

commitTransaction\(\) 

<a id="d3zdkvqddmf4"></a>The following steps are executed on the producer when commitTransaction is called:

1. Check if there is an active transaction, if not throw an exception; otherwise proceed to the next step\.
2. Call flush to make sure all sent messages in this transactions are acknowledged\.
3. Send an [EndTxnRequest](#_2dnhvqqi7mr7) with COMMIT command to the transaction coordinator, block until the corresponding response is received\.

/\* abort the transaction with its produced messages \*/

abortTransaction\(\)  

The following steps are executed on the producer when abortTransaction is called:

1. Check if there is an active transaction, if not throw an exception; otherwise proceed to the next step\.
2. Immediately fail and drop any buffered messages that are transactional\. Await any in\-flight messages which haven’t been acknowledged\. 
3. Send an [EndTxnRequest](#_2dnhvqqi7mr7) with ABORT command to the transaction coordinator, block until the corresponding response is received\.

/\* send a record within the transaction \*/

send\(ProducerRecord<K, V> record\)

With an ongoing transaction \(i\.e\. after beginTransaction is called but before commitTransaction or abortTransaction is called\), the producer will maintain the set of partitions it has produced to\. When send is called, the following steps will be added:

1. Check if the producer has a PID\. If not, send an InitPidRequest following the [procedure](#ypgqs5sy98rn) above\.
2. Check whether a transaction is ongoing\. If so, check if the destination topic partition is in the list of produced partitions\. If not, then send an [AddPartitionToTxnRequest](#_r6klddrx9ibz) to the transaction coordinator\. Block until the corresponding response is received, and update the set\. This ensures that the coordinator knows which partitions have been included in the transaction before any data has been written\.

__Discussion on Thread Safety\.__ The transactional producer can only have one outstanding transaction at any given time\. A call to beginTransaction\(\) with another ongoing transaction is treated as an error\. Once a transaction begins, it is possible to use the send\(\) API from multiple threads, but there must be one and only one subsequent call to commitTransaction\(\) or abortTransaction\(\)\.

Note that with a non\-transactional producer, the first send call will be blocking for two round trips \(GroupCoordinatorRequest and InitPidRequest\)\.

<a id="5x3010zgtp7o"></a>### <a id="_8y3tle2c2qbm"></a>Error Handling

Transactional producer handles [error codes](#ps5hofwnw55e) returned from the transaction responses above differently:

InvalidProducerEpoch: this is a fatal error, meaning the producer itself is a zombie since another instance of the producer has been up and running, stop this producer and throw an exception\.

<a id="7ztwi7m2pk9s"></a>InvalidPidMapping:the coordinator has no current PID mapping for this TransactionalId\.  Establish a new one via the InitPidRequest with the TransactionalId\.

<a id="3el3xfxbx8j5"></a>NotCoordinatorForTransactionalId: the coordinator is not assigned with the TransactionalId, try to re\-discover the transaction coordinator from brokers via the FindCoordinatorRequest with the TransactionalId\.

<a id="fpkb0rxeonhb"></a>InvalidTxnRequest: the transaction protocol is violated, this should not happen with the correct client implementation; so if it ever happens it means your client implementation is wrong\.

<a id="qrl300ydtgko"></a>CoordinatorNotAvailable: the transaction coordinator is still initializing, just retry after backing off\.

<a id="cx6ufvjedx6a"></a>DuplicateSequenceNumber: the sequence number from ProduceRequest is lower than the expected sequence number\. In this case, the messages are duplicates and hence the producer can ignore this error and proceed to the next messages queued to be sent\.

<a id="bj0lez6mjxeb"></a>InvalidSequenceNumber: this is a fatal error indicating the sequence number from ProduceRequest is larger than expected sequence number\. Assuming a correct client, this should only happen if the broker loses data for the respective partition \(i\.e\. log may have been truncated\)\. Hence we should stop this producer and raise to the user as a fatal exception\. 

<a id="nygi4tqp1tbd"></a>InvalidTransactionTimeout: fatal error sent from an InitPidRequest indicating that the timeout value passed by the producer is invalid \(not within the allowable timeout range\)\.

__Discussion on Invalid Sequence\.__ To reduce the likelihood of the InvalidSequenceNumber error code, users should have acks=all enabled on the producer and unclean leader election should be disabled\. It is still possible in some disaster scenarios to lose data in the log\. To continue producing in this case, applications must catch the exception and initialize a new producer instance\.

## <a id="_78p7cgjqcbnx"></a>Added Configurations

The following configs will be added to the producer client:

<a id="qomrk2mk882"></a>enable\.idempotence

Whether or not idempotence is enabled \(false by default\)\. If disabled, the producer will not set the PID field in produce requests and the current producer delivery semantics will be in effect\. Note that idempotence must be enabled in order to use transactions\.

When idempotence is enabled, we enforce that acks=all, retries > 1, and max\.inflight\.requests\.per\.connection=1\. Without these values for these configurations, we cannot guarantee idempotence\. If these settings are not explicitly overridden by the application, the producer will set acks=all, retries=Integer\.MAX\_VALUE, and max\.inflight\.requests\.per\.connection=1 when idempotence is enabled\.

<a id="jnzinkkapylv"></a>transaction\.timeout\.ms

The maximum amount of time in ms that the transaction coordinator will for a transaction to be completed by the client before proactively aborting the ongoing transaction\.

This config value will be sent to the transaction coordinator along with the [InitPidRequest](#_z99xar1h2enr)\.  
  
Default is 60000\. This makes a transaction to not block downstream consumption more than a minute, which is generally allowable in real\-time apps\.

<a id="g2xsf9n49puh"></a>transactional\.id

The TransactionalId to use for transactional delivery\. This enables reliability semantics which span multiple producer sessions since it allows the client to guarantee that transactions using the same TransactionalId have been completed prior to starting any new transactions\. If no TransactionalId is provided, then the producer is limited to idempotent delivery\.

Note that enable\.idempotence must be enabled if a TransactionalId is configured\.  
  
Default is “”\.

# <a id="_kcryyiqr0svu"></a>__Transaction Coordinator__

Each broker will construct a transaction coordinator module during the initialization process\. The transaction coordinator handles requests from the transactional producer to keep track of their __transaction status__, and at the same time maintain their __PIDs__ across multiple sessions via client\-provided __TransactionalIds__\. The transaction coordinator maintains the following information in memory:

1. A map from TransactionalId to assigned PID__,__ plus current epoch number, and 2\) the transaction timeout value\.
2. A map from PID to the current ongoing transaction status of the producer indicated by the PID, plus the participant topic\-partitions, and the last time when this status was updated\.

In addition, the transaction coordinator also persists both mappings to the transaction topic partitions it owns, so that they can be used for recovery\.

## <a id="_lzhx7x34k9er"></a>Transaction Log

As mentioned in the [summary](#_f0eotchjto1f), the transaction log is stored as an internal transaction topic partitioned among all the brokers\. Log compaction is turned on by default on the transaction topic\. Messages stored in this topic have versions for both the key and value fields:

/\* Producer TransactionalId mapping message \*/

Key => Version TransactionalId  

  Version => 0 \(int16\)

  TransactionalId => String

Value => Version ProducerId ProducerEpoch TxnTimeoutDuration TxnStatus \[TxnPartitions\] TxnEntryLastUpdateTime TxnStartTime

  Version => 0 \(int16\)

  ProducerId => int64

  ProducerEpoch => int16

  TxnTimeoutDuration => int32

  TxnStatus => int8

  TxnPartitions => \[Topic \[Partition\]\]

     Topic => String

     Partition => int32

  TxnLastUpdateTime => int64

  TxnStartTime => int64

<a id="6zixifmk50sp"></a>The status field above has the following possible values:

BEGIN

The transaction has started\.

PREPARE\_COMMIT

The transaction will be committed\.

PREPARE\_ABORT

The transaction will be aborted\.

COMPLETE\_COMMIT

The transaction was committed\.

COMPLETE\_ABORT

The transaction was aborted\.

Writing of the PREPARE\_XX transaction message can be treated as the synchronization point: once it is appended \(and replicated\) to the log, the transaction is guaranteed to be committed or aborted\. And even when the coordinator fails, upon recovery, this transaction will be rolled forward or rolled back as well\.

Writing of the TransactionalId message can be treated as persisting the creation or update of the TransactionalId \-> PID entry\. Note that if there are more than one transaction topic partitions owned by the transaction coordinator, the transaction messages are written only to the partition that the TransactionalId entry belongs to\.

We will use the timestamp of the transaction status message in order to determine when the transaction has timed out using the transaction timeout from the [InitPidRequest](#_z99xar1h2enr) \(which is stored in the TransactionalId mapping message\)\. Once the difference between the current time and the timestamp from the status message exceeds the timeout, the transaction will be aborted\. 

This works similarly for expiration of the TransactionalId, but note 1\) that the transactionalId will not be expired if there is an on\-going transaction, and 2\) if the client corresponding to a transactionalId has not begun any transactions, we use the timestamp from the mapping message for expiration\.

When a transaction is completed \(whether aborted or committed\), the transaction state of the producer is changed to Completed and we clear the set of topic partitions associated with the completed transaction\.

## <a id="_bg39k6ipxugn"></a>Transaction Coordinator Startup

Upon assignment of one of the transaction log partitions by the controller \(i\.e\., upon getting elected as the leader of the partition\), the coordinator will execute the following steps:

1. Read its currently assigned transaction topic partitions and bootstrap the Transaction status cache\. The coordinator will scan the transaction log from the beginning, verify basic consistency, and materialize the entries\. It performs the following actions as it reads the entries from the transaction log:
	1. Check whether there is a previous entry with the same TransactionalId and a higher epoch\. If so, throw an exception\. In particular, this indicates the log is corrupt\. All future transactional RPCs to this coordintaor will result in a \`NotCoordinatorForTransactionalId\` error code, and this partition of the log will be effectively disabled\.
	2. Update the transaction status cache for the transactionalId in question with the contents of the current log entry, including the last update time, and partitions in the transaction, and status\. If there are multiple log entries with the same transactionalId, the last copy will be the one which remains materialized in the cache\. The log cleaner will eventually compact out the older copies\.

<a id="3af5934pfogc"></a>When committing a transaction, the following steps will be executed by the coordinator:

1. Send an [WriteTxnMarkerRequest](#_jtpvkrldhb7) with the COMMIT marker to all the leaders of the transaction’s added partitions\.
2. When all the responses have been received, append a COMPLETE\_COMMIT transaction message to the transaction topic\. We do not need to wait for this record to be fully replicated since otherwise we will just redo this protocol again\.

<a id="np4653erbekz"></a>When aborting a transaction, the following steps will be executed by the coordinator:

1. Send an [WriteTxnMarkerRequest](#_29i9ru7ev0u) with the ABORT marker to all the host brokers of the transaction partitions\.
2. When all the responses have been received, append a COMPLETE\_ABORT transaction message to the transaction topic\. Do not need to wait for this record to be fully replicated since otherwise we will just redo this protocol again\.

<a id="ia8lrje8xifh"></a>__Discussion on Unavailable Partitions\. __When committing or aborting a transaction, if one of the partitions involved in the commit is unavailable, then the transaction will be unable to be completed\. Concretely, say that we have appended a PREPARE\_COMMIT message to the transaction log, and we are about to send the WriteTxnMarkerRequest, but one of the partitions is unavailable\. We cannot complete the commit until the partition comes back online, at which point the “roll forward” logic will be executed again\. This may cause a transaction to be delayed longer than the transaction timeout, but there is no alternative since consumers may be blocking awaiting the transaction’s completion\. __It is important to keep in mind that we  strongly rely on partition availability for progress\.__ Note, however, that consumers in READ\_COMMITTED mode will only be blocked from consumption on the unavailable partition; other partitions included in the transaction can be consumed before the transaction has finished rolling forward\.

## <a id="_mcphg8e8gg24"></a>Transaction Coordinator Request Handling

<a id="jro89lml46du"></a>When receiving the [InitPidRequest](#_z99xar1h2enr) from a producer *with a non\-empty TransactionalId *\(see [here](#kix.oz3ruj5vub78) for handling the empty case\), the following steps will be executed in order to send back the response:

1. Check if it is the assigned transaction coordinator for the TransactionalId, if not reply with the NotCoordinatorForTransactionalId error code\.
2. If there is already an entry with the TransactionalId in the mapping, check whether there is an ongoing transaction for the PID\. If there is and it has not been completed, then follow the abort logic\. If the transaction has been prepared, but not completed, await its completion\. We will only move to the next step after there is no incomplete transaction for the PID\.
3. Increment the epoch number, append the updated TransactionalId message\. If there is no entry with the TransactionalId in the mapping, construct a PID with the initialized epoch number; append an TransactionalId message into the transaction topic, insert into the mapping and reply with the PID / epoch / timestamp\. 
4. Respond with the latest PID and Epoch for the TransactionalId\.

Note that coordinator’s PID construction logic does NOT guarantee that it will always result in the same PID for a given TransactionalId \(more details discussed [here](#kix.uu5bwrue4nmm)\)\. In fact, in this design we make minimal assumptions about the PID returned from this API, other than that it is unique \(across the Kafka cluster\) and will never be assigned twice\. One potential way to do this is to use Zookeeper to reserve blocks of the PID space on each coordinator\. For example, when broker 0 is first initialized, it can reserve PIDs 0\-100, while broker 1 can reserve 101\-200\. In this way, the broker can ensure that it provides unique PIDs without incurring too much additional overhead\.

<a id="aywz22lp6tma"></a>When receiving the [AddPartitionsToTxnRequest](#_r6klddrx9ibz) from a producer, the following steps will be executed in order to send back the response\.

1. If the TransactionalId does not exist in the TransactionalId mapping or if the mapped PID is different from that in the request, reply with [InvalidPidMapping](#7ztwi7m2pk9s); otherwise proceed to next step\.
2. If the PID’s epoch number is different from the current TransactionalId PID mapping, reply with the InvalidProducerEpoch error code; otherwise proceed to next step\.
3. Check if there is already an entry in the transaction status mapping\.
	1. If there is already an entry in the transaction status mapping, check if its status is BEGIN and the epoch number is correct, if yes append an transaction status message into the transaction topic with the updated partition list, wait for this message to be replicated, update the transaction status entry and timestamp in the TransactionalId map and reply OK; otherwise reply with InvalidTxnRequest error code\.
	2. Otherwise append a BEGIN transaction message into the transaction topic, wait for this message to be replicated and then insert it into the transaction status mapping and update the timestamp in the TransactionalId map and reply OK\.

<a id="5xa2hzric4h0"></a>When receiving the [AddOffsetsToTxnRequest](#_cqecubxziwie) from a producer, the following steps will be executed in order to send back the response\.

1. If the TransactionalId does not exist in the TransactionalId mapping or if the mapped PID is different from that in the request, reply with [InvalidPidMapping](#7ztwi7m2pk9s); otherwise proceed to next step\.
2. If the PID’s epoch number is different from the current TransactionalId mapping, reply with the InvalidProducerEpoch error code; otherwise proceed to next step\.
3. If there is already an entry in the transaction status mapping, check if its status is BEGIN and the epoch number is correct, if yes calculate the internal offset topic partition based on the ConsumerGroupID field, append a BEGIN transaction message into the transaction topic with updated partition list, wait for this message to be replicated, update the transaction status entry and the timestamp in the TransactionalId map and reply OK with the calculated partition’s lead broker as the consumer coordinator; otherwise reply with InvalidTxnRequest error code\.
4. If there is no entry in the transaction status mapping reply with InvalidTxnRequest error code\.

<a id="aqrq1dgbix3a"></a>When receiving the [EndTxnRequest](#_2dnhvqqi7mr7) from a producer, the following steps will be executed in order to send back the response\.

1. If the TransactionalId does not exist in the TransactionalId mapping or if the mapped PID is different from that in the request, reply with [InvalidPidMapping](#7ztwi7m2pk9s); otherwise proceed to next step\.
2. Check if the PID’s epoch number is correct against the TransactionalId mapping\. If not, reply with the InvalidProducerEpoch error code; otherwise proceed to the next step\.
3. If there is already an entry in the transaction status mapping, check its status
	1. If the status is BEGIN, go on to step 4\.
	2. If the status is COMPLETE\_COMMIT and the command from the EndTxnRequest is COMMIT, return OK\.
	3. If the status is COMPLETE\_ABORT and the command from the EndTxnRequest is ABORT, return OK\.
	4. Otherwise, reply with InvalidTxnRequest error code\.
4. Update the timestamp in the TransactionalId map\.
5. Depending on the command field of the request, append a PREPARE\_XX transaction message to the transaction topic with all the transaction partitions kept in the transaction status map, wait until the message is replicated\.
6. [Commit](#3af5934pfogc) or [abort](#np4653erbekz) the transaction following the procedure depending on the command field\. 
7. Reply OK\.

__Discussion on Coordinator Committing Transactions\.__ The main motivation for having the transaction coordinator complete the commit / abort protocol after the PREPARE\_XXX transaction message is appended to the transaction log is to keep the producer client thin \(i\.e\. not letting producers to send the request to brokers to write [transaction markers](#_mylukj7bg1rf)\), and to ensure that transactions will always eventually be completed\. However, it comes with an overhead of increased inter\-broker communication traffic: suppose there are N producers sending messages in transactions, and each producer’s transaction rate is M/sec, and each transaction touches P topic partitions on average, inter\-broker communications will be increased by M \* N \* P round trips per sec\. We need to conduct some system performance test to make sure this additional inter\-broker traffic would not largely impact the broker cluster\.

__Discussion on Coordinator Failure During Transaction Completion__: It is possible for the coordinator to fail at any time during the completion of a transaction\. In general, the client responds by finding the new coordinator and retrying the EndTxnRequest\. If the coordinator had already written the PREPARE\_COMMIT or PREPARE\_ABORT status to the transaction log, and had begun writing the corresponding markers to the data partitions, then the new coordinator may repeat some of this work \(i\.e\. there may be duplicate COMMIT or ABORT markers in the log\), but this is not a problem as long as no new transactions have been started by the same producer\. It is also possible for the coordinator to fail after writing the COMPLETE\_COMMIT or COMPLETE\_ABORT status, but before the EndTxnRequest had returned to the user\. In this case, the client will retry the EndTxnRequest after finding the new coordinator\. As long as the command matches the completed state of the transaction after coordinator recovery, the coordinator will return a successful response\. If not for this, there would be no way for the client to determine what happened to the transaction\.

## <a id="_tqy9gm4d8r26"></a>Coordinator\-side Transaction Expiration

When a producer fails, its transaction coordinator should be able to pro\-actively expire its ongoing transaction\. In order to do so, the transaction coordinator will periodically trigger the following procedure:

1. Scan the transaction status map in memory\. For each transaction:
	1. If its status is BEGIN, and its corresponding expire timestamp is smaller than the current timestamp, pro\-actively expire the transaction by doing the following:
		1. First void the PID by bumping up the epoch number in the TransactionalId map and writing a new TransactionalId message into the transaction log\. Wait for it to be fully replicated\.
		2. Then [rollback](#a171p1veet5u) the transaction following the procedure __with the bumped up epoch number__, so that brokers can update their cached PID as well in order to fence Zombie writers \(see more discussions [below](#kix.uu5bwrue4nmm)\)\.
	2. If its status is PREPARE\_COMMIT, then complete the committing process of the transaction\.
	3. If its status is PREPARE\_ABORT, then complete the aborting process of the transaction\.

## <a id="_n3o7f9yqymnk"></a>Coordinator TransactionalId Expiration

Ideally, we would like to keep TransactionalId entries in the mapping forever, but for practical purposes we want to evict the ones that are not used any longer to avoid having the mapping growing without bounds\. Consequently, we need a mechanism to detect inactivity and evict the corresponding identifiers\. In order to do so, the transaction coordinator will periodically trigger the following procedure:

1. Scan the TransactionalId map in memory\. For each TransactionalId \-> PID entry, if it does NOT have a current ongoing transaction in the transaction status map, AND the age of the last completed transaction is greater than the [TransactionalId expiration config](#nfan5rg6kjgk), remove the entry from the map\. We will write the tombstone for the TransactionalId, but do not care if it fails, since in the worst case the TransactionalId will persist for a little longer \(ie\. the transactional\.id\.expration\.ms duration\)\.

__Discussion on PID Expiration__: It is possible for a producer to continue using the PID that its TransactionalId was mapped to in a non\-transactional way even after the TransactionalId has been expired\. If the producer continues writing to partitions without starting a new transaction, its PID will remain in the broker’s sequence table as long as the messages are still present in the log\. It is possible for another producer using the same TransactionalId to then acquire a new PID from the transaction coordinator and either begin using transactions or “idempotent mode\.” This does not violate any of the guarantees of either the idempotent or transactional producers\. 

1. For the transactional producer, we guarantee that there can be only one active producer at any time\. Since we ensure that active transactions are completed before expiring an TransactionalId, we can guarantee that a zombie producer will be fenced when it tries to start another one \(whether or not a new producer with the same TransactionalId has generated a new PID mapping\)\.
2. For the idempotent producer \(i\.e\., producer that do not use transactional APIs\), currently we do not make any cross\-session guarantees in any case\. In the future, we can extend this guarantee by having the producer to periodically send InitPidRequest to the transaction coordinator to keep the TransactionalId from expiring, which preserves the producer’s zombie defence\.

See [below](#_loujdamc9ptj) for more detail on how PID expiration works\.

## <a id="_iixbdsg65d7k"></a>Added Broker Configurations

The following configs will be added to the broker:

transactional\.id\.expiration\.ms

The maximum amount of time in ms that the transaction coordinator will wait before proactively expire a producer TransactionalId without receiving any transaction status updates from it\.

Default is 604800000 \(7 days\)\. This allows periodic weekly producer jobs to maintain its id\.

<a id="kbzoiyjf76y"></a>max\.transaction\.timeout\.ms

The maximum allowed timeout for transactions\. If a client’s requested transaction time exceeds this, then the broker will return an error in InitPidRequest\. This prevents a client from too large of a timeout, which can stall consumers reading from topics included in the transaction\.

Default is 900000 \(15 min\)\. This is a conservative upper bound on the period of time a transaction of messages will need to be sent\.

transaction\.state\.log\.min\.isr

The minimum number of insync replicas for the transaction state topic\. 

Default: 2

transaction\.state\.log\.replication\.factor

The number of replicas for the transaction state topic\.

Default: 3

transaction\.state\.log\.num\.partitions

The number of partitions for the transaction state topic\.

Default: 50

transaction\.state\.log\.segment\.bytes

The segment size for the transaction state topic\.

Default: 104857600 bytes\.

transaction\.state\.log\.load\.buffer\.size

The loading buffer size for the transaction stat topic\.

Default: 5242880 bytes\.

## <a id="_3q05uee3vqcq"></a>Authorization

It is desirable to control access to the transaction log to ensure that clients cannot intentionally or unintentionally interfere with each other’s transactions\. In this work, we introduce a new resource type to represent the TransactionalId tied to transactional producers, and an associated error code for authorization failures\.

<a id="vg8ouwgaahtr"></a>case object ProducerTransactionalId extends ResourceType \{

  val name = "ProducerTransactionalId"

  val errorCode = Errors\.TRANSACTIONAL\_ID\_AUTHORIZATION\_FAILED\.code

\}

 

The transaction coordinator handles each of the following requests: [InitPID](#_z99xar1h2enr), [AddPartitionsToTxn](#_r6klddrx9ibz), [AddOffsetsToTxn](#_cqecubxziwie), and [EndTxn](#_2dnhvqqi7mr7)\. Each request to the transaction coordinator includes the producer’s TransactionalId and can be used for authorization\. Each of these requests mutates the transaction state of the producer, so they all require Write access to the corresponding ProducerTransactionalId resource\. Additionally, the AddPartitionsToTxn API requires Write access to the topics corresponding to the included partitions, and the AddOffsetsToTxn API requires Read access to the group included in the request\.

<a id="8rr1zeeiqr32"></a>__Discussion on limitations of coordinator authorization__: Although we can control access to the transaction log using the TransactionalId, we cannot prevent a malicious producer from hijacking the PID of another producer and writing data to the log\. This would allow the attacker to either insert bad data into an active transaction or to fence the authorized producer by forcing an epoch bump\. It is not possible for the malicious producer to finish a transaction, however, because the brokers do not allow clients to write control messages\. Note also that the malicious producer would have to have Write permission to the same set of topics used by the legitimate producer, so it is still possible to use topic ACLs combined with TransactionalId ACLs to protect sensitive topics\. The brokers can verify the TransactionalId in produce requests, which ensures that the client has been authorized for transactions, but does not authorize the PID directly\. Future work can explore protecting the binding between TransactionalId and PID \(e\.g\. through the use of message authentication codes such as in [KIP\-48](https://cwiki.apache.org/confluence/display/KAFKA/KIP-48+Delegation+token+support+for+Kafka)\)\.

# <a id="_pabpm8thuoag"></a>__Broker__

Besides fencing duplicate messages and Zombie writers based on the PID, epoch and sequence number in the produce request as described in the [Transactional Producer](#_dz78fc8xlsd5) section, each broker must also handle requests sent from the transaction coordinators for writing the commit and abort markers into the log\.

At the same time, brokers also need to handle requests from clients asking for their assigned coordinator, which will be the leader broker of the transaction topic’s partition calculated from the producer’s TransactionalId\.

## <a id="_mylukj7bg1rf"></a>Transaction Markers

Transaction markers are written by the brokers themselves as control messages\. As described in the [Message Format](#_pau6n6buvoee) section, we will refactor the on\-disk message format by separating the message level and message\-set level schema, and one of the changes is to use 2 bytes for the message set level attributes \(previously it was only one byte\)\. We use one bit from these attributes to indicate that the message set is transactional \(i\.e\. it contains messages which are part of a transaction\)\.

Compression \(3\)

Timestamp type \(1\)

__Transactional \(1\)__

Unused \(11\)

Transaction control messages are normal Kafka messages, but we use one bit in the message\-level attributes to indicate that the message is a control message\.

__Control Flag \(1\)__

Unused \(7\)

The type of the control message is packed into the message key\. This proposal adds two [control message types](#4vuehwc5b7s):

1. COMMIT \(ControlMessageType = 0\)
2. ABORT \(ControlMessageType = 1\)

The schema of the control message value field is specific to the control message type\. Generally we assume this schema is opaque to clients\. For the transaction markers, we use the following schema:

TransactionControlMessageValue => Version CoordinatorEpoch

  Version => int16

  CoordinatorEpoch => int32

The timestamp in these control messages will always be the log append time\. However, this timestamp will not be indexed, and hence seeking by timestamp will ignore control messages\. 

<a id="ptdscx8pzota"></a>__Discussion on Coordinator Fencing: __The transaction coordinator uses the [WriteTxnMarker](#_jtpvkrldhb7) API to write the COMMIT and ABORT control messages to the leaders of partitions included in the transaction\. It is possible for an unexpected pause on the coordinator to cause that request to be delivered after another transaction coordinator has been promoted \(through partition leader election\), and even after the pending transaction was completed and a new transaction begun\. The partition leader must be able to detect this situation and reject the WriteTxnMarker request from the zombie coordinator or the current transaction could be inadvertently committed or aborted\. This is the purpose of the CoordinatorEpoch field that we have included in the control messages\. The value of this field corresponds to the leader epoch of the partition in the transaction log topic that the given producer was assigned to\. Producers which were assigned different transaction coordinators can \(and will\) write to the same data partition, so coordinator epoch tracking is per\-producer, as is shown in the section below on [PID sequence management](#_kivcfzwo3zm1)\.

### <a id="_8nl3bhqx81oo"></a>Last Stable Offset Tracking

We require the broker to track the *last stable offset* \(LSO\) for each partition\. The LSO is defined as the latest offset such that the status of all transactional messages at lower offsets have been determined \(i\.e\. committed or aborted\)\.  To do this, the broker must maintain in memory the set of active transactions along with their initial offsets\. The LSO is always equal to the minimum of the initial offsets across all active transactions\. This serves two purposes:

1. In the READ\_COMMITTED isolation level, only offsets lower than the LSO are exposed to consumers\.
2. The LSO and the initial offset of each transaction is needed when writing entries to the aborted transaction index \(discussed in the next section\)\.

### <a id="_g0jqni2vcxg4"></a>Aborted Transaction Index

In addition to writing the ABORT and COMMIT control messages when receiving [WriteTxnMarker](#_bgy58z5pyzw7) requests from the transaction coordinator, each broker maintains a separate index to keep track of aborted transactions\. This facilitates the [READ\_COMMITTED](#nogouvgyxtup) isolation level for consumers\.

Each log segment for a partition is given a separate append\-only file to serve as an index for all transactions which were aborted in the corresponding log segment\. This file is created lazily upon the first aborted transaction written to each log segment\. We assume generally that aborted transactions are rare, so this file should stay small\.

The schema for the entries in this index is the following:

TransactionEntry => 

  Version => int16

  PID => int64

  FirstOffset => int64

  LastOffset => int64

  LastStableOffset => int64

The LSO written in each entry is relative to the time that the entry was inserted\. This allows the broker to efficiently scan through the aborted transactions corresponding to a fetch range in order to find the list of transactions from that range which were aborted\. The first and last offset are used to filter the transactions which are actually needed for a given fetch response\.

When log segments are deleted, so is the corresponding aborted transaction index\. 

### <a id="_ft6h1rabna7a"></a>Compacting Transactional Data

The presence of transaction markers creates two challenges for the log cleaner:

1. The log cleaner must be transaction\-aware\. Messages from aborted transactions should not cause the removal of any messages from committed transactions\.
2. Care must be taken when removing transaction markers\. If the messages from an aborted transaction is removed at or around the same time as the ABORT marker itself, it is possible for a consumer to see the aborted data, yet miss the marker\. 

Making the log cleaner transaction\-aware is straightforward\. Since we have the aborted transaction index, we can remove the messages from aborted transactions by following the entries from the index \(in much the same way that a consumer would\)\. The only restriction is that we cannot clean beyond the LSO\. This is unlikely to be much of a restriction in practice since we usually expect the LSO to be contained in the active segment, which is not currently not cleaned\.

The second issue is similar to a known [bug](https://issues.apache.org/jira/browse/KAFKA-4545) in the log cleaner, and we propose to piggyback on top of the solution to address it\.

## <a id="_kivcfzwo3zm1"></a>PID\-Sequence Management

For each topic partition, the broker will maintain a mapping in memory from the PID to the epoch, sequence number, the last offset successfully written to the log, and the coordinator epoch from each producer \(for transactional producers\)\. The source of truth for this information is always the log itself\.

### <a id="_vjqzk4b5fdhg"></a>PID Snapshots

When a broker is restarted, it is possible to recreate the entire map of PIDs and corresponding sequence numbers by scanning the full log\. However, it is more efficient to write periodic checkpoints to disk containing the state of the map at a given offset\. We call this a *PID Snapshot*\. When the broker resumes, it only needs to read the latest snapshot and scan the remainder of the log from the corresponding snapshot offset\. This is similar to how the log cleaner works\. 

If for whatever reason, the snapshot file is deleted or the checkpointed offset is out of range, we can rebuild the map by scanning the full log\. This can happen, for example, in a power failure scenario if the log has not been flushed to disk\. To make it more likely that we can recover without requiring a full scan, we can keep several checkpoint files: if the last checkpointed offset is out of range, we can try one of the earlier ones\. 

The schema for the PID snapshot file is provided below:

PidSnapshot => Version CRC \[PidEntry\]

  Version => int16

  CRC => int32

  PidEntry => PID Epoch Sequence LastOffset

    PID => int64

    Epoch => int16

    Sequence => int32

    LastOffset => int64

    CoordinatorEpoch => int32

The name of the snapshot files indicates the corresponding offset from the log that the snapshot was taken from\. For example, “000000000050\.pidsnapshot” indicates the snapshot was taken from offset 50 \(in general, we will include 20 characters for the offset as is currently done with log segments\)\. 

In the interests of not scanning too far back in the log on startup or when log truncation happens during leader failover, we will create snapshots on a periodic basis\. A reasonable period seems to be 30 to 60 seconds\. We will maintain the two most recent snapshots so that in the worst case, we would have to scan at most 60 to 120 seconds of the log to rebuild the new Pid\-Sequence mapping\.

### <a id="_loujdamc9ptj"></a>PID Expiration

It would be undesirable to let the PID\-sequence map grew indefinitely, so we need a mechanism for __PID expiration__\. We expire producerId’s when the age of the last message with that producerId exceeds the transactionalId expiration time or the topic’s retention time, whichever happens sooner\. This rule applies even for non\-transactional producers\.

If the transactionalId expiration time is less than the topic’s retention time, then the producerId will be ‘logically’ expired\. In particular, its information will not be materialized in the producerId\->sequence mapping, but the messages with that producerId would remain in the log until they are eventually removed\.

__Discussion on PID expiration and Request Rejection\.__ Suppose that a producer sends no writes to a topic partition for a long enough duration that its PID is expired by the leader of that partition\. If it then attempts to send another write using the next expected sequence number, the broker must reject it with an error indicating the sequence number is out of range\. If not, we risk violating deduplication guarantees since we cannot know if the new message is a duplicate of a message which was already deleted\. This may cause “false negatives” since the broker could be unnecessarily rejecting the request, but users can control the risk of this through the topic’s retention settings\.

__Discussion on PID Expiration for Compacted Topics__\. PID expiration on compacted topics is a bit trickier since messages may be deleted in a different order than they were written by the producer\. Hence we take a slightly different approach: before beginning a round of cleaning, we build a set of candidate PIDs for expiration\. For example, we can populate the candidates with the PIDs which have not written to the latest log segment\. As we iterate through the messages of the log, we remove entries from the candidate set as soon as we find any entry which will be retained\. Once we reach the end, we know that any remaining PIDs can be removed as long as the last written offset is smaller than the next dirty offset\.

The difficulty with this approach is that the amount of elapsed time before PID expiration is then nondeterministic\. If a producer is “unlucky,” its last message may be overwritten by another producer shortly after its own write\. If this was its only entry in the log and we expire the PID, then the producer will receive an [InvalidSequenceNumber](#bj0lez6mjxeb) error on its next produce attempt\. We therefore need to ensure that the last sequence number for each producer is retained in the log for a deterministic amount of time\. 

To address this problem, we propose to preserve the last epoch and sequence number written by each producer\. This is allowed by the new message format we are proposing in this document\. As before, we retain this message until the transactionalId expiration timeout or the topic’s retention time elapses, whichever happens sooner\. 

## <a id="_m9yg71bk9idd"></a>Coordinator\-Broker request handling

<a id="x3qeh1kmz4ka"></a>When receiving the [WriteTxnMarkerRequest](#_jtpvkrldhb7) from a transaction coordinator, the following steps will be executed to send back the response\.

1. If the broker has a corresponding PID, verify that the received producer epoch is greater than or equal to the current epoch\. If not, return [InvalidProducerEpoch](#7mxekgcpri9x)\. Otherwise, insert the PID to its maintained list and update the epoch if necessary\.
2. Append a COMMIT or ABORT transaction marker as a control message to each of the indicated partitions in the request, depending on the Marker field of the request\. Wait for the message to be fully replicated and then reply OK\.
3. If the Marker field is COMMIT and partition lists include the internal offset topic, then proceed to [reflect](#55yzhvkppi6m) the committed offsets into its consumer coordinator’s cache\.
4. If the Marker field is ABORT, add an entry to the aborted transaction index using the current LSO and the starting and ending offsets from the transaction\.
5. Update the LSO for the partition if necessary \(i\.e\. if the current LSO is one less than the initial offset of the completed transaction\)\.

<a id="kix.uu5bwrue4nmm"></a>__Discussion on Transaction\-based Fencing\. __Upon failure recovery, a producer instance needs to fence off earlier instances before it resumes producing new data\. It does it through the [InitPidRequest](#_z99xar1h2enr) as the epoch number returned in the response is strictly greater than any previous epochs that has been ever returned in other InitPIDResponse for this TransactionalId\.

Now the question is, with TransactionalId expiration based on its producer’s transaction activity, i\.e\., if the producer does not send any of the transaction requests to the coordinator for some period of time, then its TransactionalId \-> PID will expire, so is zombie writer fencing still effective? The short answer is “yes” and the long answer goes in two folds:

1. After the producer has already lost its TransactionalId entry \(and hence becomes a zombie\), if the producer ever tries to start a new transaction, it must talk to the coordinator and hence will be notified that its PID is no longer valid\. *If the producer does not ever start a transaction again, then it can continue sending messages with the old PID and this zombie writer is hence not fenced, which is OK according to our semantics\. *We only guarantee that two producers sharing the same TransactionalId are not allowed to execute transactions concurrently\.
2. If the zombie has an ongoing transaction at the time its TransactionalId mapping is expired, we know that the transaction would have been aborted first since we ensure that the TransactionalId expiration time is strictly larger than the transaction timeout\. Hence, any future transactional requests will receive the fatal ProducerFencedException, and the producer will abort\. 

## <a id="_d42xl2uz6r8j"></a>Client\-Broker request handling

<a id="kix.oz3ruj5vub78"></a>When receiving the [InitPidRequest](#_z99xar1h2enr) from a producer, the following steps will be executed in order to send back the response:

1. If the TransactionalId is provided, forward the request to the transaction coordinator module and follow the procedure [here](#jro89lml46du)\.
2. Assign a new PID to the producer and send a response\.

<a id="8biz78fs2t9"></a>When receiving the [GroupCoordinatorRequest](#_97qeo7mkx9jx) from a producer with the CoordinatorType as “transaction,” the following steps will be executed to send back the response\.

1. Compute the partition id based on the provided TransactionalId\.
2. Check its metadata for the topic\-partition’s leader, if exist then return the broker information; otherwise reply CoordinatorNotAvailable error\.

<a id="kix.x4k78mufyunt"></a>When receiving a [ProduceRequest](#_mkk6ckzh7jxl) from a producer, the following steps will be executed to send back the response\.

1. Check whether the produce request has a PID\. 
	1. Clients which have upgraded, but which are not yet using idempotent or transactional features will have an invalid PID and will go through the normal produce logic\. 
	2. If the request has a PID, check whether the topic supports the new message format\. If not, respond with the UNSUPPORTED\_FOR\_MESSAGE\_FORMAT error\.
2. Check the sequence number map to determine if the PID is already present\. 
	1. If it is not, check sequence number\. 
		1. If it is 0, insert the PID, epoch, and sequence number into the PID mapping table and proceed to append the entries\.
		2. If the sequence is not zero, return the [InvalidSequenceNumber](#bj0lez6mjxeb) error code\.
	2. If the PID is present in the mapping, check the epoch\. 
		1. If the epoch is older than the current one, return [InvalidProducerEpoch](#7mxekgcpri9x) error code\.
		2. If the epoch matches the current epoch, check the sequence number:
			1. If it matches the current sequence number, allow the append\. 
			2. If the sequence number is older than the current sequence number, return [DuplicateSequenceNumber](#cx6ufvjedx6a)\. 
			3. If it is newer, return InvalidSequenceNumber\.
		3. If the epoch is newer, check the sequence number\. If it is 0, update the mapping and allow the append\. If it is not 0, return InvalidSequenceNumber\.

<a id="hg94n33p3rmp"></a>When receiving a [FetchRequest](#_p3hvy6kufvd1) from a consumer, the following steps will be executed to send back the response:

1. If the isolation level of the request is READ\_UNCOMMITTED, follow the normal fetch flow\. In the remaining steps, we assume READ\_COMMITTED\.
2. Fetch the data from the log according to existing logic\. The initial offset is taken from the fetch request and we use the max fetch size to find the range of the log file to return in the fetch response\.
3. Determine the range of offsets from the fetched data\. We know the initial offset from the request, but we need to do some work to find the final offset\. We propose to use the offset index to lookup an approximate upper bound for the last offset in the fetch range using the last byte position in the log file included in the fetched data\.
4. Scan the aborted transaction indices of all log segments greater than or equal to the segment including the first fetched offset\. Build a list of all aborted transactions which intersect the fetch range\. We can stop when we have either reached an entry with an LSO greater than or equal to the last offset in the fetch, or when there are no more segment indices to scan\.
5. Return the fetched data and the list of aborted transactions to the client\.

## <a id="_cbb5tvp30uqe"></a>Authorization

From a security perspective, there are three main points we need to address:

- Authorize clients to produce to a topic \(including the writing of control messages\)
- Authorize clients to consume from a topic
- Authorize clients to access the transaction log \(via coordinator only\)

We can address the first two points primarily by relying on ACLs that grant read/write access to topics\. There is no change required to the security framework in the case we use read/write operations with the topic resource\. 

However, we will introduce additional authorization to produce transactional data\. This can be used to minimize the risk of an “endless transaction attack,” in which a malicious producer writes transactional data without corresponding COMMIT or ABORT markers in order to prevent the LSO from advancing and consumers from making progress\. We can use the [ProducerTransactionalId](#vg8ouwgaahtr) resource introduced above to ensure that the producer is authorized to write transactional data\. The producer’s TransactionalId is also included in the [ProduceRequest](#_mkk6ckzh7jxl) schema\. 

Additionally, since the [WriteTxnMarker](#_jtpvkrldhb7) API should only be allowed if the request came from another broker, the client must have ClusterAction authorization on the Cluster resource\. This is the same way that other inter\-broker APIs are protected\. Also note that this is the only API through which a control message can be added to the log\. An attempt to do so through the Produce API will be rejected\.

For the transaction log, we can also use ACLs that grant read/write access to the transaction log, although in this case access is through the coordinator and the coordinator is responsible for authorizing access through authorization of the TransactionalId\. Clients will not be allowed to write directly to the transaction log using the Produce API, though it is useful to make it accessible to consumers with Read permission for the purpose of debugging\.

In addition, this design protocol does not prevent a malicious producer client from “hijacking” another producer’s PID and hence its transactions \(since the PID itself is a random 64\-bit, in practice it should be very rare\), as long as it is authorized to use transaction and / or write to one of the corresponding data partitions\. We leave this for future work\.

# <a id="_qv0jmz7iwucq"></a>__Consumer Coordinator__

As mentioned in the [summary](#_f0eotchjto1f), many Kafka streaming applications need to both consume from input topics and produce to output topics at the same time\. When consumer offsets are committed for the input topics, they need to be done along with the produced transactions as well, such that *for each message consumed from the input Kafka topics, the result message\(s\) of processing this message will be reflected in the output Kafka topics exactly once, even under failures*\. 

In order to support this scenario, we need to make the consumer coordinator transaction\-aware\. More specifically, we need a new API which allows the producer to send offset commits as part of a transaction\. For this we introduce the [TxnOffsetCommitRequest](#_5695qbm2hne) API\.

## <a id="_yct0oc2nyy0h"></a>Client\-Coordinator request handling

<a id="55yzhvkppi6m"></a>When receiving the [TxnOffsetCommitRequest](#_5695qbm2hne) from a producer, the following steps will be added before proceed to the checking consumer group metadata\.

1. Skip the check for whether or not the consumer is valid for the current generation id and the member list \(the request does actually not contain this information\), i\.e\. blindly accept the request and proceed to the next step directly\.
2. Check if the specified PID is recognized and the Epoch is correct, if not reply with [InvalidProducerEpoch](#7mxekgcpri9x); otherwise proceed to the next step\.
3. Append to the offset topic, but skip updating the offset cache in the delayed produce callback, until a [WriteTxnMarkerRequest](#_jtpvkrldhb7) from the transaction coordinator is [received](#x3qeh1kmz4ka) including the offset topic partitions\.

__Discussion on NOT Checking Consumer Status for Transactional Offset Commits\.__ Note that in the [TxnOffsetCommitRequest](#_5695qbm2hne) we do not include the consumer group memberID and the generationID fields, and hence the consumer coordinator cannot validate this information upon receiving the request as we do in the OffsetCommitRequest\. This is not necessary because if a rebalance has happened and this consumer instance becomes a zombie, even if this offset message is appended in the offset topic, the transaction will be rejected later on when it tries to commit the transaction via the [EndTxnRequest](#_2dnhvqqi7mr7)\.

## <a id="_ul06rr7fcm7w"></a>Consumer Coordinator Startup

In addition, when initializing the consumer coordinator by bootstrapping from the offset topic, we also need to change the procedure as follows:

1. For each consumer offset message read from the offset topic, check if PID and Epoch fields are specified, if yes hold it from putting into the cache\.
2. For each control message read from the offset topic, if it is a COMMIT transaction marker then put the previously kept offset entry into the cache; if it is an ABORT transaction maker then forget the previously kept offset entry\.

<a id="39evp18kkic5"></a>__Discussion on Reusing Offset Topics within Transactions__\. An alternative approach to enable applications to commit input topic offsets as part of an output transaction is to use a separate topic\. More specifically, suppose an application task that consumes from a topic *TC, *processes the input messages, and produces to a topic *TP\. *The application task can use a separate topic, e\.g\., *Tio *, to store the input offsets\. The application persists the consumed offsets in this way so that the output messages and messages containing the input offsets are produced as a transaction of messages atomically: the output messages are successfully produced to *Tp* iff the input offset messages are successfully produced \(committed\) to *Tio*\. There are pros and cons for this alternative approaches regarding implementation complexity, operations / tooling, and security, etc, which is summarized in this [doc](https://docs.google.com/document/d/1m62JZGDnAURKZbtlVMokhDk-0RG3qGysyesEPeRrPqQ)\. We decided to reuse the offset topic primarily for its tooling and operation simplicity\.

# <a id="_od2aaa53rbv"></a>__Consumer__

Consumers need to recognize the [transaction marker](#_mylukj7bg1rf) control messages when fetching from the brokers, and based on its configuration parameter to determine whether it wants to return committed messages only or all messages \(no matter if they are already committed or not\)\. In either case, messages are always delivered in offset order\.

## <a id="_wvdrakld4019"></a>Added Configurations and Consume Protocol

The following configs will be added to the KafkaConsumer client:

<a id="nogouvgyxtup"></a>isolation\.level

Here are the possible values:

read\_uncommitted: consume all available messages in offset ordering\. This is the default value\.

read\_committed: only consume non\-transactional messages or transactional messages that are already committed, in offset ordering\.

Default: read\_uncommitted

The isolation level is passed to the brokers in the [FetchRequest](#_p3hvy6kufvd1)\. For READ\_UNCOMMITTED, the consumer uses the same fetch logic as previous versions of Kafka\. For READ\_COMMITTED, the consumer must do some extra work to filter aborted transactions\. 

Recall that the fetch response contains a list of the aborted transactions which intersect with the fetch range\. Any transactions included in a fetch which are not among the aborted transactions are assumed to be committed\. The broker will not return records from any offsets lower than the current LSO in READ\_COMMITTED, so we are guaranteed that all transactions received in the fetch have been completed\.

Initially, the aborted transactions from the fetch response are arranged in a minheap, which is ordered by their initial offsets\. Separately, we maintain a set of the PIDs which have aborted transaction ranges which contain the consumer’s current position\. The logic to follow when processing the fetched data is as follows:

1. If the message is a transaction control message, and the status is ABORT, then remove the corresponding PID from the set of PIDs with active aborted transactions\. If the status is COMMIT, ignore the message\.
2. If the message is a normal message, compare the offset and PID with the head of the aborted transaction minheap\. If the PID matches and the offset is greater than or equal to the corresponding initial offset from the aborted transaction entry, remove the head from the minheap and insert the PID into the set of PIDs with aborted transactions\.
3. Check whether the PID is contained in the aborted transaction set\. If so, discard the record set; otherwise, add it to the records to be returned to the user\.

When seeking to a new offset, the consumer will clear the transaction state for that partition\. It can always rebuild it after the next fetch response is received\.

<a id="h19xy0kiexf0"></a>__Discussion on Seeks\.__ The isolation level will be used to determine the behavior of seeks\. In READ\_UNCOMMITTED, the seek API behaves exactly as it does currently\. In READ\_COMMITTED, the only difference is that the consumer is not allowed to seek beyond the log’s LSO\. In other words, a call to seekToEnd\(\) will result in the offset being set to the LSO instead of the log end offset\. For this, we have updated the [ListOffsets](#_d2pvxax47irz) API to support retrieval of the LSO\.

__Discussion on Pro\-active Transaction Timeout\.__ One motivation to let transaction coordinator to pro\-actively timeout transactions is that upon producer failure, we do not want to rely on the producer eventually recovering and completing the transaction: for example, if a producer fails within a transaction and the coordinator does not pro\-actively abort it, this transaction will become a “dangling” transaction that will not be completed until the producer resumes with the same TransactionalId, and any consumers fetching on the partitions included in this transaction in READ\_COMMITTED will be effectively blocked waiting for the LSO to advance\. This issue will be more severe if one topic partition has multiple transactional producers writing to it in an interleaving manner, since one dangling transaction will cause all other transactions to not be able to be consumed due to the offset ordering\.

One question though, is whether a pro\-active timeout of a transaction will still fence a zombie writer\. The short answer is yes\. Upon timing out and aborting the transaction, the coordinator will bump the epoch associated with the PID and write ABORT markers to all partitions which had been included in the transaction\. If the zombie is still attempting to write to any of these partitions, it will be fenced as soon as the ABORT marker is written\. Alternatively, if it attempts to commit or abort the transaction, it will also be fenced by the coordinator\.

<a id="z9b0a6k2a3zm"></a>__Discussion on Transaction Ordering\. __In this design, we are assuming that the consumer delivers messages in offset order to preserve the behavior that Kafka users currently expect\. A different way is to deliver messages in “transaction order”: as the consumer fetches commit markers, it enables the corresponding messages to be consumed\. For example, say that we have two TransactionalIds *P1* and *P2* both producing to the same given topic partition and the messages of these two TransactionalIds are interleaved in the log\. A consumer of this partition allocates a buffer for the messages of *P1* and another for the messages of *P2*\. Upon receiving a commit marker for *P*2, the consumer delivers the buffered messages of *P2* immediately\. If it receives a commit marker for *P1* while delivering the messages of *P2*, then it queues the messages of *P1* to be delivered after the ones of *P*2\. 

One main advantage of this approach is that it delivers messages as soon as the commit marker is processed, and hence improves latency\. The main reason for stepping away from this option is offset management: because consumed messages do not follow offset order, an application that manually manages offsets needs to store more information than just the last offset consumed\. The application needs to at least keep the first missing message and all other messages it has already processed; when automating offset commits for commit order delivery, we must track the offset of the last commit marker processed\. Upon resuming a consumer for the partition, we need to start processing commit markers from the last persisted, and for every new commit marker the consumer processes, it needs to scan the logs backwards to find the messages to deliver, assuming they are not already buffered\. If they are already buffered, then it is just a matter of delivering\. To illustrate the worst case scenario, say we have the following content in the log:

C2, M3, C1, M2, M1

- M1 is the head \(offset 0\) 
- M1 is associated to commit C1
- M2 and M3 are associated to commit C2 

Say that a consumer reads *C1*, *M2*, *M1* and delivers *M1* after processing *C1*\. The consumer commits the offset for *C1*, which is 2\. The consumer crashes and some other consumer in the group picks up the partition\. The new consumer resumes from offset 2, it reads *C2* and* M3*, but once it processes the commit marker, it does not know what other messages from that transaction it is missing\. In particular, it is missing *M2* and it needs to scan backwards from C1 to find *M2*\. Once it finds *M2*, it continues to scan backwards depending on the commit sequence number of the message: if it is greater than zero, then it needs to keep scanning\.

It is important to note that this behavior can be implemented at a later time if we determine that it is useful and critical for the success of the feature\. For the moment, given that we have no strong evidence that applications can benefit from the commit order option, we opted for not implementing it\. 

# <a id="_pau6n6buvoee"></a>__Message Format__

In order to add new fields such as PID and epoch into the produced messages for transactional messaging and de\-duplication, we need to change Kafka’s message format and bump up its version \(i\.e\. the “magic byte”\)\. More specifically, we need to add the following fields into each message:

- PID => int64
- Epoch => int16
- Sequence number => int32

Adding these fields on the message\-level format schema potentially adds a considerable amount of overhead; on the other hand, at least the PID and epoch will never change within a set of messages from a given producer\. We therefore propose to enhance the current concept of a __message set__ by giving it a separate schema from an individual message\. In this way, we can locate these fields only at the message set level which allows the additional overhead to be amortized across batches of messages rather than paying the cost for each message separately\. 

Both the epoch and sequence number will wrap around once int16\_max and int32\_max are reached\. Since there is a single point of allocation and validation for both the epoch and sequence number, wrapping these values will not break either the idempotent or transactional semantics\. 

For reference, the current message format \(v1\) is the following:

MessageSet => \[Offset MessageSize Message\]

  Offset => int64

  MessageSize => int32

Message => Crc Magic Attributes Timestamp Key Value

  Crc => int32

  Magic => int8

  Attributes => int8

  Timestamp => int64

  Key => bytes

  Value => bytes

A message set is a sequence of messages\. To support compression, we currently play a trick with this format and allow the compressed output of a message set to be embedded in the value field of another message \(a\.k\.a\., the “wrapper message”\)\. In this design, we propose to extend this concept to non\-compressed messages and to decouple the schema for the message wrapper \(which contains the compressed message set\)\. This allows us to maintain a separate set of fields at the message set level and avoid some costly redundancy \(old fields in blue, changes in red\):

MessageSet => 

- FirstOffset => int64
- Length => int32
- CRC => int32
- Magic => int8  /\* bump up to “2” \*/
- Attributes => int16
- LastOffsetDelta => int32
- FirstTimestamp => int64
- MaxTimestamp => int64
- PID => int64
- Epoch => int16
- FirstSequence => int32
- Messages => Message1, Message2, … , MessageN

Message =>

- Length => varint
- Attributes => int8
- TimestampDelta => varint
- OffsetDelta => varint
- KeyLen => varint
- Key => data
- ValueLen => varint
- Value => data

The ability to store some fields only at the message set level allows us to conserve space considerably when batching messages into a message set\. For example, there is no need to write the PID within each message since it will always be the same for all messages within each message set\. In addition, by separating the message level format and message set format, now we can also use variable\-length types for the inner \(relative\) offsets and save considerably over a fixed 8\-byte field size\. Note also that there is only a single CRC computed for the full message set\.

## <a id="_tp1lvfionrzv"></a>Message Set Fields

The first four fields of a message set in this format *must to* be the same as the existing format because any fields before the magic byte cannot be changed in order to provide a path for upgrades following a similar approach as was used in [KIP\-32](https://cwiki.apache.org/confluence/display/KAFKA/KIP-32+-+Add+timestamps+to+Kafka+message)\. Clients which request an older version of the format will require conversion on the broker\.

The offset provided in the message set header represents the offset __of the first message in the set__\. Similarly, we the sequence number field represents the sequence number of the first message\. We also include an “offset delta” at the message set level to provide an easy way to compute the last offset / sequence number in the set: i\.e\. the starting offset of the next message set should be “offset \+ offset delta”\. This also allows us to search for the message set corresponding to a particular offset without scanning the individual messages, which may or may not be compressed\. Similarly, we can use this to easily compute the next expected sequence number\.

The message set also includes the timestamp of the first message included in the message set\. All message timestamps are computed relative to this timestamp \(note that the timestamp delta in each message is a signed integer since timestamps are not assumed to be monotonic\)\. The max timestamp, which is needed for indexing, can be derived by summing the initial timestamp and the MaxTimestampDelta field, which is exposed in the message set\.

The offset, sequence number, and offset delta values of the message set *never* change after the creation of the message set\. The log cleaner may remove individual messages from the message set, and it may remove the message set itself once all messages have been removed, but we must preserve the range of sequence numbers that were ever used in a message set since we depend on this to determine the next sequence number expected for each PID\.

__Message Set Attributes__: The message set attributes are essentially the same as in the existing format, though we have added an additional byte for future use\. In addition to the existing 3 bits used to indicate the compression codec  and 1 bit for timestamp type, we will use another bit to indicate that the message set is transactional \(see [Transaction Markers](#_mylukj7bg1rf) section\)\. This lets consumers in READ\_COMMITTED know whether a transaction marker is expected for a given message set\.

Compression \(3\)

Timestamp type \(1\)

Transactional \(1\)

Unused \(11\)

__Discussion on Maximum Message Size__\. The broker’s configuration max\.message\.size previously controlled the maximum size of a single uncompressed message or a compressed set of messages\. With this design, it now controls the maximum message set size, compressed or not\. In practice, the difference is minor because a single message can be written as a singleton message set, with the small increase in overhead mentioned above\. 

## <a id="_c3oktixktv3b"></a>Message Fields

The length field of the message format is encoded as an unsigned variable\-length int, abbr\. “uintVar”\. Similarly the offset delta and key length fields are encoded as unitVar as well\. The message’s offset can then be calculated as the offset of the message set \+ offset delta\. At the end we still maintains a message\-level CRC \(reason discussed [below](#ap7gcx6kudun)\)\.

__Message Attributes__: In this format, we have also added a single byte for individual message attributes\. Only message sets can be compressed, so there is no need to reserve some of these attributes for the compression type\. The control flag indicates that the message is a control message, which means it is not intended for application consumption\. The remaining bits are currently unused, though one could be used for [KIP\-87](https://cwiki.apache.org/confluence/display/KAFKA/KIP-87+-+Add+Compaction+Tombstone+Flag) \(message tombstone marker\)\.

Control Flag \(1\)

Unused \(7\)

Control messages will always have a non\-null key, which is used to indicate the type of <a id="4vuehwc5b7s"></a>__control message type__ with the following schema:

ControlMessageKey => Version ControlMessageType

  Version => int16

  ControlMessageType => int16 

In this proposal, a control message type of 0 indicates a COMMIT marker, and a control message type of 1 indicates an ABORT marker\. The schema for control values is generally specific to the control message type\.

<a id="ap7gcx6kudun"></a>__Discussion on Message\-level Schema\.__ A few additional notes about this schema:

1. Having easy access to the offset of the first message allows us to stream messages to the user on demand\. In the existing format, we only know the last offset in each message set, so we have to read the messages fully into memory in order to compute the offset of the first message to be returned to the user\.
2. As before, the message set header has a fixed size\. This is important because it allows us to do in\-place offset/timestamp assignment on the broker before writing to disk\. 
3. We have removed the per\-message CRC in this format\. We hesitated initially to do so because of its use in some auditing applications for end\-to\-end validation\. The problem is that it is not safe, even currently, to assume that the CRC seen by the producer will match that seen by the consumer\. One case where it is not preserved is when the topic is configured to use the log append time\. Another is when messages need to be up\-converted prior to appending to the log\. For these reasons, and to conserve space and save computation, we have removed the CRC and deprecated client usage of these fields\.
4. The message set CRC covers the header and message data\. Alternatively, we could let it cover only the header, but if compressed data is corrupted, then decompression may fail with obscure errors\. Additionally, that would require us to add the message\-level CRC back to the message\.
5. Individual messages within a message set have their full size \(including header, key, and value\) as the first field\. This is designed to make deserialization efficient\. As we do for the message set itself, we can read the size from the input stream, allocate memory accordingly, and do a single read up to the end of the message\. This also makes it easier to skip over the messages if we are looking for a particular one, which potentially saves us from copying the key and value\. 
6. We have not included a field for the size of the value in the message schema since it can be computed directly using the message size and the length of the header and key\. 
7. We have used a variable length integer to represent timestamps\. Our approach is to let the first message 

## <a id="_fzysbytkejm4"></a>Space Comparison

As the batch size increases, the overhead of the new format grows smaller compared to the old format because of the eliminated redundancy\. The overhead per message in the old format is fixed at 34 bytes\. For the new format, the message set overhead is 53 bytes, while per\-message overhead ranges from 6 to 25 bytes\. This makes it more costly to send individual messages, but space is quickly recovered with even modest batching\. For example, assuming a fixed message size of 1K with 100 byte keys and reasonably close timestamps, the overhead increases by only 7 bytes for each additional batched message \(2 bytes for the message size, 1 byte for attributes, 2 bytes for timestamp delta, 1 byte for offset delta, and 1 byte for key size\) :

Batch Size

Old Format Overhead

New Format Overhead

1

34\*1 = 34

53 \+ 1\*7 = 60

3

34\*3 = 102

53 \+ 3\*7 = 74

10

34\*10 = 340

53 \+ 10\*7 = 123

50

34\*50 = 1700

53 \+ 50\*7 = 403

100

34\*100 = 3400

45 \+ 100\*7 = 745

## <a id="_mo0ibmx05lcn"></a>Client API Changes

As noted above, we have removed the individual message CRC computation from the message format\. This is currently exposed in the client API in RecordMetadata on the producer and in ConsumerRecord on the consumer\. We intend to deprecate these APIs:

class RecordMetadata \{

  @Deprecated

  long checksum\(\);

\}

class ConsumerRecord \{

  @Deprecated

  long checksum\(\);

\}

Until these methods are removed, we should continue to support these APIs\. We propose the following:

1. For old magic values \(version 0 and 1\), the checksum will continue to return the CRC from the message itself\.
2. For the new version, we will compute a checksum on\-demand using the message key, value and timestamp\.

<a id="3s6zgsaq9cep"></a>## <a id="_4q83oxy4ofov"></a>Migration Plan

We follow the same approach used in [KIP\-32](https://cwiki.apache.org/confluence/display/KAFKA/KIP-32+-+Add+timestamps+to+Kafka+message)\. To upgrade from a previous message format version, users should:

1. Upgrade the brokers once with the inter\-broker protocol set to the previous deployed version\.
2. Upgrade the brokers again with an updated inter\-broker protocol, but leaving the message format unchanged\.
3. Upgrade all or most clients, but continue with idempotence/transactions disabled\. Clients which attempt to use idempotent or transactional features will be rejected by the broker\.
4. Restart the brokers, with the message format version set to the latest\. It is also possible to enable the new message format on topics individually\.
5. Once the message format is upgraded, clients can enable idempotence/transactions production\.

The reason for step 3 is to avoid the performance cost for down\-converting messages to an older format, which effectively loses the “zero\-copy” optimization\. Ideally, all consumers are upgraded before the producers even begin writing to the new message format\.

### <a id="_u76s202ebj43"></a>Client Compatibility

In KIP\-97, the Java client enabled support for compatibility across different versions of the broker\. Below we describe the behavior for several important cases:

1. Since we were only supporting versions later than 0\.10\.0\.0, we have so far only needed to deal with one message format version\. After this KIP, the producer will need to support magic version 1 \(the current version\), and magic version 2 \(proposed here\)\. The version to use will depend on the version of the ProduceRequest that the broker supports\. A challenge here is that we need to know the supported version at the time record accumulation begins\. Our initial plan to address this is to connect to the partition leader prior to beginning accumulation for that partition\.
2. Old versions of the broker will obviously not support idempotent producing or transactions\. If either of these facilities are enabled and we encounter a broker which does not support them, an exception will be raised to the user\.
3. When fetching from an older broker \(with an older message format\), READ\_COMMITED behaves exactly the same as READ\_UNCOMMITTED\. The behavior only differs for messages which have the transactional bit set\.
4. When in READ\_COMMITTED mode, the behavior of seekToEnd is modified to seek to the LSO\. For older brokers which do not support the new ListOffsetRequest version \(which allows fetching the LSO\), we will retain the current behavior of seeking to the log end offset\.

### <a id="_tiigsrv40pcn"></a>Third\-party Authorizers

The addition of the ProducerTransactionalId resource means that third\-party authorizers will need to be updated before requests to the transaction coordinator can be protected\. In general we should emphasize that authorizers should reject authorization requests for unknown resource types\.

# <a id="_68og8h96rdei"></a>__RPC Protocol Summary__

We summarize all the new request / response pairs as well as modified requests in this section\. 

## <a id="_p3hvy6kufvd1"></a>FetchRequest

Sent by the consumer to any partition leaders to fetch messages\. We bump the API version to allow the consumer to specify the required [isolation level](#nogouvgyxtup)\.  We also modify the response schema to include the list of aborted transactions included in the range of fetched messages\.

// FetchRequest v4

FetchRequest => ReplicaId MaxWaitTime MinBytes MaxBytes IsolationLevel \[TopicName \[Partition FetchOffset MaxBytes\]\]

  ReplicaId => int32

  MaxWaitTime => int32

__  __MinBytes => int32

  __IsolationLevel => int8 \(READ\_COMMITTED | READ\_UNCOMMITTED\)__

  TopicName => string

  Partition => int32

  FetchOffset => int64

  MaxBytes => int32

  

// FetchResponse v4

FetchResponse => ThrottleTime \[TopicName \[Partition ErrorCode HighwaterMarkOffset __LastStableOffset AbortedTransactions__ MessageSetSize MessageSet\]\]

  ThrottleTime => int32

  TopicName => string

  Partition => int32

  ErrorCode => int16

  HighwaterMarkOffset => int64

  __LastStableOffset => int64__

  __AbortedTransactions => \[PID FirstOffset\]__

__    PID => int64__

__    FirstOffset => int64__

  MessageSetSize => int32

When the consumer sends a request for an older version, the broker assumes the READ\_UNCOMMITTED isolation level and converts the message set to the appropriate format before sending back the response\. Hence zero\-copy cannot be used\. This conversion can be costly when compression is enabled, so it is important to update the client as soon as possible\.

We have also added the LSO to the fetch response\. In READ\_COMMMITED, the consumer will use this to compute lag instead of the high watermark\.

## <a id="_mkk6ckzh7jxl"></a>ProduceRequest

Sent by the producer to any brokers to produce messages\. Instead of allowing the protocol to send multiple message sets for each partition, we change the handling of this request to only allow one message set for each partition\.  As long as there is only one message set to be written to the log, partial produce failures are no longer possible\. The full message set is either successfully written to the log \(and replicated\) or it is not\.

We include the TransactionalId in order to ensure that producers using transactional messages \(i\.e\. those with the transaction bit set in the attributes\) are authorized to do so\. If the client is not using transactions, this field should be null\.

// ProduceRequest v3

ProduceRequest => __TransactionalId__ 

                  RequiredAcks

 Timeout

                  \[TopicName \[Partition MessageSetSize MessageSet\]\]

  __TransactionalId => nullableString__

  RequiredAcks => int16

  Timeout => int32

  Partition => int32

  MessageSetSize => int32

  MessageSet => bytes

// ProduceResponse v3

ProduceResponse => \[TopicName \[Partition ErrorCode Offset Timestamp\]\]

                   ThrottleTime

  TopicName => string

  Partition => int32

  ErrorCode => int16

  Offset => int64

  Timestamp => int64

  ThrottleTime => int32

Error code:

- DuplicateSequenceNumber \[NEW\]
- InvalidSequenceNumber \[NEW\]
- InvalidProducerEpoch \[NEW\]
- UNSUPPORTED\_FOR\_MESSAGE\_FORMAT

Note that clients sending version 3 of the produce request MUST use the new [message set format](#_pau6n6buvoee)\. The broker may still down\-convert the message to an older format when writing to the log, depending on the internal message format specified\.

## <a id="_d2pvxax47irz"></a>ListOffsetRequest

Sent by the client to search offsets by timestamp and to find the first and last offsets for a partition\. In this proposal, we modify this request to also support retrieval of the last stable offset, which is needed by the consumer to implement seekToEnd\(\) in READ\_COMMITTED mode\.

// v2

ListOffsetRequest => ReplicaId \[TopicName \[Partition Time\]\]

  ReplicaId => int32

  TopicName => string

  Partition => int32

  Time => int64

ListOffsetResponse => \[TopicName \[PartitionOffsets\]\]

  PartitionOffsets => Partition ErrorCode Timestamp \[Offset\]

  Partition => int32

  ErrorCode => int16

  Timestamp => int64

  Offset => int64

The schema is exactly the same as version 1, but we now support a new sentinel timestamp in the request \(\-3\) to retrieve the LSO\.

## <a id="_97qeo7mkx9jx"></a>FindCoordinatorRequest

Sent by client to any broker to find the corresponding coordinator\. This is the same API that was previously used to find the group coordinator, but we have changed the name to reflect the more general usage \(there is no group for transactional producers\)\. We bump up the version of the request and add a new field indicating the group type, which can be either Consumer or Txn\. Request handling details can be found [here](#8biz78fs2t9)\.

// v2

FindCoordinatorRequest => CoordinatorKey CoordinatorType

  CoordinatorKey => string

  CoordinatorType => byte	/\* 0: consumer, 1: transaction \*/

FindCoordinatorResponse => ErrorCode Coordinator

  ErrorCode => int16

  Coordinator => NodeId Host Port

    NodeId => int32

    Host => string

    Port => int32

Error code:

- Ok
- CoordinatorNotAvailable

The node id is the identifier of the broker\. We use the coordinator id to identify the connection to the corresponding broker\.

## <a id="_z99xar1h2enr"></a>InitPidRequest

Sent by producer to its transaction coordinator to to get the assigned PID, increment its epoch, and fence any previous producers sharing the same TransactionalId\. Request handling details can be found [here](#jro89lml46du)\.

InitPidRequest => TransactionalId TransactionTimeoutMs

  TransactionalId => String

  TransactionTimeoutMs => int32

InitPidResponse => Error PID Epoch

  Error => Int16

  PID => Int64

  Epoch => Int16

Error code:

- Ok
- NotCoordinatorForTransactionalId
- CoordinatorNotAvailable
- ConcurrentTransactions
- InvalidTransactionTimeout

## <a id="_r6klddrx9ibz"></a>AddPartitionsToTxnRequest

Sent by producer to its transaction coordinator to add a partition to the current ongoing transaction\. Request handling details can be found [here](#aywz22lp6tma)\.

AddPartitionsToTxnRequest => TransactionalId PID Epoch \[Topic \[Partition\]\]

  TransactionalId => string

  PID => int64

  Epoch => int32

  Topic => string

  Partition => int32

AddPartitionsToTxnResponse => ErrorCode

  ErrorCode: int16

Error code:

- Ok
- InvalidProducerEpoch
- InvalidPidMapping
- NotCoordinatorForTransactionalId
- CoordinatorNotAvailable
- ConcurrentTransactions
- InvalidTxnRequest

#### <a id="_lm1uwwnjchsq"></a>

## <a id="_cqecubxziwie"></a>AddOffsetsToTxnRequest

Sent by the producer to its transaction coordinator to indicate a consumer offset commit operation is called as part of the current ongoing transaction\. Request handling details can be found [here](#5xa2hzric4h0)\.

AddOffsetsToTxnRequest => TransactionalId PID Epoch ConsumerGroupID

  TransactionalId => string

  PID => int64

  Epoch => int32

  ConsumerGroupID => string

AddOffsetsToTxnResponse => ErrorCode

  ErrorCode: int16

Error code:

- Ok
- InvalidProducerEpoch
- InvalidPidMapping
- ConcurrentTransactions
- NotCoordinatorForTransactionalId
- CoordinatorNotAvailable
- InvalidTxnRequest

## <a id="_2dnhvqqi7mr7"></a>EndTxnRequest

Sent by producer to its transaction coordinator to prepare committing or aborting the current ongoing transaction\. Request handling details can be found [here](#aqrq1dgbix3a)\.

EndTxnRequest => TransactionalId PID Epoch Command

  TransactionalId => string

  PID => int64

  Epoch => int32

  Command => boolean \(0 means ABORT, 1 means COMMIT\)

EndTxnResponse => ErrorCode

  ErrorCode => int16

Error code:

- Ok
- InvalidProducerEpoch
- InvalidPidMapping
- CoordinatorNotAvailable
- ConcurrentTransactions
- NotCoordinatorForTransactionalId
- InvalidTxnRequest

## <a id="_jtpvkrldhb7"></a>WriteTxnMarkerRequest

Sent by transaction coordinator to broker to commit the transaction\. Request handling details can be found [here](#x3qeh1kmz4ka)\.

WriteTxnMarkerRequest => \[PID Epoch Marker CoordinatorEpoch \[Topic \[Partition\]\]\]

  PID => int64

  Epoch => int32

  CoordinatorEpoch => int32

  Marker => int8 \(0 = COMMIT, 1 = ABORT\)

  Topic => string

  Partition => int32

WriteTxnMarkerResponse => \[Pid \[Topic \[Partition ErrorCode\]\]\]

  Topic => string

  Partition => int32

  ErrorCode => int16

Error code:

- Ok

## <a id="_5695qbm2hne"></a>TxnOffsetCommitRequest

Sent by transactional producers to consumer group coordinator to commit offsets within a single transaction\. Request handling details can be found [here](#55yzhvkppi6m)\. 

Note that just like consumers, users will not be exposed to set the retention time explicitly, and the default value \(\-1\) will always be used which lets broker to determine its retention time\.

TxnOffsetCommitRequest   => ConsumerGroupID 

                            PID

                            Epoch

                            RetentionTime

                            OffsetAndMetadata 

  ConsumerGroupID => string

  PID => int64

  Epoch => int32

  RetentionTime => int64

  OffsetAndMetadata => \[TopicName \[Partition Offset Metadata\]\]

    TopicName => string

    Partition => int32

    Offset => int64

    Metadata => string

TxnOffsetCommitResponse => \[TopicName \[Partition ErrorCode\]\]\]

  TopicName => string

  Partition => int32

  ErrorCode => int16

Error code:

- InvalidProducerEpoch

# <a id="_epod1jnz2d5"></a>Metrics

As part of this work, we would need to expose new metrics to make the system operable\. These would include:

1. Number of live PIDs \(a proxy for the size of the PID\->Sequence map\)
2. Current LSO per partition \(useful to detect stuck consumers and lost commit/abort markers\)\.
3. Number of active transactionalIds \(proxy for the memory consumed by the transaction coordinator\)\.

# <a id="_hapfg5juqkyc"></a>Future Work 

## <a id="_oc95ehvigl7v"></a>Recovering from correlated hard failures\.

When there is a correlated hard failure \(e\.g\., power outage\), it's possible that an existing commit/abort marker is lost in all replicas\. This may not be fixed by the transaction coordinator automatically and the consumer may get stuck on that incomplete transaction forever\. 

In particular, if a correlated hard failure causes us to lose the markers everywhere, the LSO on the broker would not advance and consumers would block \(but not buffer\)\. This would be a noticeable situation\.

A simple tool may make use of internal functions to effectively do a 'beginTransaction', 'AddTopicPartitiontoTransaction', 'commitTransaction'\. This would ensure that the markers are rewritten to all partitions by the transaction coordinator\.

To make this workable, we need to make sure that the transaction coordinator adds a sufficient logging so that we know the TransactionalId \-> PID mapping as well as the partitions participating in each transaction\. The broker should also probably log information so that we know which unfinished transaction \(ie\. which PID\) is preventing the LSO from moving forward\. Both these things will make it fairly easy to configure the tool\. 

Of course, it is possible for the producer to continue onto another transaction before the tool is run, in which case the data will be corrupt since the second transaction will include messages from the first\. But this is no worse than Kafka's existing durability semantics which this proposal relies on\.

## <a id="_u5fw38sut3ef"></a>Producer HeartBeat

Currently, there is no check for producer liveness\. This means that if a producer has not been sending transactional requests for a long time, its TransactionalId will be expired by the coordinator, making it a zombie who can no longer initiate transactions\. Adding a heartbeat thread would solve this problem and avoid surprises for applications\.

Another use case of producer heartbeat is that we can then do not let the “newcomers” \(i\.e\. a new client with the same TransactionalId\) to always kick out the old ones, which will be useful in some single\-writer scenario\.

## <a id="_3ie86d5xx5xx"></a>Update Producer Interceptor

It would make sense to add new APIs to the producer interceptor to expose the transaction lifecycle \(i\.e\. beginning, committing, and aborting transactions\)\. A challenge here is the fact that the client will not necessarily receive a commit or abort event in the case of a failure\. 

# <a id="_eze4c49k73f6"></a>Rejected Alternatives

<a id="6uypa62m3vgw"></a>## <a id="_lizhp2urqn59"></a>On TransactionalIds and PIDs

There have been multiple questions about the notions of TransactionalId and PID: about whether we need both, and if we need both, then can we generate the PID from the TransactionalId by some hash function on the client \(vs\. a broker side zookeeper based mapping\)\. 

The reason we went with a user\-provided TransactionalId that maps to a broker\-generated PID is the following: 

- A persistent identifier for a producer is essential for transactions to be recoverable across producer sessions\. For a generated identifier, the application must be responsible for its persistence\. In some cases, this is quite difficult\. For example, in Kafka Streams, we would like for each task to have its own transactional identifier\. If it is generated, then we must create a separate topic to map the taskId to the generated identifier so that we can always lookup the transactional id when a task is restarted\. This leads to a “bootstrapping problem” in which two instances of the same task \(say one of them is a zombie\) may both try to generate the identifier at the same time\. We cannot let them both succeed since we can only have one writer for each task\. One way to resolve this is to use a datastore with “first\-write\-wins” semantics, but this is awkward at best if implemented using a Kafka topic\. In any case, it seemed much simpler for the task to simply derive its transactional identifier from the taskId itself\.
- We would like to be able to  restrict access to the transaction log\. Doing so requires that we define the “resource” we are trying to protect\. For consumers, we have the consumer groupID, but producers would not have anything similar to use if the transactional ids were generated\. We could create a producer group, but it is quite awkward to need to introduce this concept only for the sake of authorization\. On the other hand, the TransactionalId gives us exactly what we need\. Authorizing a principal for a given TransactionalId gives them access to write to the transaction log using that TransactionalId\.
- We chose not to use the externally created TransactionalId directly in the message set because size is a premium\. The TransactionalId is a variable length string, and could be very long, so we were concerned about the increased overhead\. Hence we chose to store an internally generated PID in the message set instead\.
- We chose to generate the PID on the broker using zookeeper versus doing a static hash of TransactionalId to PID on the producer because we did not want to risk collisions\. When PIDs collide, one producer will fence the other off, and the only solution would be to restart one of the producers with a new TransactionalId with the hope that it doesn’t clash either\. This failure mode is non intuitive and potentially hard to fix\. Hence we decided not to use any sort of static hashing to map the TransactionalId to PID\. 

