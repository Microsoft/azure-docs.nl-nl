---
title: Commerciële Marketplace-partner en toewijzing van klant gebruik
description: Bekijk een overzicht van het bijhouden van klant gebruik voor Azure Marketplace-oplossingen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 11/4/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: e9e630f4199e0bfb67509f008f403c0a7a490887
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436688"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Commerciële Marketplace-partner en toewijzing van klant gebruik

Toewijzing van klant gebruik is een methode om Azure-resources te koppelen die worden uitgevoerd in klant abonnementen, geïmplementeerd om uw oplossing uit te voeren, met u als partner. Door deze koppelingen in interne micro soft-systemen te vormen, beschikt u over meer inzicht in de Azure-footprint die uw software uitvoert. Wanneer u deze tracking-functie aanneemt, wordt u afgestemd op de verkoop teams van micro soft en tegoed voor micro soft Partner-Program ma's.

U kunt de koppeling vormen via Azure Marketplace, de Quick Start-opslag plaats, persoonlijke GitHub-opslag plaatsen en 1:1 klant afspraken die een duurzaam IP-adres maken (zoals de ontwikkeling van een app).

De toewijzing van klant gebruik ondersteunt drie implementatie opties:

- Azure Resource Manager sjablonen: partners kunnen Resource Manager-sjablonen gebruiken om de Azure-Services te implementeren om de software van de partner uit te voeren. Partners kunnen een resource manager-sjabloon maken om de infra structuur en configuratie van hun Azure-oplossing te definiëren. Met een resource manager-sjabloon kunnen u en uw klanten uw oplossing gedurende de hele levens cyclus implementeren. U kunt erop vertrouwen dat uw resources in een consistente staat worden geïmplementeerd.
- Azure Resource Manager-Api's: partners kunnen de Resource Manager-Api's rechtstreeks aanroepen voor het implementeren van een resource manager-sjabloon of voor het genereren van de API-aanroepen om direct Azure-Services in te richten.
- Terraform: partners kunnen terraform gebruiken om een resource manager-sjabloon te implementeren of om Azure-Services rechtstreeks te implementeren.

>[!IMPORTANT]
>- De toewijzing van klant gebruik is niet bedoeld voor het bijhouden van het werk van systeem integrators, beheerde service providers of hulpprogram ma's die zijn ontworpen voor het implementeren en beheren van software die wordt uitgevoerd op Azure.
>
>- De toewijzing van klant gebruik is voor nieuwe implementaties en biedt geen ondersteuning voor het coderen van bestaande resources die al zijn geïmplementeerd.
>
>- De toewijzing van klant gebruik is vereist voor [Azure-toepassing](./create-new-azure-apps-offer.md) aanbiedingen die naar Azure Marketplace worden gepubliceerd.
>
>- Niet alle Azure-Services zijn compatibel met de gebruiks toewijzing van de klant. Azure Kubernetes Services (AKS) en VM Scale Sets hebben vandaag bekende problemen die een minder rapportage van het gebruik veroorzaken.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>GUID'S maken

Een GUID is een unieke referentie-id met 32 hexadecimale cijfers. Als u GUID'S wilt maken voor bijhouden, moet u een GUID-generator gebruiken, bijvoorbeeld via Power shell.

```powershell
[guid]::NewGuid()
```

We raden u aan om voor elk product een unieke GUID te maken voor elk aanbod en distributie kanaal. U kunt ervoor kiezen om een enkele GUID voor de meerdere distributie kanalen van het product te gebruiken als u niet wilt dat rapportage wordt gesplitst.

Als u een product implementeert met behulp van een sjabloon en deze beschikbaar is op Azure Marketplace en op GitHub, kunt u twee afzonderlijke GUID'S maken en registreren:

- Product A in azure Marketplace
- Product A op GitHub

Rapportage geschiedt door Microsoft Partner Network ID en GUID.

U kunt het gebruik ook op een meer gedetailleerd niveau bijhouden door extra GUID'S te registreren en GUID'S tussen plannen te wijzigen, waarbij plannen varianten zijn van een aanbieding.

