---
title: Kuralları yapılandırma ve uyarıları yönetme
description: Farmtempts 'de kuralların nasıl yapılandırılacağını ve uyarıların nasıl yönetileceğini açıklar
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: a04f973cbfa3a68016065f50e9e2ff4f7566da94
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182936"
---
# <a name="configure-rules-and-manage-alerts"></a>Kuralları yapılandırma ve uyarıları yönetme

Azure Farmtempler, grubunuzda dağıtılan sensörlerden ve cihazlardan akan algılayıcı verilerine ek olarak iş mantığını temel alan kurallar oluşturmanıza olanak sağlar. Kurallar, algılayıcı değeri bir eşik değeri arasında olduğunda sistemdeki uyarıları tetikler. Eşik değerlerinden sonra oluşturulan uyarıları görüntüleyerek ve çözümleyerek, herhangi bir sorunla ilgili olarak hızlıca işlem yapabilir ve gerekli çözümleri alabilirsiniz.

## <a name="create-rule"></a>Kural Oluştur

1. Giriş sayfasında, **kurallar**' a gidin.
2. **Yeni kural**' ı seçin. Yeni kural penceresi görüntülenir.

    ![Yeni kural düğmesini ve yeni kural bölümünü vurgulayan ekran görüntüsü.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. **Kural adını** ve **Kural açıklamasını** girip **Grup Seç** açılan menüsünden bir grup seçin.
4. Grup ve **koşullar** bölümünün aynı pencerede göründüğünü seçmek için Grup adınızı yazın.  

    ![Koşullar bölümünü vurgulayan ekran görüntüsü.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. **Koşullar**' da, **Ölçü**, **işleç** ve **değer** değerlerini girin.
6. Ölçü adı ' nı **Ölçü** açılan menüsünde yazın.
7. Kurala daha fazla koşul eklemek için **+ Koşul Ekle** ' yi seçin.
8. **Önem derecesini** seçin.
9. **Eylem**' de e-posta uyarılarını etkinleştirmek Için **e-posta etkin** geçiş düğmesi ' ne geçin.

    ![E-posta etkin seçeneğini gösteren ekran görüntüsü.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. E-posta ile **ilgili** uyarı göndermek istediğiniz e-posta **adreslerini** ve **ek notları** girin.  
11. Kural **durumu**' nda, kuralı etkinleştirmek veya devre dışı bırakmak için **etkin** geçiş düğmesi ' ne geçin.
    Kuraldan etkilenecek cihaz sayısını görüntüleyebilirsiniz.
12. Kuralı oluşturmak için **Uygula** ' yı seçin.

## <a name="view-rule"></a>Kuralı görüntüle

**Grup** sayfası kullanılabilir kuralların listesini görüntüler. Bir **kural adı** seçin. Bir pencerede seçili kural için geçerli olan aşağıdaki ayrıntılar görüntülenir:
 - Kural adı
 - Kuralın ilişkilendirildiği gruba bağlantı
 - Oluşturulma tarihi
 - Son güncelleme tarihi
 - Önem derecesi
 - Kural durumu
 - Koşulların listesi  
 - Kuraldan etkilenen cihaz sayısı

    ![Kural ayrıntıları ekranını gösteren ekran görüntüsü.](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Kuralı düzenleme

Bir kuralı düzenlemek için aşağıdaki adımları izleyin:

1. Giriş sayfasında, sol gezinti menüsünden **kurallar** ' ı seçin.
   Kurallar penceresi görüntülenir.
2. Düzenlemek istediğiniz kuralı seçin.

    ![Seçili kuralı gösteren ekran görüntüsü.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Eylem çubuğundan **Düzenle** ' yi seçin, **kural düzenleme** penceresi görüntülenir.

    ![Kural düzenleme ekranını gösteren ekran görüntüsü.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. **Kural adını** ve **Kural açıklamasını** değiştirip **Grup Seç** açılan menüsünden bir grup seçin.
5. Grubu seçmek için Grup adınızı yazın ve **Koşulları** aynı pencerede görüntülenir.  
6. **Koşullarda**, **ölçüyü**, **işleci** ve **değeri** düzenleyin.
7. Ölçü adı ' nı **Ölçü** açılan menüsünde yazın.
8. Kurallara koşul eklemek/kuralları düzenlemek için **+ Koşul Ekle** ' yi seçin.

    ![Koşul Ekle düğmesini vurgulayan ekran görüntüsü.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  **Önem derecesini** seçin.  
10. **Eylem**' de e-posta uyarılarını etkinleştirmek Için **e-posta etkin** geçiş düğmesi ' ne geçin.
11. E-posta uyarısını, e-posta **konusunu** ve **ek notları** göndermek istediğiniz **e-posta adreslerini** düzenleyin.  
12. Kural **durumu**' nda, kuralı etkinleştirmek veya devre dışı bırakmak için **etkin** geçiş düğmesi ' ne geçin.
Bu kuraldan etkilenecek cihaz sayısını görüntüleyebilirsiniz.
13. Kuralı düzenlemek için **Uygula** ' yı seçin.

## <a name="change-rule-status"></a>Kural durumunu değiştir

Bir kuralın durumunu değiştirmek için şu adımları izleyin:

1. Giriş sayfasında, sol gezinti menüsünden **kurallar** ' ı seçin. Kurallar penceresi görüntülenir.
2. Durumunu değiştirmek istediğiniz kuralı seçin.

    ![Durumu Değiştir düğmesini gösteren ekran görüntüsü.](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Eylem çubuğundan **Durumu Değiştir** ' i seçin. **Durumu Değiştir** penceresi görüntülenir.

    ![Değişiklik durumu ekranını gösteren ekran görüntüsü.](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. **Durum değiştirme** Değiştir düğmesini kullanarak kural durumunu değiştirin.
   Kuraldan etkilenecek cihaz sayısını görüntüleyebilirsiniz.
4. Kuralın durumunu değiştirmek için **Uygula** ' yı seçin.

## <a name="delete-rule"></a>Kuralı silme

Bir kuralı silmek için şu adımları izleyin:

1. Giriş sayfasında, sol gezinti menüsünden **kurallar** ' ı seçin. Kurallar penceresi görüntülenir.
2. Silmek istediğiniz kuralı seçin.

    ![Sil düğmesini vurgulayan ekran görüntüsü.](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Eylem çubuğundan **Sil** ' i seçin.

    ![Proje grubu ları](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. **Kuralı Sil** iletişim kutusu görüntülenir. **Sil**’i seçin.
