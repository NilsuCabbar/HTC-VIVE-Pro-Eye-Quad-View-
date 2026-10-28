# Vive Pro Eye – Eye-Tracked Quad Views Foveation (Unity 6.3)

Unity 6.3 (6000.3.21f1) + URP + OpenXR 1.17 projesi. HTC Vive Pro Eye üzerinde, Unity'nin OpenXR **quad views** render yolu ile foveated rendering denemesi. Özel shader yok.

## Nasıl çalışıyor
- `Assets/Foveation/QuadViewsFoveationFeature.cs`: `XR_VARJO_quad_views` uzantısını isteyen OpenXR feature. Unity her göz için 2 görüntü çizer (geniş düşük çözünürlüklü + dar tam çözünürlüklü).
- SteamVR bu uzantıyı kendisi sunmuyor; [Quad-Views-Foveated](https://github.com/mbucchia/Quad-Views-Foveated) API layer'ı sağlıyor ve tam çözünürlüklü alanı göz takibiyle hareket ettiriyor.
- `Assets/Foveation/Editor/FoveationProjectSetup.cs`: **Tools → Foveated Rendering → Configure Eye-Tracked Quad Views (Vive Pro Eye)** menüsü. D3D11, Multi-pass, Quad Views + Eye Gaze + HTC Vive Controller feature'larını ayarlar.

## Gereksinimler (PC)
1. Vive Console for SteamVR, `sr_runtime.exe` (SRanipal) çalışıyor, göz kalibrasyonu yapılmış.
2. SteamVR aktif OpenXR runtime.
3. Quad-Views-Foveated kurulu. **Sadece Direct3D 11** ile çalışıyor.
4. Göz verisi için OpenXR eye gaze katmanı (aşağıya bakın).

## Bulgular / durum
- Unity'nin normal (VRS) foveated rendering'i PC + SteamVR'da göz takibi yapamıyor: gaze'i sadece `XR_META_foveation_eye_tracked` / `XR_FB_foveation` uzantılarından alıyor, SteamVR bunları sunmuyor.
- Quad views çalışıyor: ~%52 piksel tasarrufu (`Session is using quad views`).
- HTC'nin `ViveOpenXRFacialTracking` katmanı `supportsEyeGazeInteraction`'ı hep `false` bildiriyor ve OpenXR üzerinden göz verisi gelmiyor (`tracked=0`). Bu yüzden şu an **sabit** foveation.
- Sonraki adım: HTC katmanı yerine [OpenXR Layer VIVE SDK (DragonDreams)](https://dragondreams.ch/index.php/openxr-layer-vive-sdk/) denemek. Bayrak yine `false` gelirse Quad-Views-Foveated yamalanıp derlenecek.
- Aynı katmanın iki sürümü (eski `VIVE\Updater` ve yeni `VIVEDriver`) birlikte açıkken Unity çöküyordu; eskisi registry'den kapatıldı:
  `HKLM\SOFTWARE\Khronos\OpenXR\1\ApiLayers\Implicit` → eski `ViveOpenXRFacialTracking.json` değeri `1`.

## Teşhis logları
- Unity Console: `[QuadViews] ...` satırları (uzantı durumu, eye gaze bayrağı, 20 sn boyunca gaze verisi).
- `%LocalAppData%\Quad-Views-Foveated\Quad-Views-Foveated.log` (`Eye tracking is supported / not supported`).
- `%LocalAppData%\Quad-Views-Foveated\settings.cfg` içinde `debug_eye_gaze=1` bakılan noktayı gösterir.
