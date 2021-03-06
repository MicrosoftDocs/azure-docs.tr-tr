---
title: Azure portal (Önizleme) içindeki sorgu düzenleyicisini kullanarak bir SQL veritabanını sorgulama
description: Azure SQL veritabanı 'ndaki bir veritabanında Transact-SQL (T-SQL) sorgularını çalıştırmak için sorgu düzenleyicisini nasıl kullanacağınızı öğrenin.
titleSuffix: Azure SQL Database
keywords: SQL veritabanı 'na bağlanma, SQL veritabanı, Azure Portal, Portal, sorgu Düzenleyicisi 'ni sorgulama
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, contperf-fy21q3-portal
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: a6f13e27a5aa2684a16565c616d781e3d5c3a750
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594198"
---
# <a name="quickstart-use-the-azure-portals-query-editor-preview-to-query-an-azure-sql-database"></a>Hızlı başlangıç: Azure SQL veritabanını sorgulamak için Azure portal sorgu düzenleyicisini (Önizleme) kullanın
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Sorgu Düzenleyicisi, Azure SQL veritabanı veya Azure SYNAPSE Analytics 'teki veri ambarı 'nda veritabanınıza SQL sorguları çalıştırmak için Azure portal bir araçtır.

Bu hızlı başlangıçta, bir veritabanında Transact-SQL (T-SQL) sorgularını çalıştırmak için sorgu düzenleyicisini kullanacaksınız.

## <a name="prerequisites"></a>Önkoşullar

### <a name="create-a-database-with-sample-data"></a>Örnek verilerle veritabanı oluşturma

Bu hızlı başlangıcı tamamlamak AdventureWorksLT örnek veritabanı gerektirir. SQL veritabanında AdventureWorksLT örnek veritabanının çalışan bir kopyasına sahip değilseniz, aşağıdaki hızlı başlangıç hızlı bir şekilde bir tane oluşturmanıza yardımcı olur:

