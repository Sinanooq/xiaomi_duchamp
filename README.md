# Duchamp Değişiklik Günlüğü / Changelog

---

## 23 Mart 2025 / March 23, 2025

### Ekran ve Renk Modları / Display & Color Modes

Xiaomi'nin kendi PQ (Picture Quality) katmanı devre dışı bırakıldı. Artık renk modları (Doğal, Display P3, sRGB) Android'in kendi renk yöneticisi tarafından kontrol ediliyor. Ayarlar > Ekran > Renkler'den değiştirilebilir.

Xiaomi's proprietary PQ (Picture Quality) layer has been disabled. Color modes (Natural, Display P3, sRGB) are now managed by Android's native color manager. Adjustable via Settings > Display > Colors.

ColorService'in Xiaomi renk modlarını Android renk modlarının üzerine yazması engellendi.

ColorService no longer overrides Android color modes with Xiaomi-specific PQ mappings.

---

### Yenileme Hızı / Refresh Rate

Varsayılan ve maksimum yenileme hızı 120Hz olarak sabitlendi. Daha önce bazı durumlarda 60Hz'e düşüyordu.

Default and peak refresh rate locked to 120Hz. Previously it would fall back to 60Hz in certain scenarios.

Kilit ekranında yenileme hızı 60Hz'den 120Hz'e çıkarıldı. Parmak izi sensörü artık kilit ekranında çok daha hızlı tepki veriyor.

Keyguard refresh rate raised from 60Hz to 120Hz. The fingerprint sensor now responds significantly faster on the lock screen.

Oyun modu için gereksiz kare hızı kısıtlaması kaldırıldı. SurfaceFlinger bağlayıcı thread'i gerçek zamanlı önceliğe alındı, dokunuş gecikmesi azaldı.

Unnecessary frame rate cap for game mode removed. SurfaceFlinger binder thread promoted to real-time priority, reducing touch latency.

`debug.sf.auto_latch_unsignaled=2` eklendi — fence beklenmeden frame latch ediliyor, jank azalıyor.

Added `debug.sf.auto_latch_unsignaled=2` — frames are latched without waiting for fences, reducing jank.

Dokunuş sonrası idle timer 1100ms'den 200ms'ye düşürüldü, ekran daha hızlı tepki veriyor.

Touch timer reduced from 1100ms to 200ms, display responds faster after touch input.

`ro.surface_flinger.uclamp.min` 120'den 0'a düşürüldü — boşta gereksiz CPU boost yok.

`ro.surface_flinger.uclamp.min` lowered from 120 to 0 — no unnecessary CPU boost when idle.

---

### AOD ve Ekran Altı Parmak İzi / AOD & Under-Display Fingerprint (UDFPS)

Ekran kapalıyken parmak izi okuma aktif hale getirildi. Artık ekrana dokunmadan parmak izini okutarak ekranı açabilirsiniz.

Fingerprint unlock while screen is off is now enabled. You can wake and unlock the device by placing your finger on the sensor without pressing any button.

AOD modunda parmak izi simgesi görünür hale getirildi. AOD ekranında saat ve bildirim göstergeleri gizlendi, sadece parmak izi simgesi gösteriliyor.

Fingerprint affordance is now visible on AOD. Clock and notification indicators are hidden on AOD for a cleaner look — only the fingerprint icon is shown.

AOD monitör yenileme hızı 30Hz'den 60Hz'e çıkarıldı. Düşük hız parmak izi dokunuşlarının kaçırılmasına neden oluyordu.

AOD monitor refresh rate raised from 30Hz to 60Hz. The lower rate was causing missed fingerprint touch events.

Parmak izi basma süresi 400ms'den 200ms'ye düşürüldü, daha hızlı açılıyor.

Long press sensor timeout reduced from 400ms to 200ms for faster unlock.

Enrollment sırasında ekran parlaklığı azaltıldı, göz yorgunluğu ve yanlış okuma ihtimali düştü.

Display brightness during enrollment reduced, lowering eye strain and false read probability.

Parmak izi altındaki dim katmanı HBM yerine framework tarafından yönetiliyor, daha tutarlı davranış.

The dim layer under the fingerprint sensor is now managed by the framework instead of HBM, resulting in more consistent behavior.

