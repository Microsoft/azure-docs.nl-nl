---
title: Over Azure Cloud Services (uitgebreide ondersteuning)
description: Meer informatie over de onderliggende elementen van het netwerk configuratie-element van het service configuratie bestand, waarmee Virtual Network-en DNS-waarden worden opgegeven.
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cf7c4b881697b664403d8c817c3b9e48fb48944d
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746761"
---
# <a name="about-azure-cloud-services-extended-support"></a>Over Azure Cloud Services (uitgebreide ondersteuning)

> [!IMPORTANT]
> Cloud Services (uitgebreide ondersteuning) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Cloud Services (uitgebreide ondersteuning) is een nieuw implementatie model op basis van [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) voor [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) product en is momenteel beschikbaar als open bare preview. Cloud Services (uitgebreide ondersteuning) heeft het belangrijkste voor deel van het leveren van regionale tolerantie samen met de functie pariteit met Azure Cloud Services geïmplementeerd met Azure Service Manager. Het biedt ook een aantal ARM-mogelijkheden, zoals op rollen gebaseerde toegang en beheer (RBAC), tags, beleid en ondersteuning voor implementatie sjablonen.  

Met deze wijziging wordt de naam van het implementatie model op basis van Azure Service Manager voor Cloud Services [Cloud Services (klassiek)](../cloud-services/cloud-services-choose-me.md)gewijzigd. U behoudt de mogelijkheid om uw web-en Cloud toepassingen en-services te bouwen en snel te implementeren. U kunt uw infra structuur voor Cloud Services schalen op basis van de huidige vraag en ervoor zorgen dat de prestaties van uw toepassingen kunnen blijven bestaan terwijl tegelijkertijd de kosten worden verminderd.  

## <a name="what-does-not-change"></a>Wat wordt niet gewijzigd 
- U maakt de code, definieert de configuraties en implementeert deze in Azure. Azure stelt de reken omgeving in, voert uw code uit en bewaakt deze voor u.
- Cloud Services (uitgebreide ondersteuning) ondersteunt ook twee soorten rollen: [Web en worker](../cloud-services/cloud-services-choose-me.md). 
- De drie onderdelen, de service definitie (. csdef), de service configuratie (. cscfg) en een service pakket (. cspkg) van een Cloud service worden getransporteerd en er is geen wijziging in de [notaties](cloud-services-model-and-package.md). 
- Er zijn geen wijzigingen vereist voor runtime code omdat het gegevens vlak hetzelfde is en het besturings vlak alleen wordt gewijzigd.  

## <a name="changes-in-deployment-model"></a>Wijzigingen in het implementatie model

Er zijn minimale wijzigingen vereist voor het implementeren van de service configuratie (. cscfg) en de service definitie bestanden (. csdef) Cloud Services (uitgebreide ondersteuning). Er zijn geen wijzigingen vereist voor runtime code. Implementatie scripts moeten echter worden bijgewerkt om de nieuwe Api's op basis van Azure Resource Manager te kunnen aanroepen. 

:::image type="content" source="media/overview-image-1.png" alt-text="Afbeelding toont de klassieke Cloud service configuratie met toevoeging van sjabloon sectie. ":::

De belangrijkste verschillen tussen Cloud Services (klassiek) en Cloud Services (uitgebreide ondersteuning) ten opzichte van de implementatie zijn: 

- Azure Resource Manager implementaties gebruiken [arm-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) die een JavaScript object NOTATION (JSON)-bestand zijn waarmee de infra structuur en configuratie voor uw project worden gedefinieerd. De sjabloon gebruikt een declaratieve syntaxis. Dit is een syntaxis waarmee u kunt aangeven wat u wilt implementeren zonder hiervoor de nodige reeks programmeeropdrachten te hoeven maken. Service configuratie en service definitie bestand moeten consistent zijn met de [arm-sjabloon](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) tijdens de implementatie van Cloud Services (uitgebreide ondersteuning). Dit kan worden bereikt door [de arm-sjabloon hand matig te maken](deploy-template.md) of door [Power shell](deploy-powershell.md), [Portal](deploy-portal.md) en [Visual Studio](deploy-visual-studio.md)te gebruiken.  

