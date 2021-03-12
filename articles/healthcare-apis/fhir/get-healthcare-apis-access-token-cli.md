---
title: Toegangs Token ophalen met behulp van Azure CLI-Azure API voor FHIR
description: In dit artikel wordt uitgelegd hoe u een toegangs token voor Azure API kunt verkrijgen voor FHIR met behulp van de Azure CLI.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: matjazl
ms.openlocfilehash: b7bdb7f4a22d080f382fea984e710980428067ff
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018269"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Toegangs token voor Azure API voor FHIR ophalen met behulp van Azure CLI

In dit artikel leert u hoe u een toegangs token kunt verkrijgen voor de Azure API voor FHIR met behulp van de Azure CLI. Wanneer u [de Azure-API voor FHIR inricht](fhir-paas-portal-quickstart.md), configureert u een set gebruikers of service-principals die toegang hebben tot de service. Als uw gebruikers object-ID in de lijst met toegestane object-Id's staat, kunt u toegang krijgen tot de service met behulp van een token dat is verkregen met behulp van de Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>Een token verkrijgen

De Azure-API voor FHIR maakt `resource`  Gebruik `Audience` van een of met URI die gelijk is aan de URI van de FHIR-server `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . U kunt een Token ophalen en opslaan in een variabele ( `$token` met de naam) met de volgende opdracht:

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Gebruiken met Azure API voor FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een toegangs token kunt verkrijgen voor de Azure API voor FHIR met behulp van de Azure CLI. Als u wilt weten hoe u de FHIR-API kunt openen met behulp van Postman, gaat u verder met de zelfstudie Postman.

>[!div class="nextstepaction"]
>[Toegang tot FHIR-API met Postman](access-fhir-postman-tutorial.md)