[Hızlı başlangıç: Azure portal, PowerShell veya Azure CLı kullanarak Azure SQL veritabanı 'nda veritabanı oluşturma](single-database-create-quickstart.md)

### <a name="set-an-azure-active-directory-admin-for-the-server-optional"></a>Sunucu için Azure Active Directory Yöneticisi ayarlama (isteğe bağlı)

Azure Active Directory (Azure AD) Yöneticisi yapılandırmak, Azure portal ve veritabanınızda oturum açmak için tek bir kimlik kullanmanıza olanak sağlar. Sorgu düzenleyicisine bağlanmak için Azure AD kullanmak istiyorsanız, aşağıdaki adımları izleyin.

Bu işlem isteğe bağlıdır, bunun yerine sorgu düzenleyicisine bağlanmak için SQL kimlik doğrulaması kullanabilirsiniz.

> [!NOTE]
> * E-posta hesapları (örneğin, outlook.com, gmail.com, yahoo.com, vb.) henüz Azure AD yöneticileri olarak desteklenmez. Azure AD 'de yerel olarak oluşturulmuş veya Azure AD 'de Federasyon oluşturulmuş bir Kullanıcı seçtiğinizden emin olun.
> * Azure AD yönetici oturum açma, 2 öğeli kimlik doğrulaması etkin olan hesaplarla birlikte çalışarak, sorgu Düzenleyicisi 2 öğeli kimlik doğrulamayı desteklemez.

1. Azure portal, SQL veritabanı sunucunuza gidin.

2. **SQL Server** menüsünde **Active Directory yönetici**' yi seçin.

3. SQL Server **Active Directory yönetici** sayfası araç çubuğunda **yönetici ayarla**' yı seçin.

    ![active directory seçme](./media/connect-query-portal/select-active-directory.png)

4. **Yönetici Ekle** sayfasında, ara kutusuna bulunacak Kullanıcı veya grubu girin, yönetici olarak seçin ve ardından **Seç** düğmesini seçin.

5. SQL Server **Active Directory yönetici** sayfası araç çubuğuna geri döndüğünüzde **Kaydet**' i seçin.

## <a name="using-sql-query-editor"></a>SQL sorgu düzenleyicisini kullanma

1. [Azure Portal](https://portal.azure.com/) oturum açın ve sorgulamak istediğiniz veritabanını seçin.

2. **SQL veritabanı** menüsünde **sorgu Düzenleyicisi 'ni (Önizleme)** seçin.

    ![sorgu düzenleyicisini bulma](./media/connect-query-portal/find-query-editor.PNG)

### <a name="establish-a-connection-to-the-database"></a>Veritabanıyla bağlantı kurun

Portalda oturum açmış olsanız da, veritabanına erişmek için kimlik bilgilerini sağlamanız gerekir. Veritabanınıza bağlanmak için, SQL kimlik doğrulaması veya Azure Active Directory kullanarak bağlanabilirsiniz.

#### <a name="connect-using-sql-authentication"></a>SQL Kimlik Doğrulaması kullanarak bağlanma

1. **Oturum açma** sayfasında, **SQL Server kimlik doğrulaması**' nın altında, veritabanına erişimi olan bir Kullanıcı Için **oturum açma** ve **parola** girin. Emin değilseniz, veritabanının sunucusunun sunucu yöneticisi için oturum açma ve parolayı kullanın.

    ![oturum aç](./media/connect-query-portal/login-menu.png)

2. **Tamam**’ı seçin.

#### <a name="connect-using-azure-active-directory"></a>Azure Active Directory kullanarak bağlanma

**Sorgu Düzenleyicisi 'nde (Önizleme)**, **Active Directory kimlik doğrulaması** bölümünde **oturum açma** sayfasına bakın. Kimlik doğrulaması otomatik olarak gerçekleşir, bu nedenle veritabanına bir Azure AD yöneticisiyseniz, oturum açmış olduğunuz söyleyen bir ileti görürsünüz. Sonra **farklı devam et** *\<your user or group ID>* düğmesini seçin. Sayfada başarıyla oturum açmadıysanız, sayfayı yenilemeniz gerekebilir.

### <a name="query-a-database-in-sql-database"></a>SQL veritabanında bir veritabanını sorgulama

Aşağıdaki örnek sorgular AdventureWorksLT örnek veritabanında başarıyla çalışmalıdır.

#### <a name="run-a-select-query"></a>Seçme sorgusu çalıştırma

1. Aşağıdaki sorguyu sorgu düzenleyicisine yapıştırın:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. **Çalıştır** ' ı seçin ve ardından **sonuçlar** bölmesinde çıktıyı gözden geçirin.

   ![sorgu düzenleyicisi sonuçları](./media/connect-query-portal/query-editor-results.png)

3. İsteğe bağlı olarak, sorguyu bir. SQL dosyası olarak kaydedebilir veya döndürülen verileri bir. JSON,. csv veya. xml dosyası olarak dışarı aktarabilirsiniz.

#### <a name="run-an-insert-query"></a>EKLEME sorgusu Çalıştır

Tabloya yeni bir ürün eklemek için aşağıdaki [Insert](/sql/t-sql/statements/insert-transact-sql/) T-SQL ifadesini çalıştırın `SalesLT.Product` .

1. Önceki sorguyu bununla değiştirin.

    ```sql
    INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
    VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. Tabloya yeni bir satır eklemek için **Çalıştır**  ' ı seçin `Product` . **İletiler** bölmesi **sorgu başarılı: etkilenen satırlar: 1 ' i** görüntüler.


#### <a name="run-an-update-query"></a>GÜNCELLEŞTIRME sorgusu çalıştırma

Yeni ürününüzü değiştirmek için aşağıdaki [Update](/sql/t-sql/queries/update-transact-sql/) T-SQL ifadesini çalıştırın.

1. Önceki sorguyu bununla değiştirin.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Tablodaki belirtilen satırı güncelleştirmek için **Çalıştır** ' ı seçin `Product` . **İletiler** bölmesi **sorgu başarılı: etkilenen satırlar: 1 ' i** görüntüler.

#### <a name="run-a-delete-query"></a>SILME sorgusu Çalıştır

Yeni ürününüzü kaldırmak için aşağıdaki [Delete](/sql/t-sql/statements/delete-transact-sql/) T-SQL ifadesini çalıştırın.

1. Önceki sorguyu bununla değiştirin:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Tablodaki belirtilen satırı silmek için **Çalıştır** ' ı seçin `Product` . **İletiler** bölmesi **sorgu başarılı: etkilenen satırlar: 1 ' i** görüntüler.


## <a name="troubleshooting-and-considerations"></a>Sorun giderme ve önemli noktalar

Sorgu Düzenleyicisi ile çalışırken bilmemiz gereken birkaç nokta vardır.

### <a name="configure-local-network-settings"></a>Yerel ağ ayarlarını yapılandırma

Sorgu düzenleyicisinde aşağıdaki hatalardan birini alırsanız:
 - *Yerel Ağ ayarlarınız sorgu düzenleyicisinin sorguları vermesini engelleyebilir. Ağ ayarlarınızı yapılandırma yönergeleri için lütfen buraya tıklayın*
 - *Sunucuyla bağlantı kurulamadı. Bu, yerel güvenlik duvarı yapılandırmanızla veya ağ ara sunucu ayarlarınızda bir sorun olduğunu gösterebilir*

Bunun nedeni, sorgu düzenleyicisinin iletişim kurmak için 443 ve 1443 bağlantı noktasını kullanmaidir. Bu bağlantı noktalarında giden HTTPS trafiğini etkinleştirmiş olduğunuzdan emin olmanız gerekir. Aşağıdaki yönergeler, Işletim sisteminize bağlı olarak bunu nasıl yapacağınız konusunda size kılavuzluk eder. Bu bağlantıyı yerel ağınızda açmaya yönelik onay vermek için kurumsal BT ile çalışmanız gerekebilir.

#### <a name="steps-for-windows"></a>Windows için adımlar

1. **Windows Defender güvenlik duvarını** aç
2. Sol taraftaki menüde **Gelişmiş ayarlar** ' ı seçin.
3. **Gelişmiş Güvenlik Özellikli Windows Defender güvenlik duvarı**'nda, sol taraftaki menüdeki **giden kurallar** ' ı seçin.
4. Sağ taraftaki menüde **Yeni kural...** öğesini seçin

**Yeni giden kuralı sihirbazında** şu adımları izleyin:

1. Oluşturmak istediğiniz kural türü olarak **bağlantı noktası** ' nı seçin. **İleri**’yi seçin
2. **TCP** seçin
3. **Belirli uzak bağlantı noktalarını** seçin ve "443, 1443" yazın. Ardından **İleri** ' yi seçin
4. "Güvenliyse bağlantıya Izin ver" seçeneğini belirleyin
5. **İleri** ' yi ve **ardından yeniden Seç ' i seçin**
5. "Etki alanı", "özel" ve "genel" hepsini seçili tut
6. Kurala bir ad verin (örneğin, "Azure SQL sorgu Düzenleyicisi 'ne erişin") ve isteğe bağlı olarak bir açıklama. Ardından **son** ' u seçin

#### <a name="steps-for-mac"></a>Mac için adımlar
1. **Sistem tercihlerini** açın (Apple menü > Sistem Tercihleri).
2. **Güvenlik & gizliliği**' ne tıklayın.
3. **Güvenlik duvarı**' na tıklayın.
4. Güvenlik duvarı kapalıysa, en altta değişiklik yapmak ve **güvenlik duvarını Aç** ' ı seçmek **Için kilidi tıklatın '** ı seçin.
4. **Güvenlik duvarı seçenekleri**' ne tıklayın.
5. **Güvenlik & gizlilik** penceresinde şu seçeneği belirleyin: "imzalı yazılımın gelen bağlantıları almasına otomatik olarak izin ver."

#### <a name="steps-for-linux"></a>Linux için adımlar
Iptables 'ı güncelleştirmek için şu komutları çalıştırın
  ```
  sudo iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT
  sudo iptables -A OUTPUT -p tcp --dport 1443 -j ACCEPT
  ```

### <a name="connection-considerations"></a>Bağlantı konuları

* Sorgu Düzenleyicisi 'ne genel bağlantılar için, veritabanlarına ve veri ambarlarınıza erişmek üzere [sunucunun izin verilen güvenlik duvarı kurallarına gıden IP adresini eklemeniz](firewall-create-server-level-portal-quickstart.md) gerekir.

* Sunucuda ayarlanmış bir özel bağlantı bağlantınız varsa ve özel sanal ağdaki bir IP 'den sorgu düzenleyicisine bağlanıyorsanız, sorgu Düzenleyicisi, Istemci IP adresini SQL veritabanı sunucusu güvenlik duvarı kurallarına eklemesi gerekmeden çalışır.

* Sorgu düzenleyicisini kullanmak için gereken en temel RBAC izinleri sunucuya ve veritabanına yönelik okuma erişimidir. Bu erişim düzeyine sahip olan herkes sorgu Düzenleyicisi özelliğine erişebilir. Belirli kullanıcılarla erişimi sınırlandırmak için, Azure Active Directory veya SQL kimlik doğrulama kimlik bilgileriyle sorgu Düzenleyicisi 'nde oturum açabilmelerini engellemeniz gerekir. Kendilerini sunucu için AAD Yöneticisi olarak atayamazlar veya bir SQL yönetici hesabı ekleyemez, bu sorgular sorgu düzenleyicisini kullanamazlar.

* Sorgu Düzenleyicisi, veritabanına bağlanmayı desteklemiyor `master` .

* Sorgu Düzenleyicisi, ile bir çoğaltma veritabanına bağlanamaz `ApplicationIntent=ReadOnly`

* "X-CSRF-Signature üstbilgisi doğrulanamadı" hata iletisini görüyorsanız, sorunu çözmek için aşağıdaki eylemi gerçekleştirin:

    * Bilgisayarınızın saatinin doğru saat ve saat dilimine ayarlandığından emin olun. Ayrıca, örneğin Doğu ABD, Pasifik vb. gibi, örneğinizin konumunun saat dilimini arayarak, bilgisayarınızın saat dilimini Azure ile eşleştirmeye da çalışırsınız.
    * Bir ara sunucu kullanıyorsanız, "X-CSRF-Signature" istek üstbilgisinin değiştirilmediğinden veya bırakılmadığından emin olun.

### <a name="other-considerations"></a>Diğer önemli noktalar

* **F5** tuşuna basıldığında sorgu Düzenleyicisi sayfası yenilenir ve üzerinde çalışılan sorgular kaybolur.

* Sorgu yürütmesi için 5 dakikalık bir zaman aşımı vardır.

* Sorgu Düzenleyicisi, Coğrafya veri türleri için yalnızca silindir projeksiyonu destekler.

* Veritabanı tabloları ve görünümleri için IntelliSense desteği yoktur, ancak düzenleyici zaten girilmiş adlarla otomatik tamamlamayı destekler.

## <a name="next-steps"></a>Sonraki adımlar

Azure SQL veritabanı 'nda desteklenen Transact-SQL (T-SQL) hakkında daha fazla bilgi edinmek için bkz. [SQL veritabanına geçiş sırasında Transact-SQL farklılıklarını çözümleme](transact-sql-tsql-differences-sql-server.md).
