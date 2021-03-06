---
title: Java Web uygulamanızda Azure Application Insights telemetrisini filtreleyin
description: İzlemeniz gerekmeyen olayları filtreleyerek telemetri trafiğini azaltın.
ms.topic: conceptual
ms.date: 3/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 35a95ac32fc4390e08d3c7fee2b9f9ff52202e4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100573819"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Java Web uygulamanızda telemetri filtreleme

> [!IMPORTANT]
> Bu belgede açıklanan yaklaşım artık önerilmez.
>
> Java uygulamalarını izlemek için önerilen yaklaşım, kodu değiştirmeden otomatik izleme kullanmaktır. Lütfen [Application Insights Java 3,0 Aracısı](./java-in-process-agent.md)için yönergeleri izleyin.

Filtreler, [Java Web uygulamanızın Application Insights gönderdiği](java-get-started.md)Telemetriyi seçmek için bir yol sağlar. Kullanabileceğiniz bazı hazır olmayan filtreler vardır ve kendi özel filtrelerinizi de yazabilirsiniz.

Kullanıma hazır filtreler şunları içerir:

* İzleme önem düzeyi
* Belirli URL 'Ler, anahtar sözcükler veya yanıt kodları
* Hızlı yanıtlar-diğer bir deyişle, uygulamanızın hızla yanıt verdiği istekleriniz
* Belirli olay adları

> [!NOTE]
> Filtreler uygulamanızın ölçümlerini eğriltin. Örneğin, yavaş yanıtları tanılamak için hızlı yanıt sürelerini atmak üzere bir filtre ayarlayacağınıza karar verebilirsiniz. Ancak, Application Insights tarafından raporlanan ortalama yanıt sürelerinin gerçek hızından daha yavaş olacağını ve istek sayısının gerçek sayıdan küçük olacağını bilmeniz gerekir.
> Bu sorun olursa bunun yerine [örnekleme](./sampling.md) kullanın.

## <a name="setting-filters"></a>Filtre ayarlama

ApplicationInsights.xml, `TelemetryProcessors` aşağıdaki örneğe benzer bir bölüm ekleyin:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```

[Yerleşik işlemcilerin tam kümesini inceleyin](https://github.com/microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal).

## <a name="built-in-filters"></a>Yerleşik filtreler

### <a name="metric-telemetry-filter"></a>Ölçüm telemetri filtresi

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` -Özel Ölçüm adlarının virgülle ayrılmış listesi.


### <a name="page-view-telemetry-filter"></a>Sayfa görüntüleme telemetri filtresi

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` -Duration, sayfanın yüklenmesi için geçen süreyi ifade eder. Bu ayarlanırsa, bu süreden daha hızlı yüklenen sayfalar raporlanmaz.
* `NotNeededNames` -Sayfa adlarının virgülle ayrılmış listesi.
* `NotNeededUrls` -URL parçalarının virgülle ayrılmış listesi. Örneğin, `"home"` URL 'de "giriş" olan tüm sayfaları filtreler.


### <a name="request-telemetry-filter"></a>Telemetri filtresi iste


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Yapay kaynak filtresi

SyntheticSource özelliğinde değerleri olan tüm Telemetriyi filtreler. Bunlar, botların ve kullanılabilirlik testlerinin isteklerini içerir.

Tüm yapay istekler için telemetri filtreleme:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Belirli yapay kaynaklar için telemetri filtreleme:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` -Yapay kaynak adlarının virgülle ayrılmış listesi.

### <a name="telemetry-event-filter"></a>Telemetri olay filtresi

Özel olayları filtreler ( [Trackevent ()](./api-custom-events-metrics.md#trackevent)kullanılarak günlüğe kaydedilir).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` -Olay adlarının virgülle ayrılmış listesi.


### <a name="trace-telemetry-filter"></a>Telemetri filtresini izleme

Günlük izlemelerini filtreler ( [Tracktrace ()](./api-custom-events-metrics.md#tracktrace) veya bir [günlük çerçevesi toplayıcısı](java-trace-logs.md)kullanılarak günlüğe kaydedilir).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` geçerli değerler şunlardır:
  *  KAPALı-tüm izlemeleri filtreleme
  *  TRACE-filtreleme yok. Izleme düzeyine eşit
  *  BILGI-Izleme düzeyini filtrele
  *  UYAR-Izleme ve BILGI filtreleme
  *  HATA-uyarı filtreleme, BILGI, Izleme
  *  KRITIK-tüm KRITIK bir filtre


## <a name="custom-filters"></a>Özel filtreler

### <a name="1-code-your-filter"></a>1. filtrenizi kodlayın

Kodunuzda şunu uygulayan bir sınıf oluşturun `TelemetryProcessor` :

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

        /* Any parameters that are required to support the filter.*/
        private final String successful;

        /* Initializers for the parameters, named "setParameterName" */
        public void setNotNeeded(String successful)
        {
            this.successful = successful;
        }

        /* This method is called for each item of telemetry to be sent.
           Return false to discard it.
           Return true to allow other processors to inspect it. */
        @Override
        public boolean process(Telemetry telemetry) {
            if (telemetry == null) { return true; }
            if (telemetry instanceof RequestTelemetry)
            {
                RequestTelemetry requestTelemetry = (RequestTelemetry)    telemetry;
                return request.getSuccess() == successful;
            }
            return true;
        }
    }

```

### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. yapılandırma dosyasında filtrenizi çağırma

ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

### <a name="3-invoke-your-filter-java-spring"></a>3. filtrenizi çağırma (Java Spring)

Yay çerçevesini temel alan uygulamalar için, özel telemetri işlemcilerin ana uygulama sınıfınıza bir çekirdeklere olarak kaydedilmesi gerekir. Daha sonra uygulama başladığında bu kullanıcılar bu şekilde yeniden kablolu olur.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

`application.properties`Bu parametreleri özel Filtrenizle geçirmek için içinde kendi filtre parametrelerinizi oluşturmanız ve Spring Boot 'ın externalized Configuration Framework 'ü kullanmanız gerekir. 


## <a name="troubleshooting"></a>Sorun giderme

*Filtreim çalışmıyor.*

* Geçerli parametre değerleri sağladıysanız emin olun. Örneğin, süreler tamsayı olmalıdır. Geçersiz değerler filtrenin yoksayılmasına neden olacak. Özel filtreniz bir Oluşturucu veya ayarlama yönteminden bir özel durum oluşturursa, yok sayılır.

## <a name="next-steps"></a>Sonraki adımlar

* [Örnekleme](./sampling.md) -örneklerinizi eğmez bir alternatif olarak örnekleme yapmayı düşünün.

