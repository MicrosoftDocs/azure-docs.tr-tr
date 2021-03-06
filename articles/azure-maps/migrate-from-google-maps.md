---
title: Öğretici-Google Maps 'tan Azure Maps 'e geçiş | Microsoft Azure haritaları
description: Google Maps 'tan Microsoft Azure Maps 'e geçiş hakkında öğretici. Yönergeler, Azure Maps API 'Lerine ve SDK 'larına nasıl geçlenebileceğine kılavuzluk eder.
author: rbrundritt
ms.author: richbrun
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6241f6156b01c3c90f00578ae5416e4e77270930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386807"
---
# <a name="tutorial-migrate-from-google-maps-to-azure-maps"></a>Öğretici: Google Maps 'tan Azure Maps 'e geçiş

Bu makalede, Google Maps 'tan Web, mobil ve sunucu tabanlı uygulamaların Microsoft Azure haritalar platformuna nasıl geçirileceğiyle ilgili Öngörüler sunulmaktadır. Bu öğretici, Azure Maps 'a geçiş için karşılaştırılma kodu örnekleri, geçiş önerileri ve en iyi yöntemleri içerir. Bu öğreticide şunları öğreneceksiniz:

> [!div class="checklist"]
> * Azure haritalar 'da bulunan eşdeğer Google Maps özellikleri için üst düzey karşılaştırma.
> * Hangi lisans farklılıkları göz önünde bulundurulmalıdır.
> * Geçişinizi planlayın.
> * Teknik kaynakların ve desteğin nerede bulunacağı.

## <a name="prerequisites"></a>Önkoşullar

