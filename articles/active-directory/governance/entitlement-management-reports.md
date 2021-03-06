---
title: Yetkilendirme yönetiminde raporları & günlükleri görüntüleme-Azure AD
description: Azure Active Directory yetkilendirme yönetiminde Kullanıcı atamaları raporu ve denetim günlüklerini görüntülemeyi öğrenin.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6eb44c1efd683b6febe9a355ef72c80cc6f2e40d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746635"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Azure AD Yetkilendirme Yönetimi 'nde raporları ve günlükleri görüntüleme

Azure AD Yetkilendirme Yönetimi raporları ve Azure AD denetim günlüğü, kullanıcıların erişebileceği kaynakları hakkında ek ayrıntılar sağlar. Yönetici olarak, bir kullanıcı için erişim paketlerini ve kaynak atamalarını görüntüleyebilir, denetim amaçlarıyla istek günlüklerini görüntüleyebilir veya bir kullanıcının isteğinin durumunu belirleyebilirsiniz. Bu makalede, Yetkilendirme Yönetimi raporlarının ve Azure AD denetim günlüklerinin nasıl kullanılacağı açıklanır.

Kullanıcılara Yetkilendirme Yönetimi 'nde hangi kaynakların erişimi olduğunu görüntüleme hakkında bilgi edinmek için aşağıdaki videoyu izleyin:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Bir kullanıcı için erişim paketlerini görüntüleme

Bu rapor, bir kullanıcının isteyebilmesini ve kullanıcıya şu anda atanmış olan erişim paketlerini listelemenize olanak sağlar.

**Önkoşul rolü:** Genel yönetici veya Kullanıcı Yöneticisi

1. **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

1. Sol taraftaki menüden **raporlar**' a tıklayın.

1. **Bir Kullanıcı Için erişim paketlerine** tıklayın.

1. Kullanıcıları Seç bölmesini açmak için **Kullanıcıları Seç** ' e tıklayın.

1. Listeden kullanıcıyı bulun ve ardından **Seç**' e tıklayın.

    **İstek** sekmesi, kullanıcının isteyeceği erişim paketlerinin bir listesini görüntüler. Bu liste, erişim paketleri için tanımlanan [istek ilkelerine](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) göre belirlenir. 

    ![Bir kullanıcı için paketlere erişim](./media/entitlement-management-reports/access-packages-report.png)

1. Bir erişim paketi için birden fazla kaynak rolü veya ilkesi varsa, seçim ayrıntılarını görmek için kaynak rolleri veya ilkeler girdisine tıklayın.

1. Kullanıcıya şu anda atanmış olan erişim paketlerinin listesini görmek için **atanan** sekmesine tıklayın. Bir kullanıcıya erişim paketi atandığında, kullanıcının erişim paketindeki tüm kaynak rollerine erişimi olduğu anlamına gelir.

## <a name="view-resource-assignments-for-a-user"></a>Bir kullanıcı için kaynak atamalarını görüntüleme

Bu rapor, Yetkilendirme Yönetimi 'nde bir kullanıcıya şu anda atanmış olan kaynakları listelemenize olanak sağlar. Bu raporun, yetkilendirme yönetimiyle yönetilen kaynaklara yönelik olduğunu unutmayın. Kullanıcının, dizininizdeki diğer kaynaklara yetkilendirme yönetimi dışında erişimi olabilir.

**Önkoşul rolü:** Genel yönetici veya Kullanıcı Yöneticisi

1. **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

1. Sol taraftaki menüden **raporlar**' a tıklayın.

1. **Bir Kullanıcı Için kaynak atamaları**' na tıklayın.

1. Kullanıcıları Seç bölmesini açmak için **Kullanıcıları Seç** ' e tıklayın.

1. Listeden kullanıcıyı bulun ve ardından **Seç**' e tıklayın.

    Kullanıcıya şu anda atanmış kaynakların bir listesi görüntülenir. Listede Ayrıca, erişim için başlangıç ve bitiş tarihi ile birlikte kaynak rolünü aldıkları erişim paketi ve ilke de gösterilir.
    
    Bir Kullanıcı iki veya daha fazla pakette aynı kaynağa erişim içeriyorsa, her bir paketi ve ilkeyi görmek için bir oka tıklayabilirsiniz.

    ![Bir kullanıcı için kaynak atamaları](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Kullanıcının isteğinin durumunu belirleme

Bir kullanıcının erişim paketine erişim isteği ve erişimi alma hakkında daha fazla bilgi edinmek için Azure AD denetim günlüğünü kullanabilirsiniz. Özellikle, ve kategorilerindeki günlük kayıtlarını, `EntitlementManagement` `UserManagement` her istek için işleme adımlarıyla ilgili ek ayrıntılar almak için kullanabilirsiniz.  

1. **Azure Active Directory** ' a ve sonra **Denetim günlükleri**' ne tıklayın.

1. En üstte,  `EntitlementManagement` `UserManagement` Aradığınız denetim kaydına bağlı olarak kategoriyi veya ya da olarak değiştirin.  

1. **Uygula**’ya tıklayın.

1. Günlükleri indirmek için **İndir**' e tıklayın.

Azure AD yeni bir istek aldığında, **kategorinin** olduğu `EntitlementManagement` ve **etkinliğin** genellikle olduğu bir denetim kaydı yazar `User requests access package assignment` .  Azure portal doğrudan atama durumunda, denetim kaydının **etkinlik** alanı olur `Administrator directly assigns user to access package` ve atamayı gerçekleştiren kullanıcı **actoruserprincipalname** tarafından tanımlanır.

Azure AD, istek sürerken aşağıdakiler de dahil olmak üzere ek denetim kayıtları yazacak:

| Kategori | Etkinlik | İstek durumu |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | İstek onay gerektirmiyor |
| `UserManagement` | `Create request approval` | İstek onay gerektiriyor |
| `UserManagement` | `Add approver to request approval` | İstek onay gerektiriyor |
| `EntitlementManagement` | `Approve access package assignment request` | İstek onaylandı |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |İstek onaylandı veya onay gerektirmiyor |

Bir kullanıcıya erişim atandığında Azure AD, `EntitlementManagement` **etkinlik** içeren kategori için bir denetim kaydı yazar `Fulfill access package assignment` .  Erişimi alan Kullanıcı **Actoruserprincipalname** alanı tarafından tanımlanır.

Erişim atanmamışsa, Azure AD, `EntitlementManagement` **etkinlik** `Deny access package assignment request` bir onaylayan tarafından reddedildiyse veya `Access package assignment request timed out (no approver action taken)` bir onaylayanın onaylanmadan önce zaman aşımına uğradığından, etkinlik içeren kategori için bir denetim kaydı yazar.

Kullanıcının erişim paketi atamasının süresi dolarsa, Kullanıcı tarafından iptal edilir veya bir yönetici tarafından kaldırıldıktan sonra Azure AD, `EntitlementManagement` **etkinliği** olan kategori için bir denetim kaydı yazar `Remove access package assignment` .

## <a name="next-steps"></a>Sonraki adımlar

- [Raporları ve günlükleri Arşivle](entitlement-management-logs-and-reporting.md)
- [Azure AD yetkilendirme yönetimi sorunlarını giderme](entitlement-management-troubleshoot.md)
- [Genel senaryolar](entitlement-management-scenarios.md)
