duchamp: Kapsamlı cihaz iyileştirmeleri ve hata düzeltmeleri

Renk modları:
- persist.sys.sf.native_mode: 257 -> 0 (Xiaomi PQ bypass kaldırıldı)
- ro.vendor.mtk_pq_color_mode: 3 -> 0 (Android renk modu kontrolü geri alındı)
- vendor.displayfeature.entry.enable: true -> false
- ColorService.kt: fromId() fonksiyonuna androidToXiaomiMap eklendi

Yenileme hızı:
- config_defaultRefreshRate ve config_defaultPeakRefreshRate: 120Hz olarak ayarlandı
- Keyguard yenileme hızı: 60 -> 120Hz
- ro.surface_flinger.enable_frame_rate_override: false -> true
- ro.surface_flinger.game_default_frame_rate_override: 60 -> 0
- debug.sf.disable_backpressure=1 ve debug.sf.set_binder_thread_rt=1 eklendi
- ro.surface_flinger.set_touch_timer_ms=1100 eklendi

AOD ve UDFPS:
- persist.vendor.sys.fp.fod.aod.enable=1 eklendi
- persist.vendor.sys.fp.fod.screen_off.enable=1 eklendi
- ro.vendor.display.aod_monitor_default_fps: 30 -> 60
- config_show_udfps_affordance_on_aod=true eklendi
- AOD ekranında saat ve bildirim göstergeleri gizlendi
- config_dozeAlwaysOnEnabled=false, doze_pulse_duration_visible=10000ms
- doze_suspend_display_state_supported=true eklendi
- config_udfpsLongPressSensorTimeout: 400 -> 200ms
- persist.vendor.sys.fp.fod.dimlayer.hbm: 1 -> 0
- persist.vendor.sys.fp.expolevel: 0x88 -> 0x22

UDFPS handler:
- onFingerDown: setFingerDown önce çağrılıyor, LHBM 1000nit -> 110nit
- onFingerDown: COMMAND_NIT eklendi
- onFingerUp: LHBM kapatma en başa alındı, COMMAND_NIT OFF eklendi
- FOD event thread: race condition düzeltildi, sadece UI ready durumunda NIT gönderiliyor
- onAcquired: AcquiredInfo::VENDOR case eklendi

SELinux:
- hal_fingerprint_default.te: rw_file_perms -> { ioctl read write open }
- hal_fingerprint_default.te: allowxperm ile LHBM ve event ioctl'leri eklendi
- genfs_contexts: fod_ui ve hbm sysfs node'ları vendor_fingerprint_sysfs olarak etiketlendi
- property_contexts: persist.vendor.fingerprint. prefix eklendi
- hal_lineage_health_default.te oluşturuldu
- property.te: vendor_thermal_prop tipi eklendi
- vendor_init.te: set_prop(vendor_init, vendor_thermal_prop) eklendi
- device_xiaomi_duchamp thermal servis: mediatek -> pixel

init.fingerprint.rc:
- /dev/mi_display/disp_feature için chown/chmod eklendi

CPU/GPU/Güç:
- powerhint.json: LAUNCH Big cluster min frekans 2.2 -> 2.6GHz
- powerhint.json: LAUNCH GPU power level 62 -> 20
- powerhint.json: EXPENSIVE_RENDERING Big cluster 2.8GHz eklendi
- vendor.schedutil.rate_limit_us=500 eklendi
- BoardConfig.mk: TARGET_CPU_VARIANT cortex-a55 -> cortex-a76
- thermal_info_config.json: skin temp eşikleri gevşetildi
- pm.sleep_mode=1 eklendi
- init.mt6897.power.rc: cpuidle enable=1 ve mcdi state=1 eklendi

LMK:
- ro.lmk.kill_timeout_ms: 50 -> 100
- ro.lmk.thrashing_limit, psi_partial_stall_ms, swap_free_low_percentage eklendi
- ro.lmk.kill_heaviest_task=true eklendi

IMS/VoLTE/VoNR:
- persist.dbg.volte_avail_ovr=1, vt_avail_ovr=1, wfc_avail_ovr=1 eklendi
- persist.vendor.ims_support=1, vilte_support=1, volte_support=1 eklendi
- ro.vendor.radio.5g=3 eklendi
- persist.vendor.mtk.vonr.enable: 0 -> 1

Şarj animasyonu:
- init.mt6897.rc on charger: ro.charger.enable_suspend=false eklendi
- init.mt6897.rc on charger: sys.charger.disable_init_blank=true eklendi
- ro.charger.enable_suspend=false ve ro.enable_boot_charger_mode=1 eklendi

Ağ ve izinler:
- manifest.xml: android.hardware.gnss AIDL v3 eklendi
- WiFi config dosyaları oluşturuldu
- TelephonyResOverlayDuchamp overlay eklendi
- 7 adet MediaTek permission XML eklendi
- configs/rsc GL ve IN: default_network 33 -> 32
- bluetooth.profile.sap.server.enabled=false eklendi
- android.hardware.opengles.aep.xml eklendi
- android.hardware.nfc.any.xml eklendi
- android.hardware.telephony.data.xml ve android.hardware.location.xml eklendi
- persist.sys.gps.lpp=3 eklendi

Ses:
- audio_policy_volumes.xml: speaker +6dB boost
- default_volume_tables.xml oluşturuldu
- audio_spatial_db_reduce.xml oluşturuldu
- ro.config.media_vol_steps: 15 -> 25
- ro.config.vc_call_vol_steps: 11 -> 15
- persist.bluetooth.a2dp_offload.cap=sbc-aac-ldac-aptx-aptxhd

Batarya:
- config_screenBrightnessNits ve config_screenBrightnessBacklight array'leri eklendi
- init.mt6897.rc: uid_io ve uid_time_in_state için chown/chmod eklendi
- power_profile.xml eski format array'leri temizlendi
- persist.vendor.battery.health=true ve health.optimise=true eklendi
