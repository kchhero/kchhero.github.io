---
title: 'webrtc> memo'
layout: post
tags:
  - WebRTC
category: service
---
```
"sdp" : 
"v=0
o=- 8079379161823522038 2 IN IP4 127.0.0.1
s=- 
t=0 0 
a=group:BUNDLE 0 1 2 
a=msid-semantic: WMS stream_id 
m=audio 9 UDP/TLS/RTP/SAVPF 111 103 104 9 102 0 8 106 105 13 110 112 113 126 
c=IN IP4 0.0.0.0 
a=rtcp:9 IN IP4 0.0.0.0 
a=ice-ufrag:jTVF 
a=ice-pwd:IvDrhSJLHFJ2VqXvMqwDJrUm 
a=ice-options:trickle 
a=fingerprint:sha-256 85:A6:9C:B7:8E:0D:A6:B6:AB:05:B8:D3:32:24:2A:EB:1B:B3:CE:47:31:8D:A9:F8:B9:EC:AA:2E:13:5B:4F:CC 
a=setup:actpass 
a=mid:0 
a=extmap:1 urn:ietf:params:rtp-hdrext:ssrc-audio-level 
a=extmap:2 http://www.ietf.org/id/draft-holmer-rmcat-transport-wide-cc-extensions-01 
a=extmap:3 urn:ietf:params:rtp-hdrext:sdes:mid 
a=extmap:4 urn:ietf:params:rtp-hdrext:sdes:rtp-stream-id 
a=extmap:5 urn:ietf:params:rtp-hdrext:sdes:repaired-rtp-stream-id 
a=sendrecv 
a=msid:stream_id audio_label 
a=rtcp-mux 
a=rtpmap:111 opus/48000/2 
a=rtcp-fb:111 transport-cc 
a=fmtp:111 minptime=10;useinbandfec=1 
a=rtpmap:103 ISAC/16000 
a=rtpmap:104 ISAC/32000 
a=rtpmap:9 G722/8000 
a=rtpmap:102 ILBC/8000 
a=rtpmap:0 PCMU/8000 
a=rtpmap:8 PCMA/8000 
a=rtpmap:106 CN/32000 
a=rtpmap:105 CN/16000 
a=rtpmap:13 CN/8000 
a=rtpmap:110 telephone-event/48000 
a=rtpmap:112 telephone-event/32000 
a=rtpmap:113 telephone-event/16000 
a=rtpmap:126 telephone-event/8000 
a=ssrc:3949911766 cname:QQNecaDCsBtxlmFJ 
a=ssrc:3949911766 msid:stream_id audio_label 
a=ssrc:3949911766 mslabel:stream_id 
a=ssrc:3949911766 label:audio_label 
m=video 9 UDP/TLS/RTP/SAVPF 96 97 98 99 100 101 127 124 125 
c=IN IP4 0.0.0.0 
a=rtcp:9 IN IP4 0.0.0.0 
a=ice-ufrag:jTVF 
a=ice-pwd:IvDrhSJLHFJ2VqXvMqwDJrUm 
a=ice-options:trickle 
a=fingerprint:sha-256 85:A6:9C:B7:8E:0D:A6:B6:AB:05:B8:D3:32:24:2A:EB:1B:B3:CE:47:31:8D:A9:F8:B9:EC:AA:2E:13:5B:4F:CC 
a=setup:actpass 
a=mid:1 
a=extmap:14 urn:ietf:params:rtp-hdrext:toffset 
a=extmap:13 http://www.webrtc.org/experiments/rtp-hdrext/abs-send-time 
a=extmap:12 urn:3gpp:video-orientation 
a=extmap:2 http://www.ietf.org/id/draft-holmer-rmcat-transport-wide-cc-extensions-01 
a=extmap:11 http://www.webrtc.org/experiments/rtp-hdrext/playout-delay 
a=extmap:6 http://www.webrtc.org/experiments/rtp-hdrext/video-content-type 
a=extmap:7 http://www.webrtc.org/experiments/rtp-hdrext/video-timing 
a=extmap:8 http://tools.ietf.org/html/draft-ietf-avtext-framemarking-07 
a=extmap:9 http://www.webrtc.org/experiments/rtp-hdrext/color-space 
a=extmap:3 urn:ietf:params:rtp-hdrext:sdes:mid 
a=extmap:4 urn:ietf:params:rtp-hdrext:sdes:rtp-stream-id 
a=extmap:5 urn:ietf:params:rtp-hdrext:sdes:repaired-rtp-stream-id 
a=sendrecv 
a=msid:stream_id video_label 
a=rtcp-mux 
a=rtcp-rsize 
a=rtpmap:96 VP8/90000 
a=rtcp-fb:96 goog-remb 
a=rtcp-fb:96 transport-cc 
a=rtcp-fb:96 ccm fir 
a=rtcp-fb:96 nack 
a=rtcp-fb:96 nack pli 
a=rtpmap:97 rtx/90000 
a=fmtp:97 apt=96 
a=rtpmap:98 VP9/90000 
a=rtcp-fb:98 goog-remb 
a=rtcp-fb:98 transport-cc 
a=rtcp-fb:98 ccm fir 
a=rtcp-fb:98 nack 
a=rtcp-fb:98 nack pli 
a=fmtp:98 profile-id=0 
a=rtpmap:99 rtx/90000 
a=fmtp:99 apt=98 
a=rtpmap:100 VP9/90000 
a=rtcp-fb:100 goog-remb 
a=rtcp-fb:100 transport-cc 
a=rtcp-fb:100 ccm fir 
a=rtcp-fb:100 nack 
a=rtcp-fb:100 nack pli 
a=fmtp:100 profile-id=2 
a=rtpmap:101 rtx/90000 
a=fmtp:101 apt=100 
a=rtpmap:127 red/90000 
a=rtpmap:124 rtx/90000 
a=fmtp:124 apt=127 
a=rtpmap:125 ulpfec/90000 
a=ssrc-group:FID 2002896060 1323194704 
a=ssrc:2002896060 cname:QQNecaDCsBtxlmFJ 
a=ssrc:2002896060 msid:stream_id video_label 
a=ssrc:2002896060 mslabel:stream_id 
a=ssrc:2002896060 label:video_label 
a=ssrc:1323194704 cname:QQNecaDCsBtxlmFJ 
a=ssrc:1323194704 msid:stream_id video_label 
a=ssrc:1323194704 mslabel:stream_id 
a=ssrc:1323194704 label:video_label 
m=application 9 DTLS/SCTP 5000 
c=IN IP4 0.0.0.0 
a=ice-ufrag:jTVF 
a=ice-pwd:IvDrhSJLHFJ2VqXvMqwDJrUm 
a=ice-options:trickle 
a=fingerprint:sha-256 85:A6:9C:B7:8E:0D:A6:B6:AB:05:B8:D3:32:24:2A:EB:1B:B3:CE:47:31:8D:A9:F8:B9:EC:AA:2E:13:5B:4F:CC 
a=setup:actpass 
a=mid:2 
a=sctpmap:5000 webrtc-datachannel 1024 yy
"
```