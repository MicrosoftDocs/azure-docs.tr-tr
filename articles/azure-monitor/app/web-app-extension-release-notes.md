---
title: Azure Web App uzantısı için sürüm notları-Application Insights
description: Application Insights ile çalışma zamanı izleme için Azure Web Apps uzantısı notlarını yayınlar.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 07ba61f630b849a377f1c7ba881f95518eb73606
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042615"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Application Insights için Azure Web App uzantısı sürüm notları

Bu makalede, Application Insights ile çalışma zamanı izleme için Azure Web Apps uzantısı için sürümler notları bulunur. Bu yalnızca önceden yüklenmiş uzantılar için geçerlidir.

Application Insights için Azure Web App uzantısı hakkında daha fazla [bilgi edinin](azure-web-apps.md) .

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

- Şu anda hangi uzantının hangi sürümünü buldum?
    - `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights` öğesine gidin. Daha fazla bilgi için [Uzantı/Aracı tabanlı izleme için adım adım sorun giderme kılavuzu ' nu](./azure-web-apps.md?tabs=net#troubleshooting) ziyaret edin.

- Özel uzantıları kullanıyorsam ne yapmalıyım?
    - Artık desteklenmediğinden özel site uzantılarını kaldırın.

## <a name="release-notes"></a>Sürüm notları

### <a name="2838"></a>2.8.38

- JAVA uzantısı: 2.5.1 'den [Java Agent 3.0.2 (GA)](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.0.2) sürümüne yükseltildi.
- Node.js uzantısı: 1.8.7 'den [1.8.8](https://github.com/microsoft/ApplicationInsights-node.js/releases/tag/1.8.8) 'e AI SDK 'sı güncelleştirildi.
- .NET Core: destek dışı sürümler (2,0, 2,2, 3,0) kaldırıldı. Desteklenen sürümler 2,1 ve 3,1 ' dir.

### <a name="2837"></a>2.8.37

- AppSvc Windows uzantısı: .Net Core System.Diagnostics.DiagnosticSource.dll herhangi bir sürümüyle çalışma yapıldı.

### <a name="2836"></a>2.8.36

- AppSvc Windows uzantısı: .NET Core 'da AI SDK 'Sı ile Işlemler etkinleştirildi.

### <a name="2835"></a>2.8.35

- AppSvc Windows uzantısı: .NET Core 3,1 desteği eklendi.

### <a name="2833"></a>2.8.33

- .Net, .NET Core, Java ve Node.js aracıları ve Windows uzantısı: bağımsız bulutları için destek. Bağlantı dizeleri, bağımsız bulutlara veri göndermek için kullanılabilir.

### <a name="2831"></a>2.8.31

- ASP.NET Core Aracısı: güncelleştirilmiş Application Insights SDK başvurularından biriyle ilgili sorun düzeltildi (bkz. 2.8.26 için bilinen sorunlar). Yanlış sürümü `System.Diagnostics.DiagnosticSource.dll` çalışma zamanı tarafından zaten yüklenmişse, kodsuz kullanacaksınız uzantısı artık uygulamayı kilitleyerek ve geri kapatmayacaktır. Bu sorundan etkilenen müşteriler için, ' `System.Diagnostics.DiagnosticSource.dll` ın bin klasöründen kaldırılması veya "ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24" ayarını yaparak uzantının eski sürümünü kullanmanız önerilir; Aksi takdirde, uygulama izleme etkin değildir.

### <a name="2826"></a>2.8.26

- ASP.NET Core Aracısı: güncelleştirilmiş Application Insights SDK ile ilgili sorun düzeltildi. `AiHostingStartup`ApplicationInsights.dll bin klasöründe zaten mevcutsa aracı yüklemeye çalışır. Bu, yansıma ile ilgili sorunları derleme yoluyla çözer \<AiHostingStartup\> . GetTypes ().
- Bilinen sorunlar: `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` başka bir dll sürümü yüklüyse özel durum oluşturulabilir `DiagnosticSource` . Bu durum, örneğin Publish klasöründe varsa olabilir `System.Diagnostics.DiagnosticSource.dll` . Risk azaltma olarak, uygulama hizmetlerinde uygulama ayarlarını ayarlayarak önceki uzantı sürümünü kullanın: ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24.

### <a name="2824"></a>2.8.24

- 2.8.21 'in yeniden paketlenmiş sürümü.

### <a name="2823"></a>2.8.23

- ASP.NET Core 3,0 kodsuz kullanacaksınız izleme desteği eklendi.
- 2,1, 2,2 ve 3,0 çalışma zamanı sürümleri için SDK ASP.NET Core [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) olarak güncelleştirildi. .NET Core 2,0 ' i hedefleyen uygulamalar SDK 2.1.1 kullanmaya devam eder.

### <a name="2814"></a>2.8.14

- ASP.NET Core SDK sürümü, .NET Core 2,1, 2,2 ' i hedefleyen uygulamalar için 2.3.0 'den en son (2.6.1) sürümüne güncelleştirildi. .NET Core 2,0 ' i hedefleyen uygulamalar SDK 2.1.1 kullanmaya devam eder.

### <a name="2812"></a>2.8.12

- ASP.NET Core 2,2 uygulamaları için destek.
- ASP.NET Core uzantısında hata düzeltildi, uygulama zaten SDK ile işaretlenmiş olsa bile SDK 'ya eklenmesine neden olur. 2,1 ve 2,2 uygulamalarında, uygulama klasöründeki ApplicationInsights.dll varlığı artık uzantının yeniden başlatılmasına neden olur. 2,0 uygulamaları için, uzantı yalnızca ApplicationInsights bir çağrı ile etkinleştirilirse kapalıdır `UseApplicationInsights()` .

- ASP.NET Core uygulamalar için tamamlanmamış HTML yanıtı için kalıcı düzelme. Bu çözüm artık .NET Core 2,2 uygulamaları için çalışacak şekilde genişletilir.

- ASP.NET Core uygulamalar () için JavaScript ekleme özelliğini devre dışı bırakma desteği eklendi `APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting` . ASP.NET Core için, açıkça devre dışı bırakılmadığı takdirde JavaScript ekleme işlemi varsayılan olarak "kabul etme" modundadır. (Varsayılan ayar geçerli davranışı koruyacak şekilde yapılır.)

- Ikey mevcut olmasa bile ekleme hatasına neden olan uzantı hatası düzeltildi ASP.NET Core.
- Telemetride yanlış SDK sürümüne neden olan SDK sürüm ön eki mantığındaki bir hata düzeltildi.

- Telemetrinin nasıl toplandığını belirlemek için ASP.NET Core uygulamalar için SDK sürüm ön eki eklendi.
- Önceden yüklenmiş uzantının sürümünü doğru şekilde göstermek için sabit SCM-ApplicationInsights sayfası.

### <a name="2810"></a>2.8.10

- ASP.NET Core uygulamalar için tamamlanmamış HTML yanıtı için çözüm.

## <a name="next-steps"></a>Sonraki adımlar

- Azure Uygulama Hizmetleri için izlemeyi yapılandırma hakkında daha fazla bilgi için [Azure App Service belgelerini](azure-web-apps.md) ziyaret edin. 
