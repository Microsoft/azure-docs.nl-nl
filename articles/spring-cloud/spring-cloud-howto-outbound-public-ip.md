---
title: Instructies voor het identificeren van uitgaande open bare IP-adressen in azure lente Cloud
description: De statische uitgaande open bare IP-adressen weer geven om te communiceren met externe resources, zoals data base, opslag, Key Vault, enzovoort.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 04174b9cffb7e853dee235a4141ccda74a7847c6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663583"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>Uitgaande open bare IP-adressen in azure lente Cloud identificeren

Op deze pagina wordt uitgelegd hoe u statische uitgaande open bare IP-adressen van Azure lente-Cloud toepassingen kunt weer geven.  Open bare Ip's worden gebruikt om te communiceren met externe bronnen, zoals data bases, opslag en sleutel kluizen.

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Hoe IP-adressen werken in azure lente-Cloud

Een Azure lente-Cloud service heeft een of meer uitgaande open bare IP-adressen. Het aantal uitgaande open bare IP-adressen kan variëren afhankelijk van de lagen en andere factoren. 

De uitgaande open bare IP-adressen zijn meestal constant en blijven hetzelfde, maar er zijn uitzonde ringen.

## <a name="when-outbound-ips-change"></a>Wanneer uitgaande Ip's worden gewijzigd

Elk Azure veer Cloud-exemplaar heeft op elk gewenst moment een bepaald aantal uitgaande open bare IP-adressen. Elke uitgaande verbinding van de toepassingen, zoals een back-enddatabase, gebruikt een van de uitgaande open bare IP-adressen als het IP-bron adres. Het IP-adres wordt wille keurig in runtime geselecteerd, zodat de back-end-service de firewall moet openen voor alle uitgaande IP-adressen.

Het aantal uitgaande open bare Ip's verandert wanneer u een van de volgende acties uitvoert:

- Voer een upgrade uit van uw Azure veer Cloud-exemplaar tussen lagen.
- Verhoog een ondersteunings ticket voor meer uitgaande open bare Ip's voor bedrijfs behoeften.

## <a name="find-outbound-ips"></a>Uitgaande Ip's zoeken

Als u de uitgaande open bare IP-adressen wilt vinden die momenteel worden gebruikt door uw service-exemplaar in het Azure Portal, klikt u op **netwerken** in het navigatie deel venster van uw exemplaar. Deze worden weer gegeven in het veld **uitgaande IP-adressen** .

U kunt dezelfde informatie vinden door de volgende opdracht uit te voeren in de Cloud Shell

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
* [Meer informatie over beheerde identiteiten voor Azure-resources](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Meer informatie over de sleutel kluis in azure lente Cloud](spring-cloud-tutorial-managed-identities-key-vault.md)
