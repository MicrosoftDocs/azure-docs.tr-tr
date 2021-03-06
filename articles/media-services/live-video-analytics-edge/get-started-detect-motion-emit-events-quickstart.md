---
title: IoT Edge Azure 'da canlı video analiziyle çalışmaya başlama
description: Bu hızlı başlangıçta IoT Edge 'da canlı video analiziyle çalışmaya başlama gösterilmektedir. Canlı video akışında hareket algılamayı öğrenin.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 57edf1721249f839f5c781756b3e09bf59888dab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730295"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Hızlı başlangıç: IoT Edge kullanmaya başlama-canlı video analizi

Bu hızlı başlangıç, IoT Edge üzerinde canlı video analizlerini kullanmaya başlamanıza yönelik adımlarda size yol gösterir. Bir Azure VM 'yi IoT Edge bir cihaz olarak kullanır. Ayrıca, sanal bir canlı video akışı kullanır. 

Kurulum adımlarını tamamladıktan sonra, bu akıştaki tüm hareketleri algılayan ve raporlayan bir medya grafiğiyle sanal bir canlı video akışı çalıştırabileceksiniz. Aşağıdaki diyagramda, bu medya grafiği grafik olarak temsil eder.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Hareket algılamayı temel alan canlı video analizi":::

IoT Edge ' de canlı video analiziyle çalışmaya başlama hakkında ayrıntılı adımlar içeren aşağıdaki videoyu görüntüleyebilirsiniz:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Hcax]

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. Henüz bir [hesabınız yoksa ücretsiz olarak bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

  > [!NOTE]
  > Hizmet sorumluları oluşturma izinlerine sahip bir Azure aboneliğine ihtiyacınız olacak (**sahip rolü** bunu sağlar). Doğru izinleriniz yoksa, size doğru izinleri vermek için lütfen hesap yöneticinize ulaşın.  

* Geliştirme makinenizde [Visual Studio Code](https://code.visualstudio.com/) . [Azure IoT araçları uzantısına](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)sahip olduğunuzdan emin olun.
* Geliştirme makinenizin bağlı olduğu ağın, giden trafik için 5671 numaralı bağlantı noktası üzerinden gelişmiş Ileti sıraya alma Protokolü (AMQP) izin verdiğinden emin olun. Bu kurulum, Azure IoT araçlarının Azure IoT Hub ile iletişim kurmasını sağlar.

> [!TIP]
> Azure IoT araçları uzantısını yüklerken Docker yüklemeniz istenebilir. İstemi yok saymaktan çekinmeyin.

## <a name="set-up-azure-resources"></a>Azure kaynakları ayarlama

Bu öğreticide aşağıdaki Azure kaynakları gereklidir:

* IoT Hub
* Depolama hesabı
* Azure Media Services hesabı
* Azure 'da [IoT Edge çalışma zamanı](../../iot-edge/how-to-install-iot-edge.md) yüklü bir Linux sanal makinesi

Bu hızlı başlangıç için, Azure aboneliğinizde gerekli kaynakları dağıtmak üzere [canlı video analizi kaynakları kurulum betiğini](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) kullanmanızı öneririz. Bunu yapmak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) gidin ve Cloud Shell simgesini seçin.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. İlk kez Cloud Shell kullanıyorsanız, bir depolama hesabı ve bir Microsoft Azure dosya paylaşımının oluşturulması için bir abonelik seçmeniz istenir. Cloud Shell oturum bilgileriniz için bir depolama hesabı oluşturmak üzere **depolama oluştur** ' u seçin. Bu depolama hesabı, komut dosyasının Azure Media Services hesabınızla kullanılmak üzere oluşturulacağı hesaptan farklıdır.
1. Cloud Shell penceresinin sol tarafındaki açılan menüde, ortamınız olarak **Bash** ' i seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/env-selector.png" alt-text="Ortam Seçicisi":::
1. Aşağıdaki komutu çalıştırın.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Betiği başarıyla tamamladıktan sonra aboneliğinizdeki tüm gerekli kaynakları görmeniz gerekir. Komut dosyası tarafından toplam 12 kaynak oluşturulur:
    1. **Akış uç noktası** -bu, kayıtlı AMS varlığını yürütmeye yardımcı olur.
    1. **Sanal makine** -bu, Edge cihazınız olarak görev yapacak bir sanal makinedir.
    1. **Disk** -bu, medya ve yapıtları depolamak için sanal makineye bağlı bir depolama disktir.
    1. **Ağ güvenlik grubu** -bu, bir Azure sanal ağındaki Azure kaynaklarından gelen ve giden ağ trafiğini filtrelemek için kullanılır.
    1. **Ağ arabirimi** -bu, bir Azure sanal makinesinin Internet, Azure ve diğer kaynaklarla iletişim kurmasını sağlar.
    1. Savunma **bağlantısı** -bu, tarayıcınızı ve Azure Portal kullanarak sanal makinenize bağlanmanızı sağlar.
    1. **Genel IP adresi** -bu, Azure kaynaklarının Internet ve genel kullanıma yönelik Azure hizmetleriyle iletişim kurmasını sağlar
    1. **Sanal ağ** -bu, sanal makineniz gibi birçok Azure Kaynak türünün, internet ve şirket içi ağlarla güvenli bir şekilde iletişim kurmasını sağlar. [Sanal ağlar](../../virtual-network/virtual-networks-overview.md)hakkında daha fazla bilgi edinin.
    1. **IoT Hub** -bu, IoT uygulamanız, IoT Edge modülleriniz ve yönettiği cihazlar arasında çift yönlü iletişim için bir merkezi ileti hub 'ı görevi görür.
    1. **Medya hizmeti hesabı** -bu, Azure 'da medya içeriğini yönetmeye ve akışa yardımcı olur.
    1. **Depolama hesabı** -bir birincil depolama hesabınız olmalıdır ve Media Services hesabınızla Ilişkili birkaç ikincil depolama hesabı olabilir. Daha fazla bilgi için bkz. [Azure Media Services hesapları Ile Azure depolama hesapları](../latest/storage-account-concept.md).
    1. **Kapsayıcı kayıt defteri** -bu, özel Docker kapsayıcı görüntülerinizi ve ilgili yapıtlarınızı depolamaya ve yönetmeye yardımcı olur.

Betik çıktısında, kaynak tablosu IoT Hub adını listeler. Kaynak türünü bulun **`Microsoft.Devices/IotHubs`** ve adı aklınızda yazın. Sonraki adımda bu ada ihtiyacınız olacaktır.  

> [!NOTE]
> Betik, ***~/CloudDrive/LVA-Sample/*** dizininde birkaç yapılandırma dosyası da oluşturur. Bu dosyalar hızlı başlangıçta daha sonra gerekecektir.

> [!TIP]
> Oluşturulan Azure kaynaklarıyla ilgili sorunlar yaşıyorsanız, bazı sık karşılaşılan sorunları çözmek için lütfen **[sorun giderme kılavuzumuzu](troubleshoot-how-to.md#common-error-resolutions)** görüntüleyin.

## <a name="deploy-modules-on-your-edge-device"></a>Sınır cihazınızda modüller dağıtma

Cloud Shell ' den aşağıdaki komutu çalıştırın.

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Bu komut, aşağıdaki modülleri bu durumda Linux VM olan Edge cihazına dağıtır.

* IoT Edge (modül adı) üzerinde canlı video analizi `lvaEdge`
* Real-Time streaming Protocol (RTSP) Simülatörü (modül adı `rtspsim` )

RTSP simülatör modülü, [canlı video analizi kaynakları kurulum betiğini](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)çalıştırdığınızda Edge cihazınıza kopyalanmış bir video dosyası kullanarak canlı bir video akışının benzetimini yapar. 

Artık modüller dağıtılır, ancak hiçbir medya grafiği etkin değildir.

## <a name="configure-the-azure-iot-tools-extension"></a>Azure IoT araçları uzantısını yapılandırma

Azure IoT araçları uzantısını kullanarak IoT Hub 'ınıza bağlanmak için bu yönergeleri izleyin.

1. Visual Studio Code, **Uzantılar** sekmesini açın (veya CTRL + SHIFT + X tuşlarına basın) ve Azure IoT Hub aratın.
1. Sağ tıklayıp **uzantı ayarları**' nı seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Uzantı ayarları":::
1. "Ayrıntılı Iletiyi göster" i arayın ve etkinleştirin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Ayrıntılı Iletiyi göster":::
1. **Görünüm**  >  **Gezgini**' ni seçin. Ya da CTRL + SHIFT + E ' yi seçin.
1. **Gezgin** sekmesinin sol alt köşesinde **Azure IoT Hub**' yi seçin.
1. Bağlam menüsünü görmek için **diğer seçenekler** simgesini seçin. Sonra **IoT Hub bağlantı dizesi ayarla**' yı seçin.
1. Bir giriş kutusu göründüğünde IoT Hub bağlantı dizenizi girin. Cloud Shell, bağlantı dizesini *~/CloudDrive/LVA-Sample/appsettings.jstarihinde* alabilirsiniz.

> [!NOTE]
> IoT Hub için yerleşik uç nokta bilgisi sağlamanız istenebilir. Bu bilgileri almak için Azure portal ' de IoT Hub gidin ve sol gezinti bölmesindeki **yerleşik uç noktalar** seçeneğini bulun. Buraya tıklayın ve **Olay Hub** 'ı ile uyumlu uç nokta bölümünde **Olay Hub 'ı ile uyumlu uç noktası** bölümüne bakın. Kutusunda metni kopyalayın ve kullanın. Uç nokta şuna benzer şekilde görünecektir:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

Bağlantı başarılı olursa Edge cihazlarının listesi görüntülenir. **LVA-Sample-Device** adlı en az bir cihaz görmeniz gerekir. Artık IoT Edge cihazlarınızı yönetebilir ve bağlam menüsü aracılığıyla Azure IoT Hub etkileşim kurabilirsiniz. Sınır cihazında dağıtılan modülleri görüntülemek için, **LVA-örnek-cihaz** altında **modüller** düğümünü genişletin.

![LVA-örnek-cihaz düğümü](./media/quickstarts/lva-sample-device-node.png)

> [!TIP]
> Canlı video analizlerini bir sınır cihazında (örn. bir ARM64 cihaz) [IoT Edge yourselves üzerinde el ile dağıttıysanız](deploy-iot-edge-device.md) , modülün bu cihazın altında, Azure IoT Hub altında görünür olduğunu görürsünüz. Bu modülü seçebilir ve aşağıdaki adımların geri kalanını takip edebilirsiniz.

## <a name="use-direct-method-calls"></a>Doğrudan Yöntem çağrılarını kullanın

Doğrudan yöntemleri çağırarak canlı video akışlarını çözümlemek için modülünü kullanabilirsiniz. Daha fazla bilgi için bkz. [IoT Edge Için doğrudan canlı video analizi yöntemleri](direct-methods.md). 

### <a name="invoke-graphtopologylist"></a>Graphtopologyılist komutunu çağır

Modüldeki tüm [grafik topolojilerini](media-graph-concept.md#media-graph-topologies-and-instances) listelemek için:

1. Visual Studio Code, **Lvaedge** modülüne sağ tıklayıp **Modül doğrudan yöntemini çağır**' ı seçin.
1. Görüntülenen kutuya *Graphtopologyılist* yazın.
1. Aşağıdaki JSON yükünü kopyalayın ve kutuya yapıştırın. Ardından Enter tuşunu seçin.

    ```
    {
        "@apiVersion" : "2.0"
    }
    ```

    Birkaç saniye içinde, **Çıkış** penceresi aşağıdaki yanıtı gösterir.

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    Grafik topolojisi oluşturulmadığından bu yanıt beklenmektedir.
    

### <a name="invoke-graphtopologyset"></a>Graphtopologyıset komutunu çağır

Daha önce yaptığımız gibi, artık `GraphTopologySet` bir [Graph topolojisi](media-graph-concept.md#media-graph-topologies-and-instances)ayarlamak için komutunu çalıştırabilirsiniz. Yük olarak aşağıdaki JSON 'ı kullanın.

```
{
    "@apiVersion": "2.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```

Bu JSON yükü, üç parametreyi tanımlayan bir grafik topolojisi oluşturur. Bu parametrelerden ikisi varsayılan değerlere sahiptir. Topolojide bir kaynak (RTSP kaynağı) düğümü, bir işlemci (hareket algılama işlemcisi) düğümü ve bir havuz (IoT Hub havuzu) düğümü vardır.

Birkaç saniye içinde **Çıkış** penceresinde aşağıdaki yanıtı görürsünüz.

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Döndürülen durum 201 ' dir. Bu durum yeni bir topoloji oluşturulduğunu gösterir. 

Aşağıdaki adımları deneyin:

1. `GraphTopologySet`Yeniden çağırın. Döndürülen durum kodu 200 ' dir. Bu kod, var olan bir topolojinin başarıyla güncelleştirildiğini gösterir.
1. `GraphTopologySet`Yeniden çağırın, ancak açıklama dizesini değiştirin. Döndürülen durum kodu 200 ' dir ve açıklama yeni değere güncelleştirilir.
1. `GraphTopologyList`Önceki bölümde özetlenen şekilde çağırın. Artık `MotionDetection` topolojiyi döndürülen yükün içinde görebilirsiniz.

### <a name="invoke-graphtopologyget"></a>Graphtopologyıget çağırma

`GraphTopologyGet`Aşağıdaki yükü kullanarak çağırın.

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

Birkaç saniye içinde **Çıkış** penceresinde aşağıdaki yanıtı görürsünüz:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Yanıt yükünde, bu ayrıntılara dikkat edin:

* Durum kodu, başarıyı belirten 200 ' dir.
* Yük, `created` zaman damgasını ve `lastModified` zaman damgasını içerir.

### <a name="invoke-graphinstanceset"></a>Graphınstanceset 'i çağır

Önceki grafik topolojisine başvuran bir grafik örneği oluşturun. Grafik örnekleri, aynı grafik topolojisini kullanarak birçok kameradan canlı video akışlarını analiz etmenize olanak tanır. Daha fazla bilgi için bkz. [medya grafiği topolojileri ve örnekleri](media-graph-concept.md#media-graph-topologies-and-instances).

`GraphInstanceSet`Aşağıdaki yükü kullanarak doğrudan yöntemi çağırın.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Bu yükün şu olduğuna dikkat edin:

* Örneğin oluşturulması gereken topoloji adını ( `MotionDetection` ) belirtir.
* `rtspUrl`Grafik topolojisi yükünde varsayılan bir değere sahip olmayan için bir parametre değeri içerir. Bu değer aşağıdaki örnek videonun bir bağlantı örneğidir:
    > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]
Birkaç saniye içinde **Çıkış** penceresinde aşağıdaki yanıtı görürsünüz:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Yanıt yükünde şunları unutmayın:

* Durum kodu, yeni bir örneğin oluşturulduğunu belirten 201 ' dir.
* Durum `Inactive` , grafik örneğinin oluşturulduğunu ancak etkinleştirilmediğini belirtir. Daha fazla bilgi için bkz. [medya grafiği durumları](media-graph-concept.md).

Aşağıdaki adımları deneyin:

1. `GraphInstanceSet`Aynı yükü kullanarak yeniden çağırın. Döndürülen durum kodu 200 olduğunu unutmayın.
1. `GraphInstanceSet`Yeniden çağırın, ancak farklı bir açıklama kullanın. Yanıt yükünde, grafik örneğinin başarıyla güncelleştirildiğini belirten güncelleştirilmiş açıklamaya dikkat edin.
1. Çağırın `GraphInstanceSet` , ancak adı olarak değiştirin `Sample-Graph-2` . Yanıt yükünde, yeni oluşturulan grafik örneğine (durum kodu 201) dikkat edin.

### <a name="invoke-graphinstanceactivate"></a>Graphınstanceactivate çağır

Şimdi, etkin video akışını modül aracılığıyla başlatmak için grafik örneğini etkinleştirin. `GraphInstanceActivate`Aşağıdaki yükü kullanarak doğrudan yöntemi çağırın.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Birkaç saniye içinde **Çıkış** penceresinde aşağıdaki yanıtı görürsünüz.

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

200 durum kodu, grafik örneğinin başarıyla etkinleştirildiğini gösterir.

### <a name="invoke-graphinstanceget"></a>Graphınstanceget 'i çağır

Şimdi `GraphInstanceGet` aşağıdaki yükü kullanarak doğrudan yöntemi çağırın.

```
 {
     "@apiVersion" : "2.0",
     "name" : "Sample-Graph-1"
 }
 ```

Birkaç saniye içinde **Çıkış** penceresinde aşağıdaki yanıtı görürsünüz.

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Yanıt yükünde aşağıdaki ayrıntılara dikkat edin:

* Durum kodu, başarıyı belirten 200 ' dir.
* Durum, `Active` grafik örneğinin artık etkin olduğunu gösterir.

## <a name="observe-results"></a>Sonuçları gözlemleyin

Oluşturduğumuz ve etkinleştirdiğimiz grafik örneği, gelen canlı video akışındaki hareketi algılamak için hareket algılama işlemcisi düğümünü kullanır. IoT Hub havuz düğümüne olayları gönderir. Bu olaylar IoT Edge hub 'ına aktarıltı. 

Sonuçları gözlemlemek için aşağıdaki adımları izleyin.

1. Visual Studio Code ' de **Gezgin** bölmesini açın. Sol alt köşede **Azure IoT Hub**' yi arayın.
2. **Cihazlar** düğümünü genişletin.
3. **LVA-Sample-Device** ' a sağ tıklayın ve ardından **yerleşik olay izlemeyi izlemeyi Başlat**' ı seçin.

    ![IoT Hub olaylarını izlemeye başlama](./media/quickstarts/start-monitoring-iothub-events.png)

    > [!NOTE]
    > IoT Hub için yerleşik uç nokta bilgisi sağlamanız istenebilir. Bu bilgileri almak için Azure portal ' de IoT Hub gidin ve sol gezinti bölmesindeki **yerleşik uç noktalar** seçeneğini bulun. Buraya tıklayın ve **Olay Hub** 'ı ile uyumlu uç nokta bölümünde **Olay Hub 'ı ile uyumlu uç noktası** bölümüne bakın. Kutusunda metni kopyalayın ve kullanın. Uç nokta şuna benzer şekilde görünecektir:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
    
**Çıkış** penceresinde şu ileti görüntülenir:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    }
}
```

Bu ayrıntılara dikkat edin:

* İleti bir `body` bölüm ve bir bölüm içerir `applicationProperties` . Daha fazla bilgi için bkz. [IoT Hub Iletileri oluşturma ve okuma](../../iot-hub/iot-hub-devguide-messages-construct.md).
* ' De, içindeki `applicationProperties` `subject` `MediaGraph` iletinin oluşturulduğu düğüme başvurur. Bu durumda, ileti hareket algılama işlemcisinden kaynaklanır.
* `applicationProperties`' De, `eventType` Bu olayın bir analiz olayı olduğunu gösterir.
* `eventTime`Değer, olayın gerçekleştiği zaman değeridir.
* `body`Bölüm, analiz olayı hakkındaki verileri içerir. Bu durumda, olay bir çıkarım olayıdır, bu nedenle gövde `timestamp` ve veri içerir `inferences` .
* Bölümü, olduğunu `inferences` gösterir `type` `motion` . Olayla ilgili ek veriler sağlar `motion` .

Medya grafiğinin bir süre içinde çalışmasına izin verirseniz **Çıkış** penceresinde aşağıdaki iletiyi görürsünüz.

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Bu iletide aşağıdaki ayrıntılara dikkat edin:

* `applicationProperties`' De, `subject` iletinin medya grafiğindeki RTSP kaynak düğümünden oluşturulduğunu gösterir.
* `applicationProperties`' De, `eventType` Bu olayın tanılama olduğunu gösterir.
* `body`Tanılama olayı hakkındaki verileri içerir. Bu durumda, olay olduğu için ileti gövdesini içerir `MediaSessionEstablished` .

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Temizlemek için ek doğrudan Yöntemler çağırma

Önce grafik örneğini devre dışı bırakıp doğrudan yöntemleri çağırın ve sonra silin.

### <a name="invoke-graphinstancedeactivate"></a>Graphınstancedeactivate öğesini çağır

`GraphInstanceDeactivate`Aşağıdaki yükü kullanarak doğrudan yöntemi çağırın.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Birkaç saniye içinde **Çıkış** penceresinde aşağıdaki yanıtı görürsünüz:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

200 durum kodu, grafik örneğinin başarıyla devre dışı bırakıldığını gösterir.

Ardından, `GraphInstanceGet` Bu makalede daha önce belirtildiği gibi çağırma yapmayı deneyin. Değeri gözlemleyin `state` .

### <a name="invoke-graphinstancedelete"></a>Graphınstancedelete 'i çağır

`GraphInstanceDelete`Aşağıdaki yükü kullanarak doğrudan yöntemi çağırın.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Birkaç saniye içinde **Çıkış** penceresinde aşağıdaki yanıtı görürsünüz:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

200 durum kodu, grafik örneğinin başarıyla silindiğini gösterir.

### <a name="invoke-graphtopologydelete"></a>Graphtopologyıdelete komutunu çağır

`GraphTopologyDelete`Aşağıdaki yükü kullanarak doğrudan yöntemi çağırın.

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

Birkaç saniye içinde **Çıkış** penceresinde aşağıdaki yanıtı görürsünüz.

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

200 durum kodu grafik topolojisinin başarıyla silindiğini gösterir.

Aşağıdaki adımları deneyin:

1. `GraphTopologyList`' İ çağırın ve modülün hiçbir Graf topolojisi içerdiğini gözlemleyin.
1. İle `GraphInstanceList` aynı yükü kullanarak çağırın `GraphTopologyList` . Hiç grafik örneği numaralandırılmıyor ' i gözlemleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, bu hızlı başlangıçta oluşturduğunuz kaynakları silin.

## <a name="next-steps"></a>Sonraki adımlar

* [IoT Edge üzerindeki canlı video analizlerini kullanarak nasıl video kaydedeceğinizi](continuous-video-recording-tutorial.md)öğrenin.
* [Tanılama iletileri](monitoring-logging.md)hakkında daha fazla bilgi edinin.