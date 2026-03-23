## 📅 22 Mart 2025

Bugün biraz vakit bulup cihazla ilgili birkaç şeyi düzeltmeye oturdum. Bir süredir kafama takılan renk modu sorunundan başladım.

**Renk modları artık düzgün çalışıyor.** Daha önce `persist.sys.sf.native_mode` ve `ro.vendor.mtk_pq_color_mode` yanlış değerlere ayarlıydı, bu yüzden sistem renk modları hiçbir işe yaramıyordu. `vendor.displayfeature.entry.enable` de `true` olarak kalmıştı, onu da kapattım. `ColorService.kt` tarafında da `fromId()` fonksiyonuna `androidToXiaomiMap` eklemek gerekiyordu, onu da hallettim.

**Yenileme hızı sorununu da bu arada çözdüm.** `config_defaultRefreshRate` ve `config_defaultPeakRefreshRate` 120'ye çekildi. Keyguard tarafında 60'ta kalan değerler de 120'ye alındı. `ro.surface_flinger.game_default_frame_rate_override=0` ekleyerek oyun modunun yenileme hızını zorla kısıtlamasının önüne geçtim.

**AOD parmak izi meselesi.** `persist.vendor.sys.fp.fod.aod.enable` ve `persist.vendor.sys.fp.fod.screen_off.enable` prop'ları eksikti, ekledim. AOD ekranında saat ve bildirim göstergelerini gizleyip sadece UDFPS animasyonunu bıraktım — zaten AOD'dan beklenen bu. `config_show_udfps_affordance_on_aod=true` da eklendi.

**AOD 10 saniye sonra kapanacak şekilde ayarlandı.** `config_dozeAlwaysOnEnabled=false` yapıldı, `doze_pulse_duration_visible=10000` ile 10 saniyelik süre tanımlandı. `doze_suspend_display_state_supported=true` da eklendi ki ekran gerçekten suspend olabilsin.

---

## 📅 23 Mart 2025

Dün bıraktığım yerden devam ettim, bu sefer daha teknik konulara girdim.

**Termal ve güç yönetimi.** `thermal_info_config.json` üzerindeki skin temp eşiklerini biraz gevşettim, cihaz gereksiz yere throttle yapıyordu. `powerhint.json` de optimize edildi. `vendor.schedutil.rate_limit_us=500` ve `pm.sleep_mode=1` eklenerek doze sırasında güç tasarrufu iyileştirildi.

**Şarj animasyonu düzeltmesi.** Kapalıyken şarj takıldığında animasyon bazen çıkmıyordu. `on charger` bloğuna `ro.charger.enable_suspend=false` ve `ro.enable_boot_charger_mode=1` eklendi.

**Ağ ve izin eksiklikleri.** WiFi config dosyaları eksikti, oluşturuldu. `TelephonyResOverlayDuchamp` overlay'i yoktu, eklendi. 7 adet MediaTek permission XML de bu arada eksik olduğunu fark edip ekledim.

**Harita ve konum servisleri.** `manifest.xml`'de GNSS HAL tanımı yoktu — `android.hardware.gnss` AIDL v3 eklendi. `android.hardware.telephony.data.xml` ve `android.hardware.location.xml` de eksikti, her ikisi de `device.mk`'ya eklendi.

**Ses iyileştirmeleri.** Speaker için `+6dB` boost içeren `audio_policy_volumes.xml` ve `default_volume_tables.xml` oluşturuldu. Ses adımı prop'ları da her iki ağaca eklendi — medya için 25 adım, telefon için 15 adım.

**Batarya istatistikleri.** `config_screenBrightnessNits` ve `config_screenBrightnessBacklight` array'leri eklendi. `init.mt6897.rc`'ye uid_io ve uid_time_in_state için gerekli `chown`/`chmod` satırları eklendi ki sistem batarya kullanımını doğru raporlayabilsin.

**Wakelock ve deep sleep.** GPS daemon (`mnld`) ile ilgili yanlış bir müdahale geri alındı — servis disabled yapılmıştı, bu GPS'i tamamen kırıyordu. `init.mt6897.power.rc`'ye `cpuidle enable=1` ve `mcdi state=1` eklenerek gerçek deep sleep desteği sağlandı.

**Google servisleri ve NFC.** `android.hardware.nfc.any.xml` eksikti, eklendi — Android 14+ üzerinde Google Wallet bunun olmadığı durumlarda çalışmıyor. `bluetooth.profile.sap.server.enabled` güvenlik riski oluşturduğu için `false` yapıldı.

**SELinux düzeltmeleri.** `hal_lineage_health_default.te` `xiaomi_duchamp` ağacında yoktu, oluşturuldu. `vendor_thermal_prop` tipi her iki ağacın `property.te` dosyasında tanımsız kalmıştı, eklendi. `vendor_init` bu prop'u set edemiyordu, `vendor_init.te` güncellendi. `device_xiaomi_duchamp/BoardConfig.mk`'da `TARGET_CPU_VARIANT` yanlışlıkla `cortex-a55` olarak kalmıştı, `cortex-a76` olarak düzeltildi. Thermal servis de `device_xiaomi_duchamp`'ta `mediatek` olarak tanımlanmıştı ama `thermal_info_config.json` Pixel thermal servis formatında olduğundan `pixel` olarak düzeltildi.
