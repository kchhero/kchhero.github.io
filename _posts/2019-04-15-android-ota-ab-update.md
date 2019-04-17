---
layout: post
tags:
  - Android
title: 'Android OTA A/B update'
category: Android
---
#### Android - OTA A/B update working log

A/B update 작업의 working log

---

### step
1. u-boot(bootloader) 의 ab select, boot time의 dtb modify (vendor의 dev/block point runtime 변경)
2. device/nexell/con_svma : fstab, partmat, BoardConfig.mk, device.mk 추가/수정
3. bootctrl HAL 구현, IBootControl HIDL enable
4. TEST
5. incremental OTA packaging and TEST

---

### step 1

refs : git clone "https://android.googlesource.com/platform/external/u-boot"
[==> u-boot diff](https://github.com/kchhero/kchhero.github.io/blob/master/_posts_data/android/49ae454.diff "patch")
```
/* partition number infomation */
/* 1,2 - boot_a, boot_b */
/* 3,5 - system_a, system_b */
/* 6,7 - vendor_a, vendor_b */
/* 8   - misc */
#define CONTROL_PARTITION 8 //"misc"

#if defined(CONFIG_CMD_AB_SELECT)
#define SET_AB_SELECT \
       "if ab_select slot_name mmc 0:${misc_partition_num}; " \
       "then " \
               "echo ab_select get slot_name success;" \
       "else " \
               "echo ab_select get slot_name failed, set slot \"a\";" \
               "setenv slot_name a;" \
       "fi;" \
       "setenv slot_suffix _${slot_name};" \
       "setenv android_boot_option androidboot.slot_suffix=${slot_suffix};" \
       "setenv android_boot_ab run bootcmd_${slot_name};" \
       "if test ${slot_name} = a ; " \
       "then " \
               "setenv root_dev_blk_system_ab /dev/mmcblk0p3 ;" \
       "else " \
               "setenv root_dev_blk_system_ab /dev/mmcblk0p5 ;" \
       "fi;" \
       "setenv bootargs_ab1 root=${root_dev_blk_system_ab};" \
       "setenv bootargs_ab2 ${android_boot_option};"
#else
#define SET_AB_SELECT ""
#endif //CONFIG_CMD_AB_SELECT

#define CONFIG_EXTRA_ENV_SETTINGS	\
	"fdt_high=0xffffffff\0"		\
	"initrd_high=0xffffffff\0"	\
	"kerneladdr=0x40008000\0"	\
	"kernel_file=zImage\0"		\
	"fdtaddr=0x49000000\0"		\
        "misc_partition_num=" __stringify(CONTROL_PARTITION) "\0"       \
        "set_ab_select=" \
                SET_AB_SELECT \
                "\0" \
        "set_bootargs_ab1=setenv bootargs \"${bootargs} ${bootargs_ab1}\" \0" \
        "set_bootargs_ab2=setenv bootargs \"${bootargs} ${bootargs_ab2}\" \0" \
        "bootcmd_set_ab=run set_ab_select;" \
                       "run set_bootargs_ab1;" \
                       "run set_bootargs_ab2;" \
                       "\0"                    \
        "bootcmd=run bootcmd_set_ab;run android_boot_ab\0" \
        "load_fdt="			\
...(snip)...
```

---

### step 2

[==> device/nexell/con_svma diff](https://github.com/kchhero/kchhero.github.io/blob/master/_posts_data/android/ee1c9e3.diff "patch")

```
# hal
PRODUCT_PACKAGES += \
    gralloc.s5pxx18 \
    libGLES_mali \
    hwcomposer.s5pxx18 \
    lights.s5pxx18 \
    audio.primary.s5pxx18 \
    gatekeeper.s5pxx18 \
    camera.s5pxx18 \
    bootctrl.s5pxx18

...(snip)...

########################################################################
# A/B OTA UPDATE
########################################################################
# target definitions
AB_OTA_UPDATER := true
AB_OTA_PARTITIONS := \
  boot \
  system \
  vendor

BOARD_BUILD_SYSTEM_ROOT_IMAGE := true
TARGET_NO_RECOVERY := true
BOARD_USES_RECOVERY_AS_BOOT := false
PRODUCT_PACKAGES += \
  update_engine \
  update_verifier


PRODUCT_PACKAGES_DEBUG += update_engine_client

# Boot control HAL
PRODUCT_PACKAGES += \
    android.hardware.boot@1.0-impl \
    android.hardware.boot@1.0-service \

# bootctrl HAL
PRODUCT_PACKAGES += \
    bootctrl.default \
    bootctrl.$(TARGET_BOARD_PLATFORM) \
    bootctl

PRODUCT_STATIC_BOOT_CONTROL_HAL := \
    libz \
    libcutils

# A/B OTA post actions
PRODUCT_PACKAGES += cfigPostInstall
AB_OTA_POSTINSTALL_CONFIG += \
    RUN_POSTINSTALL_system=true \
    POSTINSTALL_PATH_system=bin/cfigPostInstall \
    FILESYSTEM_TYPE_system=ext4 \
    POSTINSTALL_OPTIONAL_system=true

# App compilation in background
PRODUCT_PACKAGES += otapreopt_script
AB_OTA_POSTINSTALL_CONFIG += \
  RUN_POSTINSTALL_system=true \
  POSTINSTALL_PATH_system=system/bin/otapreopt_script \
  FILESYSTEM_TYPE_system=ext4 \
  POSTINSTALL_OPTIONAL_system=true


# For A/B update test
PRODUCT_PACKAGES += \
  updater
```

---

### step 3

refs : https://android.googlesource.com/platform/hardware/bsp/
[==> bootctrl HAL diff](https://github.com/kchhero/kchhero.github.io/blob/master/_posts_data/android/669f828.diff "patch")

---

### step 4

update_engine_client 를 사용함에 있어서 new line, space 를 잘 맞춰주지 않으면 update_engine parser가 제대로 실행되지 않는다.
```
# update_engine_client --payload=file:///data/ota_package/payload.bin --update --follow \
--headers="\

FILE_HASH=UbCTf1cL7nvxEEhd3zI2S2w3jZOsgDjcLGmLN1x4Ssw=\

FILE_SIZE=279381404\

METADATA_HASH=Fnrg2Js8fFDXnnhwzKlanBYHDKQSZ14RcZ3g4yUiSxA=\

METADATA_SIZE=35956\

"
```
rebooting 후 update_verifier 를 실행해야 successful_boot flag가 true로 setting 되며, 이후 새로운 slot으로 안정적으로
booting이 된다.

```
# update_engine_verfier
```

### step 5, doing

incremental OTA update는 pacakage를 생성하는 scheme을 분석해야한다.
vendor 마다 boot.img 를 생성하는 방법이 다르기때문인듯하다.
-i option으로 생성 후 update를 실행하면, boot partittion의 hash 가 miss match 되어 정상 동작하지 않는다.
일단 아래의 script를 trace 해본다.
```
build/make/tools/releasetools/ota_from_target_files.py

build/make/tools/releasetools/ota_common.py

./host/linux-x86/bin/brillo_update_payload	
system/update_engine/scripts/brillo_update_payload
```

---

### LOG

#### First booting, Slot B update (A->B)
```
[0101/000020.171773:INFO:main.cc(182)] Chrome OS Update Engine starting
[0101/000020.191605:INFO:boot_control_android.cc(64)] Loaded boot control hidl hal.
[0101/000020.191934:INFO:daemon_state_android.cc(43)] Booted in dev mode.
[0416/061616.789785:INFO:prefs.cc(122)] update-state-next-operation not present in /data/misc/update_engine/prefs
[0416/061616.794044:INFO:update_attempter_android.cc(246)] Using this install plan:
[0416/061616.794303:INFO:install_plan.cc(83)] InstallPlan: new_update, version: , source_slot: A, target_slot: B, url: file:///data/ota_package/payload.bin, payload: (size: 279316995, metadata_size: 35956, metadata signature: , hash: 6E1EB2889763BBF66896876884FCCF15FF897EB7F23D65A037BAC870357B020A, payload type: unknown), hash_checks_mandatory: true, powerwash_required: false, switch_slot_on_reboot: true, run_post_install: true
[0416/061616.794697:INFO:update_attempter_android.cc(547)] Marking booted slot as good.
[0416/061616.839381:INFO:metrics_utils.cc(336)] Number of Reboots during current update attempt = 0
[0416/061616.839824:INFO:metrics_utils.cc(344)] Payload Attempt Number = 1
[0416/061616.840275:INFO:metrics_utils.cc(361)] Update Timestamp Start = 1/1/1970 0:20:37 GMT
[0416/061616.841158:INFO:update_attempter_android.cc(562)] Scheduling an action processor start.
[0416/061616.841433:INFO:action_processor.cc(46)] ActionProcessor: starting InstallPlanAction
[0416/061616.841585:INFO:action_processor.cc(116)] ActionProcessor: finished InstallPlanAction with code ErrorCode::kSuccess
[0416/061616.841717:INFO:action_processor.cc(143)] ActionProcessor: starting DownloadAction
[0416/061616.841870:INFO:install_plan.cc(83)] InstallPlan: new_update, version: , source_slot: A, target_slot: B, url: file:///data/ota_package/payload.bin, payload: (size: 279316995, metadata_size: 35956, metadata signature: , hash: 6E1EB2889763BBF66896876884FCCF15FF897EB7F23D65A037BAC870357B020A, payload type: unknown), hash_checks_mandatory: true, powerwash_required: false, switch_slot_on_reboot: true, run_post_install: true
[0416/061616.842009:INFO:download_action.cc(200)] Marking new slot as unbootable
[0416/061616.845318:INFO:multi_range_http_fetcher.cc(45)] starting first transfer
[0416/061616.845506:INFO:multi_range_http_fetcher.cc(74)] starting transfer of range 0+279316995
[0416/061616.846871:INFO:delta_performer.cc(210)] Completed 0/? operations, 16384/279316995 bytes downloaded (0%), overall progress 0%
[0416/061616.850034:INFO:delta_performer.cc(470)] Manifest size in payload matches expected value from Omaha
[0416/061616.850315:INFO:payload_metadata.cc(182)] Verifying metadata hash signature using public key: /etc/update_engine/update-payload-key.pub.pem
[0416/061616.851248:INFO:payload_verifier.cc(93)] signature blob size = 264
[0416/061616.856040:INFO:payload_verifier.cc(112)] Verified correct signature 1 out of 1 signatures.
[0416/061616.856291:INFO:payload_metadata.cc(224)] Metadata hash signature matches value in Omaha response.
[0416/061616.861437:INFO:delta_performer.cc(1405)] Detected a 'full' payload.
[0416/061616.881012:INFO:delta_performer.cc(400)] PartitionInfo new boot sha256: 2GrkWw+rzlYb5LWpVMO0sMr4HnA9LlhIGDxeEZxmr18= size: 7585792
[0416/061616.881256:INFO:delta_performer.cc(400)] PartitionInfo new system sha256: PRr4umq2QCUT3Mz9hseUXI+A59OBzv06wNY77T928vs= size: 1073741824
[0416/061616.881395:INFO:delta_performer.cc(400)] PartitionInfo new vendor sha256: 68N53f8cqab/NxD2aaMB2rxHYov4mU0IAksoVZbkEG4= size: 268435456
[0416/061616.882482:INFO:delta_performer.cc(374)] Opening /dev/block/platform/c0000000.soc/c0062000.dwmmc/by-name/boot_b partition without O_DSYNC
[0416/061616.893740:INFO:delta_performer.cc(126)] Caching writes.
[0416/061616.894378:INFO:delta_performer.cc(386)] Applying 4 operations to partition "boot"
[0416/061616.943018:INFO:delta_performer.cc(601)] Starting to apply update payload operations
[0416/061618.709401:INFO:delta_performer.cc(374)] Opening /dev/block/platform/c0000000.soc/c0062000.dwmmc/by-name/system_b partition without O_DSYNC
[0416/061618.720623:INFO:delta_performer.cc(126)] Caching writes.
[0416/061618.721300:INFO:delta_performer.cc(386)] Applying 512 operations to partition "system"
[0416/061641.721962:INFO:delta_performer.cc(210)] Completed 33/644 operations (5%), 44695552/279316995 bytes downloaded (16%), overall progress 10%
[0416/061706.977252:INFO:delta_performer.cc(210)] Completed 78/644 operations (12%), 80199680/279316995 bytes downloaded (28%), overall progress 20%
[0416/061733.263804:INFO:delta_performer.cc(210)] Completed 124/644 operations (19%), 117325824/279316995 bytes downloaded (42%), overall progress 30%
[0416/061803.049549:INFO:delta_performer.cc(210)] Completed 181/644 operations (28%), 149962752/279316995 bytes downloaded (53%), overall progress 40%
[0416/061829.359853:INFO:delta_performer.cc(210)] Completed 232/644 operations (36%), 184123392/279316995 bytes downloaded (65%), overall progress 50%
[0416/061854.618578:INFO:delta_performer.cc(210)] Completed 278/644 operations (43%), 217874432/279316995 bytes downloaded (78%), overall progress 60%
[0416/061919.910194:INFO:delta_performer.cc(210)] Completed 331/644 operations (51%), 251396096/279316995 bytes downloaded (90%), overall progress 70%
[0416/061941.585331:INFO:delta_performer.cc(210)] Completed 400/644 operations (62%), 276365312/279316995 bytes downloaded (98%), overall progress 80%
[0416/061958.678519:INFO:delta_performer.cc(374)] Opening /dev/block/platform/c0000000.soc/c0062000.dwmmc/by-name/vendor_b partition without O_DSYNC
[0416/061958.690658:INFO:delta_performer.cc(126)] Caching writes.
[0416/061958.691263:INFO:delta_performer.cc(386)] Applying 128 operations to partition "vendor"
[0416/062001.138593:INFO:delta_performer.cc(210)] Completed 529/644 operations (82%), 279314432/279316995 bytes downloaded (99%), overall progress 90%
[0416/062015.906202:INFO:delta_performer.cc(210)] Completed 644/644 operations (100%), 279316995/279316995 bytes downloaded (100%), overall progress 100%
[0416/062015.910252:INFO:delta_performer.cc(1370)] Extracted signature data of size 264 at 279280511
[0416/062015.913158:INFO:multi_range_http_fetcher.cc(112)] terminating transfer
[0416/062015.913413:INFO:multi_range_http_fetcher.cc(172)] Received transfer terminated.
[0416/062015.913551:INFO:multi_range_http_fetcher.cc(124)] TransferEnded w/ code 200
[0416/062015.913693:INFO:multi_range_http_fetcher.cc(158)] Done w/ all transfers
[0416/062017.407693:INFO:delta_performer.cc(1549)] Verifying payload using public key: /etc/update_engine/update-payload-key.pub.pem
[0416/062017.408377:INFO:payload_verifier.cc(93)] signature blob size = 264
[0416/062017.412406:INFO:payload_verifier.cc(112)] Verified correct signature 1 out of 1 signatures.
[0416/062017.412668:INFO:delta_performer.cc(1586)] Payload hash matches value in payload.
[0416/062017.413035:INFO:download_action.cc(395)] Collections of histograms for UpdateEngine.DownloadAction.
Histogram: UpdateEngine.DownloadAction.InstallOperation::REPLACE.Duration recorded 644 samples, mean = 245.8
0    ... 
32   ------------------------------------------------------------------------O (285 = 44.3%) {0.0%}
57   -O                                                                        (4 = 0.6%) {44.3%}
101  ---O                                                                      (12 = 1.9%) {44.9%}
179  --------------------O                                                     (80 = 12.4%) {46.7%}
317  --------------------------------------------------------O                 (221 = 34.3%) {59.2%}
561  -----------O                                                              (42 = 6.5%) {93.5%}
993  ... 


[0416/062017.413527:INFO:action_processor.cc(116)] ActionProcessor: finished DownloadAction with code ErrorCode::kSuccess
[0416/062017.413741:INFO:action_processor.cc(143)] ActionProcessor: starting FilesystemVerifierAction
[0416/062017.413912:INFO:filesystem_verifier_action.cc(108)] Hashing partition 0 (boot) on device /dev/block/platform/c0000000.soc/c0062000.dwmmc/by-name/boot_b
[0416/062017.751308:INFO:filesystem_verifier_action.cc(199)] Hash of boot: 2GrkWw+rzlYb5LWpVMO0sMr4HnA9LlhIGDxeEZxmr18=
[0416/062017.760435:INFO:filesystem_verifier_action.cc(108)] Hashing partition 1 (system) on device /dev/block/platform/c0000000.soc/c0062000.dwmmc/by-name/system_b
[0416/062103.666695:INFO:filesystem_verifier_action.cc(199)] Hash of system: PRr4umq2QCUT3Mz9hseUXI+A59OBzv06wNY77T928vs=
[0416/062104.195082:INFO:filesystem_verifier_action.cc(108)] Hashing partition 2 (vendor) on device /dev/block/platform/c0000000.soc/c0062000.dwmmc/by-name/vendor_b
[0416/062115.663964:INFO:filesystem_verifier_action.cc(199)] Hash of vendor: 68N53f8cqab/NxD2aaMB2rxHYov4mU0IAksoVZbkEG4=
[0416/062115.935613:INFO:action_processor.cc(116)] ActionProcessor: finished FilesystemVerifierAction with code ErrorCode::kSuccess
[0416/062115.935973:INFO:action_processor.cc(143)] ActionProcessor: starting PostinstallRunnerAction
[0416/062115.948510:INFO:postinstall_runner_action.cc(171)] Performing postinst (system/bin/otapreopt_script at /postinstall/system/bin/otapreopt_script) installed on device /dev/block/platform/c0000000.soc/c0062000.dwmmc/by-name/system_b and mountable device /dev/block/platform/c0000000.soc/c0062000.dwmmc/by-name/system_b
[0416/062115.948859:INFO:postinstall_runner_action.cc(178)] Format file for new system/bin/otapreopt_script is: data
[0416/062325.230020:INFO:subprocess.cc(156)] Subprocess output:
Complete or error.

[0416/062325.303559:INFO:postinstall_runner_action.cc(363)] All post-install commands succeeded
[0416/062325.305153:INFO:action_processor.cc(116)] ActionProcessor: finished last action PostinstallRunnerAction with code ErrorCode::kSuccess
[0416/062325.305351:INFO:update_attempter_android.cc(431)] Processing Done.
[0416/062325.306441:INFO:update_attempter_android.cc(439)] Update successfully applied, waiting to reboot.
[0416/062325.310911:INFO:metrics_reporter_android.cc(29)] uploading 1 to histogram for metric ota_update_engine_attempt_number
[0416/062325.311163:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_attempt_payload_type
[0416/062325.311351:INFO:metrics_reporter_android.cc(29)] uploading 7 to histogram for metric ota_update_engine_attempt_duration_boottime_in_minutes
[0416/062325.311523:INFO:metrics_reporter_android.cc(29)] uploading 7 to histogram for metric ota_update_engine_attempt_duration_monotonic_in_minutes
[0416/062325.311691:INFO:metrics_reporter_android.cc(29)] uploading 266 to histogram for metric ota_update_engine_attempt_payload_size_mib
[0416/062325.311860:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_attempt_result
[0416/062325.312026:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_attempt_error_code
[0416/062325.312376:INFO:metrics_reporter_android.cc(29)] uploading 279316995 to histogram for metric ota_update_engine_attempt_current_bytes_downloaded_mib
[0416/062325.313718:INFO:metrics_reporter_android.cc(29)] uploading 1 to histogram for metric ota_update_engine_successful_update_attempt_count
[0416/062325.313961:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_successful_update_payload_type
[0416/062325.314289:INFO:metrics_reporter_android.cc(29)] uploading 266 to histogram for metric ota_update_engine_successful_update_payload_size_mib
[0416/062325.314624:INFO:metrics_reporter_android.cc(29)] uploading 266 to histogram for metric ota_update_engine_successful_update_total_bytes_downloaded_mib
[0416/062325.314811:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_successful_update_download_overhead_percentage
[0416/062325.314994:INFO:metrics_reporter_android.cc(29)] uploading 7 to histogram for metric ota_update_engine_successful_update_total_duration_in_minutes
[0416/062325.315261:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_successful_update_reboot_count
[0416/062325.317129:INFO:metrics_utils.cc(353)] Updated Marker = 1/1/1970 0:27:45 GMT
[0416/062434.136919:INFO:main.cc(197)] Chrome OS Update Engine terminating with exit code 0
```

#### Normal booting, Slot A incremental update (A->B->A)
```
[0101/000020.809060:INFO:main.cc(182)] Chrome OS Update Engine starting
[0101/000020.827144:INFO:boot_control_android.cc(64)] Loaded boot control hidl hal.
[0101/000020.827558:INFO:daemon_state_android.cc(43)] Booted in dev mode.
[0101/000020.850485:ERROR:metrics_utils.cc(378)] time_to_reboot is negative - system_updated_at: 1/1/1970 0:27:45 GMT
[1231/150255.012052:INFO:update_attempter_android.cc(246)] Using this install plan:
[1231/150255.012476:INFO:install_plan.cc(83)] InstallPlan: new_update, version: , source_slot: B, target_slot: A, url: file:///data/ota_package/payload.bin, payload: (size: 60309, metadata_size: 59781, metadata signature: , hash: CC9D4FDA5DEDBAB772456696E1F7982F581F351192572C365F9D21BB33131F1B, payload type: unknown), hash_checks_mandatory: true, powerwash_required: false, switch_slot_on_reboot: true, run_post_install: true
[1231/150255.012925:INFO:update_attempter_android.cc(547)] Marking booted slot as good.
[1231/150255.037197:INFO:metrics_utils.cc(336)] Number of Reboots during current update attempt = 0
[1231/150255.037856:INFO:metrics_utils.cc(344)] Payload Attempt Number = 1
[1231/150255.038342:INFO:metrics_utils.cc(361)] Update Timestamp Start = 1/1/1970 0:03:33 GMT
[1231/150255.038847:INFO:update_attempter_android.cc(562)] Scheduling an action processor start.
[1231/150255.039260:INFO:action_processor.cc(46)] ActionProcessor: starting InstallPlanAction
[1231/150255.039480:INFO:action_processor.cc(116)] ActionProcessor: finished InstallPlanAction with code ErrorCode::kSuccess
[1231/150255.039735:INFO:action_processor.cc(143)] ActionProcessor: starting DownloadAction
[1231/150255.039927:INFO:install_plan.cc(83)] InstallPlan: new_update, version: , source_slot: B, target_slot: A, url: file:///data/ota_package/payload.bin, payload: (size: 60309, metadata_size: 59781, metadata signature: , hash: CC9D4FDA5DEDBAB772456696E1F7982F581F351192572C365F9D21BB33131F1B, payload type: unknown), hash_checks_mandatory: true, powerwash_required: false, switch_slot_on_reboot: true, run_post_install: true
[1231/150255.040099:INFO:download_action.cc(200)] Marking new slot as unbootable
[1231/150255.061345:INFO:multi_range_http_fetcher.cc(45)] starting first transfer
[1231/150255.061717:INFO:multi_range_http_fetcher.cc(74)] starting transfer of range 0+60309
[1231/150255.065262:INFO:delta_performer.cc(210)] Completed 0/? operations, 16384/60309 bytes downloaded (27%), overall progress 13%
[1231/150255.069055:INFO:delta_performer.cc(210)] Completed 0/? operations, 32768/60309 bytes downloaded (54%), overall progress 27%
[1231/150255.072344:INFO:delta_performer.cc(210)] Completed 0/? operations, 49152/60309 bytes downloaded (81%), overall progress 40%
[1231/150255.075923:INFO:delta_performer.cc(210)] Completed 0/? operations, 60309/60309 bytes downloaded (100%), overall progress 50%
[1231/150255.076366:INFO:delta_performer.cc(470)] Manifest size in payload matches expected value from Omaha
[1231/150255.076559:INFO:payload_metadata.cc(182)] Verifying metadata hash signature using public key: /etc/update_engine/update-payload-key.pub.pem
[1231/150255.078120:INFO:payload_verifier.cc(93)] signature blob size = 264
[1231/150255.103726:INFO:payload_verifier.cc(112)] Verified correct signature 1 out of 1 signatures.
[1231/150255.104013:INFO:payload_metadata.cc(224)] Metadata hash signature matches value in Omaha response.
[1231/150255.120300:INFO:delta_performer.cc(1405)] Detected a 'delta' payload.
[1231/150255.157493:INFO:delta_performer.cc(400)] PartitionInfo old boot sha256: 2GrkWw+rzlYb5LWpVMO0sMr4HnA9LlhIGDxeEZxmr18= size: 7585792
[1231/150255.157871:INFO:delta_performer.cc(400)] PartitionInfo new boot sha256: 2GrkWw+rzlYb5LWpVMO0sMr4HnA9LlhIGDxeEZxmr18= size: 7585792
[1231/150255.158034:INFO:delta_performer.cc(400)] PartitionInfo old system sha256: PRr4umq2QCUT3Mz9hseUXI+A59OBzv06wNY77T928vs= size: 1073741824
[1231/150255.158179:INFO:delta_performer.cc(400)] PartitionInfo new system sha256: PRr4umq2QCUT3Mz9hseUXI+A59OBzv06wNY77T928vs= size: 1073741824
[1231/150255.158324:INFO:delta_performer.cc(400)] PartitionInfo old vendor sha256: 68N53f8cqab/NxD2aaMB2rxHYov4mU0IAksoVZbkEG4= size: 268435456
[1231/150255.158541:INFO:delta_performer.cc(400)] PartitionInfo new vendor sha256: 68N53f8cqab/NxD2aaMB2rxHYov4mU0IAksoVZbkEG4= size: 268435456
[1231/150255.160775:INFO:delta_performer.cc(374)] Opening /dev/block/platform/c0000000.soc/c0062000.dwmmc/by-name/boot_a partition without O_DSYNC
[1231/150255.172070:INFO:delta_performer.cc(126)] Caching writes.
[1231/150255.172553:INFO:delta_performer.cc(386)] Applying 4 operations to partition "boot"
[1231/150255.219235:INFO:delta_performer.cc(601)] Starting to apply update payload operations
[1231/150256.186233:INFO:delta_performer.cc(374)] Opening /dev/block/platform/c0000000.soc/c0062000.dwmmc/by-name/system_a partition without O_DSYNC
[1231/150256.197472:INFO:delta_performer.cc(126)] Caching writes.
[1231/150256.197899:INFO:delta_performer.cc(386)] Applying 523 operations to partition "system"
[1231/150256.232517:ERROR:delta_performer.cc(990)] The hash of the source data on disk for this operation doesn't match the expected value. This could mean that the delta update payload was targeted for another version, or that the source partition was modified after it was installed, for example, by mounting a filesystem.
[1231/150256.232882:ERROR:delta_performer.cc(995)] Expected:   sha256|hex = 11BA9EB54A2C8F2ACFA14E1B22736D07BBA7F1276BD7B3F7170F89649F781CE2
[1231/150256.233020:ERROR:delta_performer.cc(998)] Calculated: sha256|hex = DB761747018BEFF14CFEF5A33308E6C891B518C4D257839D80A8E751B2310490
[1231/150256.233163:ERROR:delta_performer.cc(1009)] Operation source (offset:size) in blocks: 0:66,65:1,65:1,65:1,65:1,70:1
[1231/150256.233339:WARNING:mount_history.cc(66)] Device was remounted R/W 1 times. Last remount happened on 1970-01-01 00:00:38.000 UTC.
[1231/150256.233492:ERROR:delta_performer.cc(1038)] ValidateSourceHash(source_hash, operation, source_fd_, error) failed.
[1231/150256.233663:ERROR:delta_performer.cc(298)] Failed to perform SOURCE_COPY operation 4, which is the operation 0 in partition "system"
[1231/150256.233807:ERROR:download_action.cc(337)] Error ErrorCode::kDownloadStateInitializationError (20) in DeltaPerformer's Write method when processing the received payload -- Terminating processing
[1231/150256.252282:INFO:multi_range_http_fetcher.cc(172)] Received transfer terminated.
[1231/150256.252529:INFO:multi_range_http_fetcher.cc(124)] TransferEnded w/ code 200
[1231/150256.252709:INFO:multi_range_http_fetcher.cc(126)] Terminating.
[1231/150256.252841:INFO:action_processor.cc(116)] ActionProcessor: finished DownloadAction with code ErrorCode::kDownloadStateInitializationError
[1231/150256.252965:INFO:action_processor.cc(121)] ActionProcessor: Aborting processing due to failure.
[1231/150256.253083:INFO:update_attempter_android.cc(431)] Processing Done.
[1231/150256.256601:INFO:update_attempter_android.cc(450)] Resetting update progress.
[1231/150256.266932:INFO:metrics_reporter_android.cc(29)] uploading 1 to histogram for metric ota_update_engine_attempt_number
[1231/150256.267207:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_attempt_payload_type
[1231/150256.267401:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_attempt_duration_boottime_in_minutes
[1231/150256.267584:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_attempt_duration_monotonic_in_minutes
[1231/150256.267896:INFO:metrics_reporter_android.cc(29)] uploading 0 to histogram for metric ota_update_engine_attempt_payload_size_mib
[1231/150256.268068:INFO:metrics_reporter_android.cc(29)] uploading 1 to histogram for metric ota_update_engine_attempt_result
[1231/150256.268237:INFO:metrics_reporter_android.cc(29)] uploading 20 to histogram for metric ota_update_engine_attempt_error_code
[1231/150256.268568:INFO:metrics_reporter_android.cc(29)] uploading 60309 to histogram for metric ota_update_engine_attempt_current_bytes_downloaded_mib
```