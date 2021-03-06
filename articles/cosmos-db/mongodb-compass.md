---
title: Pusula kullanarak Azure Cosmos DB bağlanma
description: Azure Cosmos DB verileri depolamak ve yönetmek için MongoDB pusula 'i kullanmayı öğrenin.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/05/2020
author: christopheranderson
ms.author: chrande
ms.openlocfilehash: 43bcd54955cb1a8aaf08785368faf13c14f8322c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94413063"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API 'sine bağlanmak için MongoDB pusula kullanma
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Bu öğreticide, Cosmos DB veri depolarken ve/veya yönetirken [MongoDB pusula](https://www.mongodb.com/products/compass) kullanımı gösterilmektedir. Bu izlenecek yol için Azure Cosmos DB MongoDB API 'sini kullanıyoruz. Bilmediğiniz kişiler için, pusula MongoDB için bir GUI 'dir. Genellikle verilerinizi görselleştirmek, geçici sorguları çalıştırmak ve verilerinizi yönetmek için kullanılır.

Cosmos DB, Microsoft 'un genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Cosmos DB temel dağıtım ve yatay ölçek özelliğinden faydalanabilir ve her şey belge, anahtar/değer ve grafik veritabanlarını hızlıca oluşturup sorgulayabilirsiniz.

## <a name="pre-requisites"></a>Ön koşullar

MongoDB pusula kullanarak Cosmos DB hesabınıza bağlanmak için şunları yapmanız gerekir:

* [Pusula](https://www.mongodb.com/download-center/compass?jmp=hero) indirme ve yükleme
* Cosmos DB [bağlantı dizesi](connect-mongodb-account.md) bilgilerinizi içermelidir

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>MongoDB için Cosmos DB API 'sine bağlanma

Cosmos DB hesabınızı pusula 'e bağlamak için aşağıdaki adımları izleyebilirsiniz:

1. Azure Cosmos DB API MongoDB ile yapılandırılan Cosmos hesabınız için [buradaki](connect-mongodb-account.md)yönergeleri kullanarak bağlantı bilgilerini alın.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-connection.png" alt-text="Bağlantı dizesi dikey penceresinin ekran görüntüsü":::

2. Cosmos DB içindeki **birincil/ikincil bağlantı dizeniz** yanında **Panoya Kopyala** yazılı düğmeye tıklayın. Bu düğmeye tıkladığınızda tüm bağlantı dizeniz panonuza kopyalanır.

    :::image type="content" source="./media/mongodb-compass/mongodb-connection-copy.png" alt-text="Panoya kopyala düğmesinin ekran görüntüsü":::

3. Masaüstünüzde/makinenizde pusula ' i açın ve **Bağlan** ' a ve ardından **Bağlan... öğesine** tıklayın.

4. Pusula, panodaki bir bağlantı dizesini otomatik olarak algılar ve bağlanmak için kullanmak isteyip istemediğinizi sorar. Aşağıdaki ekran görüntüsünde gösterildiği gibi **Evet** ' e tıklayın.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-detect.png" alt-text="Ekran görüntüsünde, panonuzda bir bağlantı dizeniz olduğunu açıklayan bir iletişim kutusu gösterilir.":::

5. Yukarıdaki adımda **Evet 'e** tıklandıktan sonra bağlantı dizesindeki ayrıntılarınız otomatik olarak doldurulur. Boş bırakılmış olduğundan emin olmak için **çoğaltma kümesi adı** alanında otomatik olarak doldurulmuş değeri kaldırın.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-replica.png" alt-text="Ekran görüntüsü, çoğaltma kümesi adı metin kutusunu gösterir.":::

6. Sayfanın alt kısmındaki **Bağlan** ' a tıklayın. Cosmos DB hesabınız ve veritabanları artık MongoDB pusula içinde görünür olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

- [Studio 3T](mongodb-mongochef.md) 'ı Azure Cosmos DB MongoDB IÇIN API 'si ile nasıl kullanacağınızı öğrenin.
- MongoDB için Azure Cosmos DB API 'siyle MongoDB [örneklerini](mongodb-samples.md) gezin.
