---
title: Azure Kubernetes hizmeti (AKS) ile Apache Spark işi çalıştırma
description: Azure Kubernetes Service (AKS) kullanarak büyük ölçekli veri işleme için bir Apache Spark işi oluşturun ve çalıştırın.
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 74a3fe79291f3a5c7f5bbd664bf6d55a5fb77eae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181202"
---
# <a name="running-apache-spark-jobs-on-aks"></a>AKS üzerinde Apache Spark işleri çalıştırma

[Apache Spark][apache-spark] , büyük ölçekli veri işleme için hızlı bir altyapıdır. [Spark 2.3.0 sürümünden][spark-kubernetes-earliest-version]itibaren Apache Spark, Kubernetes kümeleriyle yerel tümleştirmeyi destekler. Azure Kubernetes hizmeti (AKS), Azure 'da çalışan yönetilen bir Kubernetes ortamıdır. Bu belge, Azure Kubernetes Service (AKS) kümesinde Apache Spark işleri hazırlamaya ve çalıştırmaya ilişkin ayrıntıları sağlar.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlayabilmeniz için aşağıdakiler gerekir.

* Kubernetes ve [Apache Spark][spark-quickstart]temel olarak anlaşılmaktadır.
* [Docker Hub][docker-hub] hesabı veya bir [Azure Container Registry][acr-create].
* Azure CLı, geliştirme sisteminize [yüklendi][azure-cli] .
* Sisteminizde yüklü [JDK 8][java-install] .
* [Apache Maven][maven-install] , sisteminizde yüklü.
* Sisteminizde yüklü SBT ([Scala derleme aracı][sbt-install]).
* Sisteminizde yüklü olan git komut satırı araçları.

## <a name="create-an-aks-cluster"></a>AKS kümesi oluşturma

Spark, büyük ölçekli veri işleme için kullanılır ve Kubernetes düğümlerinin Spark kaynak gereksinimlerini karşılayacak şekilde boyutlandırılması gerekir. `Standard_D3_v2`Azure Kubernetes Service (AKS) düğümleriniz için en düşük boyut önerilir.

Bu en düşük öneriyi karşılayan bir AKS kümesine ihtiyacınız varsa aşağıdaki komutları çalıştırın.

Küme için bir kaynak grubu oluşturun.

```azurecli
az group create --name mySparkCluster --location eastus
```

Küme için bir hizmet sorumlusu oluşturun. Oluşturulduktan sonra, sonraki komut için hizmet sorumlusu uygulama kimliği ve parolası gerekir.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Boyut `Standard_D3_v2` değerleri ve hizmet sorumlusu ve istemci gizli parametreleri olarak geçirilen AppID ve parola değerleri Ile AKS kümesini oluşturun.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

AKS kümesine bağlanın.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Kapsayıcı görüntülerini depolamak için Azure Container Registry (ACR) kullanıyorsanız, AKS ve ACR arasında kimlik doğrulamasını yapılandırın. Bu adımlar için [ACR kimlik doğrulama belgelerine][acr-aks] bakın.

## <a name="build-the-spark-source"></a>Spark kaynağını oluşturma

Spark işlerini bir AKS kümesinde çalıştırmadan önce Spark kaynak kodunu oluşturmanız ve bir kapsayıcı görüntüsüne paketetmeniz gerekir. Spark kaynağı, bu işlemi gerçekleştirmek için kullanılabilecek betikleri içerir.

Spark proje deposunu geliştirme sisteminize kopyalayın.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Kopyalanmış deponun dizinine geçin ve Spark kaynağının yolunu bir değişkene kaydedin.

```bash
cd spark
sparkdir=$(pwd)
```

Birden fazla JDK sürümü yüklüyse, `JAVA_HOME` geçerli oturum için sürüm 8 ' i kullanın.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Kubernetes desteğiyle Spark kaynak kodunu derlemek için aşağıdaki komutu çalıştırın.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Aşağıdaki komutlar Spark kapsayıcı görüntüsünü oluşturur ve bir kapsayıcı görüntüsü kayıt defterine gönderir. `registry.example.com`Kapsayıcı kayıt defterinizin adıyla ve `v1` kullanmayı tercih ettiğiniz etiketle değiştirin. Docker Hub kullanıyorsanız, bu değer kayıt defteri adıdır. Azure Container Registry (ACR) kullanıyorsanız, bu değer ACR oturum açma sunucusu adıdır.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Kapsayıcı görüntüsünü kapsayıcı görüntü kayıt defterinize gönderin.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Spark işi hazırlama

Sonra, bir Spark işi hazırlayın. Bir jar dosyası Spark işini tutmak için kullanılır ve komutu çalıştırırken gereklidir `spark-submit` . Jar, ortak bir URL aracılığıyla veya bir kapsayıcı görüntüsü içinde önceden paketlenmiş olarak erişilebilir hale getirilebilir. Bu örnekte, Pi değerini hesaplamak için bir örnek jar oluşturulur. Bu jar daha sonra Azure depolama 'ya yüklenir. Mevcut bir kavanaya sahipseniz, bunu değiştirme

