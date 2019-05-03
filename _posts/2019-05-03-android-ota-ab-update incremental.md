---
title: 'Android OTA A/B update incremental'
layout: post
tags:
  - Android
category: Android
---
#### Android - OTA A/B update working log2

A/B update 작업의 working log - incremental update

---

### step 5 
5. incremental OTA packaging and TEST

```
build/tools/releasetools/ota_from_target_files.py -i ${target_files} --full_bootloader
```
build 시 추가 옵션이 붙는거외에는 full OTA A/B update와 동일하다.

TEST중 문제점 발견 bootloader를 분리하는 scheme을 추가한 뒤 
full OTA A/B update --> incremental update 순서로 진행중에 다음과 같은 error log를 만나게 되었다.

```
12-31 15:01:10.809  1925  1925 I update_engine: [1231/150110.809031:INFO:metrics_utils.cc(336)] Number of Reboots during current update attempt = 0
12-31 15:01:10.809  1925  1925 I update_engine: [1231/150110.809546:INFO:metrics_utils.cc(344)] Payload Attempt Number = 1
12-31 15:01:10.827  1925  1925 I update_engine: [1231/150110.809929:INFO:metrics_utils.cc(361)] Update Timestamp Start = 1/1/1970 0:01:38 GMT
12-31 15:01:10.829  1925  1925 I update_engine: [1231/150110.829146:INFO:update_attempter_android.cc(562)] Scheduling an action processor start.
12-31 15:01:10.829  2937  2937 I update_engine_client: [INFO:update_engine_client_android.cc(90)] onStatusUpdate(UPDATE_STATUS_IDLE (0), 0)
12-31 15:01:10.829  1925  1925 I update_engine: [1231/150110.829465:INFO:action_processor.cc(46)] ActionProcessor: starting InstallPlanAction
12-31 15:01:10.829  2937  2937 I update_engine_client: [INFO:update_engine_client_android.cc(90)] onStatusUpdate(UPDATE_STATUS_UPDATE_AVAILABLE (2), 0)
12-31 15:01:10.829  1925  1925 I update_engine: [1231/150110.829706:INFO:action_processor.cc(116)] ActionProcessor: finished InstallPlanAction with code ErrorCode::kSuccess
12-31 15:01:10.829  1925  1925 I update_engine: [1231/150110.829888:INFO:action_processor.cc(143)] ActionProcessor: starting DownloadAction
12-31 15:01:10.830  1925  1925 I update_engine: [1231/150110.830112:INFO:install_plan.cc(83)] InstallPlan: new_update, version: , source_slot: B, target_slot: A, url: file:///data/ota_package/payload.bin, payload: (size: 60740, metadata_size: 60212, metadata signature: , hash: 5663FA2340CE9E8FFBE18E7654D46EF8ABA42478E8D1BE3F8EBF9950CA897D75, payload type: unknown), hash_checks_mandatory: true, powerwash_required: false, switch_slot_on_reboot: true, run_post_install: true
12-31 15:01:10.830  1925  1925 I update_engine: [1231/150110.830255:INFO:download_action.cc(200)] Marking new slot as unbootable
12-31 15:01:10.852  1925  1925 I update_engine: [1231/150110.852642:INFO:multi_range_http_fetcher.cc(45)] starting first transfer
12-31 15:01:10.852  1925  1925 I update_engine: [1231/150110.852851:INFO:multi_range_http_fetcher.cc(74)] starting transfer of range 0+60740
12-31 15:01:10.853  2937  2937 I update_engine_client: [INFO:update_engine_client_android.cc(90)] onStatusUpdate(UPDATE_STATUS_DOWNLOADING (3), 0.26974)
12-31 15:01:10.855  1925  1925 I update_engine: [1231/150110.855860:INFO:delta_performer.cc(210)] Completed 0/? operations, 16384/60740 bytes downloaded (26%), overall progress 13%
12-31 15:01:10.856  2937  2937 I update_engine_client: [INFO:update_engine_client_android.cc(90)] onStatusUpdate(UPDATE_STATUS_DOWNLOADING (3), 0.53948)
12-31 15:01:10.876  1925  1925 I update_engine: [1231/150110.876567:INFO:delta_performer.cc(210)] Completed 0/? operations, 32768/60740 bytes downloaded (53%), overall progress 26%
12-31 15:01:10.878  1925  1925 I update_engine: [1231/150110.878578:INFO:delta_performer.cc(210)] Completed 0/? operations, 49152/60740 bytes downloaded (80%), overall progress 40%
12-31 15:01:10.880  1925  1925 I update_engine: [1231/150110.880333:INFO:delta_performer.cc(210)] Completed 0/? operations, 60740/60740 bytes downloaded (100%), overall progress 50%
12-31 15:01:10.880  1925  1925 I update_engine: [1231/150110.880712:INFO:delta_performer.cc(470)] Manifest size in payload matches expected value from Omaha
12-31 15:01:10.880  1925  1925 I update_engine: [1231/150110.880887:INFO:payload_metadata.cc(182)] Verifying metadata hash signature using public key: /etc/update_engine/update-payload-key.pub.pem
12-31 15:01:10.882  1925  1925 I update_engine: [1231/150110.882417:INFO:payload_verifier.cc(93)] signature blob size = 264
12-31 15:01:10.887  1925  1925 I update_engine: [1231/150110.887235:INFO:payload_verifier.cc(112)] Verified correct signature 1 out of 1 signatures.
12-31 15:01:10.887  1925  1925 I update_engine: [1231/150110.887439:INFO:payload_metadata.cc(224)] Metadata hash signature matches value in Omaha response.
12-31 15:01:10.901  2937  2937 I update_engine_client: [INFO:update_engine_client_android.cc(90)] onStatusUpdate(UPDATE_STATUS_DOWNLOADING (3), 0.80922)
12-31 15:01:10.901  2937  2937 I update_engine_client: [INFO:update_engine_client_android.cc(90)] onStatusUpdate(UPDATE_STATUS_DOWNLOADING (3), 1)
12-31 15:01:10.903  1925  1925 I update_engine: [1231/150110.903707:INFO:delta_performer.cc(1405)] Detected a 'delta' payload.
12-31 15:01:10.932  1925  1925 I update_engine: [1231/150110.932001:INFO:delta_performer.cc(400)] PartitionInfo old bootloader sha256: XshwEvfhAl9PqLH8y9rygETfQxZ11BR3P4mieiNAxcs= size: 4808704
12-31 15:01:10.932  1925  1925 I update_engine: [1231/150110.932252:INFO:delta_performer.cc(400)] PartitionInfo new bootloader sha256: HPTIIIo7a2idxK3YZYgVG66eSeDffqJbUcSqVDpwzl0= size: 4812800
12-31 15:01:10.932  1925  1925 I update_engine: [1231/150110.932410:INFO:delta_performer.cc(400)] PartitionInfo old boot sha256: ZZuMmt/dtx1kJDL2OllDzofVqIXGOPS+mgCU3UG9Uww= size: 7585792
12-31 15:01:10.932  1925  1925 I update_engine: [1231/150110.932552:INFO:delta_performer.cc(400)] PartitionInfo new boot sha256: ZZuMmt/dtx1kJDL2OllDzofVqIXGOPS+mgCU3UG9Uww= size: 7585792
12-31 15:01:10.932  1925  1925 I update_engine: [1231/150110.932705:INFO:delta_performer.cc(400)] PartitionInfo old system sha256: VlxXVANiF3h/o3B4qIoCPl14cyatQ+sw2zOwt7VP3tw= size: 1073741824
12-31 15:01:10.932  1925  1925 I update_engine: [1231/150110.932847:INFO:delta_performer.cc(400)] PartitionInfo new system sha256: VlxXVANiF3h/o3B4qIoCPl14cyatQ+sw2zOwt7VP3tw= size: 1073741824
12-31 15:01:10.933  1925  1925 I update_engine: [1231/150110.933036:INFO:delta_performer.cc(400)] PartitionInfo old vendor sha256: v6laPTdWgHa8pwAXbmzdwhgopE6ei8SuQKb3osNNOwU= size: 268435456
12-31 15:01:10.933  1925  1925 I update_engine: [1231/150110.933174:INFO:delta_performer.cc(400)] PartitionInfo new vendor sha256: v6laPTdWgHa8pwAXbmzdwhgopE6ei8SuQKb3osNNOwU= size: 268435456
12-31 15:01:10.935  1925  1925 I update_engine: [1231/150110.935074:INFO:delta_performer.cc(374)] Opening /dev/block/platform/c0000000.soc/c0062000.dwmmc/by-name/bootloader_a partition without O_DSYNC
12-31 15:01:10.945  1925  1925 I update_engine: [1231/150110.945549:INFO:delta_performer.cc(126)] Caching writes.
12-31 15:01:10.946  1925  1925 I update_engine: [1231/150110.946193:INFO:delta_performer.cc(386)] Applying 10 operations to partition "bootloader"
12-31 15:01:10.933  1925  1925 I update_engine: type=1400 audit(0.0:10): avc: denied { read } for name="mmcblk0p2" dev="tmpfs" ino=1393 scontext=u:r:update_engine:s0 tcontext=u:object_r:block_device:s0 tclass=blk_file permissive=1
12-31 15:01:10.982  1925  1925 I update_engine: [1231/150110.982753:INFO:delta_performer.cc(601)] Starting to apply update payload operations
12-31 15:01:11.000  1925  1925 E update_engine: [1231/150111.000081:ERROR:delta_performer.cc(990)] The hash of the source data on disk for this operation doesn't match the expected value. This could mean that the delta update payload was targeted for another version, or that the source partition was modified after it was installed, for example, by mounting a filesystem.
12-31 15:01:11.000  1925  1925 E update_engine: [1231/150111.000337:ERROR:delta_performer.cc(995)] Expected:   sha256|hex = 1A97D48CC0707CD1D1184E122D716655F65939FAD909EED86786D9F1499B7E85
12-31 15:01:11.000  1925  1925 E update_engine: [1231/150111.000488:ERROR:delta_performer.cc(998)] Calculated: sha256|hex = 3A5DC131C62C5C0A9376E55FC73D68C4FAF3171C77F2998ADD825E419ACAEFE1
12-31 15:01:11.000  1925  1925 E update_engine: [1231/150111.000634:ERROR:delta_performer.cc(1009)] Operation source (offset:size) in blocks: 0:7
12-31 15:01:11.000  1925  1925 E update_engine: [1231/150111.000824:ERROR:delta_performer.cc(1038)] ValidateSourceHash(source_hash, operation, source_fd_, error) failed.
12-31 15:01:11.001  1925  1925 E update_engine: [1231/150111.001070:ERROR:delta_performer.cc(298)] Failed to perform SOURCE_COPY operation 0, which is the operation 0 in partition "bootloader"
12-31 15:01:11.001  1925  1925 E update_engine: [1231/150111.001232:ERROR:download_action.cc(337)] Error ErrorCode::kDownloadStateInitializationError (20) in DeltaPerformer's Write method when processing the received payload -- Terminating processing
12-31 15:01:11.004  1925  1925 I update_engine: [1231/150111.004266:INFO:multi_range_http_fetcher.cc(172)] Received transfer terminated.
12-31 15:01:11.004  1925  1925 I update_engine: [1231/150111.004466:INFO:multi_range_http_fetcher.cc(124)] TransferEnded w/ code 200
12-31 15:01:11.004  1925  1925 I update_engine: [1231/150111.004610:INFO:multi_range_http_fetcher.cc(126)] Terminating.
12-31 15:01:11.004  1925  1925 I update_engine: [1231/150111.004750:INFO:action_processor.cc(116)] ActionProcessor: finished DownloadAction with code ErrorCode::kDownloadStateInitializationError
12-31 15:01:11.004  1925  1925 I update_engine: [1231/150111.004887:INFO:action_processor.cc(121)] ActionProcessor: Aborting processing due to failure.
12-31 15:01:11.005  1925  1925 I update_engine: [1231/150111.005060:INFO:update_attempter_android.cc(431)] Processing Done.
12-31 15:01:11.008  1925  1925 I update_engine: [1231/150111.008319:INFO:update_attempter_android.cc(450)] Resetting update progress.
12-31 15:01:11.012  1925  1925 I update_engine: [1231/150111.012591:INFO:metrics_reporter_android.cc(29)] uploading 1 to histogram for metric ota_update_engine_attempt_number
12-31 15:01:11.012  1925  1925 I update_engine: [1231/150111.012848:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_attempt_payload_type
12-31 15:01:11.013  1925  1925 I update_engine: [1231/150111.013102:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_attempt_duration_boottime_in_minutes
12-31 15:01:11.013  1925  1925 I update_engine: [1231/150111.013360:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_attempt_duration_monotonic_in_minutes
12-31 15:01:11.013  1925  1925 I update_engine: [1231/150111.013551:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_attempt_payload_size_mib
12-31 15:01:11.013  1925  1925 I update_engine: [1231/150111.013735:INFO:metrics_reporter_android.cc(29)] uploading 1 to histogram for metric ota_update_engine_attempt_result
12-31 15:01:11.014  1925  1925 I update_engine: [1231/150111.013917:INFO:metrics_reporter_android.cc(29)] uploading 20 to histogram for metric ota_update_engine_attempt_error_code
12-31 15:01:11.014  1925  1925 I update_engine: [1231/150111.014304:INFO:metrics_reporter_android.cc(29)] uploading 60740 to histogram for metric ota_update_engine_attempt_current_bytes_downloaded_mib
12-31 15:01:11.023  2937  2937 I update_engine_client: [INFO:update_engine_client_android.cc(90)] onStatusUpdate(UPDATE_STATUS_IDLE (0), 0)
12-31 15:01:11.023  2937  2937 I update_engine_client: [INFO:update_engine_client_android.cc(98)] onPayloadApplicationComplete(ErrorCode::kDownloadStateInitializationError (20))
```

