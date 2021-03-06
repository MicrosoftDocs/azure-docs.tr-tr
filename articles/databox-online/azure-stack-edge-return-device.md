---
title: Azure Stack Edge Pro cihazınızı geri döndürme | Microsoft Docs
description: Verileri silme ve Azure Stack Edge Pro cihazınızı döndürme hakkında bilgi edinin ve ardından cihazla ilişkili kaynağı silin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.openlocfilehash: cb11d7d3b2da9ab793cb18814e4021ea7afeb806
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443599"
---
# <a name="return-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro cihazınızı döndürün

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede, verileri silme ve sonra Azure Stack Edge Pro cihazınızı döndürme açıklanmaktadır. Cihazı geri aldıktan sonra cihazla ilişkili kaynağı da silebilirsiniz.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
>
> * Cihazdaki veri disklerinin verileri silme
> * Azure portalda cihaz iadesi başlatma
> * Cihazı paketleme ve bir toplama zamanlama
> * Azure portal kaynağı silme

## <a name="erase-data-from-the-device"></a>Cihazdan verileri silme

Cihazınızın veri disklerinin verilerini silmek için cihazınızı sıfırlamanız gerekir.

Sıfırlamadan önce, gerekirse, cihazdaki yerel verilerin bir kopyasını oluşturun. Cihazdan verileri bir Azure depolama kapsayıcısına kopyalayabilirsiniz. 

Cihaz sıfırlanmadan önce, cihazın dönmesini başlatabilirsiniz.

Cihazınızı yerel Web Kullanıcı arabiriminde veya PowerShell 'de sıfırlayabilirsiniz. PowerShell yönergeleri için bkz. [cihazınızı sıfırlama](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

