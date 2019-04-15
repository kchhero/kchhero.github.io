---
title: 'Android OTA A/B update'
layout: post
tags:
  - Android
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

### step 5

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