---

### UDFPS Handler İyileştirmeleri / UDFPS Handler Improvements

Parmak izi okuma sırası yeniden düzenlendi: önce sensör aktif ediliyor, sonra ekran parlaklığı ayarlanıyor. Daha önce ters sıraydı ve zaman zaman okuma başarısız oluyordu.

Fingerprint read sequence reordered: sensor is activated first, then display brightness is adjusted. The previous order was reversed and occasionally caused failed reads.

LHBM (Local High Brightness Mode) parlaklığı 1000 nit'ten 110 nit'e düşürüldü. Karanlıkta parmak izi okutmak artık gözleri yakmıyor.

LHBM (Local High Brightness Mode) brightness reduced from 1000 nits to 110 nits. Unlocking in the dark no longer causes eye discomfort.

Parmak kaldırıldığında ekran parlaklığı hemen normale dönüyor, gecikme yok. Çoklu dokunuş yarış koşulu (race condition) düzeltildi, nadiren yaşanan donma sorunu giderildi.

Display brightness returns to normal immediately on finger lift with no delay. A race condition on multi-touch was fixed, resolving rare freeze issues.

---

### IMS, VoLTE ve VoNR / IMS, VoLTE & VoNR

VoLTE, VoWiFi ve görüntülü arama zorla aktif hale getirildi. Operatör kısıtlamalarından bağımsız çalışıyor.

VoLTE, VoWiFi and video calling forcefully enabled, independent of carrier provisioning.

5G üzerinden sesli arama (VoNR) aktif edildi. Daha önce kapalıydı. 5G NSA+SA desteği açıkça tanımlandı.

Voice over NR (VoNR) enabled — was previously disabled. 5G NSA+SA support explicitly declared.

ImsService doğrudan cihaz ağacına entegre edildi, vendor blob bağımlılığı kaldırıldı.

ImsService integrated directly into the device tree, removing the vendor blob dependency.

---

### Ses / Audio

Hoparlör ses seviyesi +6dB artırıldı. Daha yüksek ve net ses.

Speaker volume boosted by +6dB for louder and clearer output.

Medya ses adımı 15'ten 25'e çıkarıldı, daha hassas ses kontrolü. Telefon ses adımı 11'den 15'e çıkarıldı.

Media volume steps increased from 15 to 25 for finer control. Call volume steps increased from 11 to 15.

Bluetooth üzerinden LDAC, aptX ve aptX HD codec desteği eklendi. Yüksek kaliteli kablosuz ses için A2DP offload cap güncellendi.

LDAC, aptX and aptX HD Bluetooth codec support added. A2DP offload cap updated for high-quality wireless audio.

Uzamsal ses (spatial audio) varsayılan olarak aktif.

Spatial audio enabled by default.

---

### Şarj Animasyonu / Charging Animation

Kapalı cihaz şarj edilirken animasyon düzgün çalışıyor. Güç tuşuna basıldığında ekran açılıyor ve animasyon gösteriliyor. Şarj modunda gereksiz suspend engellendi.

Charging animation now works correctly when the device is off. Pressing the power button wakes the screen and shows the animation. Unnecessary suspend during charging has been prevented.

---

### Ağ ve Konum / Network & Location

GNSS HAL AIDL v3 manifest'e eklendi, konum doğruluğu arttı. LPP (LTE Positioning Protocol) aktif edildi, GPS daha hızlı fix alıyor.

GNSS HAL AIDL v3 added to manifest for improved location accuracy. LPP (LTE Positioning Protocol) enabled for faster GPS fix.

WiFi Direct ve P2P yapılandırması eklendi. 5GHz DFS kanalları hotspot modunda kullanılabilir hale getirildi.

WiFi Direct and P2P configuration added. 5GHz DFS channels enabled for hotspot mode.

---

### Batarya / Battery

Ekran parlaklık eğrisi düzeltildi, batarya istatistikleri artık doğru görünüyor. Batarya sağlığı optimizasyonu ve gece şarj modu aktif edildi.

Display brightness curve corrected, battery stats now display accurately. Battery health optimization and night charging mode enabled.

---

### SELinux ve Güvenlik / SELinux & Security

