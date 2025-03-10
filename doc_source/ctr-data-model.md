# Contact trace records \(CTR\) data model<a name="ctr-data-model"></a>

This article describes the data model for Amazon Connect contact trace records \(CTRs\)\. CTRs capture the events associated with a contact in your contact center\. Real\-time and historical metrics are based on the data captured in the CTRs\.

For the CTR retention period and maximum size of the CTR attributes section, see [Feature specifications](amazon-connect-service-limits.md#feature-limits)\.

For information about when a CTR is created \(and thus can be exported or used for data reporting\), see [Events in the contact trace record \(CTR\)](about-contact-states.md#ctr-events)\.

**Tip**  
Amazon Connect delivers CTRs at least once\. CTRs may be delivered again for multiple reasons, such as new information arriving after initial delivery\. If you're building a system that consumes CTR export streams, be sure to include logic that checks for duplicate CTRs for a contact\. Use the **LastUpdateTimestamp** property to determine if a copy contains new data than previous copies\. Then use the **ContactId** property for deduplication\. 

## Agent<a name="ctr-Agent"></a>

Information about the agent who accepted the incoming contact\.

**AgentInteractionDuration**  <a name="AgentInteractionDuration-CTR"></a>
The time, in whole seconds, that an agent interacted with a customer\.  
Type: Integer  
Min value: 0

**AfterContactWorkDuration**  <a name="AfterContactWorkDuration-CTR"></a>
The difference in time, in whole seconds, between `AfterContactWorkStartTimestamp` and `AfterContactWorkEndTimestamp`\.  
Type: Integer  
Min value: 0

**AfterContactWorkEndTimestamp**  
The date and time when the agent stopped doing After Contact Work for the contact, in UTC time\.  
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**AfterContactWorkStartTimestamp**  
The date and time when the agent started doing After Contact Work for the contact, in UTC time\.   
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**ARN**  
The Amazon Resource Name of the agent\.  
Type: ARN

**ConnectedToAgentTimestamp**  
The date and time the contact was connected to the agent, in UTC time\.  
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**CustomerHoldDuration**  <a name="CustomerHoldDuration-CTR"></a>
The time, in whole seconds, that the customer spent on hold while connected to the agent\.  
Type: Integer  
Min value: 0

**HierarchyGroups**  
The agent hierarchy groups for the agent\.  
Type: [AgentHierarchyGroups](#ctr-AgentHierarchyGroups)

**LongestHoldDuration**  
The longest time, in whole seconds, that the customer was put on hold by the agent\.  
Type: Integer  
Min value: 0

**NumberOfHolds**  
The number of times the customer was put on hold while connected to the agent\.  
Type: Integer  
Min value: 0

**RoutingProfile**  
The routing profile of the agent\.  
Type: [RoutingProfile](#ctr-RoutingProfile)

**Username**  
The username of the agent\.  
Type: String  
Length: 1\-100

## AgentHierarchyGroup<a name="ctr-AgentHierarchyGroup"></a>

Information about an agent hierarchy group\.

**ARN**  
The Amazon Resource Name \(ARN\) of the group\.  
Type: ARN

**GroupName**  
The name of the hierarchy group\.  
Type: String  
Length: 1\-256

## AgentHierarchyGroups<a name="ctr-AgentHierarchyGroups"></a>

Information about the agent hierarchy\. Hierarchies can be configured with up to five levels\.

**Level1**  
The group at level one of the agent hierarchy\.  
Type: [AgentHierarchyGroup](#ctr-AgentHierarchyGroup)

**Level2**  
The group at level two of the agent hierarchy\.  
Type: [AgentHierarchyGroup](#ctr-AgentHierarchyGroup)

**Level3**  
The group at level three of the agent hierarchy\.  
Type: [AgentHierarchyGroup](#ctr-AgentHierarchyGroup)

**Level4**  
The group at level four of the agent hierarchy\.  
Type: [AgentHierarchyGroup](#ctr-AgentHierarchyGroup)

**Level5**  
The group at level five of the agent hierarchy\.  
Type: [AgentHierarchyGroup](#ctr-AgentHierarchyGroup)

## ContactDetails<a name="ctr-contact-details"></a>

Contains user\-defined attributes which are lightly typed within the contact\.

**ContactDetailsName**  
Type: String  
Length: 1\-128

**ContactDetailsValue**  
Type: String  
Length: 0\-1024

**ReferenceAttributeName**  
Type: String  
Length: 1\-128

**ReferenceAttributesValue**  
Type: String  
Length: 0\-1024

## ContactTraceRecord<a name="ctr-ContactTraceRecord"></a>

Information about a contact\.

**Agent**  
If this contact successfully connected to an agent, this is information about the agent\.  
Type: [Agent](#ctr-Agent)

**AgentConnectionAttempts**  
The number of times Amazon Connect attempted to connect this contact with an agent\.  
Type: Integer  
Min value: 0

**Attributes**  
The contact attributes, formatted as a map of keys and values\.  
Type: Attributes  
Members: `AttributeName`, `AttributeValue` 

**AWSAccountId**  
The ID of the AWS account that owns the contact\.  
Type: String

**AWSContactTraceRecordFormatVersion**  
The record format version\.  
Type: String

**Channel**  
How the contact reached your contact center\.  
Valid values: Voice, Chat, Tasks

**ConnectedToSystemTimestamp**  
The date and time the customer endpoint connected to Amazon Connect, in UTC time\. For `INBOUND`, this matches `InitiationTimestamp`\. For `OUTBOUND`, `CALLBACK`, and `API`, this is when the customer endpoint answers\.  
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**ContactId**  
The ID of the contact\.  
Type: String  
Length: 1\-256

**CustomerEndpoint**  
The customer endpoint\.  
Type: [Endpoint](#ctr-endpoint)

**DisconnectTimestamp**  
The date and time that the customer endpoint disconnected from Amazon Connect, in UTC time\.  
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**DisconnectReason**  
Indicates how the contact was terminated\. This data is currently available in the Amazon Connect CTR stream only\.  
Type: String   
Voice contacts can have the following disconnect reasons:  
+ `CUSTOMER_DISCONNECT`: Contact disconnected first\.
+ `AGENT_DISCONNECT`: Agent disconnected when the contact was still on the call\.
+ `THIRD_PARTY_DISCONNECT`: In a third\-party call, after the agent has left, the third\-party disconnected the call while the contact was still on the call\.
+ `TELECOM_PROBLEM`: Disconnected due to an issue with connecting the call from the carrier, network congestion, network error, etc\.
+ `CONTACT_FLOW_DISCONNECT`: Call was disconnected in a flow\.
+ `OTHER`: This includes any reason not explicitly covered by the previous codes\. For example, the contact was disconnected by an API\.
Tasks can have the following disconnect reasons:  
+ `AGENT_DISCONNECT`: Agent marked the task as complete\.
+ `EXPIRED`: Task expired automatically because it was not assigned or completed within 7 days\.
+ `CONTACT_FLOW_DISCONNECT`: Task was disconnected or completed by a flow\.
+ `API`: The [StopContact](https://docs.aws.amazon.com/connect/latest/APIReference/API_StopContact.html) API was called to end the task\.
+ `OTHER`: This includes any reason not explicitly covered by the previous codes\.

**InitialContactId**  
If this contact is related to other contacts, this is the ID of the initial contact\.  
Type: String  
Length: 1\-256

**InitiationMethod**  
Indicates how the contact was initiated\.  
Valid values:  
+  `INBOUND`: The customer initiated voice \(phone\) contact with your contact center\. 
+  `OUTBOUND`: An agent initiated voice \(phone\) contact with the customer, by using the CCP to call their number\. This initiation method calls the [StartOutboundVoiceContact](https://docs.aws.amazon.com/connect/latest/APIReference/API_StartOutboundVoiceContact.html) API\.
+  `TRANSFER`: The customer was transferred by an agent to another agent or to a queue, using quick connects in the CCP\. This results in a new CTR being created\.
+  `CALLBACK`: The customer was contacted as part of a callback flow\. 

  For more information about the InitiationMethod in this scenario, see [About queued callbacks in metrics](about-queued-callbacks.md)\. 
+  `API`: The contact was initiated with Amazon Connect by API\. This could be an outbound contact you created and queued to an agent, using the [StartOutboundVoiceContact](https://docs.aws.amazon.com/connect/latest/APIReference/API_StartOutboundVoiceContact.html) API, or it could be a live chat that was initiated by the customer with your contact center, where you called the [StartChatConnect](https://docs.aws.amazon.com/connect/latest/APIReference/API_StartChatContact.html) API\.
+  `QUEUE_TRANSFER`: While the customer was in one queue \(listening to Customer queue flow\), they were transferred into another queue using a contact flow block\.
+  `DISCONNECT`: When a [Set disconnect flow](set-disconnect-flow.md) block is triggered, it specifies which contact flow to run after a disconnect event during a conversation\. A disconnect event is when an agent disconnects\. When the disconnect event occurs, the corresponding content flow runs\.

  If a new contact is created while running a disconnect flow, then the initiation method for that new contact is DISCONNECT\.

**InitiationTimestamp**  
The date and time this contact was initiated, in UTC time\. For `INBOUND`, this is when the contact arrived\. For `OUTBOUND`, this is when the agent began dialing\. For `CALLBACK`, this is when the callback contact was created\. For `TRANSFER` and `QUEUE_TRANSFER`, this is when the transfer was initiated\. For `API`, this is when the request arrived\.  
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**InstanceARN**  
The Amazon Resource Name of the Amazon Connect instance\.  
Type: ARN

**LastUpdateTimestamp**  
The date and time this contact was last updated, in UTC time\.  
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**MediaStreams**  
The media streams\.  
Type: Array of [MediaStream](#MediaStream)

**NextContactId**  
If this contact is not the last contact, this is the ID of the next contact\.  
Type: String  
Length: 1\-256

**PreviousContactId**  
If this contact is not the first contact, this is the ID of the previous contact\.  
Type: String  
Length: 1\-256

**Queue**  
If this contact was queued, this is information about the queue\.  
Type: [QueueInfo](#ctr-QueueInfo)

**Recording**  
If recording was enabled, this is information about the recording\.  
Type: [RecordingInfo](#ctr-RecordingInfo)

**Recordings**  
If recording was enabled, this is information about the recording\.  
Type: Array of [RecordingsInfo](#ctr-RecordingsInfo)  
The first recording for a contact will appear in both the Recording and Recordings sections of the CTR\.

**SystemEndpoint**  
The system endpoint\. For `INBOUND`, this is the phone number that the customer dialed\. For `OUTBOUND`, this is the caller ID phone number that Amazon Connect used to dial the customer\.  
Type: [Endpoint](#ctr-endpoint)

**TransferCompletedTimestamp**  
If this contact was transferred out of Amazon Connect, the date and time the transfer endpoint was connected, in UTC time\.  
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**TransferredToEndpoint**  
If this contact was transferred out of Amazon Connect, the transfer endpoint\.  
Type: [Endpoint](#ctr-endpoint)

## Endpoint<a name="ctr-endpoint"></a>

Information about an endpoint\. In Amazon Connect, an endpoint is the destination for a contact, such as a customer phone number, or a phone number for your contact center\.

**Address**  
The value for the type of endpoint\. For TELEPHONE\_NUMBER, the value is a phone number in E\.164 format\.  
Type: String  
Length: 1\-256

**Type**  
The endpoint type\. Currently, an endpoint can only be a telephone number\.  
Valid values: `TELEPHONE_NUMBER` 

## MediaStream<a name="MediaStream"></a>

Information about the media stream used during the contact\.

**Type**  
Type: MediaStreamType  
Valid value: AUDIO, VIDEO, CHAT

## QueueInfo<a name="ctr-QueueInfo"></a>

Information about a queue\.

**ARN**  
The Amazon Resource Name of the queue\.  
Type: ARN

**DequeueTimestamp**  <a name="DequeueTimestamp-CTR"></a>
The date and time the contact was removed from the queue, in UTC time\. Either the customer disconnected or the contact was connected to an agent\.  
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**Duration**  <a name="Duration-CTR"></a>
The difference in time, in whole seconds, between `EnqueueTimestamp` and `DequeueTimestamp`\.  
Type: Integer  
Min value: 0

**EnqueueTimestamp**  <a name="EnqueueTimestamp-CTR"></a>
The date and time the contact was added to the queue, in UTC time\.  
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**Name**  
The name of the queue\.  
Type: String  
Length: 1\-256

## RecordingInfo<a name="ctr-RecordingInfo"></a>

Information about a voice recording\.

**DeletionReason**  
If the recording was deleted, this is the reason entered for the deletion\.  
Type: String

**Location**  
The location, in Amazon S3, for the recording\.  
Type: String  
Length: 0\-256

**Status**  
The recording status\.  
Valid values: `AVAILABLE` \| `DELETED` \| `NULL` 

**Type**  
The recording type\.  
Valid values: `AUDIO` 

## RecordingsInfo<a name="ctr-RecordingsInfo"></a>

Information about a voice recording or chat transcript\.

**DeletionReason**  
If the recording/transcript was deleted, this is the reason entered for the deletion\.  
Type: String

**FragmentStartNumber**  
The number that identifies the Kinesis Video Streams fragment where the customer audio stream started\.  
Type: String

**FragmentStopNumber**  
The number that identifies the Kinesis Video Streams fragment where the customer audio stream stopped\.  
Type: String

**Location**  
The location, in Amazon S3, for the recording/transcript\.  
Type: String  
Length: 0\-256

**MediaStreamType**  
Information about the media stream used during the conversation\.   
Type: String  
Valid values: AUDIO, VIDEO, CHAT

**ParticipantType**  
Information about the conversation participant: whether they are an agent or contact\.  
Type: String

**StartTimestamp**  
When the conversation started\.  
Type: String *\(yyyy\-mm\-ddThh:mm:ssZ\)*

**Status**  
The status of the recording/transcript\.  
Valid values: `AVAILABLE` \| `DELETED` \| `NULL` 

**StopTimestamp**  
When the conversation stopped\.  
Type: String *\(yyyy\-mm\-ddThh:mm:ssZ\)*

**StorageType**  
Where the recording/transcript is stored\.  
Type: String  
Valid values: Amazon S3`` 

## References<a name="ctr-contact-references"></a>

Contains links to other documents that are related to a contact\.

**Reference Info**  
ReferenceType  
ContentType  
Location

## RoutingProfile<a name="ctr-RoutingProfile"></a>

Information about a routing profile\.

**ARN**  
The Amazon Resource Name of the routing profile\.  
Type: ARN

**Name**  
The name of the routing profile\.  
Type: String  
Length: 1\-100

## How to identify abandoned contacts<a name="abandoned-contact"></a>

An abandoned contact refers to a contact that was disconnected by the customer while in queue\. This means that they weren't connected to an agent\. 

The CTR for an abandoned contact has a **Queue**, and an **Enqueue Timestamp** because it was enqueued\. It won’t have a **ConnectedToAgentTimestamp**, or any of the other fields that populate only after the contact has been connected to an agent\.