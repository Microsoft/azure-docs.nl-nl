---
title: Een Apache Spark-taak uitvoeren met Azure Kubernetes service (AKS)
description: Gebruik Azure Kubernetes service (AKS) om een Apache Spark taak voor grootschalige gegevens verwerking te maken en uit te voeren.
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 74a3fe79291f3a5c7f5bbd664bf6d55a5fb77eae
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181190"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Apache Spark taken uitvoeren op AKS

[Apache Spark][apache-spark] is een snelle engine voor grootschalige gegevens verwerking. Vanaf de [Spark 2.3.0-release][spark-kubernetes-earliest-version]ondersteunt Apache Spark systeem eigen integratie met Kubernetes-clusters. Azure Kubernetes service (AKS) is een beheerde Kubernetes-omgeving die wordt uitgevoerd in Azure. Dit document bevat informatie over het voorbereiden en uitvoeren van Apache Spark taken op een AKS-cluster (Azure Kubernetes service).

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt uitvoeren, hebt u het volgende nodig.

* Basis informatie over Kubernetes en [Apache Spark][spark-quickstart].
* [Docker hub][docker-hub] -account of een [Azure container Registry][acr-create].
* Azure CLI is [geïnstalleerd][azure-cli] op uw ontwikkel systeem.
* [Jdk 8][java-install] geïnstalleerd op uw systeem.
* [Apache Maven][maven-install] geïnstalleerd op uw systeem.
* SBT ([scala Build Tool][sbt-install]) dat op uw systeem is geïnstalleerd.
* Git-opdracht regel Programma's die op uw systeem zijn geïnstalleerd.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Spark wordt gebruikt voor grootschalige gegevens verwerking en vereist dat de grootte van de Kubernetes-knoop punten voldoet aan de vereisten van de Spark-resources. U kunt het beste een minimale grootte van `Standard_D3_v2` de AKS-knoop punten (Azure Kubernetes service) aanraden.

Als u een AKS-cluster nodig hebt dat voldoet aan deze minimale aanbeveling, voert u de volgende opdrachten uit.

Maak een resource groep voor het cluster.

```azurecli
az group create --name mySparkCluster --location eastus
```

Maak een service-principal voor het cluster. Nadat deze is gemaakt, hebt u de Service-Principal appId en het wacht woord nodig voor de volgende opdracht.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Maak het AKS-cluster met knoop punten met een grootte `Standard_D3_v2` , en de waarden van de AppID en het wacht woord dat is door gegeven als Service-Principal-en client-geheime para meters.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Maak verbinding met het AKS-cluster.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Als u Azure Container Registry (ACR) gebruikt om container installatie kopieën op te slaan, configureert u verificatie tussen AKS en ACR. Zie de [documentatie voor ACR-verificatie][acr-aks] voor deze stappen.

## <a name="build-the-spark-source"></a>De Spark-bron bouwen

Voordat u Spark-taken uitvoert op een AKS-cluster, moet u de Spark-bron code maken en deze inpakken in een container installatie kopie. De Spark-bron bevat scripts die kunnen worden gebruikt om dit proces te volt ooien.

Kloon de Spark-project opslagplaats naar uw ontwikkel systeem.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Ga naar de map van de gekloonde opslag plaats en sla het pad van de Spark-bron op in een variabele.

```bash
cd spark
sparkdir=$(pwd)
```

Als er meerdere JDK-versies zijn geïnstalleerd, stelt u `JAVA_HOME` in dat versie 8 moet worden gebruikt voor de huidige sessie.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Voer de volgende opdracht uit om de Spark-bron code te maken met Kubernetes-ondersteuning.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Met de volgende opdrachten maakt u de Spark-container installatie kopie en pusht u deze naar een container installatie kopie register. Vervang door `registry.example.com` de naam van het container register en `v1` met het label dat u wilt gebruiken. Als docker hub wordt gebruikt, is deze waarde de register naam. Als u Azure Container Registry (ACR) gebruikt, is deze waarde de naam van de ACR-aanmeldings server.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Push de container installatie kopie naar het REGI ster van de container installatie kopie.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Een Spark-taak voorbereiden

Vervolgens moet u een Spark-taak voorbereiden. Een jar-bestand wordt gebruikt om de Spark-taak te bewaren en is nodig voor het uitvoeren van de `spark-submit` opdracht. Het jar kan toegankelijk worden gemaakt via een open bare URL of vooraf verpakt in een container installatie kopie. In dit voor beeld wordt een jar-voorbeeld gemaakt om de waarde van pi te berekenen. Dit jar wordt vervolgens geüpload naar Azure Storage. Als u een bestaand jar hebt, kunt u vervangen

