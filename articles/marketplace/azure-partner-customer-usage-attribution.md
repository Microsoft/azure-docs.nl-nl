---
title: Toewijzing van Azure-klant gebruik
description: Bekijk een overzicht van het bijhouden van het gebruik van klanten voor Azure-toepassingen op de commerciële Marketplace en een ander implementeerbaar IP-adres dat is ontwikkeld door partners.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 03/19/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: 79f3276347aa64655f0c9086db5f152c4ff5fbcf
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771087"
---
# <a name="azure-customer-usage-attribution"></a>Toewijzing van Azure-klant gebruik

Met de toewijzing van klant gebruik wordt het gebruik van Azure-resources gekoppeld aan abonnementen van klanten die zijn gemaakt tijdens de implementatie van uw IP-adres bij u als partner. Door deze koppelingen in interne micro soft-systemen te vormen, beschikt u over meer inzicht in de Azure-footprint die uw software uitvoert. Voor [Azure-toepassing aanbiedingen in de commerciële Marketplace](#commercial-marketplace-azure-apps)kunt u met deze tracking-mogelijkheid u afstemmen op de verkoop teams van micro soft en tegoed voor micro soft-partner Programma's krijgen.

De toewijzing van klant gebruik ondersteunt drie implementatie opties:

1. Azure Resource Manager sjablonen (de algemene basis van Azure-apps, ook wel aangeduid als ' oplossings sjablonen ' of ' beheerde apps ' genoemd): partners maken Resource Manager-sjablonen om de infra structuur en configuratie van hun Azure-oplossingen te definiëren. Met een resource manager-sjabloon kunnen uw klanten de resources van uw oplossing implementeren in een consistente en herhaal bare status.
1. Azure Resource Manager-Api's: partners kunnen de Resource Manager-Api's aanroepen om een resource manager-sjabloon te implementeren of om rechtstreeks Azure-Services in te richten.
1. Terraform: partners kunnen terraform gebruiken om een resource manager-sjabloon te implementeren of om Azure-Services rechtstreeks te implementeren.

Er zijn secundaire use cases voor de toewijzing van klant gebruik buiten de commerciële Marketplace, zoals [verderop in dit artikel](#other-use-cases)wordt beschreven.

>[!IMPORTANT]
>- De toewijzing van klant gebruik is niet bedoeld om het werk van systeem integrators, beheerde service providers of hulpprogram ma's bij te houden die voornamelijk zijn ontworpen om Azure-resources te implementeren en te beheren.
>- Toewijzing van klant gebruik is voor nieuwe implementaties en biedt geen ondersteuning voor het bijhouden van resources die al zijn geïmplementeerd.
>- Niet alle Azure-Services zijn compatibel met de gebruiks toewijzing van de klant. Azure Kubernetes Services (AKS), VM Scale Sets en Azure Batch hebben bekende problemen die kunnen leiden tot minder rapportage van het gebruik.

## <a name="commercial-marketplace-azure-apps"></a>Commerciële Marketplace Azure-apps

Het bijhouden van Azure-gebruik van Azure-apps die worden gepubliceerd naar de commerciële Marketplace is grotendeels automatisch. Wanneer u een resource manager-sjabloon uploadt als onderdeel van de [technische configuratie van het abonnement van de Azure-app van uw Marketplace](https://docs.microsoft.com/azure/marketplace/create-new-azure-apps-offer-solution#define-the-technical-configuration), voegt Partner Center een tracking-ID toe die kan worden gelezen door Azure Resource Manager.

Als u Azure Resource Manager Api's gebruikt, moet u uw tracking-ID toevoegen aan de hand van de [onderstaande instructies](#use-resource-manager-apis) om deze door te geven aan Azure Resource Manager als uw code resources implementeert. Deze ID is zichtbaar in het partner centrum op de pagina met technische configuratie van uw abonnement. 

> [!NOTE]
> Voor bestaande Azure-apps werd een eenmalige migratie uitgevoerd in maart 2021 om de tracerings-Id's in de technische configuratie van elk abonnement bij te werken. Het gebruik van eerdere implementaties van deze aanbiedingen blijft bijgehouden in micro soft-systemen.

## <a name="other-use-cases"></a>Andere gebruiksvoorbeelden 

U kunt gebruikmaken van de toewijzing van klant gebruik voor het bijhouden van Azure-gebruik van oplossingen die niet beschikbaar zijn in de commerciële Marketplace. Deze oplossingen bevinden zich doorgaans in de Quick Start-opslag plaats, persoonlijke GitHub-opslag plaatsen of zijn afkomstig van 1:1-klant afspraken die een duurzaam IP-adres maken (zoals een Implementeer bare en scalable app).

Er zijn verschillende hand matige stappen vereist:

1. Maak een of meer GUID'S die u wilt gebruiken als tracerings-ID.
1. Registreer deze GUID'S in het partner centrum.
1. Voeg uw geregistreerde GUID'S toe aan uw Azure-app en/of gebruikers agent teken reeksen.

### <a name="create-guids"></a>GUID'S maken

In tegens telling tot de tracerings-Id's die partner Center namens u maakt voor Azure-apps in de commerciële Marketplace, moet u voor ander gebruik van CUA een GUID maken om te gebruiken als traceer-ID. Een GUID is een unieke referentie-id met 32 hexadecimale cijfers. Als u GUID'S voor bijhouden wilt maken, moet u een GUID-generator gebruiken, bijvoorbeeld via Power shell:

```powershell
[guid]::NewGuid()
```

U moet een unieke GUID voor elk product en distributie kanaal maken. U kunt een enkele GUID gebruiken voor meerdere distributie kanalen van een product als u niet wilt dat rapportage wordt gesplitst. De rapportage vindt plaats door Microsoft Partner Network ID en GUID.

### <a name="register-guids"></a>GUID'S registreren

GUID'S moeten nu worden geregistreerd in het partner centrum zodat ze als een partner aan u kunnen worden gekoppeld:

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard).

1. Meld u aan als een [commerciële Marketplace-Uitgever](https://aka.ms/JoinMarketplace).

1. Selecteer **instellingen** (tandwiel pictogram) in de rechter bovenhoek en klik vervolgens op **account instellingen**.

1. Selecteer **organisatie profiel-**  >  **id's**  >  **Voeg tracking GUID toe**.

1. Voer in het vak **GUID** uw tracking-GUID in. Voer alleen de GUID in zonder het `pid-` voor voegsel. Voer in het vak **Beschrijving** de naam of beschrijving van de oplossing in.

1. Als u meer dan één GUID wilt registreren, selecteert u opnieuw **Tracking GUID toevoegen** . Er worden aanvullende vakken op de pagina weer gegeven.

1. Selecteer **Opslaan**.

### <a name="add-a-guid-to-a-resource-manager-template"></a>Een GUID toevoegen aan een resource manager-sjabloon

Ga als volgt te werk om een geregistreerde GUID toe te voegen aan een resource manager-sjabloon:

1. Open de Resource Manager-sjabloon.

1. Voeg een nieuwe resource van het type [micro soft. resources/Deployments](/azure/templates/microsoft.resources/deployments) toe in het hoofd sjabloon bestand. De resource moet zich in het **mainTemplate.jsop** of **azuredeploy.js** alleen in het bestand, niet in geneste of gekoppelde sjablonen.

1. Voer de GUID-waarde na het `pid-` voor voegsel in als de naam van de resource. Als de GUID bijvoorbeeld eb7927c8-dd66-43e1-b0cf-c346a422063 is, is de resource naam **PID-eb7927c8-dd66-43e1-b0cf-c346a422063**. Voorbeeld:
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. Controleer de sjabloon op fouten.

1. Publiceer de sjabloon opnieuw in de juiste opslag plaatsen.

1. [Controleer of de GUID is geslaagd in de sjabloon implementatie](#verify-deployments-tracked-with-a-guid).

> [!TIP]
> Zie voor meer informatie over het maken en publiceren van Resource Manager-sjablonen: [uw eerste Resource Manager-sjabloon maken en implementeren](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="verify-deployments-tracked-with-a-guid"></a>Controleren of implementaties met een GUID worden bijgehouden

Nadat u de sjabloon hebt gewijzigd en een test implementatie hebt uitgevoerd, gebruikt u het volgende Power shell-script om de resources op te halen die u hebt geïmplementeerd en gelabeld.

U kunt het script gebruiken om te controleren of de GUID is toegevoegd aan uw Resource Manager-sjabloon. Het script is niet van toepassing op de Resource Manager-API of terraform-implementaties.

Meld u aan bij Azure. Selecteer het abonnement met de implementatie die u wilt controleren voordat u het script uitvoert. Voer het script uit binnen de context van het abonnement van de implementatie.

De **GUID** (onder de naam ' naam van de implementatie) en de **resourceGroupName** van de implementatie zijn vereiste para meters.

U kunt [het oorspronkelijke script](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1) verkrijgen op github.

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>Uw klanten op de hoogte stellen

Partners moeten hun klanten informeren over implementaties die gebruikmaken van de toewijzing van klant gebruik. Micro soft rapporteert het Azure-gebruik dat is gekoppeld aan deze implementaties aan de partner. De volgende voor beelden bevatten inhoud die u kunt gebruiken om uw klanten op de hoogte te stellen van deze implementaties. Vervang in de voor beelden door \<PARTNER> de naam van uw bedrijf. Partners moeten ervoor zorgen dat de melding wordt uitgelijnd met hun privacy-en verzamelings beleid voor gegevens, waaronder opties voor klanten die moeten worden uitgesloten van het bijhouden van wijzigingen.

#### <a name="notification-for-resource-manager-template-deployments"></a>Melding voor implementaties van Resource Manager-sjablonen

Wanneer u deze sjabloon implementeert, kan micro soft de software-installatie van \<PARTNER> de geïmplementeerde Azure-resources identificeren. Micro soft kan deze bronnen correleren die worden gebruikt voor de ondersteuning van de software. Micro soft verzamelt deze informatie om de beste ervaring met hun producten te bieden en hun bedrijf te kunnen bedienen. De gegevens worden verzameld en geregeld door het privacybeleid van micro soft, dat zich bevindt in [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

#### <a name="notification-for-sdk-or-api-deployments"></a>Melding voor SDK-of API-implementaties

Wanneer u software implementeert \<PARTNER> , kan micro soft de software-installatie van \<PARTNER> de geïmplementeerde Azure-resources identificeren. Micro soft kan deze bronnen correleren die worden gebruikt voor de ondersteuning van de software. Micro soft verzamelt deze informatie om de beste ervaring met hun producten te bieden en hun bedrijf te kunnen bedienen. De gegevens worden verzameld en geregeld door het privacybeleid van micro soft, dat zich bevindt in [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="use-resource-manager-apis"></a>Resource Manager-Api's gebruiken

In sommige gevallen kunt u rechtstreeks aanroepen voor de Resource Manager REST-Api's om Azure-Services te implementeren. [Azure ondersteunt meerdere sdk's](../index.yml?pivot=sdkstools) om deze aanroepen mogelijk te maken. U kunt een van de Sdk's gebruiken of de REST-Api's rechtstreeks aanroepen om resources te implementeren.

Als u de toewijzing van klant gebruik wilt inschakelen, neemt u bij het ontwerpen van de API-aanroepen uw tracking-ID op in de header van de gebruikers agent in de aanvraag. Format teer de teken reeks met het `pid-` voor voegsel. Voorbeelden:

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> Als u gebruikmaakt van Resource Manager-Api's met een Azure-app in de commerciële Marketplace, gebruikt u de tracking-ID van het partner centrum. Gebruik geen GUID.

Diverse Sdk's communiceren met de Resource Manager-Api's anders en er zijn enkele verschillen in uw code nodig. In de onderstaande voor beelden wordt de niet-commerciële Marketplace-benadering gebruikt voor het gebruik van een GUID en wordt een aantal van de populairste Azure-Sdk's beschreven.

#### <a name="example-python-sdk"></a>Voor beeld: python SDK

Gebruik het **configuratie** kenmerk voor python. U kunt het kenmerk alleen toevoegen aan een User agent. Voorbeeld:

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> Voeg het kenmerk voor elke client toe. Er is geen globale statische configuratie. U kunt een client-Factory labelen om ervoor te zorgen dat elke client bijhoudt. Zie voor meer informatie dit voor [beeld van client Factory op github](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-net-sdk"></a>Voor beeld: .NET SDK

Zorg ervoor dat u de gebruikers agent instelt voor .NET. Gebruik de bibliotheek [micro soft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) om de gebruikers agent in te stellen met de volgende code (voor beeld in C#):

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>Voor beeld: Azure PowerShell

Als u resources implementeert via Azure PowerShell, voegt u uw GUID toe met behulp van deze methode:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>Voor beeld: Azure CLI

Wanneer u de Azure CLI gebruikt om uw GUID toe te voegen, stelt u de omgevings variabele **AZURE_HTTP_USER_AGENT** in binnen het bereik van een script. U kunt de variabele ook globaal instellen voor het shell-bereik:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Zie [Azure SDK voor Go](/azure/developer/go/)voor meer informatie.

## <a name="use-terraform"></a>Terraform gebruiken

Ondersteuning voor terraform is beschikbaar via de 1.21.0-versie van Azure provider: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) . Dit geldt voor alle partners die hun oplossing implementeren via terraform en alle resources die zijn geïmplementeerd en worden gemeten door de Azure-provider (versie 1.21.0 of hoger).

Azure provider voor terraform heeft een nieuw optioneel veld met de naam [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) toegevoegd voor het opgeven van de tracerings-GUID die wordt gebruikt voor uw oplossing. De waarde van dit veld kan ook worden gebrond op basis van de variabele *ARM_PARTNER_ID* omgeving.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Stel de waarde van *partner_id* in op een geregistreerde GUID. Maak geen voor voegsel van de GUID met ' PID-', maar stel deze in op de daad werkelijke GUID.

> [!IMPORTANT]
> Als u terraform gebruikt met een Azure-app in de commerciële Marketplace, gebruikt u de volledige tracking-ID van het partner centrum. Gebruik geen GUID.

## <a name="get-support"></a>Ondersteuning krijgen

Meer informatie over de ondersteunings opties in de commerciële Marketplace met [ondersteuning voor het commerciële Marketplace-programma in het partner centrum](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Een technische consultatie aanvraag indienen

1. Ga naar [technische services van partners](https://aka.ms/TechnicalJourney).
1. Selecteer **Cloud infrastructuur en-beheer** om de technische reis te bekijken.
1. Selecteer **implementatie services**  >  **een aanvraag indienen**.
1. Meld u aan met uw MSA (MPN-account) of uw AAD (partner dashboard account).
1. Voltooi/Controleer de contact gegevens in het formulier dat wordt geopend. De consultatie Details zijn mogelijk vooraf ingevuld of u hebt mogelijk vervolg keuze opties.
1. Voer een titel en een gedetailleerde beschrijving van het probleem in.
1. Selecteer **Indienen**.

Bekijk stapsgewijze instructies voor het [gebruik van technische preverkoop-en implementatie services](https://aka.ms/TechConsultInstructions)met scherm opnamen.

U kunt contact opnemen met een technische consultant van micro soft-partners om een oproep naar uw behoeften te stellen.

## <a name="report"></a>Rapport
Rapportage voor Azure-gebruik bijgehouden via toewijzing van klant gebruik is momenteel niet beschikbaar voor ISV-partners. Het toevoegen van rapportage aan het commerciële Marketplace-programma in het partner centrum voor de dekking van klant gebruik is bedoeld voor de tweede helft van 2021.

## <a name="faq"></a>Veelgestelde vragen

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>Nadat een tracerings-ID is toegevoegd, kan deze worden gewijzigd?

Tracking-Id's voor Azure-apps in de commerciële Marketplace worden automatisch beheerd door het partner centrum. Een klant kan echter een sjabloon downloaden en de tracerings-ID wijzigen of verwijderen. Partners moeten de rol van de tracerings-ID proactief beschrijven om te voor komen dat ze worden verwijderd of bewerkt. Het wijzigen van de tracerings-ID is alleen van invloed op nieuwe implementaties en resources, niet op bestaande.

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>Kan ik sjablonen volgen die zijn geïmplementeerd vanuit een niet-micro soft-opslag plaats, zoals GitHub?

Ja, zolang de tracerings-ID aanwezig is wanneer de sjabloon wordt geïmplementeerd, wordt het gebruik bijgehouden. Als u de koppeling tussen u als een uitgever en uw sjabloon hebt geïmplementeerd vanuit een niet-micro soft-opslag plaats, downloadt u eerst een kopie van uw gepubliceerde sjabloon (die de tracking-ID bevat) van de aanbieding Commercial Marketplace van uw aanbieding in de Azure Portal. Publiceer die versie naar GitHub of een andere niet-micro soft-opslag plaats.

Als uw sjabloon niet wordt vermeld in commerciële Marketplace en een geregistreerde GUID bevat, moet u ervoor zorgen dat de GUID aanwezig is in de versie die u publiceert naar GitHub of een andere niet-micro soft-opslag plaats.

#### <a name="does-the-customer-receive-reporting-as-well"></a>Ontvangt de klant ook een rapportage?

Nee. Klanten kunnen hun gebruik van alle resources of resource groepen binnen de Azure Portal bijhouden. Klanten zien niet dat het gebruik is afgebroken door de CUA tracking-ID.

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>Is de toewijzing van klant gebruik vergelijkbaar met de digitale partner van record (DPOR) of de partner beheer koppeling (PAL)?

Toewijzing van klant gebruik is een mechanisme voor het koppelen van Azure-gebruik aan een door Herhaal bare, implementeer bare IP-verbinding van een partner op het moment van de implementatie. DPOR en PAL zijn bedoeld om een Consulting (Systems Integrator) of Management (Managed Service Provider)-partner te koppelen aan de relevante Azure-footprint van een klant voor het moment waarop de partner met de klant wordt betrokken.