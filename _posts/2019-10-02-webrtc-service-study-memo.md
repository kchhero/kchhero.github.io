---
title: 'webrtc> memo'
layout: post
tags:
  - WebRTC
category: service
---
"sdp" : 
"v=0\r\n
o=- 8079379161823522038 2 IN IP4 127.0.0.1\r\n
s=-\r\n
t=0 0\r\n
a=group:BUNDLE 0 1 2\r\n
a=msid-semantic: WMS stream_id\r\n
m=audio 9 UDP/TLS/RTP/SAVPF 111 103 104 9 102 0 8 106 105 13 110 112 113 126\r\n
c=IN IP4 0.0.0.0\r\n
a=rtcp:9 IN IP4 0.0.0.0\r\n
a=ice-ufrag:jTVF\r\n
a=ice-pwd:IvDrhSJLHFJ2VqXvMqwDJrUm\r\n
a=ice-options:trickle\r\n
a=fingerprint:sha-256 85:A6:9C:B7:8E:0D:A6:B6:AB:05:B8:D3:32:24:2A:EB:1B:B3:CE:47:31:8D:A9:F8:B9:EC:AA:2E:13:5B:4F:CC\r\n
a=setup:actpass\r\n
a=mid:0\r\n
a=extmap:1 urn:ietf:params:rtp-hdrext:ssrc-audio-level\r\n
a=extmap:2 http://www.ietf.org/id/draft-holmer-rmcat-transport-wide-cc-extensions-01\r\n
a=extmap:3 urn:ietf:params:rtp-hdrext:sdes:mid\r\n
a=extmap:4 urn:ietf:params:rtp-hdrext:sdes:rtp-stream-id\r\n
a=extmap:5 urn:ietf:params:rtp-hdrext:sdes:repaired-rtp-stream-id\r\n
a=sendrecv\r\n
a=msid:stream_id audio_label\r\n
a=rtcp-mux\r\n
a=rtpmap:111 opus/48000/2\r\n
a=rtcp-fb:111 transport-cc\r\n
a=fmtp:111 minptime=10;useinbandfec=1\r\n
a=rtpmap:103 ISAC/16000\r\n
a=rtpmap:104 ISAC/32000\r\n
a=rtpmap:9 G722/8000\r\n
a=rtpmap:102 ILBC/8000\r\n
a=rtpmap:0 PCMU/8000\r\n
a=rtpmap:8 PCMA/8000\r\n
a=rtpmap:106 CN/32000\r\n
a=rtpmap:105 CN/16000\r\n
a=rtpmap:13 CN/8000\r\n
a=rtpmap:110 telephone-event/48000\r\n
a=rtpmap:112 telephone-event/32000\r\n
a=rtpmap:113 telephone-event/16000\r\n
a=rtpmap:126 telephone-event/8000\r\n
a=ssrc:3949911766 cname:QQNecaDCsBtxlmFJ\r\n
a=ssrc:3949911766 msid:stream_id audio_label\r\n
a=ssrc:3949911766 mslabel:stream_id\r\n
a=ssrc:3949911766 label:audio_label\r\n
m=video 9 UDP/TLS/RTP/SAVPF 96 97 98 99 100 101 127 124 125\r\n
c=IN IP4 0.0.0.0\r\n
a=rtcp:9 IN IP4 0.0.0.0\r\n
a=ice-ufrag:jTVF\r\n
a=ice-pwd:IvDrhSJLHFJ2VqXvMqwDJrUm\r\n
a=ice-options:trickle\r\n
a=fingerprint:sha-256 85:A6:9C:B7:8E:0D:A6:B6:AB:05:B8:D3:32:24:2A:EB:1B:B3:CE:47:31:8D:A9:F8:B9:EC:AA:2E:13:5B:4F:CC\r\n
a=setup:actpass\r\n
a=mid:1\r\n
a=extmap:14 urn:ietf:params:rtp-hdrext:toffset\r\n
a=extmap:13 http://www.webrtc.org/experiments/rtp-hdrext/abs-send-time\r\n
a=extmap:12 urn:3gpp:video-orientation\r\n
a=extmap:2 http://www.ietf.org/id/draft-holmer-rmcat-transport-wide-cc-extensions-01\r\n
a=extmap:11 http://www.webrtc.org/experiments/rtp-hdrext/playout-delay\r\n
a=extmap:6 http://www.webrtc.org/experiments/rtp-hdrext/video-content-type\r\n
a=extmap:7 http://www.webrtc.org/experiments/rtp-hdrext/video-timing\r\n
a=extmap:8 http://tools.ietf.org/html/draft-ietf-avtext-framemarking-07\r\n
a=extmap:9 http://www.webrtc.org/experiments/rtp-hdrext/color-space\r\n
a=extmap:3 urn:ietf:params:rtp-hdrext:sdes:mid\r\n
a=extmap:4 urn:ietf:params:rtp-hdrext:sdes:rtp-stream-id\r\n
a=extmap:5 urn:ietf:params:rtp-hdrext:sdes:repaired-rtp-stream-id\r\n
a=sendrecv\r\n
a=msid:stream_id video_label\r\n
a=rtcp-mux\r\n
a=rtcp-rsize\r\n
a=rtpmap:96 VP8/90000\r\n
a=rtcp-fb:96 goog-remb\r\n
a=rtcp-fb:96 transport-cc\r\n
a=rtcp-fb:96 ccm fir\r\n
a=rtcp-fb:96 nack\r\n
a=rtcp-fb:96 nack pli\r\n
a=rtpmap:97 rtx/90000\r\n
a=fmtp:97 apt=96\r\n
a=rtpmap:98 VP9/90000\r\n
a=rtcp-fb:98 goog-remb\r\n
a=rtcp-fb:98 transport-cc\r\n
a=rtcp-fb:98 ccm fir\r\n
a=rtcp-fb:98 nack\r\n
a=rtcp-fb:98 nack pli\r\n
a=fmtp:98 profile-id=0\r\n
a=rtpmap:99 rtx/90000\r\n
a=fmtp:99 apt=98\r\n
a=rtpmap:100 VP9/90000\r\n
a=rtcp-fb:100 goog-remb\r\n
a=rtcp-fb:100 transport-cc\r\n
a=rtcp-fb:100 ccm fir\r\n
a=rtcp-fb:100 nack\r\n
a=rtcp-fb:100 nack pli\r\n
a=fmtp:100 profile-id=2\r\n
a=rtpmap:101 rtx/90000\r\n
a=fmtp:101 apt=100\r\n
a=rtpmap:127 red/90000\r\n
a=rtpmap:124 rtx/90000\r\n
a=fmtp:124 apt=127\r\n
a=rtpmap:125 ulpfec/90000\r\n
a=ssrc-group:FID 2002896060 1323194704\r\n
a=ssrc:2002896060 cname:QQNecaDCsBtxlmFJ\r\n
a=ssrc:2002896060 msid:stream_id video_label\r\n
a=ssrc:2002896060 mslabel:stream_id\r\n
a=ssrc:2002896060 label:video_label\r\n
a=ssrc:1323194704 cname:QQNecaDCsBtxlmFJ\r\n
a=ssrc:1323194704 msid:stream_id video_label\r\n
a=ssrc:1323194704 mslabel:stream_id\r\n
a=ssrc:1323194704 label:video_label\r\n
m=application 9 DTLS/SCTP 5000\r\n
c=IN IP4 0.0.0.0\r\n
a=ice-ufrag:jTVF\r\n
a=ice-pwd:IvDrhSJLHFJ2VqXvMqwDJrUm\r\n
a=ice-options:trickle\r\n
a=fingerprint:sha-256 85:A6:9C:B7:8E:0D:A6:B6:AB:05:B8:D3:32:24:2A:EB:1B:B3:CE:47:31:8D:A9:F8:B9:EC:AA:2E:13:5B:4F:CC\r\n
a=setup:actpass\r\n
a=mid:2\r\n
a=sctpmap:5000 webrtc-datachannel 1024\r\n
"