Parmak izi HAL için gerekli ioctl izinleri eklendi, SELinux reddi olmadan çalışıyor. Termal servis için property tipi tanımlandı. ImsService için doğru SELinux bağlamı tanımlandı.

Required ioctl permissions added for fingerprint HAL — no more SELinux denials. Property type defined for thermal service. Correct SELinux context assigned for ImsService.

---

## 24 Mart 2025 / March 24, 2025

### CPU, GPU ve Güç Yönetimi / CPU, GPU & Power Management

Uygulama açılışlarında CPU boost profili güçlendirildi. Büyük çekirdek minimum frekansı 2.2GHz'den 2.6GHz'e çıkarıldı, uygulamalar daha hızlı açılıyor.

CPU boost profile strengthened for app launches. Big cluster minimum frequency raised from 2.2GHz to 2.6GHz for faster app startup.

Yoğun grafik işlemlerinde (oyun, video düzenleme) GPU daha erken yüksek frekanslara çıkıyor. INTERACTION hint'ine GPU boost eklendi, scroll ve dokunuş sırasında GPU da hızlanıyor.

GPU ramps up to higher frequencies earlier during intensive workloads. GPU boost added to INTERACTION hint — GPU now accelerates during scrolling and touch events.

CPU varyantı cortex-a55'ten cortex-a76'ya güncellendi, derleyici optimizasyonları cihaza özgü hale geldi.

CPU variant updated from cortex-a55 to cortex-a76, enabling device-specific compiler optimizations.

Termal eşikler biraz gevşetildi. Cihaz artık daha az agresif throttle yapıyor, performans daha tutarlı.

Thermal thresholds slightly relaxed. The device throttles less aggressively, resulting in more consistent performance.

Derin uyku (deep idle) ve çok çekirdekli derin uyku (MCDI) aktif hale getirildi, bekleme süresi pil tüketimi azaldı.

Deep idle and Multi-Core Deep Idle (MCDI) enabled, reducing battery drain during standby.

Render thread boost aktif edildi — top-app render thread'i büyük core'lara taşınıyor.

Render thread boost enabled — top-app render thread is now migrated to big cores.

`sched_util_clamp_min` 1024'ten 0'a düşürüldü — boşta gereksiz CPU yük tahmini yok.

`sched_util_clamp_min` lowered from 1024 to 0 — no unnecessary CPU utilization clamping at idle.

---

### Bellek ve I/O Yönetimi / Memory & I/O Management

ZRAM için `page-cluster` 3'ten 0'a düşürüldü. UFS depolama HDD değil, tek sayfa swap okuma daha az gecikme sağlıyor.

`page-cluster` for ZRAM reduced from 3 to 0. UFS storage is not a HDD — single-page swap reads result in lower latency.

`swappiness` 100'den 80'e düşürüldü, RAM daha verimli kullanılıyor.

`swappiness` reduced from 100 to 80 for more efficient RAM utilization.

Boot sırasında tüm UFS blok cihazları (sda/sdb/sdc) için `nr_requests=256` ve `read_ahead_kb=2048` ayarlandı. Boot tamamlandıktan sonra sırasıyla 64 ve 128KB'ye düşürülüyor. Daha önce sadece sdc için ayarlanıyordu, sda ve sdb atlanıyordu.

During boot, `nr_requests=256` and `read_ahead_kb=2048` set for all UFS block devices (sda/sdb/sdc). After boot completes, values are reduced to 64 and 128KB respectively. Previously only sdc was configured — sda and sdb were missed.

`vm.min_free_kbytes=12288` eklendi — kernel her zaman 12MB boş bellek tutuyor, ani OOM ihtimali azalıyor.

Added `vm.min_free_kbytes=12288` — kernel always keeps 12MB free, reducing sudden OOM risk.

`vm.dirty_expire_centisecs` 200cs'den 3000cs'ye çıkarıldı. 2 saniyelik çok kısa süre gereksiz disk flush'a neden oluyordu.

`vm.dirty_expire_centisecs` raised from 200cs to 3000cs. The 2-second value was causing unnecessary disk flushes.

`vm.dirty_ratio=8`, `vm.dirty_background_ratio=4` eklendi — bellek baskısı azaltıldı, yazma gecikmesi dengelendi.

