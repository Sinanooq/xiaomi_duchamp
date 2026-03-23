## 📅 22 Mart 2025

Bugün biraz vakit bulup cihazla ilgili birkaç şeyi düzeltmeye oturdum. Bir süredir kafama takılan renk modu sorunundan başladım.

**Renk modları artık düzgün çalışıyor.** `persist.sys.sf.native_mode=257` Xiaomi'nin kendi PQ modunu zorla aktif ediyordu ve Android'in Natural/P3/sRGB seçeneklerini tamamen bypass ediyordu. `ro.vendor.mtk_pq_color_mode=3` de aynı şekilde ColorService'in kendi mapping'ini kullanmasına yol açıyordu. İkisini de sıfırladım. `vendor.displayfeature.entry.enable` de `true` olarak kalmıştı, onu da kapattım. `ColorService.kt` tarafında `fromId()` fonksiyonuna `androidToXiaomiMap` eklemek gerekiyordu, onu da hallettim.

**Yenileme hızı sorununu da bu arada çözdüm.** `config_defaultRefreshRate` ve `config_defaultPeakRefreshRate` 120'ye çekildi. Keyguard tarafında 60'ta kalan değerler de 120'ye alındı. `ro.surface_flinger.enable_frame_rate_override` `false`'tan `true`'ya çevrildi. `ro.surface_flinger.game_default_frame_rate_override` ise 60'tan 0'a alındı — oyun modunun yenileme hızını zorla kısıtlamasının önüne geçmek için. SurfaceFlinger tarafında `debug.sf.disable_backpressure=1` ve `debug.sf.set_binder_thread_rt=1` de eklendi, `set_touch_timer_ms=1100` ile dokunuş sonrası idle geçişi de düzenlendi.

**AOD parmak izi meselesi.** `persist.vendor.sys.fp.fod.aod.enable` ve `persist.vendor.sys.fp.fod.screen_off.enable` prop'ları eksikti, ekledim. AOD ekranında saat ve bildirim göstergelerini gizleyip sadece UDFPS animasyonunu bıraktım — zaten AOD'dan beklenen bu. `config_show_udfps_affordance_on_aod=true` da eklendi. AOD monitor FPS'i 30'dan 60'a çıkardım, 30'da UDFPS touch event'leri kaçıyordu.

**AOD 10 saniye sonra kapanacak şekilde ayarlandı.** `config_dozeAlwaysOnEnabled=false` yapıldı, `doze_pulse_duration_visible=10000` ile 10 saniyelik süre tanımlandı. `doze_suspend_display_state_supported=true` da eklendi ki ekran gerçekten suspend olabilsin.

---

## 📅 23 Mart 2025

Dün bıraktığım yerden devam ettim, bu sefer daha teknik konulara girdim.

**İşlemci ve GPU optimizasyonları.** `powerhint.json` baştan düzenlendi. LAUNCH hint'inde Big cluster minimum frekansı 2.2GHz'den 2.6GHz'e çıkarıldı. GPU power level LAUNCH sırasında 62'den 20'ye indirildi — bu GPU'nun daha yüksek frekansta çalışması anlamına geliyor. EXPENSIVE_RENDERING hint'ine Big cluster için 2.8GHz minimum frekans eklendi. `vendor.schedutil.rate_limit_us=500` ile CPU scheduler'ın frekans kararlarını çok daha hızlı vermesi sağlandı. `device_xiaomi_duchamp/BoardConfig.mk`'da `TARGET_CPU_VARIANT` yanlışlıkla `cortex-a55` olarak kalmıştı — MT6897 için doğru değer olan `cortex-a76` olarak düzeltildi, bu userspace kodunun doğru optimizasyon flagleriyle derlenmesi açısından kritikti.

**Termal kısıtlamalar gevşetildi.** `thermal_info_config.json` üzerindeki skin temp eşikleri optimize edildi, cihaz gereksiz yere throttle yapıyordu. `pm.sleep_mode=1` ile doze sırasında güç tasarrufu iyileştirildi. `init.mt6897.power.rc`'ye `cpuidle enable=1` ve `mcdi state=1` eklenerek gerçek deep sleep desteği sağlandı.