> [!NOTE]
> - Yeni bir cihaza değiş tokuş yapıyorsanız veya yükseltiyorsanız, Cihazınızı yalnızca yeni cihazı aldıktan sonra sıfırlamanız önerilir.
> - Cihaz sıfırlaması yalnızca cihazdaki tüm yerel verileri siler. Buluttaki veriler silinmez ve [ücretleri](https://azure.microsoft.com/pricing/details/storage/)toplar. Bu verilerin, [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)gibi bir bulut depolama yönetim aracı kullanılarak ayrı olarak silinmesi gerekir.

## <a name="initiate-device-return"></a>Cihaz döndürmeyi Başlat

Geri dönüş işlemini başlatmak için aşağıdaki adımları uygulayın.

1. Azure portal Azure Stack Edge Pro/Data Box Gateway kaynağına gidin. **Genel** görünümde, sağ bölmedeki komut çubuğuna gidin ve **cihazı geri döndür**' ü seçin. 

    ![Cihazı geri dön 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. **Geri dönüş aygıtı** dikey penceresinde, **temel Ayrıntılar** altında:

    1. Cihazın seri numarasını belirtin. Cihaz seri numarasını almak için, cihazın yerel Web Kullanıcı arabirimine gidin ve **genel bakış**' a gidin.  
    
       ![Cihaz seri numarası 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. Hizmet etiket numarasını girin. Hizmet etiket numarası, cihazınız için benzersiz olan beş veya daha fazla karakterli bir tanıtıcıdır. Hizmet etiketi, cihazın sağ alt köşesinde bulunur (cihazı hissettiden başlayarak). Bilgi etiketini çekin (bir slayt etiketi bölmesi olur). Bu panel, hizmet etiketi, NIC, MAC adresi vb. gibi sistem bilgilerini içerir. 
    
       ![Hizmet etiketi numarası 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. Açılan listeden, dönüş için bir neden seçin.

       ![Cihaz dön 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. **Gönderim ayrıntıları**:

    1. Adınızı, şirket adınızı ve tam şirket adresini belirtin. Alan kodu ve bildirim için bir e-posta KIMLIĞI dahil bir iş telefonu girin.
    2. İade sevk etme kutusuna ihtiyacınız varsa, isteği isteyebilirsiniz. Sorunun **geri dönmesi Için** **Evet** yanıtını yazın.

    ![Geri dönüş cihazı 3](media/azure-stack-edge-return-device/return-device-3.png)

4. **Gizlilik koşullarını** gözden geçirin ve gözden geçirdiğinize ve gizlilik koşullarını kabul etmiş olduğunuz NotDan onay kutusunu seçin.

5. **Döndürmeyi Başlat**' ı seçin.

    ![Geri dönüş aygıtı 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. Cihazınızın geri dönüş ayrıntıları yakalandıktan sonra, Azure Stack Edge Pro işlemler ekibine bir e-posta ile bildirim gönderebilirsiniz. E-posta uygulamanızın yüklü ve yapılandırılmış olduğu varsayıldığında e-posta uygulamanızı kullanabilirsiniz. Ayrıca, bir e-posta oluşturup göndermek için verileri kopyalayabilirsiniz.

    ![Geri dönüş aygıtı 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Azure Stack Edge Pro işlemler ekibi e-postayı aldıktan sonra, size bir ters Sevkiyat Etiketi gönderilir. Bu etiketi aldığınızda, taşıyıcı ile cihaz alımı zamanlayabilirsiniz. 

## <a name="schedule-a-pickup"></a>Çekme zamanlaması

Bir toplama zamanlamak için aşağıdaki adımları uygulayın.

1. Cihazı kapatın. Yerel Web Kullanıcı arabiriminde **bakım > güç ayarları**' na gidin.
2. **Kapat ' ı** seçin. Onay istendiğinde, devam etmek için **Evet** ' e tıklayın. Daha fazla bilgi için bkz. [güç yönetimi](../databox-gateway/data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Güç kablolarını çıkarın ve cihazdaki tüm ağ kablolarını kaldırın.
4. Kendi kutusunu veya Azure 'dan aldığınız boş kutuyu kullanarak sevkiyat paketini hazırlayın. Cihazı ve cihazla birlikte gelen güç gücünü kutuya yerleştirin.
5. Paket üzerinde Azure 'dan aldığınız sevkiyat etiketini AFBir şekilde düzeltir.
6. Bölgenizdeki taşıyıcıdan bir kargo toplama randevusu alın. Cihaz bıze döndürülürken, taşıyıcınızın UPS veya FedEx olması mümkündür. KESINTISIZ bir çekme planı zamanlamak için:

    1. Yerel UPS şubesini (ülkeye/bölgeye özel ücretsiz hatlar) arayın.
    2. Çağrınızda, basılı etiketinizde gösterildiği gibi ters Sevkiyat izleme numarasını tırnak içine edin.
    3. İzleme numarası tırnak işareti yoksa, UPS çekme sırasında ek ücret ödemeniz gerekir.

    Toplama zamanlamak yerine, en yakın bırakma konumundaki Azure Stack Edge Pro 'Yu da kapatabilirsiniz.

## <a name="delete-the-resource"></a>Kaynağı Sil

Cihaz Azure veri merkezinde alındıktan sonra, cihaz hasar veya herhangi bir değişiklik işareti için denetlenir.

- Cihaz bozulmadan ve iyi bir şekilde yer alıyorsa, faturalandırma ölçümü o kaynak için de duraklar. Azure Stack Edge Pro işlemler ekibi, cihazın döndürüldüğünden emin olmak için sizinle iletişim kuracaktır. Daha sonra Azure portal cihazla ilişkili kaynağı silebilirsiniz.
- Cihaz önemli ölçüde hasar görmüşse, cezaları uygulanabilir. Ayrıntılar için bkz. [kayıp veya hasarlı cihaz](https://azure.microsoft.com/pricing/details/databox/edge/) ve [ürün hizmet koşulları](https://www.microsoft.com/licensing/product-licensing/products)hakkında SSS.  


Azure portal cihazı silebilirsiniz:

- Sipariş ettikten sonra ve cihaz Microsoft tarafından hazırlanmadan önce.
- Bir cihazı Microsoft 'a döndürdüyseniz ve Azure Stack Edge Pro işlemler ekibi, cihazın döndürüldüğünü onaylamak için çağırılır. Döndürülen cihaz, Azure veri merkezinde fiziksel incelemeyi geçirene kadar işlemler ekibi çağrı yapmaz.

Cihazı başka bir abonelik veya konuma karşı etkinleştirdiyseniz, Microsoft siparişinizi bir iş günü içinde yeni abonelik veya konuma taşır. Sipariş taşındıktan sonra bu kaynağı silebilirsiniz.


Azure portal, cihazı ve kaynağı silmek için aşağıdaki adımları uygulayın.

1. Azure portal, kaynağına gidin ve **genel bakış**' a bakın. Komut çubuğundan **Sil**' i seçin.

    ![Sil ' i seçin](media/azure-stack-edge-return-device/delete-resource-1.png)

2. **Cihazı Sil** dikey penceresinde, silmek istediğiniz cihazın adını yazın ve **Sil**' i seçin.

    ![Silmeyi onayla](media/azure-stack-edge-return-device/delete-resource-2.png)

Cihaz ve ilişkili kaynak başarıyla silindikten sonra size bildirimde bulunuluyoruz.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro cihazının yerini almayı](azure-stack-edge-replace-device.md)öğrenin.