Added `vm.dirty_ratio=8` and `vm.dirty_background_ratio=4` — reduced memory pressure and balanced write latency.

---

### LMK (Düşük Bellek Katili) / LMK (Low Memory Killer)

`thrashing_limit_decay` 2'den 5'e çıkarıldı. Daha önce çok agresif kill davranışı vardı, arka plan uygulamaları çok çabuk kapatılıyordu.

`thrashing_limit_decay` raised from 2 to 5. Previously too aggressive — background apps were being killed too quickly.

`filecache_min_kb` 150MB'den 100MB'ye düşürüldü, bellek daha verimli kullanılıyor.

`filecache_min_kb` reduced from 150MB to 100MB for more efficient memory usage.

`swap_free_low_percentage` 20'den 10'a düşürüldü — swap daha geç devreye giriyor.

`swap_free_low_percentage` reduced from 20 to 10 — swap kicks in later.

---

### Dalvik / ART Optimizasyonları / Dalvik / ART Optimizations

`heaptargetutilization` 0.5'ten 0.6'ya çıkarıldı. 0.5 çok sık GC tetikliyordu, 0.6 daha az GC demek, daha az jank.

`heaptargetutilization` raised from 0.5 to 0.6. The lower value triggered GC too frequently — 0.6 means less GC and less jank.

`dex2oat-threads=4` ve `dex2oat-cpu-set=0,1,2,3` eklendi — arka plan derleme küçük core'larda kalıyor, foreground etkilenmiyor.

Added `dex2oat-threads=4` and `dex2oat-cpu-set=0,1,2,3` — background compilation stays on little cores, foreground is unaffected.

`dalvik.vm.usejit=true` ve `usejitprofiles=true` eklendi — JIT profil tabanlı AOT derleme aktif.

Added `dalvik.vm.usejit=true` and `usejitprofiles=true` — JIT profile-guided AOT compilation enabled.

Boot sonrası `dex2oat` cpuset küçük core'larla (0-3) kısıtlandı.

After boot, `dex2oat` cpuset restricted to little cores (0-3).

---

### Medya / Media

`media.stagefright.thumbnail.prefer_hw_codecs=true` eklendi — thumbnail oluşturma için donanım codec tercih ediliyor.

Added `media.stagefright.thumbnail.prefer_hw_codecs=true` — hardware codec preferred for thumbnail generation.

---

### SELinux Düzeltmesi / SELinux Fix

`vendor_thermal_prop` tipi vendor blob sepolicy'de zaten tanımlıydı, bizim `property.te`'deki duplicate tanım derleme hatasına neden oluyordu. Kaldırıldı.

`vendor_thermal_prop` type was already declared in the vendor blob sepolicy. The duplicate declaration in our `property.te` was causing a build error. Removed.

---

### WiFi Eski Modem Uyumluluğu / WiFi Legacy Router Compatibility

`pmf=1`'den `pmf=0`'a düşürüldü. PMF (Protected Management Frames) desteklemeyen eski router'larda bağlantı el sıkışması başarısız oluyordu.

`pmf` changed from 1 to 0. Routers that don't support PMF (Protected Management Frames) were failing the connection handshake.

`sae_pwe=2`'den `sae_pwe=0`'a düşürüldü. Önceki ayar yalnızca H2E (Hash-to-Element) yöntemini kabul ediyordu; hunting-and-pecking kullanan eski WPA3 router'lar bağlanamıyordu. Artık her iki yöntem de kabul ediliyor.

`sae_pwe` changed from 2 to 0. The previous setting only accepted H2E (Hash-to-Element); older WPA3 routers using hunting-and-pecking couldn't connect. Both methods are now accepted.

`wpa_allow_tkip_in_gt=1` eklendi. TKIP group cipher kullanan eski WPA/WPA2 ağlarına bağlanabilmek için gerekli.

Added `wpa_allow_tkip_in_gt=1`. Required to connect to legacy WPA/WPA2 networks using TKIP as the group cipher.

`bss_expiration_age=180` eklendi. Eski router'ların beacon aralığı uzun olabiliyor, BSS expiration süresi buna göre artırıldı.

Added `bss_expiration_age=180`. Legacy routers can have longer beacon intervals — BSS expiration time increased accordingly.
