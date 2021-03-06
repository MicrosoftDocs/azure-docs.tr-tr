---
title: Azure NetApp Files çapraz bölge çoğaltması için birim çoğaltmaları veya birimleri silme | Microsoft Docs
description: Kaynak ve hedef birimler arasında artık gerekli olmayan bir çoğaltma bağlantısının nasıl silineceğini açıklar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95249903"
---
# <a name="delete-volume-replications-or-volumes"></a>Birim çoğaltmalarını veya birimleri silme

Bu makalede birim çoğaltmaları silme işlemini açıklar. Ayrıca, kaynak veya hedef birimin nasıl silineceğini de açıklar.

## <a name="delete-volume-replications"></a>Birim çoğaltmaları silme

Birim çoğaltmasını silerek kaynak ve hedef birimler arasındaki çoğaltma bağlantısını sonlandırabilirsiniz. Hedef birimden çoğaltmayı silmeniz gerekir. Silme işlemi yalnızca çoğaltma için Yetkilendirmeyi kaldırır; kaynağı veya hedef birimi kaldırmaz. 

1. Birim çoğaltmasını silmeden önce çoğaltma eşlemesinin bozuk olduğundan emin olun. Çoğaltma eşlemesini bölmek için: 

    1. *Hedef* birimi seçin. Depolama hizmeti altında **çoğaltma** ' ya tıklayın.  

    2.  Devam etmeden önce aşağıdaki alanları kontrol edin:  
        * Yansıtma durumunun ***yansıtmalı*** olduğundan emin olun.   
            Yansıtma durumu *başlatılmamış* olarak görünüyorsa çoğaltma eşlemesini kesmeyi denemeyin.
        * Ilişki durumunun ***Boşta*** gösterildiğinden emin olun.   
            Ilişki durumu *aktarmayı* gösteriyorsa çoğaltma eşlemesini kesmeyi denemeyin.   

        Bkz. [çoğaltma ilişkisinin sistem durumunu görüntüleme](cross-region-replication-display-health-status.md). 

    3.  **Eşlemeyi kes**' e tıklayın.  

    4.  İstendiğinde **Evet** yazın ve **Kes**' e tıklayın. 

        ![Çoğaltma eşlemesini kes](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. Birim çoğaltmasını silmek için kaynaktan veya hedef birimden **çoğaltma** ' yı seçin.  

2. **Sil**'e tıklayın.    

3. **Evet** yazarak silmeyi onaylayın ve **Sil**' i tıklatın.   

    ![Çoğaltmayı Sil](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>Kaynak veya hedef birimleri silme

Kaynak veya hedef birimi silmek istiyorsanız, açıklanan sırada aşağıdaki adımları gerçekleştirmeniz gerekir. Aksi takdirde, `Volume with replication cannot be deleted` hata oluşur.  

1. Hedef birimden, [birim çoğaltmasını silin](#delete-volume-replications).   

2. Birim adına sağ tıklayıp **Sil**' i seçerek hedef veya kaynak birimini gerekli şekilde silin.   

    ![Bir birimin sağ tıklama menüsünü gösteren ekran görüntüsü.](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>Sonraki adımlar  

* [Bölgeler arası çoğaltma](cross-region-replication-introduction.md)
* [Bölgeler arası çoğaltmayı kullanma gereksinimleri ve konuları](cross-region-replication-requirements-considerations.md)
* [Çoğaltma ilişkisinin uygunluk durumunu görüntüleme](cross-region-replication-display-health-status.md)
* [Bölgeler arası çoğaltma sorunlarını giderme](troubleshoot-cross-region-replication.md)

