---
title: Azure SQL Edge 'in desteklenen özellikleri
description: Azure SQL Edge tarafından desteklenen özelliklerin ayrıntıları hakkında bilgi edinin.
keywords: SQL Edge 'e giriş, SQL Edge nedir, SQL Edge 'e genel bakış
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 19dcbbf102a1d8d21f1b14780ea33816a1677c55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392036"
---
# <a name="supported-features-of-azure-sql-edge"></a>Azure SQL Edge 'in desteklenen özellikleri 

Azure SQL Edge, SQL veritabanı altyapısının en son sürümü üzerine kurulmuştur. Linux üzerinde SQL Server 2019 ' de desteklenen özelliklerin bir alt kümesini destekler. Bu özellik, Linux üzerinde SQL Server 2019 ' de (veya Windows üzerinde SQL Server) desteklenmeyen veya mevcut bazı özelliklere ek olarak.

Linux üzerinde SQL Server desteklenen özelliklerin kapsamlı bir listesi için bkz. [Linux üzerinde SQL Server 2019 'Nin sürümleri ve desteklenen özellikleri](/sql/linux/sql-server-linux-editions-and-components-2019). Windows üzerinde SQL Server sürümleri ve desteklenen özellikleri için, bkz. [sürümler ve desteklenen özellikler SQL Server 2019 (15. x)](/sql/sql-server/editions-and-components-of-sql-server-version-15).

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge sürümleri

Azure SQL Edge, iki farklı sürüm veya yazılım planlarıyla kullanılabilir. Bu sürümler aynı özellik kümelerine sahiptir ve yalnızca kullanım haklarının yanı sıra, ana bilgisayar sisteminde erişebileceği bellek ve çekirdek miktarına göre farklılık gösterir.

   |**Plan**  |**Açıklama**  |
   |---------|---------|
   |Azure SQL Edge geliştiricisi  |  Yalnızca geliştirme için. Her Azure SQL Edge geliştirici kapsayıcısı 4 çekirdeğe ve 32 GB bellekle sınırlıdır.  |
   |Azure SQL Edge    |  Üretim için. Her Azure SQL Edge kapsayıcısı, en fazla 8 çekirdek ve 64 GB bellek ile sınırlıdır.  |

## <a name="operating-system"></a>İşletim sistemi

Azure SQL Edge kapsayıcıları Ubuntu 18,04 tabanlıdır ve bu nedenle yalnızca Ubuntu 18,04 LTS (önerilen) veya Ubuntu 20,04 LTS çalıştıran Docker konaklarında çalışmak üzere desteklenir. Azure SQL Edge kapsayıcılarını diğer işletim sistemi konaklarında çalıştırmak mümkündür. Örneğin, Linux veya Windows üzerinde (Docker CE veya Docker EE kullanarak) çalışabilir, ancak Microsoft bunu yapmanızı önermez, çünkü bu yapılandırma kapsamlı bir şekilde sınanmamıştır.

Windows üzerinde Azure SQL Edge çalıştırmak için önerilen yapılandırma Windows konakta bir Ubuntu VM 'yi yapılandırmak ve ardından Linux sanal makinesi içinde Azure SQL Edge 'i çalıştırmaktır.

Azure SQL Edge için önerilen ve desteklenen dosya sistemi, EXT4 ve XFS 'dir. Kalıcı birimler Azure SQL Edge veritabanı depolamasını geri yüklemek için kullanılıyorsa, temeldeki ana bilgisayar dosya sisteminin EXT4 ve XFS olması gerekir.

## <a name="hardware-support"></a>Donanım desteği

Azure SQL Edge, en az bir adet işlemci ve konakta bir GB RAM ile 64 bitlik bir işlemci (x64 veya ARM64) gerektirir. Azure SQL Edge 'in başlangıç belleği parmak izi 450MB 'a yakın olsa da, uç cihazda çalışan diğer IoT Edge modüller veya süreçler için ek bellek gerekir. Azure SQL Edge için gerçek bellek ve CPU gereksinimleri, işlenen veri yükünün ve hacminin karmaşıklığına göre değişir. Çözümünüz için bir donanım seçerken, Microsoft, çözümünüz için gerekli performans özelliklerinin karşılanmasını sağlamak üzere kapsamlı performans testlerini çalıştırmanızı önerir.  

## <a name="azure-sql-edge-components"></a>Azure SQL Edge bileşenleri

Azure SQL Edge yalnızca veritabanı altyapısını destekler. Windows üzerinde SQL Server 2019 ile veya Linux üzerinde SQL Server 2019 ile kullanılabilen diğer bileşenlere yönelik destek içermez. Azure SQL Edge, Analysis Services, Reporting Services, Integration Services, Ana Veri Hizmetleri, Machine Learning Services (-Database) ve Machine Learning Server (tek başına) gibi SQL Server bileşenleri desteklemez.

## <a name="supported-features"></a>Desteklenen özellikler

Azure SQL Edge, Linux üzerinde SQL Server özelliklerinin bir alt kümesini desteklemeye ek olarak aşağıdaki yeni özellikler için destek içerir: 

- Azure Stream Analytics destekleyen aynı altyapıyı temel alan SQL akışı, Azure SQL Edge 'de gerçek zamanlı veri akışı özellikleri sağlar. 
- Time-Series veri analizi için T-SQL işlev çağrısı `Date_Bucket` .
- SQL altyapısına dahil edilen ONNX çalışma zamanı aracılığıyla makine öğrenimi özellikleri.

## <a name="unsupported-features"></a>Desteklenmeyen özellikler

Aşağıdaki listede, Azure SQL Edge 'de Şu anda desteklenmeyen Linux özelliklerine yönelik SQL Server 2019 yer almaktadır.

| Alan | Desteklenmeyen Özellik veya hizmet |
|-----|-----|
| **Veritabanı tasarımı** | Bellek içi OLTP ve ilgili DDL komutları ve Transact-SQL işlevleri, katalog görünümleri ve dinamik yönetim görünümleri. |
| &nbsp; | `HierarchyID` veri türü ve ilgili DDL komutları ve Transact-SQL işlevleri, katalog görünümleri ve dinamik yönetim görünümleri. |
| &nbsp; | `Spatial` veri türü ve ilgili DDL komutları ve Transact-SQL işlevleri, katalog görünümleri ve dinamik yönetim görünümleri. |
| &nbsp; | DB ve ilgili DDL komutları ile Transact-SQL işlevleri, katalog görünümleri ve dinamik yönetim görünümlerini uzatın. |
| &nbsp; | Tam metin dizinleri ve arama ve ilgili DDL komutları ve Transact-SQL işlevleri, katalog görünümleri ve dinamik yönetim görünümleri.|
| &nbsp; | `FileTable`, `FILESTREAM` ve ile ılgılı DDL komutları ve Transact-SQL işlevleri, katalog görünümleri ve dinamik yönetim görünümleri.|
| **Veritabanı altyapısı** | Yinelemesi. Azure SQL Edge 'i bir çoğaltma topolojisinin gönderim abonesi olarak yapılandırabileceğinizi unutmayın. |
| &nbsp; | PolyBase. PolyBase 'de dış tablolar için Azure SQL Edge 'i bir hedef olarak yapılandırabileceğinizi unutmayın. |
| &nbsp; | Java ve Spark ile dil genişletilebilirliği. |
| &nbsp; | Active Directory tümleştirme. |
| &nbsp; | Veritabanı otomatik küçültme. Bir veritabanı için Otomatik küçültme özelliği, komutu kullanılarak ayarlanabilir `ALTER DATABASE <database_name> SET AUTO_SHRINK ON` , ancak bu değişikliğin etkisi yoktur. Otomatik küçültme görevi veritabanına karşı çalışmaz. Kullanıcılar, ' DBCC ' komutlarını kullanarak veritabanı dosyalarını daraltabilir. |
| &nbsp; | Veritabanı anlık görüntüleri. |
| &nbsp; | Kalıcı bellek desteği. |
| &nbsp; | Microsoft Dağıtılmış İşlem Düzenleyicisi. |
| &nbsp; | Kaynak idarecisi ve GÇ kaynak İdaresi. |
| &nbsp; | Arabellek havuzu uzantısı. |
| &nbsp; | Üçüncü taraf bağlantılarıyla dağıtılmış sorgu. |
| &nbsp; | Bağlı sunucular. |
| &nbsp; | Sistem genişletilmiş saklı yordamları (gibi `XP_CMDSHELL` ). |
| &nbsp; | CLR derlemeleri ve ilgili DDL komutları ve Transact-SQL işlevleri, katalog görünümleri ve dinamik yönetim görünümleri. |
| &nbsp; | ,, Ve gibi CLR 'ye bağımlı T-SQL `ASSEMBLYPROPERTY` işlevleri `FORMAT` `PARSE` `TRY_PARSE` . |
| &nbsp; | CLR bağımlı tarih ve saat Kataloğu görünümleri, işlevleri ve sorgu yan tümceleri. |
| &nbsp; | Arabellek havuzu uzantısı. |
| &nbsp; | Veritabanı posta. |
| &nbsp; | Hizmet Aracısı |
| &nbsp; | İlke tabanlı yönetim |
| &nbsp; | Yönetim veri ambarı |
| &nbsp; | Kapsanan veritabanları |
| **SQL Server Agent** |  Alt sistemler: CmdExec, PowerShell, Queue Reader, SSIS, SSAS ve SSRS. |
| &nbsp; | Larınız. |
| &nbsp; | Yönetilen yedekleme. |
| **Yüksek kullanılabilirlik** | Always on kullanılabilirlik grupları.  |
| &nbsp; | Temel kullanılabilirlik grupları. |
| &nbsp; | Her zaman yük devretme kümesi örneği. |
| &nbsp; | Veritabanı yansıtma. |
| &nbsp; | Etkin bellek ve CPU ekleme. |
| **Güvenlik** | Genişletilebilir anahtar yönetimi. |
| &nbsp; | Active Directory tümleştirme.|
| &nbsp; | Güvenli şifreleme desteği.|
| **Hizmetler** | SQL Server Browser. |
| &nbsp; | R ve Python aracılığıyla Machine Learning. |
| &nbsp; | Streamıntertim. |
| &nbsp; | Analysis Services. |
| &nbsp; | Raporlama Hizmetleri. |
| &nbsp; | Veri kalitesi hizmetleri. |
| &nbsp; | Ana Veri Hizmetleri. |
| &nbsp; | Dağıtılmış Yeniden Yürütme. |
| **Yönetilebilirlik** | Denetim noktası SQL Server Utility. |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL Edge 'i dağıtma](deploy-portal.md)
- [Azure SQL Edge 'i yapılandırma](configure.md)
- [SQL Server istemci araçlarını kullanarak Azure SQL Edge örneğine bağlanma](connect.md)
- [Azure SQL Edge 'de veritabanlarını yedekleme ve geri yükleme](backup-restore.md)