**LMK (Low Memory Killer) iyileştirmeleri.** `ro.lmk.kill_timeout_ms` 50'den 100'e çıkarıldı. `thrashing_limit`, `psi_partial_stall_ms`, `swap_free_low_percentage` gibi parametreler eklenerek bellek baskısı altında daha akıllı davranış sağlandı. `ro.lmk.kill_heaviest_task=true` ile en ağır process önce öldürülecek.

**IMS/VoLTE/VoNR düzeltmeleri.** `persist.dbg.volte_avail_ovr=1`, `persist.dbg.vt_avail_ovr=1`, `persist.dbg.wfc_avail_ovr=1` eklendi. `persist.vendor.ims_support=1`, `persist.vendor.vilte_support=1`, `persist.vendor.volte_support=1` eksikti, eklendi. 5G için `ro.vendor.radio.5g=3` eklendi.

**Şarj animasyonu düzeltmesi.** Kapalıyken şarj takıldığında animasyon bazen çıkmıyordu. `on charger` bloğuna `setprop ro.charger.enable_suspend false` ve `setprop sys.charger.disable_init_blank true` eklendi. `ro.charger.enable_suspend=false` ve `ro.enable_boot_charger_mode=1` vendor.prop'a da eklendi.

**Ağ ve izin eksiklikleri.** WiFi config dosyaları eksikti, oluşturuldu. `TelephonyResOverlayDuchamp` overlay'i yoktu, eklendi. 7 adet MediaTek permission XML eksik olduğunu fark edip ekledim. `configs/rsc` GL ve IN prop'larında `default_network=33` (C2K/CDMA içeriyor, cihaz desteklemiyor) yanlışlıkla girilmişti, `32`'ye düzeltildi.

**Harita ve konum servisleri.** `manifest.xml`'de GNSS HAL tanımı yoktu — `android.hardware.gnss` AIDL v3 eklendi. `android.hardware.telephony.data.xml` ve `android.hardware.location.xml` de eksikti, her ikisi de `device.mk`'ya eklendi. GPS için `persist.sys.gps.lpp=3` eklendi.

**Ses iyileştirmeleri.** Speaker için `+6dB` boost içeren `audio_policy_volumes.xml` ve `default_volume_tables.xml` oluşturuldu. `audio_spatial_db_reduce.xml` da eklendi. Ses adımı prop'ları düzenlendi — medya için 25 adım (varsayılan 15), telefon için 15 adım (varsayılan 11). Bluetooth A2DP codec listesi genişletildi: `sbc-aac-ldac-aptx-aptxhd`.

**Batarya istatistikleri.** `config_screenBrightnessNits` ve `config_screenBrightnessBacklight` array'leri eklendi. `init.mt6897.rc`'ye uid_io ve uid_time_in_state için gerekli `chown`/`chmod` satırları eklendi ki sistem batarya kullanımını doğru raporlayabilsin. `power_profile.xml` eski format array'leri temizlendi. `persist.vendor.battery.health=true` ve `persist.vendor.battery.health.optimise=true` eklendi.

**Ekran yapılandırması.** `display_id_4627039422300187648.xml`'e `refreshRateConfigs` ve `hasArrSupport=true` eklendi. Parmak izi sensör konumu prop'u da eklendi.

**Wakelock ve deep sleep.** GPS daemon (`mnld`) ile ilgili yanlış bir müdahale geri alındı — servis disabled yapılmıştı, bu GPS'i tamamen kırıyordu. `init.mt6897.power.rc`'ye `cpuidle enable=1` ve `mcdi state=1` eklenerek gerçek deep sleep desteği sağlandı.

**Google servisleri ve NFC.** `android.hardware.nfc.any.xml` eksikti, eklendi — Android 14+ üzerinde Google Wallet bunun olmadığı durumlarda çalışmıyor. `bluetooth.profile.sap.server.enabled` güvenlik riski oluşturduğu için `false` yapıldı. `android.hardware.opengles.aep.xml` eklendi.

**SELinux düzeltmeleri.** `hal_lineage_health_default.te` `xiaomi_duchamp` ağacında yoktu, oluşturuldu. `vendor_thermal_prop` tipi her iki ağacın `property.te` dosyasında tanımsız kalmıştı, eklendi. `vendor_init` bu prop'u set edemiyordu, `vendor_init.te` güncellendi. Thermal servis `device_xiaomi_duchamp`'ta `mediatek` olarak tanımlanmıştı ama `thermal_info_config.json` Pixel thermal servis formatında olduğundan `pixel` olarak düzeltildi.