Maak een map waarin u het project voor een Spark-taak wilt maken.

```bash
mkdir myprojects
cd myprojects
```

Een nieuw scala-project maken op basis van een sjabloon.

```bash
sbt new sbt/scala-seed.g8
```

Voer `SparkPi` de naam van het project in wanneer dit wordt gevraagd.

```bash
name [Scala Seed Project]: SparkPi
```

Ga naar de zojuist gemaakte projectmap.

```bash
cd sparkpi
```

Voer de volgende opdrachten uit om een SBT-invoeg toepassing toe te voegen, waarmee het project kan worden ingepakt als jar-bestand.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Voer deze opdrachten uit om de voorbeeld code te kopiëren naar het zojuist gemaakte project en alle benodigde afhankelijkheden toe te voegen.

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

Als u het project wilt inpakken in een jar, voert u de volgende opdracht uit.

```bash
sbt assembly
```

Na een geslaagde verpakking ziet u uitvoer die lijkt op het volgende.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Taak naar opslag kopiëren

Maak een Azure-opslag account en een container om het jar-bestand te bewaren.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Upload het jar-bestand naar het Azure Storage-account met de volgende opdrachten.

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

Variabele `jarUrl` bevat nu het openbaar toegankelijke pad naar het jar-bestand.

## <a name="submit-a-spark-job"></a>Een Spark-taak verzenden

Start uitvoeren-proxy in een afzonderlijke opdracht regel met de volgende code.

```bash
kubectl proxy
```

Ga terug naar de hoofdmap van Spark-opslag plaats.

```bash
cd $sparkdir
```

Maak een service account dat voldoende machtigingen heeft voor het uitvoeren van een taak.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Verzend de taak met behulp van `spark-submit` .

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

Met deze bewerking wordt de Spark-taak gestart, waarmee de taak status naar uw shell-sessie wordt gestreamd. Terwijl de taak wordt uitgevoerd, kunt u de Pod en de uitvoerings tijd van het Spark-stuur programma weer geven met behulp van de opdracht kubectl Get peul. Open een tweede terminal sessie om deze opdrachten uit te voeren.

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

Terwijl de taak wordt uitgevoerd, kunt u ook toegang krijgen tot de Spark-gebruikers interface. In de tweede terminal sessie gebruikt u de `kubectl port-forward` opdracht toegang bieden tot Spark-gebruikers interface.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Als u toegang wilt krijgen tot de Spark-gebruikers interface, opent u het adres `127.0.0.1:4040` in een browser.

![Spark-gebruikers interface](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Taak resultaten en logboeken ophalen

Nadat de taak is voltooid, is het stuur programma pod de status voltooid. Haal de naam van de pod op met de volgende opdracht.

```bash
kubectl get pods --show-all
```

Uitvoer:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Gebruik de `kubectl logs` opdracht om logboeken op te halen uit het stuur programma pod van Spark. Vervang de naam van de pod door de naam van de pod van uw stuur programma.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

In deze logboeken kunt u het resultaat van de Spark-taak zien. Dit is de waarde van pi.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Pakket jar met container installatie kopie

In het bovenstaande voor beeld is het Spark jar-bestand geüpload naar Azure Storage. Een andere mogelijkheid is om het jar-bestand te verpakken in aangepaste docker-installatie kopieën.

Als u dit wilt doen, zoekt u naar de `dockerfile` Spark-installatie kopie die zich in de map bevindt `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` . Een `ADD` instructie voor de Spark-taak `jar` ergens tussen `WORKDIR` en- `ENTRYPOINT` declaraties toevoegen.

Werk het jar-pad bij naar de locatie van het `SparkPi-assembly-0.1.0-SNAPSHOT.jar` bestand in uw ontwikkel systeem. U kunt ook uw eigen aangepaste JAR-bestand gebruiken.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Bouw en push de installatie kopie met de meegeleverde Spark-scripts.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Bij het uitvoeren van de taak `local://` kan het schema worden gebruikt met het pad naar het jar-bestand in de docker-installatie kopie, in plaats van een externe Jar-URL aan te duiden.

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
> In Spark- [documentatie][spark-docs]: ' de Kubernetes scheduler is momenteel experimenteel. In toekomstige versies zijn er mogelijk gedrags wijzigingen in de configuratie, container installatie kopieën en entrypoints ".

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de Spark-documentatie voor meer informatie.

> [!div class="nextstepaction"]
> [Documentatie voor Spark][spark-docs]

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
