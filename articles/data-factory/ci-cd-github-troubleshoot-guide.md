---
title: Problemen met CI-CD-, Azure DevOps-en GitHub oplossen in ADF
description: Gebruik verschillende methoden om problemen met CI-CD in ADF op te lossen.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.openlocfilehash: 4be015b1a8ba4b6fc6ea3acc74318f9a8b298e8e
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418093"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>Problemen met CI-CD-, Azure DevOps-en GitHub oplossen in ADF 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden besproken voor doorlopende Integration-Continuous implementatie (CI-CD), Azure DevOps-en GitHub-problemen in Azure Data Factory.

Als u vragen of problemen hebt met behulp van broncode beheer-of DevOps-technieken, zijn er een aantal artikelen die u mogelijk nuttig vindt:

- Raadpleeg [broncode beheer in ADF](source-control.md) voor meer informatie over het gebruik van broncode beheer in ADF. 
- Raadpleeg  [CI-cd in ADF](continuous-integration-deployment.md) voor meer informatie over hoe DevOps CI-cd wordt gebruikt in ADF.
 
## <a name="common-errors-and-messages"></a>Veelvoorkomende fouten en berichten

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>Verbinding maken met git-opslag plaats is mislukt vanwege een andere Tenant

#### <a name="issue"></a>Probleem
    
Soms stuit u op verificatie problemen, zoals HTTP-status 401. Met name wanneer u meerdere tenants met een gast account hebt, kunnen de dingen ingewik kelder raken.

#### <a name="cause"></a>Oorzaak

We hebben geconstateerd dat het token is verkregen van de oorspronkelijke Tenant, maar ADF bevindt zich in de gast Tenant en probeert het token te gebruiken om DevOps te bezoeken in de gast Tenant. Dit is niet het verwachte gedrag.

#### <a name="recommendation"></a>Aanbeveling

U moet in plaats daarvan het token gebruiken dat is uitgegeven door gast Tenant. U moet bijvoorbeeld dezelfde Azure Active Directory toewijzen als uw gast Tenant en uw DevOps, zodat het token gedrag correct kan worden ingesteld en de juiste Tenant wordt gebruikt.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>De sjabloon parameters in het parameter bestand zijn ongeldig

#### <a name="issue"></a>Probleem

Als we een trigger in dev Branch verwijderen, die al beschikbaar is in test-of productie vertakking met **dezelfde** configuratie (zoals frequentie en interval), wordt de implementatie van de pijp lijn geslaagd en wordt de bijbehorende trigger verwijderd uit de respectieve omgevingen. Maar als u een **andere** configuratie (zoals frequentie en interval) voor de trigger in test-en productie omgevingen hebt, en als u dezelfde trigger in dev verwijdert, mislukt de implementatie met een fout.

#### <a name="cause"></a>Oorzaak

CI/CD-pijp lijn mislukt met de volgende fout:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Aanbeveling

De fout treedt op omdat we vaak een trigger verwijderen, die para meters bevat. de para meters zijn daarom niet beschikbaar in de ARM-sjabloon (omdat de trigger niet meer bestaat). Omdat de para meter zich niet meer in de ARM-sjabloon bevindt, moeten de overschreven para meters in de DevOps-pijp lijn worden bijgewerkt. Anders worden de para meters in de ARM-sjabloon gewijzigd, dan moeten ze de overschreven para meters in de DevOps-pijp lijn (in de implementatie taak) bijwerken.

### <a name="updating-property-type-is-not-supported"></a>Het bijwerken van het eigenschaps type wordt niet ondersteund

#### <a name="issue"></a>Probleem

Er is een fout opgetreden in de CI/CD-release-pijp lijn.

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>Oorzaak

Dit wordt veroorzaakt door een Integration runtime met dezelfde naam in de doel-Factory, maar met een ander type. Integration Runtime moet van hetzelfde type zijn wanneer u implementeert.

#### <a name="recommendation"></a>Aanbeveling