---

bootloader partition의 hash mismatch가 발생한다.
```
PartitionInfo old boot sha256: ZZuMmt/dtx1kJDL2OllDzofVqIXGOPS+mgCU3UG9Uww= size: 7585792
PartitionInfo new boot sha256: ZZuMmt/dtx1kJDL2OllDzofVqIXGOPS+mgCU3UG9Uww= size: 7585792
```
```
Expected:   sha256|hex = 1A97D48CC0707CD1D1184E122D716655F65939FAD909EED86786D9F1499B7E85
Calculated: sha256|hex = 3A5DC131C62C5C0A9376E55FC73D68C4FAF3171C77F2998ADD825E419ACAEFE1
```

아래는 slot_b 를 dump 한 내용인데, 마지막 512 byte에 이상한 값으로 채워져있음이 발견되었다.
boot.img의 0x30000 ~ 0x301f0 와 동일하다.
```
004afdb0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
004afdc0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
004afdd0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
004afde0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
004afdf0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
004afe00: ecd1 9499 21a3 9ffe bb9b 42c6 beb5 4adc  ....!.....B...J.
004afe10: 7e15 cea1 9031 fbc1 9031 873e b02b e1fc  ~....1...1.>.+..
004afe20: 48c7 b59d 4d1f 1f3d b740 eecb 6fd3 be04  H...M..=.@..o...
004afe30: ded7 fcca 907e dbb7 42fa fd17 0b5e 1afa  .....~..B....^..
004afe40: 98fc afd8 98f6 474b 19d7 b6d3 4f7c eec4  ......GK....O|..
004afe50: 5b15 d6b9 c4a7 14c5 772e 6e36 045f dd62  [.......w.n6._.b
004afe60: b889 af86 2c80 e76f bf55 e09e b92d b867  ....,..o.U...-.g
004afe70: 6e0b ee99 db82 7be6 4ef7 a70a d618 e067  n.....{.N......g
004afe80: 8b82 2163 de10 3e5d 1952 165d 42f4 3377  ..!c..>].R.]B.3w
004afe90: 6448 073e 1ff8 a21f d3f8 e1fd 38c9 0a63  dH.>........8..c
004afea0: a99d c072 dfc0 3e7e 35c6 dfaf 09e9 3bc7  ...r..>~5.....;.
004afeb0: 848c 7f4c 0ce9 8f4f 0e19 4fd2 e719 1abf  ...L...O..O.....
004afec0: 67e9 f33c 5dc7 bc8e 495f 2b8c 7d5e a6cd  g..<]...I_+.}^..
004afed0: 92e7 14fc 692a 2623 6668 31fb 9ddb 7cd7  ....i*&#fh1...|.
004afee0: 723c 0f68 1878 8c35 f47b a5e7 bac5 6b94  r<.h.x.5.{....k.
004afef0: c875 f99e c875 44bf e796 28a1 eb5d f43c  .u...uD...(..].<
004aff00: 62ed 31e6 b42e c72d 2d10 b134 7963 dfea  b.1....--..4yc..
004aff10: 1d49 b46c 0de7 c600 8632 e840 2456 e2a5  .I.l.....2.@$V..
004aff20: 39e4 1c55 21a3 c201 2c44 fa2f e38c 6c85  9..U!...,D./..l.
004aff30: d52a c716 e535 1ac2 66ba 24b6 d043 73e2  .*...5..f.$..Cs.
004aff40: 8d72 5c0e 6ca7 3f4c 247e 86fe bc35 30f4  .r\.l.?L$~...50.
004aff50: da77 b32e 598d 310e 39f1 0ddd 8ada 8bfd  .w..Y.1.9.......
004aff60: 05bc f2c4 6871 f6f2 3db9 3f88 77cc fa68  ....hq..=.?.w..h
004aff70: 347c 88c4 3be9 fbf6 4692 bb3f 3a94 88f7  4|..;...F..?:...
004aff80: 9708 1fbd 8912 07e2 8561 ea3f 87ed 5d4d  .........a.?..]M
004aff90: c6b3 6561 d6f1 62ff c157 dded 1778 cbbd  ..ea..b..W...x..
004affa0: 4f91 ace3 9f07 5e52 4ba7 e79e 2f21 4f46  O.....^RK.../!OF
004affb0: 9790 7f2c b932 16d2 dc7b be24 7e63 1f5f  ...,.2...{.$~c._
004affc0: 47ed 01c6 86c0 541a 8ddc f059 7ba9 0fd6  G.....T....Y{...
004affd0: f2fe 3a44 2e24 d6ff 517f 9c81 06e1 330a  ..:D.$..Q.....3.
004affe0: 4c54 c464 903c 033b 5bbe a7a1 80e4 71c7  LT.d.<.;[.....q.
004afff0: 0adf 1da3 e701 5f56 fa29 3dcc 78b4 cb62  ......_V.)=.x..b
```
debugging 필요한 상태

---

2019.05.03

slot_A는 문제없음.
slot_B의 bootloader partition의 마지막 512 byte가 문제로 보인다.
A <-> B  순서 및 횟수 상관없이 slot_B의 512 byte만 문제가 된다.
