---
title: Technische details en vereisten voor het migreren naar Azure Cloud Services (uitgebreide ondersteuning)
description: Biedt technische details en vereisten voor het migreren van Azure Cloud Services (klassiek) naar Azure Cloud Services (uitgebreide ondersteuning)
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 4ff7d9aa2075b675a7ecd979c08d5621bbdd831a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286910"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Technische details van de migratie naar Azure Cloud Services (uitgebreide ondersteuning)   

In dit artikel worden de technische details van het hulp programma voor migratie beschreven, in verband met Cloud Services (klassiek). 

> [!IMPORTANT]
> Migratie van Cloud Services (klassiek) naar Cloud Services (uitgebreide ondersteuning) met behulp van het migratie programma is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="details-about-feature--scenarios-supported-for-migration"></a>Details over functie/scenario's die worden ondersteund voor migratie 

### <a name="extensions-and-plugin-migration"></a>Extensies en migratie van invoeg toepassingen 
- Alle ingeschakelde en ondersteunde extensies worden gemigreerd. 
- Uitgeschakelde extensies worden niet gemigreerd. 
- Plugins zijn een verouderd concept en moeten vóór de migratie worden verwijderd. Deze worden ondersteund voor migratie, maar na de migratie moet de invoeg toepassing eerst worden verwijderd voordat de extensie wordt geïnstalleerd als de extensie moet worden ingeschakeld. Extern bureau blad-invoeg toepassingen en-extensies worden het meest beïnvloed.   
 
### <a name="certificate-migration"></a>Certificaat migratie
- In Cloud Services (uitgebreide ondersteuning) worden certificaten opgeslagen in een Key Vault. Als onderdeel van de migratie maken we een Key Vault voor de klanten die de naam van de Cloud service hebben en alle certificaten van Azure Service Manager naar Key Vault overzetten. 
- De verwijzing naar deze Key Vault is opgegeven in de sjabloon of door gegeven via Power shell of Azure CLI. 

