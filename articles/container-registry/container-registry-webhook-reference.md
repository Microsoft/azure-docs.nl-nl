---
title: REGI ster-webhook-schema referentie
description: Naslag informatie voor JSON-nettolading voor webhook-aanvragen in een Azure container Registry die worden gegenereerd wanneer webhooks zijn ingeschakeld voor artefact push of gebeurtenissen verwijderen
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74455976"
---
# <a name="azure-container-registry-webhook-reference"></a>Verwijzing naar Azure Container Registry webhook

U kunt [webhooks configureren](container-registry-webhook.md) voor uw container register die gebeurtenissen genereren wanneer bepaalde acties worden uitgevoerd. Schakel bijvoorbeeld webhooks in die worden geactiveerd wanneer een container installatie kopie of het helm-diagram wordt gepusht naar een REGI ster of verwijderd. Wanneer een webhook wordt geactiveerd, wordt door Azure Container Registry een HTTP-of HTTPS-aanvraag met informatie over de gebeurtenis uitgegeven aan een eind punt dat u opgeeft. De webhook kan vervolgens door het eind punt worden verwerkt en dienovereenkomstig worden toegepast.

De volgende secties beschrijven het schema van webhook-aanvragen die worden gegenereerd door ondersteunde gebeurtenissen. De gebeurtenis secties bevatten het payload-schema voor het gebeurtenis type, een voor beeld van een aanvraag lading en een of meer voorbeeld opdrachten waarmee de webhook wordt geactiveerd.

Zie [Azure container Registry-webhooks gebruiken](container-registry-webhook.md)voor meer informatie over het configureren van webhooks voor uw Azure container Registry.

## <a name="webhook-requests"></a>Webhook-aanvragen

### <a name="http-request"></a>HTTP-aanvraag

Een geactiveerde webhook maakt een HTTP- `POST` aanvraag naar het URL-eind punt dat u hebt opgegeven tijdens het configureren van de webhook.

### <a name="http-headers"></a>HTTP-headers

Webhook-aanvragen bevatten een `Content-Type` van `application/json` Als u geen `Content-Type` aangepaste koptekst voor uw webhook hebt opgegeven.

Er worden geen andere headers aan de aanvraag toegevoegd naast de aangepaste headers die u mogelijk hebt opgegeven voor de webhook.

## <a name="push-event"></a>Push gebeurtenis

Webhook geactiveerd wanneer een container installatie kopie naar een opslag plaats wordt gepusht.

### <a name="push-event-payload"></a>Nettolading van push gebeurtenis