1. [Azure portalında](https://portal.azure.com) oturum açın. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
2. [Azure haritalar hesabı oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)
3. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure haritalar 'da kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure haritalar 'da kimlik doğrulamasını yönetme](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Azure haritalar platformuna genel bakış

Azure haritalar, geliştiricilerin tüm sektörler açısından güçlü Jeo uzamsal yetenekler sağlar. Bu yetenekler, Web ve mobil uygulamalar için coğrafi bağlam sağlamak amacıyla düzenli olarak güncelleştirilmiş harita verileriyle paketlenmiştir. Azure haritalar 'ın Azure One API uyumlu bir REST API kümesi vardır. REST API 'Leri, haritalar Işleme, arama, yönlendirme, trafik, saat dilimi, coğrafi konum, bölge sınırlama, harita verileri, hava durumu, taşınabilirlik ve uzamsal Işlemler sunar. Hem Web hem de Android SDK 'larının yanı sıra, geliştirme işlemlerini kolay, esnek ve birden çok platformda taşınabilir hale getirebilirsiniz.

## <a name="high-level-platform-comparison"></a>Üst düzey platform karşılaştırması

Tablo, Google Maps özelliklerine karşılık gelen Azure Maps özelliklerinin üst düzey bir listesini sağlar. Bu liste Azure Maps özelliklerinin tümünü göstermez. Ek Azure haritaları özellikleri şunlardır: erişilebilirlik, bölge sınırlaması, ısodenes, uzamsal işlemler, doğrudan harita kutucuk erişimi, Batch Hizmetleri ve veri kapsamı karşılaştırmaları (yani, ımatay kapsamı).

| Google Haritalar özelliği         | Azure haritalar desteği                     |
|-----------------------------|:--------------------------------------:|
| Web SDK’sı                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Planlandı                                |
| REST hizmeti API 'Leri           | ✓                                      |
| Yönler (Yönlendirme)        | ✓                                      |
| Uzaklık matrisi             | ✓                                      |
| Nedeniyle                   | ✓ (Önizleme)                            |
| Coğrafi kodlama (Ileri/ters) | ✓                                      |
| Coğrafi Konum                 | Yok                                    |
| En yakın yollar               | ✓                                      |
| Konum arama               | ✓                                      |
| Yer ayrıntıları              | Yok – Web sitesi & telefon numarası kullanılabilir |
| Fotoğrafları yerleştir               | Yok                                    |
| Otomatik tamamlamayı yerleştir          | ✓                                      |
| Yola yasla                | ✓                                      |
| Hız sınırları                | ✓                                      |
| Statik haritalar                 | ✓                                      |
| Statik cadde görünümü          | Yok                                    |
| Saat Dilimi                   | ✓                                      |
| Harita katıştırılmış API           | Yok                                    |
| Harita URL 'Leri                    | Yok                                    |

Google Maps temel anahtar tabanlı kimlik doğrulaması sağlar. Azure haritalar hem temel anahtar tabanlı kimlik doğrulaması hem de Azure Active Directory kimlik doğrulaması sağlar. Azure Active Directory kimlik doğrulaması, temel anahtar tabanlı kimlik doğrulamaya kıyasla daha fazla güvenlik özelliği sağlar.

## <a name="licensing-considerations"></a>Lisanslama konuları

Google Maps 'lerden Azure Maps 'a geçiş yaparken lisanslama hakkında aşağıdaki noktaları göz önünde bulundurun.

* Azure Maps, yüklü harita kutucuklarının sayısına bağlı olarak etkileşimli haritalar kullanımına yönelik ücretler ücretlendirir. Diğer yandan, Google Haritalar harita denetimini yükleme ücretini ücretlendirir. Etkileşimli Azure Haritalar SDK 'lerinde, eşleme kutucukları, geliştirme maliyetini azaltmak için otomatik olarak önbelleğe alınır. Yüklenen her 15 harita kutucuğu için bir Azure haritalar işlemi oluşturulur. Etkileşimli Azure Haritalar SDK 'Ları 512 piksellik kutucukları kullanır ve ortalama olarak sayfa görünümü başına bir veya daha az işlem oluşturur.
* Genellikle, Azure Maps web SDK ile Google Maps Web hizmetlerinden statik harita görüntülerini değiştirme maliyeti de geçerli olur. Azure Haritalar Web SDK 'Sı harita kutucukları kullanır. Kullanıcı haritayı yakınlaştırıp yakınlaşmadığı takdirde, hizmet genellikle harita yükü başına bir işlemin bir bölümünü oluşturur. Azure Haritalar Web SDK 'Sı isterseniz, kaydırma ve yakınlaştırmayı devre dışı bırakma seçenekleri vardır. Ayrıca, Azure Maps web SDK 'Sı, statik harita Web hizmetinden daha fazla sayıda görselleştirme seçeneği sağlar.
* Azure haritalar, platformdaki verilerin Azure 'da depolanmasını sağlar. Ayrıca, veriler, [kullanım koşullarına](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)göre altı aya kadar bir yerde önbelleğe alınabilir.

Azure haritalar için bazı ilgili kaynaklar aşağıda verilmiştir:

* [Azure haritalar fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/azure-maps/)
* [Azure fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
* [Azure Haritalar kullanım koşulları](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (Microsoft Online Services koşulları 'nda bulunur)
* [Azure haritalar 'da doğru fiyatlandırma katmanını seçin](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Önerilen geçiş planı

Aşağıda, üst düzey bir geçiş planı verilmiştir.

1. Uygulamanızın kullandığı Google Maps SDK 'Ları ve hizmetlerini envanterini alın. Azure haritalar 'ın alternatif SDK 'lar ve hizmetler sağladığını doğrulayın.
2. Henüz bir aboneliğiniz yoksa, bir Azure aboneliği oluşturun [https://azure.com](https://azure.com) .
3. Azure haritalar hesabı ([Belgeler](./how-to-manage-account-keys.md)) ve kimlik doğrulama anahtarı veya Azure Active Directory ([Belgeler](./how-to-manage-authentication.md)) oluşturun.
4. Uygulama kodunuzu geçirin.
5. Geçirilen uygulamanızı test edin.
6. Geçirilen uygulamanızı üretime dağıtın.

## <a name="create-an-azure-maps-account"></a>Azure Haritalar hesabı oluşturma

Bir Azure Maps hesabı oluşturmak ve Azure haritalar platformuna erişmek için şu adımları izleyin:

1. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
2. [Azure portalında](https://portal.azure.com/) oturum açın.
3. [Azure haritalar hesabı](./how-to-manage-account-keys.md)oluşturun. 
4. Gelişmiş güvenlik için [Azure Maps abonelik anahtarınızı](./how-to-manage-authentication.md#view-authentication-details) veya kurulum Azure Active Directory kimlik doğrulamasını alın.

## <a name="azure-maps-technical-resources"></a>Azure haritalar teknik kaynakları

Azure haritalar için yararlı teknik kaynakların listesi aşağıda verilmiştir.

- Bakýþ [https://azure.com/maps](https://azure.com/maps)
- Belgelerle [https://aka.ms/AzureMapsDocs](./index.yml)
- Web SDK kodu örnekleri: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Geliştirici forumları: [https://aka.ms/AzureMapsForums](/answers/topics/azure-maps.html)
- Larınız [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Lenemeyen [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Teknik blog: [https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Azure haritalar geri bildirimi (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Azure haritalar Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

## <a name="migration-support"></a>Geçiş desteği

Geliştiriciler, [Forum](/answers/topics/azure-maps.html) aracılığıyla veya birçok Azure destek seçeneğinden biri aracılığıyla geçiş desteği arayabilir: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Temizlenecek kaynak yok.

## <a name="next-steps"></a>Sonraki adımlar

Google Maps uygulamanızı şu makalelerle nasıl geçirileceğiyle ilgili ayrıntıları öğrenin:

> [!div class="nextstepaction"]
> [Web uygulamasını geçirme](migrate-from-google-maps-web-app.md)