- Klanten moeten [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) gebruiken om [certificaten te beheren in Cloud Services (uitgebreide ondersteuning)](certificates-and-key-vault.md). Met Azure Key Vault kunt u toepassings referenties zoals geheimen, sleutels en certificaten veilig opslaan en beheren in een centrale en beveiligde Cloud opslagplaats. Uw toepassingen kunnen tijdens de uitvoering verifiëren Key Vault om referenties op te halen. 

- Alle resources die via de [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) zijn geïmplementeerd, moeten zich in een virtueel netwerk bevindt. Virtuele netwerken en subnetten worden in Azure Resource Manager gemaakt met behulp van bestaande Azure Resource Manager-Api's en er moet naar worden verwezen in de sectie NetworkConfiguration van het. cscfg bij het implementeren van Cloud Services (uitgebreide ondersteuning).   

- Elke Cloud service (uitgebreide ondersteuning) is een single-onafhankelijke implementatie. Cloud Services (uitgebreide ondersteuning) biedt geen ondersteuning voor meerdere sleuven binnen één Cloud service.  
    - Wissel <sup>*</sup> mogelijkheid voor VIP kan worden gebruikt om te wisselen tussen twee Cloud Services (uitgebreide ondersteuning). Als u een nieuwe release van een Cloud service wilt testen en faseren, implementeert u een Cloud service (uitgebreide ondersteuning) en kunt u deze als VIP-verwisselbaar labelen met een andere Cloud service (uitgebreide ondersteuning)  

- Domain Name Service-label (DNS) is optioneel voor een Cloud service (uitgebreide ondersteuning). In Azure Resource Manager is het DNS-label een eigenschap van de open bare IP-resource die aan de Cloud service is gekoppeld. 


<sup>*</sup> VIP swap voor Cloud Services (uitgebreide ondersteuning) is niet beschikbaar tijdens de open bare preview.  

## <a name="migration-to-azure-resource-manager"></a>Migratie naar Azure Resource Manager

Cloud Services (uitgebreide ondersteuning) biedt twee paden die u kunt migreren van [Azure Service Manager](https://docs.microsoft.com/powershell/azure/servicemanagement/overview?view=azuresmps-4.0.0&preserve-view=true ) naar [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview). 
1) Klanten implementeren Cloud Services rechtstreeks in Azure Resource Manager en verwijderen vervolgens de oude Cloud service in azure Service Manager. 
2) In-place migratie biedt ondersteuning voor de mogelijkheid om Cloud Services (klassiek) te migreren met minimale uitval tijd tot Cloud Services (uitgebreide ondersteuning). 

### <a name="additional-migration-options"></a>Aanvullende migratie opties

Bij het evalueren van migratie plannen van Cloud Services (klassiek) naar Cloud Services (uitgebreide ondersteuning) wilt u mogelijk aanvullende Azure-Services onderzoeken, zoals: [Virtual Machine Scale sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview), [app service](https://docs.microsoft.com/azure/app-service/overview), [azure Kubernetes service](https://docs.microsoft.com/azure/aks/intro-kubernetes)en [Azure service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview). Deze services blijven extra mogelijkheden, terwijl Cloud Services (uitgebreide ondersteuning) voornamelijk de functie pariteit onderhoudt met Cloud Services (klassiek.) 

Afhankelijk van de toepassing, heeft Cloud Services (uitgebreide ondersteuning) mogelijk aanzienlijk minder inspanning nodig om over te stappen op Azure Resource Manager vergeleken met andere opties. Als uw toepassing zich niet in ontwikkeling bevindt, is Cloud Services (uitgebreide ondersteuning) een geschikte optie om te overwegen, omdat het een snelle migratie heeft. Als uw toepassing zich voortdurend ontwikkelt en u een meer moderne functies wilt instellen, kunt u ook andere Azure-Services verkennen om uw huidige en toekomstige vereisten beter te verhelpen. 

## <a name="next-steps"></a>Volgende stappen
- Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