- Raadpleeg deze aanbevolen procedures voor CI/CD hieronder:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- Integratie-Runtimes worden niet vaak gewijzigd en zijn vergelijkbaar in alle fasen van uw CI/CD, dus Data Factory verwacht dat u dezelfde naam en hetzelfde type Integration runtime hebt in alle stadia van CI/CD. Als de naam en het type & eigenschappen verschillen, moet u ervoor zorgen dat deze overeenkomen met de bron-en doel integratie runtime configuratie en de release pijplijn te implementeren.
- Als u integratie-Runtimes in alle fasen wilt delen, kunt u overwegen een ternaire fabriek alleen te gebruiken om de gedeelde integratie-runtime te bevatten. U kunt deze gedeelde Factory in al uw omgevingen gebruiken als het type gekoppelde integratie runtime.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Het maken of bijwerken van het document is mislukt vanwege een ongeldige verwijzing

#### <a name="issue"></a>Probleem

Bij het publiceren van wijzigingen in een Data Factory wordt het volgende fout bericht weer gegeven:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`

#### <a name="symptom"></a>Symptoom

U hebt de Git-configuratie losgekoppeld en opnieuw ingesteld met de vlag ' resources importeren ' geselecteerd, waarmee de Data Factory als synchroon wordt ingesteld. Dit betekent dat er geen wijzigingen worden gepubliceerd.

#### <a name="resolution"></a>Oplossing

Ontkoppel Git-configuratie en stel deze opnieuw in en zorg ervoor dat u het selectie vakje ' bestaande resources importeren ' niet inschakelt.

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Data Factory verplaatsen van een resource groep naar een andere mislukt

#### <a name="issue"></a>Probleem

Het is niet mogelijk om Data Factory van de ene resource groep naar de andere te verplaatsen, waarbij de volgende fout optreedt:

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Oplossing

U moet de SSIS-IR en gedeelde IRs verwijderen om de verplaatsings bewerking toe te staan. Als u de Integration Runtimes niet wilt verwijderen, kunt u het beste het kopiëren en klonen van het document volgen om de kopie te maken en de oude Data Factory te verwijderen.

###  <a name="unable-to-export-and-import-arm-template"></a>Kan ARM-sjabloon niet exporteren en importeren

#### <a name="issue"></a>Probleem

Kan ARM-sjabloon niet exporteren en importeren. Er is geen fout opgetreden op de portal, maar in de browser tracering ziet u mogelijk de volgende fout:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Oorzaak

U hebt een klantrol als gebruiker gemaakt en deze heeft niet de benodigde machtigingen. Wanneer de fabriek in de gebruikers interface wordt geladen, wordt een reeks waarden voor belichtings beheer voor de Factory gecontroleerd. In dit geval heeft de Access-rol van de gebruiker geen machtiging voor toegang tot de *queryFeaturesValue* -API. De functie globale para meters is uitgeschakeld voor toegang tot deze API. Het pad naar de ARM-export code is deels afhankelijk van de functie voor algemene para meters.

#### <a name="resolution"></a>Oplossing

Om het probleem op te lossen, moet u de volgende machtiging toevoegen aan uw rol: *micro soft. DataFactory/factories/queryFeaturesValue/Action*. Deze machtiging moet standaard worden opgenomen in de rol ' Data Factory Inzender '.

###  <a name="automatic-publishing-for-cicd-without-clicking-publish-button"></a>Automatische publicatie voor CI/CD zonder te klikken op de knop publiceren  

#### <a name="issue"></a>Probleem

Als u hand matig publiceert met de knop Klik in de ADF-Portal, wordt er geen automatische CI/CD-bewerking ingeschakeld.

#### <a name="cause"></a>Oorzaak

Tot die tijd kunt u alleen ADF-pijp lijn publiceren voor implementaties met behulp van de knop ADF-Portal. Nu kunt u het proces automatisch laten uitvoeren. 

#### <a name="resolution"></a>Oplossing

Het CI/CD-proces is verbeterd. Met de functie voor **automatisch publiceren** worden alle Azure Resource Manager (arm)-sjabloon functies van de ADF UX gevalideerd en geëxporteerd. De logica kan worden verbruikt met behulp van een openbaar beschikbaar NPM-pakket [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Zo kunt u deze acties programmatisch activeren in plaats van dat u naar de ADF-gebruikers interface hoeft te gaan en op een knop klikt. Dit geeft uw CI/CD-pijp lijnen een **echte** voortdurende integratie-ervaring. Volg de [verbeteringen voor ADF CI/CD-publicatie](./continuous-integration-deployment-improvements.md) voor meer informatie. 

###  <a name="cannot-publish-because-of-4mb-arm-template-limit"></a>Kan niet publiceren vanwege een limiet van 4 MB ARM-sjablonen  

#### <a name="issue"></a>Probleem

U kunt niet implementeren omdat u Azure Resource Manager limiet van de totale sjabloon grootte van 4 MB hebt bereikt. U hebt een oplossing nodig om te implementeren na overschrijding van de limiet. 

#### <a name="cause"></a>Oorzaak

Azure Resource Manager beperkt de sjabloon grootte tot 4 MB. Beperk de grootte van uw sjabloon tot 4 MB en elk parameter bestand tot 64 KB. De limiet van 4 MB is van toepassing op de uiteindelijke status van de sjabloon nadat deze is uitgebreid met iteratieve resource definities en waarden voor variabelen en para meters. Maar u hebt de limiet overschreden. 

#### <a name="resolution"></a>Oplossing

Voor kleine tot middelgrote oplossingen is één sjabloon eenvoudiger te begrijpen en te onderhouden. U kunt alle resources en waarden in één bestand bekijken. Voor geavanceerde scenario's kunt u met gekoppelde sjablonen de oplossing opsplitsen in de beoogde onderdelen. Voer de best practice uit [met behulp van gekoppelde en geneste sjablonen](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell).

### <a name="cannot-connect-to-git-enterprise-cloud"></a>Kan geen verbinding maken met een GIT Enter prise-Cloud 

##### <a name="issue"></a>Probleem

U kunt geen verbinding maken met een GIT-bedrijfs Cloud vanwege machtigings problemen. U ziet fout als **422-entiteit** die niet kan worden verwerkt.

#### <a name="cause"></a>Oorzaak

* U gebruikt Git Enter prise on-premises server. 
* U hebt OAuth voor ADF niet geconfigureerd. 
* De URL is onjuist geconfigureerd.

##### <a name="resolution"></a>Oplossing

U verleent eerst OAuth-toegang tot ADF. Vervolgens moet u de juiste URL gebruiken om verbinding te maken met een GIT-onderneming. De configuratie moet worden ingesteld op de organisatie (s) van de klant. Voor beeld: ADF probeert eerst *https://hostname/api/v3/search/repositories?q=user%3 <customer credential> .* ... op het eerste en mislukt. Vervolgens wordt het geprobeerd *https://hostname/api/v3/orgs/ <org> / <repo> ...* en slaagt. 
 
### <a name="recover-from-a-deleted-data-factory"></a>Herstellen van een verwijderde data factory

#### <a name="issue"></a>Probleem
Klant heeft Data Factory verwijderd of de resource groep met de Data Factory. Hij wil graag weten hoe u een verwijderde data factory kunt herstellen.

#### <a name="cause"></a>Oorzaak

Het is mogelijk om de Data Factory alleen te herstellen als de klant broncode beheer (DevOps of Git) heeft geconfigureerd. Hiermee wordt de meest recente gepubliceerde resource gemaakt en **wordt de niet** -gepubliceerde pijp lijn, gegevensset en gekoppelde service niet hersteld.

Als er geen broncode beheer is, is het herstellen van een verwijderde Data Factory van de back-end niet mogelijk omdat de service de verwijderde opdracht heeft ontvangen, het exemplaar is verwijderd en er geen back-up is opgeslagen.

#### <a name="resolution"></a>Oplossing

Ga als volgt te werk om de verwijderde Data Factory met broncode beheer te herstellen:

 * Maak een nieuwe Azure Data Factory.

 * Configureer Git opnieuw met dezelfde instellingen, maar zorg ervoor dat u bestaande Data Factory resources importeert in de geselecteerde opslag plaats en kies nieuwe vertakking.

 * Maak een pull-aanvraag om de wijzigingen in de vertakking samen te voegen en te publiceren.

 * Als de klant een zelf-hosted Integration Runtime in een verwijderde ADF had, moet deze een nieuw exemplaar maken in een nieuwe ADF, het exemplaar ook verwijderen en opnieuw installeren op de on-premises machine/VM met de nieuwe sleutel die is verkregen. Nadat de installatie van IR is voltooid, moet de klant de gekoppelde service wijzigen zodat deze naar de nieuwe IR wijst en de verbinding testen of er treedt een fout op bij een **Ongeldige verwijzing.**



## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen kunt u de volgende bronnen proberen:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack overflow-forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