|Element|Type|Description|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van de webhook-gebeurtenis.|
|`timestamp`|DateTime|Het tijdstip waarop de webhook-gebeurtenis is geactiveerd.|
|`action`|Tekenreeks|De actie die de gebeurtenis webhook heeft geactiveerd.|
|[stemming](#target)|Complex type|Het doel van de gebeurtenis die de gebeurtenis webhook heeft geactiveerd.|
|[schot](#request)|Complex type|De aanvraag die de gebeurtenis webhook heeft gegenereerd.|

### <a name="target"></a><a name="target"></a>stemming

|Element|Type|Description|
|------------------|----------|-----------|
|`mediaType`|Tekenreeks|Het MIME-type van het object waarnaar wordt verwezen.|
|`size`|Int32|Het aantal bytes van de inhoud. Gelijk aan het veld lengte.|
|`digest`|Tekenreeks|De samen vatting van de inhoud, zoals gedefinieerd in de HTTP API-specificatie van Regi ster v2.|
|`length`|Int32|Het aantal bytes van de inhoud. Gelijk aan het veld grootte.|
|`repository`|Tekenreeks|De naam van de opslag plaats.|
|`tag`|Tekenreeks|De naam van de afbeeldings code.|

### <a name="request"></a><a name="request"></a>schot

|Element|Type|Description|
|------------------|----------|-----------|
|`id`|Tekenreeks|De ID van de aanvraag die de gebeurtenis heeft gestart.|
|`host`|Tekenreeks|De extern toegankelijke hostnaam van het register exemplaar, zoals opgegeven door de HTTP-host-header op binnenkomende aanvragen.|
|`method`|Tekenreeks|De aanvraag methode waarmee de gebeurtenis is gegenereerd.|
|`useragent`|Tekenreeks|De header van de gebruikers agent van de aanvraag.|

### <a name="payload-example-image-push-event"></a>Voor beeld van Payload: installatie kopie push gebeurtenis

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Voor beeld van [docker cli](https://docs.docker.com/engine/reference/commandline/cli/) -opdracht waarmee de installatie kopie **Push** Event-webhook wordt geactiveerd:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Push gebeurtenis van grafiek

Webhook geactiveerd wanneer een helm-grafiek naar een opslag plaats wordt gepusht.

### <a name="chart-push-event-payload"></a>Nettolading van push gebeurtenis van grafiek

|Element|Type|Description|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van de webhook-gebeurtenis.|
|`timestamp`|DateTime|Het tijdstip waarop de webhook-gebeurtenis is geactiveerd.|
|`action`|Tekenreeks|De actie die de gebeurtenis webhook heeft geactiveerd.|
|[stemming](#helm_target)|Complex type|Het doel van de gebeurtenis die de gebeurtenis webhook heeft geactiveerd.|

### <a name="target"></a><a name="helm_target"></a>stemming

|Element|Type|Description|
|------------------|----------|-----------|
|`mediaType`|Tekenreeks|Het MIME-type van het object waarnaar wordt verwezen.|
|`size`|Int32|Het aantal bytes van de inhoud.|
|`digest`|Tekenreeks|De samen vatting van de inhoud, zoals gedefinieerd in de HTTP API-specificatie van Regi ster v2.|
|`repository`|Tekenreeks|De naam van de opslag plaats.|
|`tag`|Tekenreeks|De naam van de grafiek code.|
|`name`|Tekenreeks|De naam van de grafiek.|
|`version`|Tekenreeks|De grafiek versie.|

### <a name="payload-example-chart-push-event"></a>Payload-voor beeld: grafiek push gebeurtenis

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Voor beeld van een [Azure cli](/cli/azure/acr) -opdracht waarmee de **chart_push** Event-webhook wordt geactiveerd:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Gebeurtenis verwijderen

Webhook wordt geactiveerd wanneer een afbeeldings opslagplaats of-manifest wordt verwijderd. Wordt niet geactiveerd wanneer een label wordt verwijderd.

### <a name="delete-event-payload"></a>Gebeurtenis lading verwijderen

|Element|Type|Description|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van de webhook-gebeurtenis.|
|`timestamp`|DateTime|Het tijdstip waarop de webhook-gebeurtenis is geactiveerd.|
|`action`|Tekenreeks|De actie die de gebeurtenis webhook heeft geactiveerd.|
|[stemming](#delete_target)|Complex type|Het doel van de gebeurtenis die de gebeurtenis webhook heeft geactiveerd.|
|[schot](#delete_request)|Complex type|De aanvraag die de gebeurtenis webhook heeft gegenereerd.|

### <a name="target"></a><a name="delete_target"></a> stemming

|Element|Type|Description|
|------------------|----------|-----------|
|`mediaType`|Tekenreeks|Het MIME-type van het object waarnaar wordt verwezen.|
|`digest`|Tekenreeks|De samen vatting van de inhoud, zoals gedefinieerd in de HTTP API-specificatie van Regi ster v2.|
|`repository`|Tekenreeks|De naam van de opslag plaats.|

### <a name="request"></a><a name="delete_request"></a> schot

|Element|Type|Description|
|------------------|----------|-----------|
|`id`|Tekenreeks|De ID van de aanvraag die de gebeurtenis heeft gestart.|
|`host`|Tekenreeks|De extern toegankelijke hostnaam van het register exemplaar, zoals opgegeven door de HTTP-host-header op binnenkomende aanvragen.|
|`method`|Tekenreeks|De aanvraag methode waarmee de gebeurtenis is gegenereerd.|
|`useragent`|Tekenreeks|De header van de gebruikers agent van de aanvraag.|

### <a name="payload-example-image-delete-event"></a>Voor beeld van Payload: afbeelding verwijderen gebeurtenis

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Voor beelden van [Azure cli](/cli/azure/acr) -opdrachten waarmee een **Delete** Event-webhook wordt geactiveerd:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Gebeurtenis grafiek verwijderen

Webhook geactiveerd wanneer een helm-grafiek of-opslag plaats wordt verwijderd. 

### <a name="chart-delete-event-payload"></a>Gebeurtenis lading van grafiek verwijderen

|Element|Type|Description|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van de webhook-gebeurtenis.|
|`timestamp`|DateTime|Het tijdstip waarop de webhook-gebeurtenis is geactiveerd.|
|`action`|Tekenreeks|De actie die de gebeurtenis webhook heeft geactiveerd.|
|[stemming](#chart_delete_target)|Complex type|Het doel van de gebeurtenis die de gebeurtenis webhook heeft geactiveerd.|

### <a name="target"></a><a name="chart_delete_target"></a> stemming

|Element|Type|Description|
|------------------|----------|-----------|
|`mediaType`|Tekenreeks|Het MIME-type van het object waarnaar wordt verwezen.|
|`size`|Int32|Het aantal bytes van de inhoud.|
|`digest`|Tekenreeks|De samen vatting van de inhoud, zoals gedefinieerd in de HTTP API-specificatie van Regi ster v2.|
|`repository`|Tekenreeks|De naam van de opslag plaats.|
|`tag`|Tekenreeks|De naam van de grafiek code.|
|`name`|Tekenreeks|De naam van de grafiek.|
|`version`|Tekenreeks|De grafiek versie.|

### <a name="payload-example-chart-delete-event"></a>Voor beeld van Payload: grafiek verwijderen gebeurtenis

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Voor beeld van een [Azure cli](/cli/azure/acr) -opdracht waarmee de **chart_delete** Event-webhook wordt geactiveerd:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Volgende stappen

[Azure Container Registry-webhooks gebruiken](container-registry-webhook.md)