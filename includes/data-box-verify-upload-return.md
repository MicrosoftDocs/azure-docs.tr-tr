---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/30/2019
ms.author: alkohli
ms.openlocfilehash: ca7b83d24f2416b224963559361faf5a7775cd0d
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631554"
---
Microsoft cihazı alıp taradığında, sipariş durumu **Alındı** olarak güncelleştirilir. Ardından cihaz hasar veya kurcalama belirtileri için fiziksel doğrulama sürecinden geçer.

Doğrulama tamamlandıktan sonra Data Box, Azure veri merkezindeki ağa bağlanır. Veri kopyalaması otomatik olarak başlar. Verilerin boyutuna bağlı olarak, kopyalama işleminin tamamlanması birkaç saatten bir güne kadar sürebilir. Kopyalama işinin ilerleme durumunu portalda izleyebilirsiniz.

Kopyalama tamamlandıktan sonra, sipariş durumu **Tamamlandı** olarak güncelleştirilir.

Kaynaktan silmeden önce verilerinizin Azure’a yüklendiğinden emin olun. Verileriniz şuralarda olabilir:

- Azure Depolama hesaplarınız. Data Box'a veri kopyaladığınızda, bu veriler Azure Depolama hesabınızda aşağıdaki yollardan birine yüklenir:

  - Blok blobları ve sayfa blobları için: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Dosyaları için: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternatif olarak Azure portalda Azure depolama hesabınıza gidip oradan ilerleyebilirsiniz.

- Yönetilen disk kaynak gruplarınız. Yönetilen diskler oluştururken, VHD'ler sayfa blobları olarak yüklenir ve ardından yönetilen disklere dönüştürülür. Yönetilen diskler, sipariş oluşturma sırasında belirtilen kaynak gruplarına iliştirilir. 

    - Azure'da yönetilen disklere kopyalama işleminiz başarılı olduysa, Azure portalındaki **Sipariş ayrıntıları** ’na gidip yönetilen diskler için belirtilen kaynak gruplarını not alabilirsiniz.

        ![Yönetilen disk kaynak gruplarını tanımlama](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        Belirtilen kaynak grubuna gidin ve yönetilen disklerinizi bulun.

        ![Kaynak gruplarına bağlı yönetilen disk](media/data-box-verify-upload-return/managed-disks-resource-group.png)

    - Bir VHDX veya dinamik ya da fark kayıt VHD'si kopyaladıysanız, VHDX veya VHD bir sayfa blobu olarak hazırlama depolama hesabına yüklenir ancak VHD'nin yönetilen diske dönüştürülmesi başarısız olur. Hazırlama **Depolama hesabı > Bloblar** ’a gidin ve uygun kapsayıcıyı seçin: Standart SSD, Standart HDD veya Premium SSD. VHD'ler, hazırlama depolama hesabınıza sayfa blobları olarak yüklenir ve ücret doğurur.


## <a name="erasure-of-data-from-data-box"></a>Data Box'tan verileri silme
 
Veriler Azure'a yüklendikten sonra Data Box disklerindeki veriyi [NIST SP 800-88 Revision 1 yönergelerine](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) uygun şekilde siler.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdaki Azure Data Box konularını öğrendiniz:

> [!div class="checklist"]
> * Önkoşullar
> * Göndermeye hazırlama
> * Data Box'ı Microsoft'a gönderme
> * Azure'a verilerin yüklendiğini doğrulama
> * Data Box'tan verileri silme

Data Box’ı yerel web arabirimini kullanarak yönetmeyi öğrenmek için şu makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Azure Data Box'ı yönetmek için yerel web arabirimini kullanma](../articles/databox/data-box-local-web-ui-admin.md)