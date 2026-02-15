
1 new notification
New missed calls
Has context menu
Chat




Unread
Channels
Chats
Meeting chats
Unread messageLast messageGroup chatMeeting chatChatPersonal at mentionEveryone at mentionImportantUrgentDraftDraftMutedMeeting in progressMeet now in progressYou can't send messages because you are not a member of the chat.PrivateSharedHas context menuChannel at mentionTeam at mentionPersonal at mentionUnreadUnreadMeeting in progressUnreadChannelTeamHas pinned messagesSee moreCommunityTemporarily shownHas context menu
Has context menu

Teams and channels

TOP3 1229978 Airtel


Chat

Shared

Has context menu

Meet now


12




Hi Jeff Song Will let you know more detai... by Yanna Chen
Yanna Chen
11/24/2025 3:38 PM

Hi Jeff Song Will let you know more details soon

👍
1 Like reaction.

Begin Reference, Hi Jeff Wang, please set u... by Jeff Wang
Jeff Wang
11/25/2025 10:54 AM

Jeff Song
11/24/2025 3:11 PM
Hi Jeff Wang, please set up a test bed. Let’s see what messages we can receive from the radius traffic.
Hi Jeff, 

Set up is on ⚓ T28209 Top3 1229978: RADIUS extensions for Bharti Airtel

Yanna Chen added Chenyong Dong to the chat and shared all chat history.
Hi Team, we already have api to insert log ... by Chenyong Dong
Chenyong Dong
11/25/2025 11:09 AM

Hi Team, we already have api to insert log fields into traffic logs. Please tell the branch, we can work on the logging part 

Current approach: 1, RADIUSD would collect ... by Yanna Chen
Yanna Chen
11/25/2025 11:12 AM

Current approach:

1, RADIUSD would collect radius attribute values from accounting messages and record in its internal db.

2, The information of rsso-3gppinfo per src will be fed to MIGLOGD from RADIUSD.

3, Auth team would provide the new CLI option so both RADIUSD and MIGLOGD knows if the extra information or log field is needed.

Thanks to Chenyong to offer centralized con... by Yanna Chen
Yanna Chen
11/25/2025 11:13 AM

Thanks to Chenyong to offer centralized control of traffic logging.

Hi Yanna, regarding 2), miglogd doesn't que... by Chenyong Dong
Chenyong Dong
11/25/2025 11:15 AM

Hi Yanna, regarding 2), miglogd doesn't query. our current framework need "feature" to report the log data whenever it's ready before session end.

here is api by Chenyong Dong
Chenyong Dong
11/25/2025 11:15 AM

here is api

