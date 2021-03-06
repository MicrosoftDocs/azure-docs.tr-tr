---
title: Öğretici-Azure Key Vault kullanarak blob 'ları şifreleme ve şifre çözme
titleSuffix: Azure Storage
description: Azure Key Vault ile istemci tarafı şifrelemeyi kullanarak bir Blobun şifrelemeyi ve şifresini çözmeyi öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: c2daed4a8df89ed176749900dc75eb231c00af87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049279"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Öğretici-Azure Key Vault kullanarak blob 'ları şifreleme ve şifre çözme

Bu öğreticide, Azure Key Vault ile istemci tarafı depolama şifrelemesi kullanımı ele alınmaktadır. Bu teknolojileri kullanarak bir konsol uygulamasında bir Blobun şifrelenmesi ve şifresinin çözülmesi konusunda size kılavuzluk eder.

**Tahmini tamamlanma süresi:** 20 dakika

Azure Key Vault hakkında genel bilgi için bkz. [Azure Key Vault nedir?](../../key-vault/general/overview.md).

Azure depolama için istemci tarafı şifreleme hakkında genel bilgi için bkz. [Istemci tarafı şifreleme ve Microsoft Azure depolama için Azure Key Vault](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakilere sahip olmanız gerekir:

* Bir Azure depolama hesabı
* Visual Studio 2013 veya üzeri
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>İstemci tarafı şifrelemeye genel bakış

Azure depolama için istemci tarafı şifrelemeye genel bakış için bkz. [Istemci tarafı şifreleme ve Azure Key Vault Microsoft Azure depolama](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

İstemci tarafı şifrelemesinin nasıl çalıştığı hakkında kısa bir açıklama aşağıda verilmiştir:

1. Azure depolama istemci SDK 'Sı, tek seferlik kullanılan simetrik anahtar olan bir içerik şifreleme anahtarı (CEK) oluşturur.
2. Müşteri verileri bu CEK kullanılarak şifrelenir.
3. CEK daha sonra anahtar şifreleme anahtarı (KEK) kullanılarak sarmalanır (şifrelenir). KEK bir anahtar tanımlayıcısı tarafından tanımlanır ve asimetrik bir anahtar çifti veya simetrik anahtar olabilir ve yerel olarak yönetilebilir veya Azure Key Vault depolanabilir. Depolama istemcisinin, KEK hiç erişimi yoktur. Yalnızca Key Vault tarafından sağlanmış olan anahtar sarmalama algoritmasını çağırır. Müşteriler, isterseniz anahtar sarmalama/sarmalama için özel sağlayıcılar kullanmayı seçebilir.
4. Şifrelenmiş veriler daha sonra Azure Storage Service 'e yüklenir.

## <a name="set-up-your-azure-key-vault"></a>Azure Key Vault ayarlama

Bu öğreticiye devam etmek için aşağıdaki adımları izlemeniz gerekir: öğreticide bir [.NET Web uygulaması kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma](../../key-vault/secrets/quick-create-net.md).

* Anahtar kasası oluşturma.
* Anahtar kasasına bir anahtar veya gizli dizi ekleyin.
* Azure Active Directory olan bir uygulamayı kaydedin.
* Anahtar veya gizli anahtarı kullanmak için uygulamayı yetkilendirin.

Azure Active Directory bir uygulama kaydedilirken oluşturulan ClientID ve ClientSecret ' i unutmayın.

Anahtar kasasında her iki anahtarı da oluşturun. Aşağıdaki adları kullandığınız öğreticinin geri kalanı için varsayılmaktadır: Contosokeykasası ve TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Paketler ve AppSettings ile bir konsol uygulaması oluşturma

Visual Studio 'da yeni bir konsol uygulaması oluşturun.

Gerekli NuGet paketlerini Paket Yöneticisi konsoluna ekleyin.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

App.Config AppSettings 'i ekleyin.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Aşağıdaki yönergeleri ekleyin `using` ve projeye System.Configbir başvuru eklediğinizden emin olun.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Şu anda Azure Storage istemci kitaplıklarının 12. x sürümünü yansıtan kod parçacıkları oluşturmak için çalışıyoruz. Daha fazla bilgi için bkz. [Azure Storage V12 Istemci kitaplıklarını duyurusu](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```
---

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Konsol uygulamanıza belirteç almak için bir yöntem ekleyin

Aşağıdaki yöntem, anahtar kasanıza erişim için kimlik doğrulaması yapması gereken Key Vault sınıfları tarafından kullanılır.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Şu anda Azure Storage istemci kitaplıklarının 12. x sürümünü yansıtan kod parçacıkları oluşturmak için çalışıyoruz. Daha fazla bilgi için bkz. [Azure Storage V12 Istemci kitaplıklarını duyurusu](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```
---

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Programınızdaki Azure depolama ve Key Vault erişin

Main () yönteminde aşağıdaki kodu ekleyin.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Şu anda Azure Storage istemci kitaplıklarının 12. x sürümünü yansıtan kod parçacıkları oluşturmak için çalışıyoruz. Daha fazla bilgi için bkz. [Azure Storage V12 Istemci kitaplıklarını duyurusu](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```
---

> [!NOTE]
> Nesne modellerini Key Vault
> 
> Gerçekten iki Key Vault nesne modeli olduğunu anlamak önemlidir: biri REST API (Keykasası ad alanı) ve diğeri ise istemci tarafı şifreleme için bir uzantıdır.
> 
> Key Vault Istemcisi, REST API etkileşimde bulunur ve Key Vault bulunan iki tür şey için JSON Web anahtarlarını ve gizli dizileri anlamıştır.
> 
> Key Vault uzantıları, Azure depolama 'da istemci tarafı şifreleme için özel olarak oluşturulan sınıflardır. Anahtar çözümleyici kavramını temel alan anahtarlar (Ikey) ve sınıflar için bir arabirim içerirler. Bilmeniz gereken iki Ikey uygulaması vardır: RSAKey ve SymmetricKey. Artık bir Key Vault yer alan şeyler ile, ancak bu noktada bağımsız sınıflardır (Bu nedenle, Key Vault Istemcisi tarafından alınan anahtar ve gizli dizi Ikey uygulamaz).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Blobu şifreleyin ve karşıya yükleyin

Bir blobu şifrelemek ve Azure depolama hesabınıza yüklemek için aşağıdaki kodu ekleyin. Kullanılan **ResolveKeyAsync** yöntemi bir Ikey döndürür.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Şu anda Azure Storage istemci kitaplıklarının 12. x sürümünü yansıtan kod parçacıkları oluşturmak için çalışıyoruz. Daha fazla bilgi için bkz. [Azure Storage V12 Istemci kitaplıklarını duyurusu](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```
---

> [!NOTE]
> BlobEncryptionPolicy oluşturucusuna bakarsanız, bir anahtarı ve/veya bir çözümleyiciyi kabul edebiliyorsanız görürsünüz. Şu anda varsayılan bir anahtarı desteklemediği için şifreleme için bir çözümleyici kullandığımıza dikkat edin.


## <a name="decrypt-blob-and-download"></a>Blob şifresini çözün ve indirin

Şifre çözme işlemi, çözümleyici sınıflarını kullanmanın anlamlı olduğu durumlarda kullanılır. Şifreleme için kullanılan anahtarın KIMLIĞI, meta verilerinde blob ile ilişkilendirilir, bu nedenle anahtarı alıp anahtar ile blob arasındaki ilişkilendirmeyi hatırlamanız için bir neden yoktur. Anahtarın Key Vault devam ettiğinden emin olmanız yeterlidir.   

Bir RSA anahtarının özel anahtarı Key Vault kalır, bu nedenle şifre çözme işleminin gerçekleşmesi için, CEK içeren blob meta verilerinden şifrelenmiş anahtar şifre çözme için Key Vault gönderilir.

Karşıya yüklediğiniz Blobun şifresini çözmek için aşağıdakileri ekleyin.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Şu anda Azure Storage istemci kitaplıklarının 12. x sürümünü yansıtan kod parçacıkları oluşturmak için çalışıyoruz. Daha fazla bilgi için bkz. [Azure Storage V12 Istemci kitaplıklarını duyurusu](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```
---

> [!NOTE]
> Anahtar yönetimini daha kolay hale getirmek için: AggregateKeyResolver ve CachingKeyResolver gibi birçok farklı tür çözümleyici vardır.

## <a name="use-key-vault-secrets"></a>Key Vault gizli dizileri kullanın

Gizli bir simetrik anahtar olduğu için, istemci tarafı şifreleme ile gizli dizi kullanma yöntemi SymmetricKey sınıfı aracılığıyla yapılır. Ancak, yukarıda belirtildiği gibi Key Vault bir gizli anahtar, bir SymmetricKey öğesine tam olarak eşlenmez. Anlamanız gereken birkaç nokta vardır:

* Bir SymmetricKey içindeki anahtar sabit uzunlukta olmalıdır: 128, 192, 256, 384 veya 512 bit.
* Bir SymmetricKey içindeki anahtar Base64 kodlamalı olmalıdır.
* SymmetricKey olarak kullanılacak Key Vault gizli anahtar, Key Vault içinde "Application/sekizli-Stream" Içerik türüne sahip olmalıdır.

Burada, simetrik olarak kullanılabilecek Key Vault bir gizli dizi oluşturma PowerShell 'de bir örnek verilmiştir.
$Key sabit kodlanmış değerin yalnızca tanıtım amaçlı olduğunu lütfen unutmayın. Kendi kodunuzda bu anahtarı oluşturmak isteyeceksiniz.

```powershell
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

Konsol uygulamanızda, bu parolayı bir SymmetricKey olarak almak için aynı çağrıyı kullanabilirsiniz.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Şu anda Azure Storage istemci kitaplıklarının 12. x sürümünü yansıtan kod parçacıkları oluşturmak için çalışıyoruz. Daha fazla bilgi için bkz. [Azure Storage V12 Istemci kitaplıklarını duyurusu](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
---

## <a name="next-steps"></a>Sonraki adımlar

C# ile Microsoft Azure Depolama kullanma hakkında daha fazla bilgi için bkz. [.NET için Microsoft Azure depolama Istemci kitaplığı](/previous-versions/azure/dn261237(v=azure.100)).

Blob REST API hakkında daha fazla bilgi için bkz. [BLOB hizmeti REST API](/rest/api/storageservices/Blob-Service-REST-API).

Microsoft Azure Depolama hakkında en son bilgiler için [Microsoft Azure depolama ekibi bloguna](/archive/blogs/windowsazurestorage/)gidin.