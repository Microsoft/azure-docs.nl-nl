---
title: Alternatieven voor zelf-hostende ontwikkelaarsportal
titleSuffix: Azure API Management
description: Meer informatie over alternatieve methoden die u kunt gebruiken wanneer u zelf een ontwikkelaarsportal host in Azure API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: cb14ecd05c1590667ac9b5618b3f0de9da30ce96
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741700"
---
# <a name="alternative-approaches-to-self-host-developer-portal"></a>Alternatieve benaderingen voor de zelf-hostende ontwikkelaarsportal

Er zijn verschillende alternatieve methoden die u kunt verkennen wanneer u [zelf een ontwikkelaarsportal host:](developer-portal-self-host.md)

* Gebruik productie-builds van de ontwerpfunctie en de uitgever.

* Gebruik een Azure-functie-app om uw portal te publiceren.

* Front-the-files van uw portal met een Content Delivery Network (CDN) om de laadtijden van pagina's te verminderen.

Dit artikel bevat informatie over elk van deze benaderingen. 

Als u dit nog niet hebt gedaan, stelt u een lokale [omgeving in](developer-portal-self-host.md#step-1-set-up-local-environment) voor de nieuwste versie van de ontwikkelaarsportal.

## <a name="build-for-production"></a>Bouwen voor productie

Als u de ontwikkelomgeving van de portal online wilt hosten voor samenwerkingsdoeleinden, gebruikt u productie-builds van de ontwerpfunctie en de uitgever. Productie-builds bundelen de bestanden, sluiten bronkaarten uit, enzovoort.

Maak een bundel in de `./dist/designer` map door de opdracht uit te voeren:

```sh
npm run build-designer
```

Het resultaat is een toepassing met één pagina, zodat u deze nog steeds kunt implementeren op een statische webhost, zoals de Azure Blob Storage statische website.

Plaats op dezelfde manier een gecompileerde en geoptimaliseerde uitgever in de `./dist/publisher` map :

```sh
npm run build-publisher
```

## <a name="use-function-app-to-publish-the-portal"></a>Functie-app gebruiken om de portal te publiceren

Voer de publicatiestap uit in de cloud als alternatief voor het lokaal uitvoeren ervan.

Als u publicatie wilt implementeren met een Azure-functie-app, hebt u de volgende vereisten nodig:

- [Maak een Azure-functie.](../azure-functions/functions-create-first-azure-function.md) De functie moet een JavaScript-taalfunctie zijn.
- Installeer Azure Functions Core Tools:
    ```console
    npm install –g azure-function-core-tools
    ```

### <a name="step-1-configure-output-storage"></a>Stap 1: uitvoeropslag configureren

De inhoud rechtstreeks uploaden naar de hostingwebsite ('$web'-container van uitvoeropslag), in plaats van een lokale map. Configureer deze wijziging in het `./src/config.publish.json` bestand:

```json
{
   ...
   "outputBlobStorageContainer": "$web",
   "outputBlobStorageConnectionString": "DefaultEndpointsProtocol=...",
   ...
}
```

### <a name="step-2-build-and-deploy-the-function-app"></a>Stap 2: de functie-app bouwen en implementeren

De map heeft een voorbeeld van een `./examples` HTTP-triggerfunctie. Voer de volgende opdracht uit om het te bouwen en in `./dist/function` te plaatsen:

```sh
npm run build-function
```

Meld u vervolgens aan bij de Azure CLI en implementeer deze:

```sh
az login
cd ./dist/function
func azure functionapp publish <function app name>
```

Zodra de aanroep is geïmplementeerd, kunt u deze aanroepen met een HTTP-aanroep:

```sh
curl -X POST https://<function app name>.azurewebsites.net/api/publish
```

## <a name="hosting-and-cdn"></a>Hosting en CDN

In [de zelfhosting van een ontwikkelaarsportal](developer-portal-self-host.md) is het aanbevolen om een Azure-opslagaccount te gebruiken om uw website te hosten. U kunt de bestanden echter via elke oplossing publiceren, met inbegrip van services van hostingproviders.

U kunt de bestanden ook fronten met een Content Delivery Network (CDN) om de laadtijden van pagina's te verminderen. We raden u aan om [Azure CDN.](https://azure.microsoft.com/services/cdn/)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de ontwikkelaarsportal:

- [Overzicht van de Azure API Management-ontwikkelaarsportal](api-management-howto-developer-portal.md)