int miglog_traffic_append(unsigned int cvf,... by Chenyong Dong
Chenyong Dong
11/25/2025 11:15 AM

int miglog_traffic_append(unsigned int cvf, const ip_addr_t *srcaddr,

    unsigned int ses_id, enum log_data_type_t type,

    void *data, int sz);

int miglog_traffic_httpstatus(unsigned int cvf, unsigned char proto,

    const ip_addr_t *srcaddr, const ip_addr_t *dstaddr,

    unsigned int ses_id, unsigned short httpstatus);

void miglog_traffic_ngfw_pol_info(unsigned int cvf, unsigned char proto,

    const ip_addr_t *srcaddr, const ip_addr_t *dstaddr,

    unsigned int ses_id, struct log_field_ngfw_pol_info *info);

void miglog_traffic_ssl_action(unsigned int cvf, unsigned char proto,

    const ip_addr_t *srcaddr, const ip_addr_t *dstaddr,

    unsigned int ses_id, enum log_ssl_action action);

 

Will do that by Yanna Chen
Yanna Chen
11/25/2025 11:15 AM

Will do that

we can define the "data type" and provide a... by Chenyong Dong
Chenyong Dong
11/25/2025 11:16 AM

we can define the "data type" and provide a wrapper func like them


👍
1 Like reaction.

Chenyong Dong added Johannah Liu to the chat and shared all chat history.
Jihe will help on logging codes. by Chenyong Dong
Chenyong Dong
11/25/2025 12:03 PM

Jihe will help on logging codes.


👍
1 Like reaction.

Morning Everyone , hope you're doing good. by Jeff Song
11/26/2025 8:06 AM
Jeff Song

Morning Everyone, hope you're doing good.

Yanna   Chen ,  Chenyong   Dong , here you ... by Jeff Song
11/26/2025 8:07 AM
Jeff Song

Yanna Chen, Chenyong Dong, here you are the branch. https://gitlab-van.corp.fortinet.com/fos/fortios/tree/br_7-4_logging_rsso_acc_msgs

 


👍
1 Like reaction.

Hi Jeff   Wang , are you able to mimic the... by Jeff Song
11/26/2025 8:10 AM
Jeff Song

Hi Jeff Wang, are you able to mimic the accounting message, sent from the radius server, in the lab? Do you need a sample from the SE?

Jeff Song added Bryan Han to the chat and shared all chat history.
Hi Bryan   Han , welcome to the top3 case ... by Jeff Song
11/26/2025 5:23 PM
Jeff Song

Hi Bryan Han, welcome to the top3 case for Airtel.

In this case, we need to capture the RADIUS... by Jeff Song
11/26/2025 5:24 PM
Jeff Song

In this case, we need to capture the RADIUS accounting message and insert it to each traffic logs that is pertinent to the user.

During the discussion, we were aware of tha... by Jeff Song
11/26/2025 5:25 PM
Jeff Song

During the discussion, we were aware of that need WAD to make a little change that facilitates this new feature. 

Can I invite you to continue the discussion by Jeff Song
11/26/2025 5:25 PM
Jeff Song

Can I invite you to continue the discussion?

Hi Jeff, for sure. I checked the discussion... by Bryan Han
Bryan Han
11/26/2025 6:26 PM

Hi Jeff, for sure. I checked the discussion and the proposed changes that will be applied. We can definitely change WAD once the API change is ready.


👍
1 Like reaction.

Jeff Song added Laura Yang to the chat and shared all chat history.
Laura Yang , please brief what we need Brya... by Jeff Song
11/26/2025 6:57 PM
Jeff Song

Laura Yang, please brief what we need Bryan to change on the WAD side. Thank you.
Add an extra field in traffic logs to indic... by Laura Yang
Laura Yang
11/26/2025 7:23 PM


Add an extra field in traffic logs to indicate if it is associated with RSSO, including kernel and all daemons. >>>>daemons need add a flag “rsso” when sending logs to miglogd. This flag can be obtained by querying rsso logon or related iprope from kernel.

👍
1 Like reaction.

Miglogd will check this flag to determine w... by Laura Yang
Laura Yang
11/26/2025 7:26 PM

Miglogd will check this flag to determine whether rsso data should be added in logs or not.

Begin Reference, Hi Jeff Wang, are you able... by Jeff Wang
Jeff Wang
11/26/2025 7:29 PM

Jeff Song
11/26/2025 8:10 AM
Hi Jeff Wang, are you able to mimic the accounting message, sent from the radius server, in the lab? Do you need a sample from the SE?
Jeff Song I already set up in lab ,⚓ T28209 Top3 1229978: RADIUS extensions for Bharti Airtel


👍
1 Like reaction.

Begin Reference, ‎• ‎Add an extra field in ... by Chenyong Dong
Chenyong Dong
11/27/2025 9:23 AM

Laura Yang
11/26/2025 7:23 PM
‎• ‎Add an extra field in traffic logs to indicate if it is associated with RSSO, including kernel and all daemons. >>>>daemons need add a flag “rsso” when sending logs to miglogd. This flag can be obtained…
To clarify, it's not new "log field", we just need to introduce a new member or flag in LOGA_TR_AUTH_INFO record to indicate the rsso logon case, so will need IPS and WAD to do the same when reporting traffic log with this record. I will try make kernel changes


👍
1 Like reaction.

This flag should be available in session by Chenyong Dong
Chenyong Dong
11/27/2025 9:24 AM

This flag should be available in session

commit b44b6b7445bc7bb66b32d14272ce2e0eac07... by Chenyong Dong
Chenyong Dong
11/27/2025 3:23 PM

commit b44b6b7445bc7bb66b32d14272ce2e0eac07eb51 (HEAD -> br_7-4_logging_rsso_acc_msgs, origin/br_7-4_logging_rsso_acc_msgs)
Author: chenyong dong <cydong@fortinet.com>
Date:   Thu Nov 27 15:16:48 2025 -0800

 

    kernel add "logon_type" in user group record in traffic log.

 

Hi Laura   Yang , please take a look at th... by Chenyong Dong
Chenyong Dong
11/27/2025 3:24 PM

Hi Laura Yang, please take a look at the patch in kernel. Whether "logon_type" is the right one we can use to check "rsso" user case. thanks.

Bryan Han added Ping Yao to the chat and shared all chat history.
Hi Chenyong, It is that one. Thank you. by Laura Yang
Laura Yang
11/27/2025 3:37 PM

Hi Chenyong, It is that one. Thank you.

Chenyong Dong added Mingyan Guo to the chat and shared all chat history.
add IPS in the chat. We need IPS change as ... by Chenyong Dong
Chenyong Dong
11/27/2025 4:00 PM

add IPS in the chat. We need IPS change as well.


👍
1 Like reaction.

Let me post my question on how to change th... by Mingyan Guo
Mingyan Guo
11/28/2025 11:53 AM

Let me post my question on how to change the IPS daemon for the field here,

  According to Chenyong, the field can be found by sys_find_fsso_logon, but I don't see it in struct fsso_logon_usr. Do I miss anything?

Hi Laura   Yang , IPS is using sys_find_f... by Chenyong Dong
Chenyong Dong
11/28/2025 11:56 AM

Hi Laura Yang, IPS is using sys_find_fsso_logon() to get and fill session "user" info. do you know whether "logon_type" can be returned in this api? or other api can return?

AUTH_T_RSSO is set as filter type in this a... by Laura Yang
Laura Yang
11/28/2025 11:59 AM

AUTH_T_RSSO is set as filter type in this api. So if there is logon returned when the filter type is set as AUTH_T_RSSO only the traffic is related to rsso , right?

There is no logon-type in the returned data... by Laura Yang
Laura Yang
11/28/2025 12:02 PM

There is no logon-type in the returned data struct fsso_logon_usr.

looks we need add "logon-type" in this stru... by Chenyong Dong
Chenyong Dong
11/28/2025 12:02 PM

looks we need add "logon-type" in this structure and fill in the api?

seems this api is to return all "type" logo... by Chenyong Dong
Chenyong Dong
11/28/2025 12:04 PM

seems this api is to return all "type" logon user, including rsso and fsso. so return "logon-type" can help tell the "type"

or add a new api to get rsso logon only.  I... by Laura Yang
Laura Yang
11/28/2025 12:04 PM

or add a new api to get rsso logon only.

 It may be easier.

we need all type "user", i think. But now n... by Chenyong Dong
Chenyong Dong
11/28/2025 12:05 PM

we need all type "user", i think. But now need an extra "type" to tell the case

feels a small change in the api? by Chenyong Dong
Chenyong Dong
11/28/2025 12:06 PM

feels a small change in the api?

Begin Reference, feels a small change in th... by Laura Yang
Laura Yang
11/28/2025 12:11 PM

Chenyong Dong
11/28/2025 12:06 PM
feels a small change in the api?
It can be done like that but needs change the code in kernel.

looks the info is ready in the userspace re... by Chenyong Dong
Chenyong Dong
11/28/2025 12:33 PM

looks the info is ready in the userspace record:auth_logon_u_t, just need to copy into fsso_logon_usr. please double check.

seems no kernel changes by Chenyong Dong
Chenyong Dong
11/28/2025 12:33 PM

seems no kernel changes

oh, yes, you're correct. Yes by Laura Yang
Laura Yang
11/28/2025 12:50 PM

oh, yes, you're correct.👍

Begin Reference, oh, yes, you're correct.👍... by Chenyong Dong
Chenyong Dong
11/28/2025 12:56 PM

Laura Yang
11/28/2025 12:50 PM
oh, yes, you're correct.👍
will you help make the change?

Sure. by Laura Yang
Laura Yang
11/28/2025 1:16 PM

Sure.

Thank you, Laura. After your change, Mingya... by Chenyong Dong
Chenyong Dong
11/28/2025 1:34 PM

Thank you, Laura. After your change, Mingyan can update IPS part.


👍
1 Like reaction.

Hi Laura   Yang , is the api change done? ... by Chenyong Dong
Chenyong Dong
12/2/2025 10:06 AM

Hi Laura Yang, is the api change done? if done, IPS may update their part then.

Chenyong  and Laura , could you review my ... by Mingyan Guo
Mingyan Guo
12/2/2025 10:07 AM

Chenyong and Laura, could you review my patch https://gitlab-van.corp.fortinet.com/fos/fortios/-/merge_requests/2152 please? Thanks.

looks good. thanks by Chenyong Dong
Chenyong Dong
12/2/2025 10:16 AM

looks good. thanks

Begin Reference, looks good. thanks, Chenyo... by Mingyan Guo
Mingyan Guo
12/2/2025 10:22 AM

Chenyong Dong
12/2/2025 10:16 AM
looks good. thanks
Thanks. The IPS daemon change has been merged into the FOS special branch.


👍
2 Like reactions.
2

It's good. Thank you.  by Laura Yang
Laura Yang
12/2/2025 10:49 AM

It's good. Thank you. 

Summay of a discussion about the  details o... by Laura Yang
Laura Yang
12/2/2025 11:59 AM

Summay of a discussion about the  details of 3gpp-radius-string by Jeff Song, Jeff Wang and Laura Yang.

 

1. Should we translate radius attribute type and value to strings?    ---- to be confirmed with customer
    example: 22 – 3GPP-User-Location-Info
                3GPP type = 22   -----show "22" or "3GPP-User-Location-Info" in logs?
    3GPP Length= m
    Geographic Location Type 
      0  CGI   >>>> show "0" or "CGI" in logs?
      1  SAI
      2  RAI
      ......
    Geographic Location (octet string)
2. There are 5 attributes listed in mantis. We need only supports these attributes, right?    ---- to be confirmed with customer
  * 3GPP-IMSI(1) -> will provide the IMSI of the subscriber
  * 3GPP-Session-S-NSSAI(125) - > While more specific to 5G networks, this will provide the network slice ID that the subscriber has assigned
  * 3GPP-IMEISV(20) -> provides the device identity, most relevant for devices that don’t have users e.g. IoT devices
  * 3GPP-RAT-Type(21) -> indicates the Radio Access Technology type that is in use e.g. UMTS, LTE, 5GNR, NB-IOT etc
  * 3GPP-User-Location-Info(22) -> provides UE location information such as CAI and/or SAI

3. If the attribute type needs to be translated into name, logs may be long especially in the case that there are many class attributes(groups) in the same radius message.
4. The format of 3gpp-info in logs(if translation is not needed) will be as following.
   3gpp-radius-info:[22:string],[1:string],[16:string]....

regarding the log field name, I prefer a mo... by Chenyong Dong
Chenyong Dong
12/2/2025 12:15 PM

regarding the log field name, I prefer a more generic one. "3gpp" seems too special. better "rssoinfo" or "radiusinfo"? we might have other rsso case in future?


👍
1 Like reaction.

FYI, example traffic log  for RSSO auth ses... by Jeff Wang
Jeff Wang
12/2/2025 12:29 PM

FYI,

example traffic log  for RSSO auth session, 

It only fill "Calling-Station-Id = "JeffWang"  as "user" ='jeffwang" , in traffic  log

 

 

date=2025-11-24 time=16:07:33 eventtime=1764029253470051761 tz="-0800" logid="0000000013" type="traffic" subtype="forward" level="notice" vd="root" srcip=192.168.1.22 srcport=49396 srcintf="port2" srcintfrole="undefined" dstip=192.168.30.30 dstport=22 dstintf="port3" dstintfrole="undefined" srccountry="Reserved" dstcountry="Reserved" sessionid=1513 proto=6 action="close" policyid=4 policytype="policy" poluuid="4f78c7ba-c3fa-51f0-7108-e1b4ed19da20" policyname="test" user="jeffwang" group="grp_rsso1" authserver="root" service="SSH" trandisp="snat" transip=192.168.30.52 transport=49396 appcat="unscanned" duration=7 sentbyte=4510 rcvdbyte=4420 sentpkt=34 rcvdpkt=29
 


👍
1 Like reaction.

please note: do not use "quote" char in thi... by Chenyong Dong
Chenyong Dong
12/2/2025 4:30 PM

please note: do not use "quote" char in this string. and other special char, like, \r\n, neither.

ok, got it. by Laura Yang
Laura Yang
12/2/2025 4:31 PM

ok, got it.

sys_find_fsso_logon is only for IPv4.  So I... by Ping Yao
Ping Yao
12/2/2025 5:28 PM

sys_find_fsso_logon is only for IPv4.  So I will only modify IPv4 part.

Chenyong   Dong  Is there much ipv6 traffic... by Laura Yang
Laura Yang
12/2/2025 5:38 PM

Chenyong Dong Is there much ipv6 traffic? If no maybe we needn't check this flag for ipv6 traffic?

Begin Reference, Chenyong Dong Is there muc... by Chenyong Dong
Chenyong Dong
12/2/2025 6:40 PM

Laura Yang
12/2/2025 5:38 PM
Chenyong Dong Is there much ipv6 traffic? If no maybe we needn't check this flag for ipv6 traffic?
Guess ip6 traffic could be a lot as it's a Telecom customer. Checked code. Looks the lower layer API does support ip6 already. You may just create a new wrapper API for ip6.

I add Ipv6 RSSO  cases on Link ⚓ T28209 To... by Jeff Wang
Jeff Wang
12/2/2025 8:52 PM

I add Ipv6 RSSO  cases on ⚓ T28209 Top3 1229978: RADIUS extensions for Bharti Airtel

Just replace Framed-Ip-Address with Framed-IPv6-Prefix

customer mention is need support "Framed-IPv6-Prefix"


👍
1 Like reaction.

commit 106a70409483bc29ecbfe6be5db6c15ec4fc... by Laura Yang
Laura Yang
12/2/2025 9:28 PM

commit 106a70409483bc29ecbfe6be5db6c15ec4fcee93 (HEAD, origin/br_7-4_logging_rsso_acc_msgs)
Author: yangl <yangl@fortinet.com>
Date:   Tue Dec 2 21:27:18 2025 -0800

 

    Add ipv6 support in api sys_find_fsso_logon

Thank you Jeff for the test cases especiall... by Laura Yang
Laura Yang
12/2/2025 9:29 PM

Thank you Jeff for the test cases especially the ipv6 cases. 

Hi Ping, Mingyan, could you help modify the... by Laura Yang
Laura Yang
12/2/2025 9:31 PM

Hi Ping, Mingyan, could you help modify the related code in ips and wad to use the api.  The change is as following:                                           
-sys_find_fsso_logon(int vfid, uint32_t src, struct fsso_logon_usr *l)
+sys_find_fsso_logon(int vfid, ip_addr_t *src, struct fsso_logon_usr *l)  >>>>>>> the type of src is changed.


👍
1 Like reaction.

Begin Reference, Hi Ping, Mingyan, could yo... by Mingyan Guo
Mingyan Guo
12/3/2025 9:25 AM

Laura Yang
12/2/2025 9:31 PM
Hi Ping, Mingyan, could you help modify the related code in ips and wad to use the api. The change is as following: -sys_find_fsso_logon(int vfid, uint32_t src, struct fsso_logon_usr *l) +sys_find_fsso_logon(int vfid, ip_addr_t *src, struct fsso_logon_u…
Sure, I will.

I just checked in WAD part including IPV6. by Ping Yao
Ping Yao
12/3/2025 10:38 AM

I just checked in WAD part including IPV6.

 

IPS daemon changes are done for the new sy... by Mingyan Guo
Mingyan Guo
12/3/2025 11:03 AM

IPS daemon changes are done for the new sys_find_fsso_logon.

Hi Laura   Yang , please check Tom's comme... by Jeff Song
12/3/2025 11:58 AM
Jeff Song

Hi Laura Yang, please check Tom's comments and my questions to him in the TOP3. 

Thank you Jeff, I will follow the format in... by Laura Yang
Laura Yang
12/3/2025 1:13 PM

Thank you Jeff, I will follow the format in the mantis.

Jeff   Song You mention  A BUGNOTE has been... by Jeff Wang
Jeff Wang
12/4/2025 8:38 AM

Jeff Song

You mention 

A BUGNOTE has been added by Jeff Song (sjeff):
        Thank you, gentlemen,

We will follow this format:

3gpp-radius-info:[31:8829990000001],[30:fictional.apn.com],[26:[10415:[1:001010000000001],[125:de93ad8b],[20:350000000000001],[21:7],[22:0000f1105ee71981]]],[25:47726f75703031],[25:536d617274]

 

It is better to replace '25'  with " Calss(25)"  , and for '25' only,   because '25' in 3gpp is :

 

ATTRIBUTE       3GPP-Packet-Filter                      25      octets


👍
1 Like reaction.

You decide it please. by Jeff Song
12/4/2025 8:42 AM
Jeff Song

You decide it please.

ok, I post it on Mantis  by Jeff Wang
Jeff Wang
12/4/2025 8:43 AM

ok, I post it on Mantis 

Jeff   Song  My fault, they add "[26:[10415... by Jeff Wang
Jeff Wang
12/4/2025 9:05 AM

Jeff Song My fault, they add "[26:[10415: .." before 3GPP, no need change, thanks

Hi Laura   Yang , does the format proposed... by Jeff Song
12/4/2025 3:15 PM
Jeff Song

Hi Laura Yang, does the format proposed by Joel make sense to you?

Yes, It saves space by using vsa code inste... by Laura Yang
Laura Yang
12/4/2025 3:17 PM

Yes, It saves space by using vsa code insteady of name. But the structure [[], [[], [], []]] mentioned is a bit complicated. I'm modifying the code. 


👍
1 Like reaction.

It is difficult to compose the string in th... by Laura Yang
Laura Yang
12/4/2025 4:23 PM

It is difficult to compose the string in the desired nested format for vendor specific vsas:

 

[26:[10415:[1:001010000000001],[125:de93ad8b],[20:350000000000001],[21:7],[22:0000f1105ee71981]]]

 

because each 3GPP attribute is encoded as an individual Vendor-Specific Attribute (VSA) in the RADIUS packet. VSAs are delivered as separate, flat attributes, rather than as a single grouped structure.

 

They may arrive in any order and are not guaranteed to be contiguous. Therefore, the decoder must process each VSA independently and aggregate them into a single logical container, instead of relying on an already nested representation in the original data.

 

My suggestion is as following. This approach is also consistent with how RADIUS VSAs are typically organized, i.e., as a flat list of independent attributes rather than a predefined hierarchical structure.

 

[26:[10415:[1:001010000000001]]],[26:[10415:[125:de93ad8b]]],[26:[10415:[20:350000000000001]]],[26:[10415:[21:7]]],[26:[10415:[22:0000f1105ee71981]]]

I also added a comment in the mantis.  by Laura Yang
Laura Yang
12/4/2025 4:24 PM

I also added a comment in the mantis. 

Don't know how many attributes there would ... by Chenyong Dong
Chenyong Dong
12/4/2025 6:34 PM

Don't know how many attributes there would be. Looks the flat format contains many redundant text? Could be a very longer string? Do we have estimation of max length? Our log max length is 2k, I remember. If field content is too long, it may get truncated.

There are 9 attributes in the mantis. And a... by Laura Yang
Laura Yang
12/4/2025 6:38 PM

There are 9 attributes in the mantis. And about 40 extra characters totally will be added in the flat format of 3Gpp attributes. It should be ok.

And as mentioned in the requirement the max... by Laura Yang
Laura Yang
12/4/2025 6:39 PM

And as mentioned in the requirement the max Len of the string will be 512.


👍
1 Like reaction.

Begin Reference, Jeff Song You mention  A B... by Laura Yang
Laura Yang
12/4/2025 6:44 PM

Jeff Wang
12/4/2025 8:38 AM
Jeff Song You mention  A BUGNOTE has been added by Jeff Song (sjeff):         Thank you, gentlemen, We will follow this format: 3gpp-radius-info:[31:8829990000001…
In this example the length is 160, it will be about 200 if using flat format.
Hi Laura   Yang , the SE proposed a new fo... by Jeff Song
12/11/2025 10:14 AM
Jeff Song

Hi Laura Yang, the SE proposed a new format for the log. Please take a look. Thank you.

HI Jeff/Laura/Yanna, wanna confirm again, w... by Chenyong Dong
Chenyong Dong
12/11/2025 11:13 AM

HI Jeff/Laura/Yanna, wanna confirm again, whether "3gpp" is just one case of "rsso info". From log perspective, I prefer a generic log field which can be extended for other cases. If we may have other "rsso info" cases in future, I would ask to use "rssoinfo" as log field name.

Yes let's use rssoinfo by Yanna Chen
Yanna Chen
12/11/2025 12:02 PM

Yes let's use rssoinfo


👍
1 Like reaction.

how about "rssoinfo="3gpp:0|31=882999000000... by Chenyong Dong
Chenyong Dong
12/11/2025 12:23 PM

how about "rssoinfo="3gpp:0|31=8829990000001,10415|1=001010000000001,0|..."?

Laura   Yang  Dose SE mention 3gpp vendor i... by Yanna Chen
Yanna Chen
12/11/2025 12:24 PM

Laura Yang Dose SE mention 3gpp vendor id in log value?

Yes I think so. It is included in the previ... by Laura Yang
Laura Yang
12/11/2025 12:29 PM

Yes I think so. It is included in the previous format. I will confirm it later. If it is I think 3gpp is not needed because the calling station id and called station id are not from 3gpp, and 3gpp info is already in the related attributes.


👍
1 Like reaction.

Jeff Wang Could you please hold the test? ... by Laura Yang
Laura Yang
12/11/2025 12:32 PM

Jeff Wang Could you please hold the test? I will update the code according to the new format. Thanks.

👍
1 Like reaction.

Hi Jeff   Wang , I’ve mostly finished my c... by Johannah Liu
Johannah Liu
12/16/2025 11:27 AM

Hi Jeff Wang, I’ve mostly finished my code. May I directly upload my image to the existing test FGT and run the tests there?

sure, 172.18.5.52 by Jeff Wang
Jeff Wang
12/16/2025 11:27 AM

sure, 172.18.5.52


👍
1 Like reaction.

Thank you! by Johannah Liu
Johannah Liu
12/16/2025 11:27 AM

Thank you!

detail setup is on Link ⚓ T28209 Top3 1229... by Jeff Wang
Jeff Wang
12/16/2025 11:28 AM

detail setup is on ⚓ T28209 Top3 1229978: RADIUS extensions for Bharti Airtel


👍
1 Like reaction.

Thx by Johannah Liu
Johannah Liu
12/16/2025 11:28 AM

Thx

Hi Jeff   Wang , my part of the code can n... by Johannah Liu
Johannah Liu
12/18/2025 10:28 AM

Hi Jeff Wang, my part of the code can now run through the basic cases, so I think we can start testing. I’ll continue to fix and optimize the code while testing.


👍
1 Like reaction.

Do you want me to push and merge my changes... by Johannah Liu
Johannah Liu
12/18/2025 10:28 AM

Do you want me to push and merge my changes, or is it okay if I just build an image locally?

Hi Johannah   Liu , please merge the code. by Jeff Song
12/18/2025 10:30 AM
Jeff Song

Hi Johannah Liu, please merge the code.

Sure by Johannah Liu
Johannah Liu
12/18/2025 10:30 AM

Sure

thank you by Jeff Song
12/18/2025 10:30 AM
Jeff Song

thank you
I will test it CM image, thanks by Jeff Wang
Jeff Wang
12/18/2025 10:37 AM

I will test it CM image, thanks


👍
1 Like reaction.

Chenyong and Laura are on PTO today. Who ca... by Johannah Liu
Johannah Liu
12/18/2025 10:44 AM

Chenyong and Laura are on PTO today. Who can help me review my merge request?🙇‍♀️

never mind. Let me trigger a build for a co... by Jeff Song
12/18/2025 10:44 AM
Jeff Song

never mind. Let me trigger a build for a confirmation.

OK. Thx by Johannah Liu
Johannah Liu
12/18/2025 10:45 AM

OK. Thx

have you pushed? by Jeff Song
12/18/2025 10:47 AM
Jeff Song

have you pushed?

Yes by Johannah Liu
Johannah Liu
12/18/2025 10:47 AM

Yes


👍
1 Like reaction.

And it's ready to merge. merge? by Johannah Liu
Johannah Liu
12/18/2025 10:48 AM

And it's ready to merge. merge?

I mean, have you pushed the code to the spe... by Jeff Song
12/18/2025 10:49 AM
Jeff Song

I mean, have you pushed the code to the special branch https://gitlab-van.corp.fortinet.com/fos/fortios/tree/br_7-4_logging_rsso_acc_msgs?

Let me see.  by Johannah Liu
Johannah Liu
12/18/2025 10:51 AM

Let me see. 

OK. Should be pushed by Johannah Liu
Johannah Liu
12/18/2025 10:54 AM

OK. Should be pushed

Hi, what's the status of test?  by Chenyong Dong
Chenyong Dong
1/5 9:42 AM

Hi, what's the status of test? 

No image provided for testing by Jeff Wang
Jeff Wang
1/5 9:43 AM

No image provided for testing

Hi Jeff   Song , can please help trigger b... by Chenyong Dong
Chenyong Dong
1/5 9:54 AM

Hi Jeff Song, can please help trigger build for test image? thanks

Sure by Jeff Song
1/5 10:01 AM
Jeff Song

Sure

Jeff   Wang , what device models do you nee... by Jeff Song
1/5 10:05 AM
Jeff Song

Jeff Wang, what device models do you need?

FG-VM64 by Jeff Wang
Jeff Wang
1/5 10:05 AM

FG-VM64

Chenyong   Dong , I tried to build the imag... by Jeff Song
1/5 10:08 AM
Jeff Song

Chenyong Dong, I tried to build the images on Dec. 18th after you had merged the code, unfortunately, it failed that time. Let me try it again.  

Link https://jenkins-sjc.corp.fortinet.com/... by Jeff Song
1/5 10:08 AM
Jeff Song

https://jenkins-sjc.corp.fortinet.com/job/Projects/job/FortiOS/job/br_7-4/job/br_7-4_logging_rsso_a…

Here is the building console output, please... by Jeff Song
1/5 10:09 AM
Jeff Song

Here is the building console output, please check if any unexpected issues lay on the code side. 


👍
2 Like reactions.
2

❤️
1 Heart reaction.

Hi Jeff   Wang , the build is ready. by Jeff Song
1/5 2:16 PM
Jeff Song

Hi Jeff Wang, the build is ready.


👍
2 Like reactions.
2

Morning Everyone , hope you're doing good. by Jeff Song
1/7 9:42 AM
Jeff Song

Morning Everyone, hope you're doing good.

Hi Jeff   Wang , please review the discuss... by Jeff Song
1/7 9:43 AM
Jeff Song

Hi Jeff Wang, please review the discussion that Chenyong and Simon posted in the mantis https://mantis.fortinet.com/bug_view_page.php?bug_id=1241309, and clarify which is the case.

yes, the root case is Radius DB is updated,... by Jeff Wang
Jeff Wang
1/7 9:45 AM

yes, the root case is Radius DB is updated, but miglogd cache it , and did not update cache


👍
1 Like reaction.

I just clear Radius DB, but miglogd still  ... by Jeff Wang
Jeff Wang
1/7 9:50 AM

I just clear Radius DB, but miglogd still  keep the cache

 

FGVM04TM25006339 # dia test application  radiusd 2
Clear RADIUS server database [vd root]

 

FGVM04TM25006339 # dia test application  miglogd 56
miglog rsso cache:
total: 1
ip              username                rsso_info
192.168.1.22    jeffwang        0|30=fictional.apn.com,0|31=JeffWang,0|25=0x64796e616d69635f70726f66696c655f31,10145|1=310150123456789,10145|125=0x02aa5501,10145|20=350000000000039,10145|21=1005,10145|22=0x050530272000a1

Chenyong said " Here what we saw in the tes... by Jeff Song
1/7 9:50 AM
Jeff Song

Chenyong said "

Here what we saw in the test, is the "rsso" info for the same ip&user gets updated after it's created with initial content.
"

means, an interim message changes the 'rsso' that was initially set to 'abc' to 'abc'+'xyz'.

 

Simon said, the initial message sets 'rsso' to 'abc' + 'xyz', the interim message does not change it.

 

Which one is your case?

no sure how long miglogd will retaind the c... by Jeff Wang
Jeff Wang
1/7 9:50 AM

no sure how long miglogd will retaind the cache

it will clear after 5 min if there is no an... by Johannah Liu
Johannah Liu
1/7 9:51 AM

it will clear after 5 min if there is no any query for this user and ip

Begin Reference, no sure how long miglogd w... by Jeff Song
1/7 9:51 AM
Jeff Song

Jeff Wang
1/7/2026 9:50 AM
no sure how long miglogd will retaind the cache
Do you mean, the miglogd doesn't be aware of a new session?

if "radius DB" is cleared manually, and ret... by Chenyong Dong
Chenyong Dong
1/7 9:53 AM

if "radius DB" is cleared manually, and retrigger the same ip/user with different "rsso" info, log daemon has no idea about this change. will keep the old content in cache.


👍
1 Like reaction.

that looks to me a corner case by Chenyong Dong
Chenyong Dong
1/7 9:54 AM

that looks to me a corner case

Jeff   Wang , why do you manually clear Rad... by Jeff Song
1/7 9:54 AM
Jeff Song

Jeff Wang, why do you manually clear Radius DB?

yes, by Jeff Wang
Jeff Wang
1/7 9:55 AM

yes,

but I send "STOP", to clear DB, miglogd sti... by Jeff Wang
Jeff Wang
1/7 9:56 AM

but I send "STOP", to clear DB, miglogd still keep the cache

let's call a meeting? by Chenyong Dong
Chenyong Dong
1/7 9:57 AM

let's call a meeting?

Begin Reference, but I send "STOP", to clea... by Jeff Song
1/7 9:58 AM
Jeff Song

Jeff Wang
1/7/2026 9:56 AM
but I send "STOP", to clear DB, miglogd still keep the cache
It remove one registered 'rsso' entry from the DB, not remove all entries from the DB, right?

Begin Reference, let's call a meeting?, Che... by Jeff Song
1/7 9:58 AM
Jeff Song

Chenyong Dong
1/7/2026 9:57 AM
let's call a meeting?
sure

1/7 9:58 AM - You started a Meeting.
yes,  by Jeff Wang
Jeff Wang
1/7 9:58 AM

yes, 

Meeting ended at 1/7 10:15 AM after 17 minutes 30 seconds
FYI, I do more test. Interim-Update will up... by Jeff Wang
Jeff Wang
1/7 10:25 AM

FYI,

I do more test.

Interim-Update will update Radius DB.

Different User /Same IP, Radius DB will override, so only one Radius DB, but it have two same IP Miglogd cache item.

 

dia test application  radiusd 33                                                                                                                                      RADIUS server database [vd root]:
"index","start time","time left","ip","endpoint","block status","log status","profile group","ref count","use default profile","rsso_info"
1,1767810091,07:59:56,"192.168.1.22""jeffwang1","allow","no log","dynamic_profile_1",1,No,"0|30=fictional.apn.com,0|31=JeffWang1,0|25=0x64796e616d69635f70726f66696c655f31,10145|1=310150123456789,10145|125=0x02aa5501,10145|20=350000000000039,10145|21=1005,10145|22=0x050530272000a1"

 

 

FGVM04TM25006339 # dia test application  miglogd 56                                                                                                                                      miglog rsso cache:
total: 2
ip              username                rsso_info
192.168.1.22    jeffwang1       0|30=fictional.apn.com,0|31=JeffWang1,0|25=0x64796e616d69635f70726f66696c655f31,10145|1=310150123456789,10145|125=0x02aa5501,10145|20=350000000000039,10145|21=1005,10145|22=0x050530272000a1
192.168.1.22    jeffwang        0|30=fictional.apn.com,0|31=JeffWang,0|25=0x64796e616d69635f70726f66696c655f31,10145|1=310150123456789,10145|125=0x02aa5501,10145|20=350000000000039,10145|21=1005,10145|22=0x050530272000a1

that's expected, given current miglogd cach... by Chenyong Dong
Chenyong Dong
1/7 10:29 AM

that's expected, given current miglogd cache design. The old entry will no longer be used, and cleaned after 5 min.

Hi Laura   Yang , are you free to discuss ... by Jeff Song
1/7 11:55 AM
Jeff Song

Hi Laura Yang, are you free to discuss the last issue?

Sure. by Laura Yang
Laura Yang
1/7 11:55 AM

Sure.

Can you call Chenyong directly? by Jeff Song
1/7 11:56 AM
Jeff Song

Can you call Chenyong directly?

Jeff, i think Laura and I already discussed by Chenyong Dong
Chenyong Dong
1/7 11:56 AM

Jeff, i think Laura and I already discussed 

the cache issue, right? by Chenyong Dong
Chenyong Dong
1/7 11:56 AM

the cache issue, right?

I talked to Chenyong. The logout can be kno... by Laura Yang
Laura Yang
1/7 11:56 AM

I talked to Chenyong. The logout can be known by monitoring kernel logon del event.


👍
2 Like reactions.
2

Thank you, Chenyong.  by Jeff Song
1/7 11:56 AM
Jeff Song

Thank you, Chenyong. 

yeah. talked with Laura and Jiahe, we will ... by Chenyong Dong
Chenyong Dong
1/7 11:57 AM

yeah. talked with Laura and Jiahe, we will make an update soon.


👍
1 Like reaction.

monitor the logon delete event, and clean o... by Chenyong Dong
Chenyong Dong
1/7 11:57 AM

monitor the logon delete event, and clean our cache


👍
1 Like reaction.

Hi Jeff   Wang   Laura   Yang , For this ... by Johannah Liu
Johannah Liu
1/7 4:33 PM

Hi Jeff Wang Laura Yang, For this ipv6 case, I found the srcip in radius accounting is 2002:192:168:1::/64, but in our traffic log the srcip is 2002:192:168:1::22/128，As a result, radius cannot found it in DB and the query returns an empty result. Is 2002:192:168:1::/64 a subnet address？ Should it include 2002:192:168:1::22/128？

2002:192:168:1::22/128  is include it in 20... by Jeff Wang
Jeff Wang
1/7 4:34 PM

2002:192:168:1::22/128  is include it in 2002:192:168:1::/64


👍
1 Like reaction.

it set up ipv6 auth sess like  dia firewall... by Jeff Wang
Jeff Wang
1/7 4:35 PM

it set up ipv6 auth sess like 

dia firewall  auth ipv6  list  

 

2002:192:168:1::/64, jeffwang
        type: rsso, id: 0, duration: 4177, idled: 2521
        flag(10): radius
        server: root
        packets: in 898 out 898, bytes: in 93392 out 93392
        group_id: 4
        group_name: grp_rsso1

 

----- 1 listed, 0 filtered ------

Oh, I see now. Laura   Yang  It looks like... by Johannah Liu
Johannah Liu
1/7 4:37 PM

Oh, I see now. Laura Yang It looks like radiusd should allow 2002:192:168:1::22/128 to match 2002:192:168:1::/64. Right now, when querying with 2002:192:168:1::22/128, it reports that no matching entry can be found in the DB.

ipv6 session  dia sys session6 list   sessi... by Jeff Wang
Jeff Wang
1/7 4:38 PM

ipv6 session 

dia sys session6 list

 

session6 info: proto=58 proto_state=00 duration=4 expire=59 timeout=0 refresh_dir=both flags=00000000 sockport=0 socktype=0 use=4
origin-shaper=
reply-shaper=
per_ip_shaper=
class_id=0 ha_id=0 policy_dir=0 tunnel=/ vlan_cos=0/0
user=jeffwang auth_server=root state=log may_dirty authed acct-ext 
statistic(bytes/packets/allow_err): org=520/5/0 reply=520/5/0 tuples=3
tx speed(Bps/kbps): 126/1 rx speed(Bps/kbps): 126/1
orgin->sink: org pre->post, reply pre->post dev=4->5/5->4
hook=post dir=org act=snat 2002:192:168:1::22:89->2002:192:168:70::40:128(2002:192:168:30::52:27355)
hook=pre dir=reply act=dnat 2002:192:168:70::40:27355->2002:192:168:30::52:129(2002:192:168:1::22:89)
hook=post dir=reply act=noop 2002:192:168:70::40:89->2002:192:168:1::22:129(:::0)
misc=0 policy_id=4 pol_uuid_idx=15853 auth_info=4 chk_client_info=0 vd=0
serial=00000100 tos=ff/ff ips_view=11620 app_list=0 app=0 url_cat=0
rpdb_link_id=00000000 ngfwid=n/a
npu_state=0x001108
total session6: 1

Like this" by Johannah Liu, has an attachment.
Johannah Liu
1/7 4:38 PM

Like this"


I will try "   Framed-IPv6-Address   by Jeff Wang
Jeff Wang
1/7 4:41 PM

I will try "

 

Framed-IPv6-Address
 

Currently we do hash and search the ipv6 in... by Laura Yang
Laura Yang
1/7 4:43 PM

Currently we do hash and search the ipv6 in a short list, it will decrease the performance of query if matching address with a subnet since we need compare them one by one. 

my understanding, the cache entry in radius... by Chenyong Dong
Chenyong Dong
1/7 5:03 PM

my understanding, the cache entry in radius db record the ipv6 subnet address. when log daemon sends request with single ipv6 address, radius daemon should match the ipv6 with subnet and mask address, right?

then should get matched result. by Chenyong Dong
Chenyong Dong
1/7 5:03 PM

then should get matched result.

yes, it should be like that. by Laura Yang
Laura Yang
1/7 5:05 PM

yes, it should be like that.

I use Framed-IPv6-Address, it can generate ... by Jeff Wang
Jeff Wang
1/7 5:19 PM

I use Framed-IPv6-Address, it can generate rssoinfo log for ip v6 traffic, I will update Mantis, thanks


👍
2 Like reactions.
2

Hi Johannah, will you send both the usernam... by Laura Yang
Laura Yang
1/8 10:00 AM

Hi Johannah, will you send both the username and ipv6 when querying?

no just ip by Johannah Liu
Johannah Liu
1/8 10:00 AM

no just ip

Could you send it so that the search can be... by Laura Yang
Laura Yang
1/8 10:18 AM

Could you send it so that the search can be narrow down to all ips of a specific user?

I'm trying to add a new api for ipv6 query. by Laura Yang
Laura Yang
1/8 10:19 AM

I'm trying to add a new api for ipv6 query.

can please make the new api a general one? ... by Chenyong Dong
Chenyong Dong
1/8 10:22 AM

can please make the new api a general one? like, query with ipv4/ipv6 + username. We don't like to do special coding for ipv4 and 6. You can choose to use "username" for ipv6 case on radius side.

Sure. It will be an api in radius db. The q... by Laura Yang
Laura Yang
1/8 10:23 AM

Sure. It will be an api in radius db. The qurey api for log will be same. 

Begin Reference, Could you send it so that ... by Johannah Liu
Johannah Liu
1/8 10:23 AM

Laura Yang
1/8/2026 10:18 AM
Could you send it so that the search can be narrow down to all ips of a specific user?
Srue I can do it. but which atrributes in radc_t is used for username

endpoint by Laura Yang
Laura Yang
1/8 10:24 AM

endpoint


👍
1 Like reaction.

Chenyong   Dong Johannah   Liu ,   Currentl... by Jeff Wang
Jeff Wang
1/8 2:59 PM

Chenyong DongJohannah Liu,  

Currently  with "set extended-radius-log enable", traffic come in ,it will create  match " rsso cache" item.

 

Then disable it  "set extended-radius-log disable" ,  it still need wait 5 min to clear  rsso cache.

Can we do it  with new fix ?  if " set extended-radius-log disable" , it clear match vdom rsso cache, thanks


👍
1 Like reaction.

not good. old sessions might report logs ev... by Chenyong Dong
Chenyong Dong
1/8 3:04 PM

not good. old sessions might report logs even after disable the option. We should let the cache timeout by itself. feels no issue if we keep the cache there.

oh, just recall after disabled option, we w... by Chenyong Dong
Chenyong Dong
1/8 3:05 PM

oh, just recall after disabled option, we won't log rsso. seems ok to clean

current behavior is 'after 'set externde-ra... by Jeff Wang
Jeff Wang
1/8 3:06 PM

current behavior is 'after 'set externde-radius-log disable', it still have rssoinfo in traffic log


🤔
1 Thinking reaction.

Should not be like this. 'Cause everytime b... by Johannah Liu
Johannah Liu
1/8 3:08 PM

Should not be like this. 'Cause everytime before filled the rsso-info field, it check the externde-radius-log status.

Morning Everyone , hope you're doing good. by Jeff Song
1/9 9:13 AM
Jeff Song

Morning Everyone, hope you're doing good.

Hi Jonathan   Gu  and Laura   Yang , have... by Jeff Song
1/9 9:14 AM
Jeff Song

Hi Jonathan Gu and Laura Yang, have you figure out an approach to settle the issues?

Laura   Yang , Tom replied that both the fr... by Jeff Song
1/9 9:18 AM
Jeff Song

Laura Yang, Tom replied that both the framed v6 address and framed v6 prefix are needed to be support. It seems that you need to change the logic on the auth side. 

Good morning Jeff, I just checked his comme... by Laura Yang
Laura Yang
1/9 9:48 AM

Good morning Jeff, I just checked his comments. So I will continue to add the api to radius db. The api for log query is not changed.


👍
2 Like reactions.
2

commit 25e3b3840a8ad67caa68db08f302c91be187... by Laura Yang
Laura Yang
1/9 4:40 PM

commit 25e3b3840a8ad67caa68db08f302c91be187cd84 (HEAD, origin/br_7-4_logging_rsso_acc_msgs)
Author: yangl <yangl@fortinet.com>
Date:   Fri Jan 9 16:39:24 2026 -0800

 

    fix: 1241381: fg_7-4_logging_rsso_acc_msgs/build_tag_8923: ipv6 rsso traffic log did not include rssoinfo field

 

Need a new build? by Jeff Song
1/9 4:40 PM
Jeff Song

Need a new build?

Jeff   Song  Could you help trigger the bui... by Laura Yang
Laura Yang
1/9 4:40 PM

Jeff Song Could you help trigger the building? Thanks.

yes yes. by Laura Yang
Laura Yang
1/9 4:41 PM

yes yes.

It can't be built in phab. by Laura Yang
Laura Yang
1/9 4:41 PM

It can't be built in phab.

Jeff   Wang , B8927 is coming by Jeff Song
1/9 4:44 PM
Jeff Song

Jeff Wang, B8927 is coming

Laura   Yang , Johannah   Liu  do we still... by Jeff Song
1/9 4:45 PM
Jeff Song

Laura Yang, Johannah Liu do we still need to fix https://mantis.fortinet.com/bug_view_page.php?bug_id=1241309?


👍
1 Like reaction.

Mine is almost ready as well by Johannah Liu
Johannah Liu
1/9 4:46 PM

Mine is almost ready as well

let me push it by Johannah Liu
Johannah Liu
1/9 4:46 PM

let me push it


👍
1 Like reaction.

has context menu



👍

❤️

😆

😮