### <a name="service-configuration-and-service-definition-files"></a>Service configuratie en service definitie bestanden
- De bestanden cscfg en csdef moeten worden bijgewerkt voor Cloud Services (uitgebreide ondersteuning) met kleine wijzigingen. 
- De namen van resources zoals het virtuele netwerk en de VM-SKU verschillen. Zie de [vertaling van resources en naam Conventie post migratie](#translation-of-resources-and-naming-convention-post-migration)
- Klanten kunnen hun nieuwe implementaties ophalen via [Power shell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) en [rest-API](https://docs.microsoft.com/rest/api/compute/cloudservices/get). 

### <a name="cloud-service-and-deployments"></a>Cloud Services en implementaties
- Elke implementatie van Cloud Services (uitgebreide ondersteuning) is een onafhankelijke Cloud service. Implementaties worden niet meer in een Cloud service met sleuven gegroepeerd.
- Als u twee sleuven in uw Cloud service (klassiek) hebt, moet u één sleuf verwijderen (fase ring) en het migratie programma gebruiken om de andere (productie)-sleuf te verplaatsen naar Azure Resource Manager. 
- Het open bare IP-adres van de Cloud service-implementatie blijft hetzelfde na de migratie naar Azure Resource Manager en wordt weer gegeven als een standaard-SKU-IP (dynamische of statische) resource. 
- De DNS-naam en het domein (cloudapp.azure.net) voor de gemigreerde Cloud service blijven hetzelfde. 

### <a name="virtual-network-migration"></a>Migratie van virtueel netwerk
- Als een Cloud Services-implementatie zich in een virtueel netwerk bevindt, wordt tijdens de migratie alle Cloud Services en de gekoppelde virtuele netwerk bronnen samen gemigreerd. 
- Na de migratie wordt het virtuele netwerk in een andere resource groep geplaatst dan de Cloud service. 
- Voor virtuele netwerken met meerdere Cloud Services wordt elke Cloud service één na de andere gemigreerd. 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>Migratie van implementaties die niet in een virtueel netwerk zijn geïmplementeerd
- In 2017 is Azure automatisch begonnen met het maken van nieuwe implementaties (zonder door de klant opgegeven virtueel netwerk) in een platform dat "standaard virtueel netwerk" is gemaakt. Deze virtuele standaard netwerken zijn verborgen voor klanten.   
- Als onderdeel van de migratie wordt dit virtuele standaard netwerk aan klanten weer gegeven in Azure Resource Manager. Voor het beheren of bijwerken van de implementatie in Azure Resource Manager, moeten klanten deze gegevens van het virtuele netwerk toevoegen aan de sectie NetworkConfiguration van het cscfg-bestand.    
- Het standaard virtuele netwerk, wanneer het naar Azure Resource Manager wordt gemigreerd, wordt in dezelfde resource groep geplaatst als de Cloud service.
- Cloud Services die vóór deze tijd zijn gemaakt, bevindt zich niet in een virtueel netwerk en kunnen niet worden gemigreerd met het hulp programma. Overweeg deze Cloud Services rechtstreeks te implementeren in Azure Resource Manager. 
- Als u wilt controleren of een implementatie in aanmerking komt voor migratie, voert u de API validate uit voor de implementatie. Het resultaat van de validate API bevat fout berichten die expliciet worden vermeld als deze implementatie in aanmerking komt voor migratie.     

### <a name="load-balancer"></a>Load Balancer   
- Voor een Cloud service die gebruikmaakt van een openbaar eind punt, wordt een platform dat is gemaakt load balancer dat is gekoppeld aan de Cloud service, weer gegeven in het abonnement van de klant in Azure Resource Manager. De load balancer is een alleen-lezen resource en updates worden alleen beperkt door de service configuratie (. cscfg) en de service definition (csdef)-bestanden. 

### <a name="key-vault"></a>Key Vault
- Als onderdeel van de migratie maakt Azure automatisch een nieuwe Key Vault en worden alle certificaten ernaar gemigreerd. U kunt met het hulp programma geen bestaande Key Vault gebruiken. 
- Voor Cloud Services (uitgebreide ondersteuning) is een Key Vault vereist dat zich in dezelfde regio en hetzelfde abonnement bevindt. Deze Key Vault wordt automatisch gemaakt als onderdeel van de migratie. 


## <a name="translation-of-resources-and-naming-convention-post-migration"></a>Vertaling van resources en naam Conventie post migratie
Als onderdeel van de migratie worden de resource namen gewijzigd en worden enkele Cloud Services-functies weer gegeven als Azure Resource Manager resources. De tabel bevat een overzicht van de wijzigingen die specifiek zijn voor Cloud Services migratie.

| Cloud Services (klassiek) <br><br> Resourcenaam | Cloud Services (klassiek) <br><br> Syntax| Cloud Services (uitgebreide ondersteuning) <br><br> Resourcenaam| Cloud Services (uitgebreide ondersteuning) <br><br> Syntax | 
|---|---|---|---|
| Cloudservice | `cloudservicename` | Niet gekoppeld| Niet gekoppeld |
| Implementatie (Portal gemaakt) <br><br> Implementatie (niet-Portal gemaakt)  | `deploymentname` | Cloud Services (uitgebreide ondersteuning) | `deploymentname` |  
| Virtual Network | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> Niet gekoppeld |  Virtual Network (geen portal gemaakt) <br><br> Virtual Network (Portal gemaakt) <br><br> Virtuele netwerken (standaard) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `DefaultRdfevirtualnetwork_vnetid`|
| Niet gekoppeld | Niet gekoppeld | Key Vault | `cloudservicename` | 
| Niet gekoppeld | Niet gekoppeld | Resource groep voor Cloud service-implementaties | `cloudservicename-migrated` | 
| Niet gekoppeld | Niet gekoppeld | Resource groep voor Virtual Network | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| Niet gekoppeld | Niet gekoppeld | Openbaar IP-adres (dynamisch) | `cloudservicenameContractContract` | 
| Gereserveerd IP naam | `reservedipname` | Gereserveerd IP (niet-Portal gemaakt) <br><br> Gereserveerd IP (Portal gemaakt) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| Niet gekoppeld| Niet gekoppeld | Load Balancer | `deploymentname-lb`|



## <a name="migration-issues-and-how-to-handle-them"></a>Migratie problemen en hoe u deze kunt afhandelen. 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>Migratie is gedurende lange tijd vastgelopen in een bewerking. 
- Het door voeren, voorbereiden en afbreken kan enige tijd duren, afhankelijk van het aantal implementaties. Er is een time-out opgestaan van bewerkingen na 24 uur.   
- Bewerkingen voor door voeren, voorbereiden en afbreken zijn idempotent. De meeste problemen zijn fixable door opnieuw te proberen. Er kunnen tijdelijke fouten optreden, die binnen enkele minuten kunnen worden uitgevoerd. het wordt aangeraden na een onderbreking een nieuwe poging uit te voeren. Als de migratie wordt uitgevoerd met behulp van de Azure Portal en de bewerking vastloopt, gebruikt u Power shell om de bewerking opnieuw uit te voeren. 
- Neem contact op met de ondersteuning om de implementatie van de back-end te migreren of terug te draaien. 

### <a name="migration-failed-in-an-operation"></a>De migratie is mislukt in een bewerking. 
- Als de validatie is mislukt, komt dit omdat de implementatie of het virtuele netwerk een niet-ondersteund scenario/onderdeel/bron bevat. Gebruik de lijst met niet-ondersteunde scenario's om de omzeiling in de documenten te vinden.  
- Bij het voorbereiden van de bewerking wordt eerst de validatie uitgevoerd, inclusief een aantal dure validaties (die niet worden behandeld in validate). Het voorbereiden van de fout kan worden veroorzaakt door een niet-ondersteund scenario. Zoek het scenario en de tijdelijke oplossing in de open bare documenten. Afbreken moet worden aangeroepen om terug te gaan naar de oorspronkelijke status en de implementatie voor updates en verwijder bewerkingen te ontgrendelen.
- Als het afbreken is mislukt, voert u de bewerking opnieuw uit. Als nieuwe pogingen mislukken, neemt u contact op met de ondersteuning.
- Als het door voeren is mislukt, voert u de bewerking opnieuw uit. Als nieuwe poging mislukt, neemt u contact op met de ondersteuning. Zelfs bij een doorvoer fout moet er geen gegevens vlak probleem zijn met uw implementatie. Uw implementatie moet het verkeer van de klant kunnen afhandelen zonder dat er problemen zijn. 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>De portal is vernieuwd na de voor bereiding. De ervaring is opnieuw gestart en door voeren of afbreken niet meer zichtbaar. 
- In de portal worden de migratie gegevens lokaal opgeslagen en daarom na het vernieuwen wordt de fase gevalideerd, zelfs als de Cloud service zich in de fase voorbereiden bevindt.  
- U kunt de portal gebruiken om door te gaan met de stappen valideren en opnieuw voorbereiden om de knop afbreken en door voeren weer te geven. Er worden geen fouten veroorzaakt.
- Klanten kunnen Power shell of rest API gebruiken om af te breken of door te voeren. 

### <a name="how-much-time-can-the-operations-takebr"></a>Hoeveel tijd kan de activiteiten nemen?<br>
Valideren is snel ontworpen. De voor bereiding is voor het eerst uitgevoerd en neemt enige tijd in beslag, afhankelijk van het totale aantal rolinstanties dat wordt gemigreerd. Het afbreken en door voeren kan enige tijd duren, maar neemt minder tijd in beslag dan voor bereiding. Alle bewerkingen worden na 24 uur getimed. 