## <a name="register-guids"></a>GUID'S registreren

De GUID'S moeten worden geregistreerd in het partner centrum om het gebruik van de klant mogelijk te maken.

Nadat u een GUID hebt toegevoegd aan de sjabloon of in de gebruikers agent en de GUID registreert in Partner Center, worden toekomstige implementaties bijgehouden.

> [!NOTE]
> Als u uw [Azure-toepassing](./create-new-azure-apps-offer.md) -aanbod publiceert naar Azure Marketplace via partner centrum, wordt een nieuwe GUID die wordt gebruikt in uw sjabloon automatisch geregistreerd bij het partner centrum-profiel wanneer de sjabloon wordt geüpload.  

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard).

1. Meld u aan als een [commerciële Marketplace-Uitgever](https://aka.ms/JoinMarketplace).

   * Partners moeten [beschikken over een profiel in Partner Center](./partner-center-portal/create-account.md). We raden u aan de aanbieding in azure Marketplace of AppSource te vermelden.
   * Partners kunnen meerdere GUID'S registreren.
   * Partners kunnen GUID'S registreren voor niet-Marketplace-oplossings sjablonen en aanbiedingen.

1. Selecteer **instellingen** (tandwiel pictogram) in de rechter bovenhoek > **account instellingen**.

1. Selecteer onder **profiel-**  >  **id's** voor organisatie de optie **Tracking GUID toevoegen**.

1. Voer in het vak **GUID** uw tracking-GUID in. Voer alleen de GUID in zonder het `pid-` voor voegsel. Typ in het vak **Beschrijving** de naam of beschrijving van uw aanbieding.

1. Als u meer dan één GUID wilt registreren, selecteert u opnieuw **Tracking GUID toevoegen** . Er worden aanvullende vakken op de pagina weer gegeven.

1. Selecteer **Opslaan**.

## <a name="use-resource-manager-templates"></a>Resource Manager-sjablonen gebruiken
Veel partner oplossingen worden geïmplementeerd met Azure Resource Manager sjablonen. Als u een resource manager-sjabloon hebt die beschikbaar is in azure Marketplace, op GitHub of als Snelstartgids, is het proces voor het wijzigen van uw sjabloon om de toewijzing van klant gebruik direct uit te scha kelen.

> [!NOTE]
> Zie voor meer informatie over het maken en publiceren van oplossings sjablonen.
> * [Maak en implementeer uw eerste Resource Manager-sjabloon](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
>* [Azure-toepassing aanbieding](./create-new-azure-apps-offer.md).
>* Video: [oplossings sjablonen en beheerde toepassingen voor Azure Marketplace bouwen](https://channel9.msdn.com/Events/Build/2018/BRK3603).


Als u een Globally Unique Identifier (GUID) wilt toevoegen, brengt u één wijziging aan in het hoofd sjabloon bestand:

1. [Maak een GUID](#create-guids) met behulp van de voorgestelde methode en [Registreer de GUID](#register-guids).

1. Open de Resource Manager-sjabloon.

1. Voeg een nieuwe resource van het type [micro soft. resources/Deployments](/azure/templates/microsoft.resources/deployments) toe in het hoofd sjabloon bestand. De resource moet zich in het **mainTemplate.jsop** of **azuredeploy.js** alleen in het bestand en niet in geneste of gekoppelde sjablonen.

1. Voer de GUID-waarde na het `pid-` voor voegsel in als de naam van de resource. Als de GUID bijvoorbeeld eb7927c8-dd66-43e1-b0cf-c346a422063 is, is de resource naam _PID-eb7927c8-dd66-43e1-b0cf-c346a422063_.

1. Controleer de sjabloon op eventuele fouten.

1. Publiceer de sjabloon opnieuw in de juiste opslag plaatsen.

1. [Controleer of de GUID is geslaagd in de sjabloon implementatie](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Voorbeeld sjabloon code van Resource Manager

Als u het bijhouden van resources voor uw sjabloon wilt inschakelen, moet u de volgende extra resource toevoegen onder de sectie resources. Zorg ervoor dat u de onderstaande voorbeeld code met uw eigen invoer waarden wijzigt wanneer u deze toevoegt aan het hoofd sjabloon bestand.
De resource moet worden toegevoegd aan de **mainTemplate.jsop** of **azuredeploy.js** alleen in het bestand en niet in geneste of gekoppelde sjablonen.

```json
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
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

## <a name="use-the-resource-manager-apis"></a>De Resource Manager-Api's gebruiken

In sommige gevallen kunt u de voor keur geven aan aanroepen direct voor de Resource Manager REST-Api's om Azure-Services te implementeren. [Azure ondersteunt meerdere sdk's](../index.yml?pivot=sdkstools) om deze aanroepen mogelijk te maken. U kunt een van de Sdk's gebruiken of de REST-Api's rechtstreeks aanroepen om resources te implementeren.

Als u een resource manager-sjabloon gebruikt, moet u uw oplossing labelen door de instructies te volgen die eerder zijn beschreven. Als u geen Resource Manager-sjabloon gebruikt en directe API-aanroepen maakt, kunt u nog steeds uw implementatie labelen om het gebruik van Azure-resources te koppelen.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Een implementatie labelen met de Resource Manager-Api's

Als u de toewijzing van klant gebruik wilt inschakelen, neemt u bij het ontwerpen van de API-aanroepen een GUID op in de header van de gebruikers agent in de aanvraag. Voeg de GUID voor elke aanbieding of SKU toe. Format teer de teken reeks met het `pid-` voor voegsel en voeg de door de partner gegenereerde GUID toe. Hier volgt een voor beeld van de GUID-indeling voor het invoegen van de gebruikers agent:

![Voor beeld van GUID-indeling](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> De indeling van de teken reeks is belang rijk. Als het `pid-` voor voegsel niet is opgenomen, is het niet mogelijk om de gegevens op te vragen. Andere Sdk's volgen verschillend. Voor het implementeren van deze methode raadpleegt u de ondersteuning en traceer benadering voor uw voor Keurs-Azure-SDK.

#### <a name="example-the-python-sdk"></a>Voor beeld: de python-SDK

Gebruik het **configuratie** kenmerk voor python. U kunt het kenmerk alleen toevoegen aan een User agent. Hier volgt een voorbeeld:

![Het kenmerk toevoegen aan een gebruikers agent](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Voeg het kenmerk voor elke client toe. Er is geen globale statische configuratie. U kunt een client-Factory labelen om ervoor te zorgen dat elke client bijhoudt. Zie voor meer informatie dit voor [beeld van client Factory op github](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-the-net-sdk"></a>Voor beeld: de .NET SDK

Zorg ervoor dat u de gebruikers agent instelt voor .NET. De bibliotheek [micro soft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) kan worden gebruikt om de gebruikers agent in te stellen met de volgende code (voor beeld in C#):

```csharp

var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Een implementatie labelen met behulp van de Azure PowerShell

Als u resources implementeert via Azure PowerShell, voegt u uw GUID toe met behulp van de volgende methode:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Een implementatie labelen met behulp van de Azure CLI

Wanneer u de Azure CLI gebruikt om uw GUID toe te voegen, stelt u de omgevings variabele **AZURE_HTTP_USER_AGENT** in. U kunt deze variabele instellen binnen het bereik van een script. U kunt de variabele ook globaal instellen voor het shell-bereik:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Zie [Azure SDK voor Go](/azure/developer/go/)voor meer informatie.

## <a name="use-terraform"></a>Terraform gebruiken

De ondersteuning voor terraform is beschikbaar via de 1.21.0-versie van Azure provider: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) .  Deze ondersteuning is van toepassing op alle partners die hun oplossing implementeren via terraform, en alle resources die zijn geïmplementeerd en gemeten door de Azure-provider (versie 1.21.0 of hoger).

Azure provider voor terraform heeft een nieuw optioneel veld met de naam [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) toegevoegd, waarin u de tracerings-GUID opgeeft die u voor uw oplossing gebruikt. De waarde van dit veld kan ook worden gebrond op basis van de variabele *ARM_PARTNER_ID* omgeving.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partners die hun implementatie willen verkrijgen via terraform bijgehouden door de toewijzing van klant gebruik, moeten het volgende doen:

* Een GUID maken (de GUID moet worden toegevoegd voor elke aanbieding of SKU)
* Werk hun Azure-provider bij om de waarde van *partner_id* in te stellen op de GUID (de GUID niet vooraf herstellen met ' PID-', maar stel deze in op de werkelijke GUID)

## <a name="verify-the-guid-deployment"></a>De GUID-implementatie verifiëren

Nadat u de sjabloon hebt gewijzigd en een test implementatie hebt uitgevoerd, gebruikt u het volgende Power shell-script om de resources op te halen die u hebt geïmplementeerd en gelabeld.

U kunt het script gebruiken om te controleren of de GUID is toegevoegd aan uw Resource Manager-sjabloon. Het script is niet van toepassing op de Resource Manager-API of terraform-implementaties.

Meld u aan bij Azure. Selecteer het abonnement met de implementatie die u wilt controleren voordat u het script uitvoert. Voer het script uit binnen de context van het abonnement van de implementatie.

De **GUID** -en **resourceGroup** -naam van de implementatie zijn vereiste para meters.

U kunt [het oorspronkelijke script](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) verkrijgen op github.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Rapport

U kunt het rapport voor de toewijzing van klant gebruik in uw partner Center-dash board ( [https://partner.microsoft.com/dashboard/partnerinsights/analytics/overview](https://partner.microsoft.com/dashboard/partnerinsights/analytics/overview) ) vinden. Als u het rapport wilt weer geven, moet u uw partner centrum referenties gebruiken om u aan te melden. Als u problemen ondervindt met een rapport of meldt, maakt u een [ondersteunings aanvraag](#get-support).

## <a name="notify-your-customers"></a>Uw klanten op de hoogte stellen

Partners moeten hun klanten informeren over implementaties die gebruikmaken van de toewijzing van klant gebruik. Micro soft rapporteert het Azure-gebruik dat is gekoppeld aan deze implementaties aan de partner. De volgende voor beelden bevatten inhoud die u kunt gebruiken om uw klanten op de hoogte te stellen van deze implementaties. Vervang in de voor beelden door \<PARTNER> de naam van uw bedrijf. Partners moeten ervoor zorgen dat de melding wordt uitgelijnd met hun privacy-en verzamelings beleid voor gegevens, waaronder opties voor klanten die moeten worden uitgesloten van het bijhouden van wijzigingen.

### <a name="notification-for-resource-manager-template-deployments"></a>Melding voor implementaties van Resource Manager-sjablonen

Wanneer u deze sjabloon implementeert, kan micro soft de software-installatie identificeren \<PARTNER> met de Azure-resources die zijn geïmplementeerd. Micro soft kan de Azure-resources correleren die worden gebruikt ter ondersteuning van de software. Micro soft verzamelt deze informatie om de beste ervaring met hun producten te bieden en hun bedrijf te kunnen bedienen. De gegevens worden verzameld en geregeld door het privacybeleid van micro soft, dat u kunt vinden op [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

### <a name="notification-for-sdk-or-api-deployments"></a>Melding voor SDK-of API-implementaties

Wanneer u software implementeert \<PARTNER> , kan micro soft de software-installatie identificeren \<PARTNER> met de Azure-resources die zijn geïmplementeerd. Micro soft kan de Azure-resources correleren die worden gebruikt ter ondersteuning van de software. Micro soft verzamelt deze informatie om de beste ervaring met hun producten te bieden en hun bedrijf te kunnen bedienen. De gegevens worden verzameld en geregeld door het privacybeleid van micro soft, dat u kunt vinden op [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="get-support"></a>Ondersteuning krijgen

Meer informatie over de ondersteunings opties in de commerciële Marketplace met [ondersteuning voor het commerciële Marketplace-programma in het partner centrum](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Een technische consultatie aanvraag indienen

1. Ga naar [technische services van partners](https://aka.ms/TechnicalJourney).
1. Selecteer Cloud infrastructuur en-beheer en er wordt een nieuwe pagina geopend om de technische reis te bekijken.
1. Klik onder implementatie services op de knop een aanvraag indienen
1. Meld u aan met uw MSA (MPN-account) of uw AAD (partner dashboard account). op basis van uw aanmeldings referenties wordt een online aanvraag formulier geopend:
    * De contact gegevens volt ooien/controleren.
    * De details van de consultatie kunnen vooraf worden ingevuld of worden geselecteerd in de vervolg keuzelijsten.
    * Voer een titel en de beschrijving van het probleem in (Geef zo veel mogelijk details op).

1. Klik op Submit

Bekijk stapsgewijze instructies voor het [gebruik van technische preverkoop-en implementatie services](https://aka.ms/TechConsultInstructions)met scherm opnamen.

### <a name="whats-next"></a>Volgend onderwerp

U kunt contact opnemen met een technische consultant van micro soft-partners om een oproep naar uw behoeften te stellen.

## <a name="faq"></a>Veelgestelde vragen

**Wat is het voor deel van het toevoegen van de GUID aan de sjabloon?**

Micro soft biedt partners een overzicht van de implementaties van hun oplossingen en inzichten over het gebruik van de klant. Zowel micro soft als de partner kunnen deze informatie gebruiken om te zorgen voor een nauwere betrokkenheid tussen verkoop teams. Zowel micro soft als de partner kunnen de gegevens gebruiken om een consistenter beeld te krijgen van de impact van een individuele partner op Azure-groei.

**Nadat een GUID is toegevoegd, kan deze worden gewijzigd?**

Ja, een klant of implementatie partner kan de sjabloon aanpassen en kan de GUID wijzigen of verwijderen. We raden aan dat partners proactief de rol van de resource en de GUID voor hun klanten en partners beschrijven om verwijdering of bewerkingen van de GUID te voor komen. Het wijzigen van de GUID is alleen van invloed op nieuwe, niet bestaande, implementaties en resources.

**Kan ik sjablonen volgen die zijn geïmplementeerd vanuit een niet-micro soft-opslag plaats, zoals GitHub?**

Ja, zolang de GUID aanwezig is op het moment dat de sjabloon wordt geïmplementeerd, wordt het gebruik bijgehouden. Partners moeten hun GUID'S nog wel registreren.

**Ontvangt de klant ook een rapportage?**

Klanten kunnen hun gebruik van afzonderlijke resources of door de klant gedefinieerde resource groepen bijhouden binnen het Azure Portal. Klanten zien niet dat het gebruik is uitgesplitst op basis van de GUID.

**Is deze methodologie vergelijkbaar met de digitale partner of record (DPOR)?**

Deze nieuwe methode voor het verbinden van de implementatie en het gebruik van een partner oplossing biedt een mechanisme om een partner oplossing te koppelen aan Azure-gebruik. DPOR is bedoeld om een Consulting (Systems Integrator) of Management (Managed Service Provider)-partner te koppelen aan het Azure-abonnement van de klant.

**Kan ik een persoonlijke, aangepaste VHD voor een oplossings sjabloon aanbieding in azure Marketplace gebruiken?**

Nee, dat kan niet. De installatie kopie van de virtuele machine moet afkomstig zijn van de Azure Marketplace. Zie de [publicatie handleiding voor aanbiedingen van virtuele machines op Azure Marketplace](marketplace-virtual-machines.md).

U kunt een VM-aanbieding in Marketplace maken met behulp van uw aangepaste VHD en markeren als privé, zodat niemand deze kan zien. Ga vervolgens naar deze virtuele machine in uw oplossings sjabloon.

**Kan de eigenschap *contentVersion* voor de hoofd sjabloon niet bijwerken?**

Dit is waarschijnlijk een bug, in gevallen waarin de sjabloon wordt geïmplementeerd met behulp van een TemplateLink van een andere sjabloon die de oudere contentVersion om een of andere reden verwacht. De tijdelijke oplossing is het gebruik van de meta gegevens eigenschap:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
