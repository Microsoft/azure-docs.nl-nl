---
title: Beheerde identiteiten gebruiken voor resources in Azure Cloud Shell
description: Code verifiëren met MSI in Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: 0fb19524079f84e92e1ddbc98a61917026492663
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89469895"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Beheerde identiteiten gebruiken voor Azure-resources in Azure Cloud Shell

Azure Cloud Shell ondersteunt autorisatie met beheerde identiteiten voor Azure-resources. Gebruik dit om toegangs tokens op te halen om veilig te communiceren met Azure-Services.

## <a name="about-managed-identities-for-azure-resources"></a>Informatie over beheerde identiteiten voor Azure-resources
Een veelvoorkomende uitdaging bij het bouwen van Cloud toepassingen is het veilig beheren van de referenties die in uw code moeten staan voor verificatie bij Cloud Services. In Cloud Shell moet u mogelijk het ophalen van de Key Vault verifiëren voor een referentie die een script mogelijk nodig heeft.

Beheerde identiteiten voor Azure-resources maken het oplossen van dit probleem eenvoudiger door Azure-Services een automatisch beheerde identiteit in Azure Active Directory (Azure AD) te geven. U kunt deze identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder dat u referenties in uw code hoeft te hebben.

## <a name="acquire-access-token-in-cloud-shell"></a>Toegangs token verkrijgen in Cloud Shell

Voer de volgende opdrachten uit om uw MSI-toegangs token in te stellen als een omgevings variabele `access_token` .
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Verloopt token verwerking

Het lokale MSI-subsysteem slaat tokens op in cache. Daarom kunt u deze zo vaak aanroepen als u wilt en een directe oproep naar Azure AD-resultaten alleen als:
- Er treedt een cache-Misser op als gevolg van een token in de cache
- het token is verlopen

Als u het token in de code in de cache opslaat, moet u voor bereid zijn op scenario's waarbij de bron aangeeft dat het token is verlopen.

Voor het afhandelen van token fouten gaat u naar de [MSI-pagina over het omslaan van MSI-toegangs tokens](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#error-handling).

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over MSI](../active-directory/managed-identities-azure-resources/overview.md)  
[Toegangs tokens verkrijgen van MSI-Vm's](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)