# Daria Hormoz (MT6897) - Device Tree Handoff

## منبع
پورت‌شده از `device_xiaomi_duchamp` (mt6897-devs)، با تمام مقادیر و blob ها
از OTA رسمی گوشی استخراج شده:
`DariaOS-6.0-20251031-RELEASE-hormoz-V6.7.2.1.BOND2-user-signed.zip`

## اطلاعات دستگاه (تأییدشده از OTA)
- Brand: Daria | Device: hormoz | Model: DM-B70104
- SoC: MT6897 (Dimensity 8350)
- Fingerprint: Daria/hormoz/hormoz:15/AP3A.241105.008/V6.7.2.1.BOND2:user/release-keys
- Android 15, Security patch: 2025-09-05
- Super partition size: 9661579264 bytes (از payload.bin استخراج شده)
- Dynamic partition group: mtk_dynamic_partitions

## سخت‌افزار شناسایی‌شده
- فینگرپرینت زیر صفحه: Goodix GW9598 (brl-d family)
- NFC: چیپ سامسونگ (sec-nfc)، نه NXP
- GPU: Mali با درایور مدیاتک r44
- صدا: aw882xx / rt5512 / tfa98xx (کدوم دقیقاً فعاله، نیاز به تست روی دستگاه)
- WiFi/BT: مدیاتک نسل ۴ (gen4m)

## ریپوی کرنل (device_daria_hormoz-kernel)
**این کرنل prebuilt است، نه کامپایل‌شده از سورس.**
شامل:
- `Image.lz4` — مستقیم از boot.img خودِ گوشی
- `dtb/mt6897.dtb` — device tree واقعی دستگاه (از vendor_boot.img استخراج و
  round-trip validate شده با mkbootimg/unpack_bootimg)
- `modules/` — ۴۴۶ ماژول کرنل واقعی (از vendor_boot ramdisk +
  vendor_dlkm.img + system_dlkm.img)
- `modules/*.modules.load*` — لیست‌های لود دستی ساخته‌شده بر اساس
  محل واقعی هر ماژول

⚠️ این کرنل از سورس کامپایل نشده. اگر نیاز به تغییر کرنل (پچ، دیباگ،
فیچر جدید) باشه، باید سورس واقعی از `android_kernel_6.1` (GKI مشترک) +
`android_vendor_mediatek_kernel_modules` (drivers) با DTS این پروژه
کامپایل بشه؛ این کار در این مرحله انجام نشده.

## محدودیت‌های شناخته‌شده / کارهای باقی‌مانده
1. **power/power-mode.cpp**: توابع `DOUBLE_TAP_TO_WAKE` و `DISPLAY_INACTIVE`
   موقتاً stub شدن (غیرفعال) چون مسیر device node واقعی تاچ‌پنل hormoz
   (معادل `/dev/xiaomi-touch` در duchamp) هنوز شناسایی نشده. نیاز به بررسی
   روی دستگاه واقعی.
2. **udfps/UdfpsHandler.cpp**: نسخه‌ی ساده‌شده (stub) جایگزین شده. فینگرپرینت
   پایه کار می‌کنه ولی بدون بوست روشنایی هنگام لمس. نیاز به پیاده‌سازی
   کامل بر اساس رفتار واقعی Goodix GW9598 روی این دستگاه.
3. **دوربین**: چند سنسور کاندید در بلاب‌ها پیدا شد (OV64B40, OV08D10Ultra,
   OV16A1QFront, SC202PCSMacro از duchamp حذف شدن؛ سنسورهای واقعی hormoz
   باید در پارتیشن‌های vendor بررسی بشه — این تسک انجام نشده).
4. **تست عملی build**: این پروژه در یک AOSP source tree کامل تست نشده.
   اولین build احتمالاً به رفع چند اشکال کوچک نیاز داره.

## ساختار ریپوها برای build
device/daria/hormoz          <- device_daria_hormoz
device/daria/hormoz-kernel   <- device_daria_hormoz-kernel
vendor/daria/hormoz          <- باید با اجرای extract-files.py روی OTA ساخته بشه
                                 (proprietary-files.txt/extract-files.py آماده و تست‌شده است)