Spark işi için projeyi oluşturmak istediğiniz bir dizin oluşturun.

```bash
mkdir myprojects
cd myprojects
```

Bir şablondan yeni bir Scala projesi oluşturun.

```bash
sbt new sbt/scala-seed.g8
```

İstendiğinde, `SparkPi` Proje adı için girin.

```bash
name [Scala Seed Project]: SparkPi
```

Yeni oluşturulan proje dizinine gidin.

```bash
cd sparkpi
```

Projeyi jar dosyası olarak paketlemeye izin veren bir SBT eklentisi eklemek için aşağıdaki komutları çalıştırın.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Örnek kodu yeni oluşturulan projeye kopyalamak ve tüm gerekli bağımlılıkları eklemek için bu komutları çalıştırın.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11"/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Projeyi bir jar içine paketlemek için aşağıdaki komutu çalıştırın.

```bash
sbt assembly
```

Başarılı paketleme sonrasında aşağıdakine benzer bir çıktı görmeniz gerekir.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>İşi depolamaya Kopyala

Jar dosyasını tutmak için bir Azure depolama hesabı ve kapsayıcısı oluşturun.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Aşağıdaki komutlarla jar dosyasını Azure Storage hesabına yükleyin.

```azurecli
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

Değişken `jarUrl` Şimdi jar dosyasının genel olarak erişilebilir yolunu içeriyor.

## <a name="submit-a-spark-job"></a>Spark işi gönderme

Kuin-proxy ' y i aşağıdaki kodla ayrı bir komut satırında başlatın.

```bash
kubectl proxy
```

Spark deposunun köküne geri gidin.

```bash
cd $sparkdir
```

Bir işi çalıştırmak için yeterli izinlere sahip bir hizmet hesabı oluşturun.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Kullanarak işi gönderebilirsiniz `spark-submit` .

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Bu işlem, iş durumunu kabuk oturumunuza akıp Spark işini başlatır. İş çalışırken, kubectl Get Pod komutunu kullanarak Spark sürücüsü Pod ve yürütücü Pod 'yi görebilirsiniz. Bu komutları çalıştırmak için ikinci bir terminal oturumu açın.

```console
kubectl get pods
```

```output
NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

İş çalışırken Spark Kullanıcı arabirimine de erişebilirsiniz. İkinci Terminal oturumunda, `kubectl port-forward` Spark Kullanıcı arabirimine erişim sağlama komutunu kullanın.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Spark Kullanıcı arabirimine erişmek için adresi `127.0.0.1:4040` bir tarayıcıda açın.

![Spark Kullanıcı arabirimi](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>İş sonuçlarını ve günlükleri al

İş bittikten sonra, Pod sürücüsü "tamamlandı" durumunda olur. Aşağıdaki komutla Pod 'un adını alın.

```bash
kubectl get pods --show-all
```

Çıkış:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

`kubectl logs`Spark sürücüsü Pod 'dan günlükleri almak için komutunu kullanın. Pod adını sürücü Pod adının adıyla değiştirin.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Bu günlüklerde, PI 'nin değeri olan Spark işinin sonucunu görebilirsiniz.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Kapsayıcı görüntüsü ile paket jar 'i

Yukarıdaki örnekte Spark jar dosyası Azure depolama 'ya yüklendi. Diğer bir seçenek de jar dosyasını özel olarak oluşturulmuş Docker görüntülerine paketlemenize olanak sağlar.

Bunu yapmak için, `dockerfile` dizininde bulunan Spark görüntüsünü bulun `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` . `ADD` `jar` Ve bildirimleri arasında bir yerde Spark işi için bir ifade ekleyin `WORKDIR` `ENTRYPOINT` .

Jar yolunu `SparkPi-assembly-0.1.0-SNAPSHOT.jar` geliştirme sisteminizdeki dosyanın konumuna güncelleştirin. Kendi özel jar dosyanızı da kullanabilirsiniz.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Dahil edilen Spark betiklerine sahip görüntüyü derleyin ve gönderin.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

İşi çalıştırırken, uzak bir jar URL 'SI olarak kullanmak yerine, `local://` Düzen Docker görüntüsündeki jar dosyasının yoluyla kullanılabilir.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Spark [belgelerinden][spark-docs]: "Kubernetes Zamanlayıcısı Şu anda deneysel. Gelecekteki sürümlerde yapılandırma, kapsayıcı görüntüleri ve entryPoints gibi davranış değişiklikleri olabilir.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için Spark belgelerine göz atın.

> [!div class="nextstepaction"]
> [Spark belgeleri][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: /azure/developer/java/fundamentals/java-jdk-long-term-support
[maven-install]: https://maven.apache.org/install.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-kubernetes-earliest-version]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-quickstart]: ./index.yml
[azure-cli]: /cli/azure/
[storage-account]: ../storage/blobs/storage-quickstart-blobs-cli.md
