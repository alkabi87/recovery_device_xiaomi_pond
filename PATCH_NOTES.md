# POCO C75 / Redmi 14C pond/lake TWRP patch

This patch is based only on the uploaded device tree and the supplied TWRP logs.

## Changes

### BoardConfig.mk
- Added `BOARD_INCLUDE_RECOVERY_RAMDISK_IN_VENDOR_BOOT := true`.
  - The built vendor_boot v4 previously contained a single PLATFORM ramdisk fragment even though it contained TWRP recovery resources.
  - AOSP documents `BOARD_INCLUDE_RECOVERY_RAMDISK_IN_VENDOR_BOOT := true` for building recovery resources as a dedicated vendor_boot recovery ramdisk fragment, which is tagged `VENDOR_RAMDISK_TYPE_RECOVERY`.
- Changed `BOARD_MOVE_GSI_AVB_KEYS_TO_VENDOR_BOOT` from empty to `true`, matching the AOSP configuration for devices where recovery resources are moved to vendor_boot.

## Deliberately NOT changed

- `recovery.fstab` metadata mapping was not changed because the uploaded source already maps `/metadata` to `/dev/block/by-name/metadata`, which matches the partition name reported by the supplied fastboot output.
- The custom USB init was not replaced blindly. The supplied logs show repeated `musb-hdrc` UDC `-19` errors, but ADB eventually comes up; changing the whole USB implementation without a matching kernel/vendor configuration would be speculative.
- Crypto/keymaster configuration was not disabled. The supplied logs show a recovery SIGSEGV after ~340 seconds, but the available logs do not contain a crash backtrace because the crash_dump helper is missing. Disabling crypto would be an unsafe guess and could break /data decryption.

## Next build

Build this tree again and inspect the resulting vendor_boot v4 ramdisk table. The recovery fragment should be named `recovery` and have ramdisk type `RECOVERY` rather than `PLATFORM` according to AOSP's vendor_boot v4 model.

This patch is a source-level correction, not a guarantee that the resulting image will boot on every firmware revision.
