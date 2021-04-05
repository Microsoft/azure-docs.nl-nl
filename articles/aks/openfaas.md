---
title: OpenFaaS gebruiken met Azure Kubernetes service (AKS)
description: Meer informatie over het implementeren en gebruiken van OpenFaaS op een AKS-cluster (Azure Kubernetes service) voor het bouwen van serverloze functies met containers.
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 319107127b79383fc3b49f0eeb856a0e6c5b09f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747762"
---
# <a name="using-openfaas-on-aks"></a>OpenFaaS gebruiken op AKS

[OpenFaaS][open-faas] is een framework voor het bouwen van serverloze functies met behulp van containers. Als een open-source project heeft het een grootschalige acceptatie binnen de Community verworven. Dit document bevat informatie over het installeren en gebruiken van OpenFaas op een cluster met Azure Kubernetes service (AKS).

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt uitvoeren, hebt u het volgende nodig.

* Basis informatie over Kubernetes.
* Een Azure Kubernetes service (AKS)-cluster en AKS-referenties die zijn geconfigureerd voor uw ontwikkel systeem.
* Azure CLI is geïnstalleerd op uw ontwikkel systeem.
* Git-opdracht regel Programma's die op uw systeem zijn geïnstalleerd.

## <a name="add-the-openfaas-helm-chart-repo"></a>Voeg de OpenFaaS helm-grafiek toe opslag plaats

Ga naar [https://shell.azure.com](https://shell.azure.com) om Azure Cloud shell in uw browser te openen.

OpenFaaS houdt zijn eigen helm-grafieken bij om up-to-date te blijven met de meest recente wijzigingen.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>OpenFaaS implementeren

De functies OpenFaaS en OpenFaaS moeten zo goed mogelijk worden opgeslagen in hun eigen Kubernetes-naam ruimte.

Maak een naam ruimte voor het OpenFaaS-systeem en de functies:

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Genereer een wacht woord voor de OpenFaaS UI-Portal en REST API:

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

U kunt de waarde van het geheim ophalen met `echo $PASSWORD` .

Het wacht woord dat u hier maakt, wordt gebruikt door de helm-grafiek om basis verificatie in te scha kelen op de OpenFaaS-gateway, die wordt blootgesteld aan Internet via een Cloud-LoadBalancer.

Een helm-grafiek voor OpenFaaS is opgenomen in de gekloonde opslag plaats. Gebruik dit diagram om OpenFaaS in uw AKS-cluster te implementeren.

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Uitvoer:

```output
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

```console
kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Er wordt een openbaar IP-adres gemaakt voor toegang tot de OpenFaaS-gateway. Gebruik de opdracht [kubectl Get service][kubectl-get] om dit IP-adres op te halen. Het kan een minuut duren voordat het IP-adres is toegewezen aan de service.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Uitvoer.

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Als u het OpenFaaS-systeem wilt testen, bladert u naar het externe IP-adres op poort 8080, `http://52.186.64.52:8080` in dit voor beeld. U wordt gevraagd om u aan te melden. Voer in om uw wacht woord op te halen `echo $PASSWORD` .

![OpenFaaS-gebruikers interface](media/container-service-serverless/openfaas.png)

Installeer tot slot de OpenFaaS CLI. In dit voor beeld wordt Brew gebruikt, raadpleegt u de [OPENFAAS cli-documentatie][open-faas-cli] voor meer opties.

```console
brew install faas-cli
```

Ingesteld `$OPENFAAS_URL` op het open bare IP-adres dat hierboven is gevonden.

Meld u aan met Azure CLI:

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Eerste functie maken

Nu OpenFaaS operationeel is, maakt u een functie met behulp van de OpenFaas-Portal.

Klik op **nieuwe functie implementeren** en zoek naar **figlet**. Selecteer de functie figlet en klik op **implementeren**.

![Scherm afbeelding toont het dialoog venster een nieuwe functie implementeren met de tekst figlet op de zoek regel.](media/container-service-serverless/figlet.png)

Gebruik krul om de functie aan te roepen. Vervang het IP-adres in het volgende voor beeld met dat van uw OpenFaas-gateway.

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Uitvoer:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Tweede functie maken

Maak nu een tweede functie. Dit voor beeld wordt geïmplementeerd met behulp van de OpenFaaS CLI en bevat een aangepaste container installatie kopie en haalt gegevens op uit een Cosmos DB. Er moeten verschillende items worden geconfigureerd voordat u de functie maakt.

Maak eerst een nieuwe resource groep voor de Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Implementeer een CosmosDB-exemplaar van de soort `MongoDB` . Het exemplaar heeft een unieke naam nodig en `openfaas-cosmos` moet worden bijgewerkt naar iets dat uniek is voor uw omgeving.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Haal de Cosmos-data base connection string op en sla deze op in een variabele.

Werk de waarde voor het `--resource-group` argument bij naar de naam van de resource groep en het `--name` argument in de naam van uw Cosmos db.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Vul nu de Cosmos DB in met test gegevens. Maak een bestand `plans.json` met de naam en kopie in de volgende JSON.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Gebruik het hulp programma *mongoimport* om het CosmosDB-exemplaar met gegevens te laden.

Installeer, indien nodig, de MongoDB-hulpprogram ma's. In het volgende voor beeld worden deze hulpprogram ma's geïnstalleerd met behulp van Brew. Zie de [MongoDb-documentatie][install-mongo] voor andere opties.

```console
brew install mongodb
```

Laad de gegevens in de data base.

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

Uitvoer:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Voer de volgende opdracht uit om de functie te maken. Werk de waarde van het `-g` argument bij met uw OpenFaaS gateway-adres.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Na de implementatie ziet u het zojuist gemaakte OpenFaaS-eind punt voor de functie.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Test de functie met behulp van krul. Werk het IP-adres bij met het OpenFaaS gateway-adres.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Uitvoer:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

U kunt ook de functie in de OpenFaaS-gebruikers interface testen.

![alternatieve tekst](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Volgende stappen

U kunt door gaan met de OpenFaaS workshop met behulp van een reeks praktijk gerichte Labs die onderwerpen bevatten over het maken van uw eigen GitHub-bot, het gebruiken van geheimen, het weer geven van metrische gegevens en het automatisch schalen.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
