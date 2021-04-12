---
title: Azure Cloud Services (klassiek) migreren naar Azure Cloud Services (uitgebreide ondersteuning)
description: Overzicht van de migratie van Cloud Services (klassiek) naar de Cloud service (uitgebreide ondersteuning)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 96315899f80d0bd02ac3d2108b7cd76876025218
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286911"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Azure Cloud Services (klassiek) migreren naar Azure Cloud Services (uitgebreide ondersteuning)

Dit artikel bevat een overzicht van het door het platform ondersteunde migratie programma en hoe u dit kunt gebruiken om [azure Cloud Services (klassiek)](../cloud-services/cloud-services-choose-me.md) te migreren naar [Azure Cloud Services (uitgebreide ondersteuning)](overview.md).

Het migratie hulpprogramma gebruikt dezelfde Api's en heeft dezelfde ervaring als de migratie van de [virtuele machine (klassiek)](https://docs.microsoft.com/azure/virtual-machines/migration-classic-resource-manager-overview). 

> [!IMPORTANT]
> Migratie van Cloud Services (klassiek) naar Cloud Services (uitgebreide ondersteuning) met behulp van het migratie programma is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Raadpleeg de volgende bronnen als u hulp nodig hebt bij uw migratie: 

- [Micro soft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html): micro soft en Community-ondersteuning voor migratie.
- [Azure-migratie ondersteuning](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%22pesId%22:%22e79dcabe-5f77-3326-2112-74487e1e5f78%22,%22supportTopicId%22:%22fca528d2-48bd-7c9f-5806-ce5d5b1d226f%22%7D): specifiek ondersteunings team voor technische ondersteuning tijdens de migratie. Klanten die geen technische ondersteuning hebben, kunnen [gratis ondersteuning](https://aka.ms/cs-migration-errors) gebruiken die specifiek is voor deze migratie.
- Als uw bedrijf/organisatie is gekoppeld aan micro soft of werkt met vertegenwoordigers van micro soft, zoals Cloud Solution Architects of Technical Account managers, kunt u contact met hen opdoen voor meer bronnen voor migratie.
- Vul [deze enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR--AgudUMwJKgRGMO84rHQtUQzZYNklWUk4xOTFXVFBPOFdGOE85RUIwVC4u) in om feedback te geven of om problemen op te lossen voor het product team van de Cloud Services (uitgebreide ondersteuning). 

## <a name="migration-benefits"></a>Migratie voordelen
De door het platform ondersteunde migratie biedt de volgende belang rijke voor delen:
- De migratie is volledig georganiseerd door het platform, waarbij de volledige implementatie en gekoppelde resources worden verplaatst naar Azure Resource Manager.
- Geen downtime-migratie.
- Eenvoudige en snelle migratie in vergelijking met andere migratie paden door hand matige taken te minimaliseren. 
- Behoudt Cloud Services IP-adres en DNS-label als onderdeel van de migratie. 

Zie [Cloud Services (uitgebreide ondersteuning)](overview.md) en [Azure Resource Manager](../azure-resource-manager/management/overview.md)voor andere voor delen en waarom u wilt migreren. 

## <a name="setup-access-for-migration"></a>Toegang instellen voor migratie

Als u deze migratie wilt uitvoeren, moet u worden toegevoegd als een cobeheerder voor het abonnement en de benodigde providers registreren. 

1. Meld u aan bij Azure Portal.
3. Selecteer in het menu hub de optie abonnement. Als u deze niet ziet, selecteert u alle services.
3. Zoek de juiste abonnements vermelding en Bekijk het veld mijn rol. Voor een cobeheerder moet de waarde account beheerder zijn. Als u geen mede beheerder kunt toevoegen, neemt u contact op met een service beheerder of mede beheerder voor het abonnement om aan de slag te gaan.

4. Uw abonnement voor micro soft. ClassicInfrastructureMigrate-naam ruimte registreren met behulp van [Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Power shell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) of [cli](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. Uw abonnement voor de preview-functie van Cloud Services Migration registreren met behulp van [Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Power shell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) of [cli](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)

    ```powershell
    Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

6. Controleer de status van uw registratie. Het volt ooien van de registratie kan enkele minuten duren. 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>Hoe wordt de migratie voor Cloud Services (klassiek) afwijkend van Virtual Machines (klassiek)?
Azure Service Manager ondersteunt twee verschillende Compute-producten, [azure virtual machines (klassiek)](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) en [Azure Cloud Services (klassiek)](../cloud-services/cloud-services-choose-me.md) of Web/werk rollen. De twee producten verschillen op basis van het implementatie type dat binnen de Cloud service valt. Azure Cloud Services (klassiek) maakt gebruik van Cloud service met implementaties met Web/werk rollen. Azure Virtual Machines (klassiek) maakt gebruik van een Cloud service met implementaties met IaaS-Vm's.

De lijst met ondersteunde scenario's verschilt van Cloud Services (klassiek) en Virtual Machines (klassiek) vanwege verschillen in de implementatie typen.

## <a name="migration-steps"></a>Migratiestappen
 
Klanten kunnen hun Cloud Services (klassieke) implementaties migreren met behulp van dezelfde vier bewerkingen die worden gebruikt voor het migreren van Virtual Machines (klassiek). 

1. **Migratie valideren** : valideert dat de migratie niet wordt voor komen door algemene niet-ondersteunde scenario's.
2. **Migratie voorbereiden** : de meta gegevens van de resource worden gedupliceerd in azure Resource Manager. Alle resources zijn vergrendeld voor bewerkingen voor maken/bijwerken/verwijderen om ervoor te zorgen dat de meta gegevens van de bron synchroon zijn tussen Azure Serverbeheer en Azure Resource Manager. Alle Lees bewerkingen werken met behulp van de Api's Cloud Services (klassiek) en Cloud Services (uitgebreide ondersteuning).
3. **Migratie afbreken** : verwijdert de meta gegevens van de bron uit Azure Resource Manager. Hiermee ontgrendelt u alle resources voor bewerkingen voor maken/bijwerken/verwijderen.
4. **Migratie door voeren** : verwijdert de meta gegevens van de resource uit Azure Service Manager. Hiermee ontgrendelt u de resource voor bewerkingen voor maken/bijwerken/verwijderen. Afbreken is niet langer toegestaan na het door voeren van de bewerking.

>[!NOTE]
> Voorbereiden, afbreken en door voeren zijn idempotent. als dit mislukt, moet u een nieuwe poging doen om het probleem op te lossen.

:::image type="content" source="media/in-place-migration-1.png" alt-text="Afbeelding toont een diagram van de stappen die zijn gekoppeld aan de migratie.":::

Zie [overzicht van door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md) voor meer informatie

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>Ondersteunde bronnen en functies die beschikbaar zijn voor migratie die is gekoppeld aan Cloud Services (klassiek)
-   Storage Accounts
-   Virtuele netwerken
-   Netwerkbeveiligingsgroepen
-   Gereserveerde open bare IP-adressen
-   Access Control-lijsten van eind punten
-   Door de gebruiker gedefinieerde routes
-   Interne load balancer
-   Certificaat migratie naar sleutel kluis
-   Invoeg toepassingen en uitbrei dingen (op XML en JSON gebaseerd)
-   Bij starten/op Stop taken
-   Implementaties met versneld netwerken
-   Implementaties met behulp van één of meerdere rollen
-   Basis load balancer
-   Invoer, instantie-invoer, interne eind punten
-   Dynamische open bare IP-adressen
-   DNS-naam 
-   Regels voor netwerk verkeer
-   Hypernet virtueel netwerk

## <a name="supported-configurations--migration-scenarios"></a>Ondersteunde configuraties/migratie scenario's
Dit zijn de belangrijkste scenario's met betrekking tot combi Naties van resources, functies en Cloud Services. Deze lijst is niet limitatief.

| Service | Configuratie | Opmerkingen | 
|---|---|---|
| [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet) | Virtuele netwerken die Azure Active Directory Domain Services bevatten. | Virtueel netwerk met Cloud service-implementatie en Azure AD Domain Services wordt ondersteund. De klant moet eerst Azure AD Domain Services afzonderlijk migreren en het virtuele netwerk vervolgens alleen naar links migreren met de Cloud service-implementatie |
| Cloudservice | Cloud service met een implementatie in slechts één sleuf. | Cloud Services met een implementatie van een productie-of faserings sleuf kan worden gemigreerd |
| Cloudservice | Implementatie niet in een openbaar zichtbaar virtueel netwerk (standaard implementatie van virtueel netwerk) | Een Cloud service kan zich in een openbaar zichtbaar virtueel netwerk bevinden, in een verborgen virtueel netwerk of niet in een virtueel netwerk.  Cloud Services in een verborgen virtueel netwerk en open bare virtuele netwerken worden ondersteund voor migratie. De klant kan de validate API gebruiken om te bepalen of een implementatie zich in een standaard virtueel netwerk bevindt, of niet, en zo vast te stellen of het kan worden gemigreerd. |
|Cloudservice | XML-extensies (BGInfo, Visual Studio Debugger, Web Deploy en Remote Debugging). | Alle XML-extensies worden ondersteund voor migratie 
| Virtual Network | Virtueel netwerk met meerdere Cloud Services. | Het virtuele netwerk bevat meerdere Cloud Services die worden ondersteund voor migratie. Het virtuele netwerk en alle Cloud Services erin worden samen met Azure Resource Manager gemigreerd. |
| Virtual Network | Migratie van virtuele netwerken die zijn gemaakt via de portal (vereist het gebruik van ' groeps bron-groeps naam VNet-naam ' in. cscfg-bestand)  | Als onderdeel van de migratie wordt de naam van het virtuele netwerk in cscfg gewijzigd om Azure Resource Manager ID van het virtuele netwerk te gebruiken. (abonnement/abonnement-id/resource-groep/resource-groep-naam/resource/vnet-naam) <br><br>Als u de implementatie na de migratie wilt beheren, werkt u het lokale exemplaar van het. cscfg-bestand bij om Azure Resource Manager-ID te gebruiken in plaats van de naam van het virtuele netwerk. <br><br>Een. cscfg-bestand dat gebruikmaakt van het oude naamgevings schema, wordt niet gevalideerd. 
| Virtual Network | Migratie van de implementatie met rollen in een ander subnet. | Een Cloud service met verschillende rollen in verschillende subnetten wordt ondersteund voor migratie. |
    

## <a name="resources-and-features-not-available-for-migration"></a>Resources en functies zijn niet beschikbaar voor migratie
Dit zijn de belangrijkste scenario's met betrekking tot combi Naties van resources, functies en Cloud Services. Deze lijst is niet limitatief. 

| Resource | Volgende stappen/omzeilen | 
|---|---|
| Regels automatisch schalen | De migratie loopt door, maar regels worden verwijderd. [Maak de regels na de](https://docs.microsoft.com/azure/cloud-services-extended-support/configure-scaling) migratie op Cloud Services (uitgebreide ondersteuning) opnieuw. | 
| Waarschuwingen | De migratie gaat door, maar waarschuwingen worden verwijderd. [Maak de regels na de](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-alerts) migratie op Cloud Services (uitgebreide ondersteuning) opnieuw. | 
| VPN Gateway | Verwijder de VPN Gateway voordat u begint met de migratie en maak de VPN Gateway opnieuw nadat de migratie is voltooid. | 
| Express route-gateway (in hetzelfde abonnement als alleen Virtual Network) | Verwijder de Express route-gateway voordat u begint met de migratie en maak de gateway opnieuw nadat de migratie is voltooid. | 
| Quota  | Het quotum wordt niet gemigreerd. [Vraag een nieuw quotum](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-resource-quota#solution) aan Azure Resource Manager voorafgaand aan de migratie voordat de validatie is geslaagd. | 
| Affiniteitsgroepen | Wordt niet ondersteund. Verwijder alle affiniteits groepen vóór de migratie.  | 
| Virtuele netwerken die gebruikmaken van [peering op virtueel netwerk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)| Voordat u een virtueel netwerk migreert dat is gekoppeld aan een ander virtueel netwerk, verwijdert u de peering, migreert u het virtuele netwerk naar Resource Manager en maakt u peering opnieuw. Dit kan uitval tijd veroorzaken afhankelijk van de architectuur. | 
| Virtuele netwerken die App Service omgevingen bevatten | Niet ondersteund | 
| Virtuele netwerken die HDInsight-services bevatten | Wordt niet ondersteund. 
| Virtuele netwerken die Azure API Management-implementaties bevatten | Wordt niet ondersteund. <br><br> Als u het virtuele netwerk wilt migreren, wijzigt u het virtuele netwerk van de API Management-implementatie. Dit is een niet-downtime-bewerking. | 
| Klassieke Express route-circuits | Wordt niet ondersteund. <br><br>Deze circuits moeten naar Azure Resource Manager worden gemigreerd voordat de migratie van PaaS wordt gestart. Zie [ExpressRoute-circuits verplaatsen van het klassieke naar het Resource Manager-implementatie model](../expressroute/expressroute-howto-move-arm.md)voor meer informatie. |  
| Op rollen gebaseerd toegangsbeheer | Na de migratie moet de URI van de bron worden `Microsoft.ClassicCompute` bijgewerkt van naar het `Microsoft.Compute` RBAC-beleid. | 
| Application Gateway | Niet ondersteund. <br><br> Verwijder de Application Gateway voordat u begint met de migratie en maak de Application Gateway opnieuw nadat de migratie is voltooid naar Azure Resource Manager | 

## <a name="unsupported-configurations--migration-scenarios"></a>Niet-ondersteunde configuraties/migratie scenario's

| Configuratie/scenario  | Volgende stappen/omzeilen | 
|---|---|
| Migratie van een aantal oudere implementaties die niet in een virtueel netwerk zijn | Sommige Cloud service-implementaties die zich niet in een virtueel netwerk bevinden, worden niet ondersteund voor migratie. <br><br> 1. Gebruik de validate API om te controleren of de implementatie in aanmerking komt voor migratie. <br> 2. Als u in aanmerking komt, worden de implementaties verplaatst naar Azure Resource Manager onder een virtueel netwerk met het voor voegsel ' DefaultRdfeVnet ' | 
| Migratie van implementaties die zowel implementatie van productie-als faserings sleuf met dynamische IP-adressen bevatten | Voor de migratie van een Cloud service met twee sleuven moet de faserings sleuf worden verwijderd. Zodra de faserings sleuf is verwijderd, migreert u de productie site als een onafhankelijke Cloud service (uitgebreide ondersteuning) in Azure Resource Manager. Implementeer de faserings omgeving vervolgens opnieuw als een nieuwe Cloud service (uitgebreide ondersteuning) en maak deze verwisselbaar met de eerste. | 
| Migratie van implementaties met behulp van de implementatie van productie-en faserings sleuven met Gereserveerd IP adressen | Wordt niet ondersteund. | 
| Migratie van productie-en faserings implementatie in een ander virtueel netwerk|Voor de migratie van een Cloud service met twee sleuven moet de faserings sleuf worden verwijderd. Zodra de faserings sleuf is verwijderd, migreert u de productie site als een onafhankelijke Cloud service (uitgebreide ondersteuning) in Azure Resource Manager. Een nieuwe implementatie van Cloud Services (uitgebreide ondersteuning) kan vervolgens worden gekoppeld aan de gemigreerde implementatie with swapable-eigenschap ingeschakeld. Implementatie bestanden van de oude implementatie van de faserings sleuf kunnen opnieuw worden gebruikt voor het maken van deze nieuwe Verwissel bare implementatie. | 
| Migratie van een lege Cloud service (Cloud service zonder implementatie) | Wordt niet ondersteund. | 
| Migratie van de implementatie met de extern bureau blad-invoeg toepassing en de extern bureau blad-extensies | Optie 1: Verwijder de invoeg toepassing extern bureau blad vóór de migratie. Dit vereist wijzigingen in de implementatie bestanden. De migratie wordt vervolgens door lopen. <br><br> Optie 2: de extensie extern bureau blad verwijderen en de implementatie migreren. Na de migratie verwijdert u de invoeg toepassing en installeert u de extensie. Dit vereist wijzigingen in de implementatie bestanden. <br><br> Verwijder de invoeg toepassing en de extensie vóór de migratie. [Invoeg toepassingen worden niet aanbevolen](https://docs.microsoft.com/azure/cloud-services-extended-support/deploy-prerequisite#required-service-definition-file-csdef-updates) voor gebruik op Cloud Services (uitgebreide ondersteuning).| 
| Virtuele netwerken met PaaS-en IaaS-implementatie |Niet ondersteund <br><br> Verplaats de implementaties van PaaS of IaaS naar een ander virtueel netwerk. Dit leidt tot uitval tijd. | 
Cloud service-implementaties met behulp van verouderde rollen grootten (zoals kleine of ExtraLarge). | De migratie wordt voltooid, maar de grootte van de rollen wordt bijgewerkt voor gebruik van moderne rollen. Er zijn geen wijzigingen in de kosten-of SKU-eigenschappen en de virtuele machine wordt niet opnieuw opgestart voor deze wijziging. Werk alle implementatie artefacten bij om te verwijzen naar deze nieuwe grootten van moderne rollen. Zie [beschik bare VM-grootten](available-sizes.md) voor meer informatie|
| Migratie van Cloud service naar ander virtueel netwerk | Niet ondersteund <br><br> 1. Verplaats de implementatie naar een ander klassiek virtueel netwerk vóór de migratie. Dit leidt tot uitval tijd. <br> 2. Migreer het nieuwe virtuele netwerk naar Azure Resource Manager. <br><br> of <br><br> 1. Migreer het virtuele netwerk naar Azure Resource Manager <br>2. Verplaats de Cloud service naar een nieuw virtueel netwerk. Dit leidt tot uitval tijd. | 
| Cloud service in een virtueel netwerk, maar er is geen expliciet subnet toegewezen | Wordt niet ondersteund. Voor risico beperking moet u de rol in een subnet verplaatsen. hiervoor moet een rol opnieuw worden opgestart (downtime) | 


## <a name="post-migration-changes"></a>Wijzigingen na de migratie
De implementatie van de Cloud Services (klassiek) wordt geconverteerd naar een implementatie van een Cloud service (uitgebreide ondersteuning). Raadpleeg de [documentatie van Cloud Services (uitgebreide ondersteuning)](deploy-prerequisite.md) voor meer informatie.  

### <a name="changes-to-deployment-files"></a>Wijzigingen in de implementatie bestanden 

Er worden kleine wijzigingen aangebracht in het. csdef-en. cscfg-bestand van de klant om ervoor te zorgen dat de implementatie bestanden voldoen aan de vereisten voor Azure Resource Manager en Cloud Services (uitgebreide ondersteuning). Na de migratie worden de nieuwe implementatie bestanden opgehaald of worden de bestaande bestanden bijgewerkt. Dit is nodig voor bijwerk-en verwijder bewerkingen.  

- Virtual Network gebruikt een volledige Azure Resource Manager Resource-ID in plaats van alleen de resource naam in de sectie NetworkConfiguration van het cscfg-bestand. Bijvoorbeeld `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`. Voor virtuele netwerken die deel uitmaken van dezelfde resource groep als de Cloud service, kunt u ervoor kiezen om het. cscfg-bestand weer bij te werken met alleen de naam van het virtuele netwerk.  

- Klassieke grootten zoals kleine, grote en ExtraLarge worden vervangen door de nieuwe grootte namen Standard_A *. De grootte namen moeten worden gewijzigd in de nieuwe namen in csdef-bestand. Zie voor meer informatie de [vereisten voor de implementatie van Cloud Services (uitgebreide ondersteuning)](deploy-prerequisite.md#required-service-definition-file-csdef-updates)

- Gebruik de Get API om de nieuwste kopie van de implementatie bestanden op te halen. 
    - De sjabloon ophalen met behulp van [Portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal), [Power shell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell#export-resource-groups-to-templates), [cli](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli#export-resource-groups-to-templates)en [rest API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate) 
    - Haal het. csdef-bestand op met behulp van [Power shell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) of [rest-API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package). 
    - Haal het. cscfg-bestand op met behulp van [Power shell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) of [rest-API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package). 
    
 

### <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>Wijzigingen in de automatiserings-, CI/CD-pijp lijn van de klant, aangepaste scripts, aangepaste Dash boards, aangepaste hulp middelen, enzovoort.  

Klanten moeten hun hulp middelen en automatisering bijwerken om te beginnen met het gebruik van de nieuwe Api's/opdrachten voor het beheren van hun implementatie. De klant kan eenvoudig nieuwe functies en mogelijkheden van Azure Resource Manager/Cloud Services (uitgebreide ondersteuning) nemen als onderdeel van deze wijziging. 

- Wijzigingen in de namen van resources en resource groepen na migratie
    - Als onderdeel van de migratie worden de namen van enkele resources zoals de Cloud service, de open bare IP-adressen enz. gewijzigd. Deze wijzigingen moeten mogelijk worden doorgevoerd in de implementatie bestanden vóór de update van de Cloud service. Meer [informatie over de namen van resources die worden gewijzigd](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration).  

- Regels en beleids regels opnieuw maken die nodig zijn voor het beheren en schalen van Cloud Services 
    - [Regels voor automatisch schalen](configure-scaling.md) worden niet gemigreerd. Na de migratie maakt u de regels voor automatisch schalen opnieuw.  
    - [Waarschuwingen](enable-alerts.md) worden niet gemigreerd. Nadat u de migratie hebt gemaakt, maakt u de waarschuwingen opnieuw.
    - De Key Vault wordt gemaakt zonder beleids regels voor toegang. [Maak de juiste beleids regels](../key-vault/general/assign-access-policy-portal.md) op het Key Vault om uw certificaten weer te geven of te beheren. Certificaten worden weer gegeven onder instellingen op het tabblad geheimen.

## <a name="next-steps"></a>Volgende stappen
- [Overzicht van door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Migreren naar Cloud Services (uitgebreide ondersteuning) met behulp van de [Azure Portal](in-place-migration-portal.md)
- Migreren naar Cloud Services (uitgebreide ondersteuning) met behulp van [Power shell](in-place-migration-powershell.md)
