---
title: Azure Service Bus naam ruimten migreren-standaard naar Premium
description: Gids voor het toestaan van de migratie van bestaande Azure Service Bus Standard-naam ruimten naar Premium
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1ed09a077f086390c658e6650171c552b361008d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85340740"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Bestaande Azure Service Bus Standard-naam ruimten migreren naar de Premium-laag

Voorheen beAzure Service Bus de aangeboden naam ruimten alleen in de laag standaard. Naam ruimten zijn instellingen voor multi tenants die zijn geoptimaliseerd voor omgevingen met lage door Voer en ontwikkel aars. De Premium-laag biedt toegewezen bronnen per naam ruimte voor voorspel bare latentie en verhoogde door voer tegen een vaste prijs. De Premium-laag is geoptimaliseerd voor omgevingen met hoge door Voer en productie waarvoor extra bedrijfs functies nodig zijn.

In dit artikel wordt beschreven hoe u bestaande naam ruimten van de Standard-laag migreert naar de laag Premium.  

>[!WARNING]
> Migratie is bedoeld voor Service Bus standaard naam ruimten die moeten worden bijgewerkt naar de Premium-laag. Het hulp programma voor migratie biedt geen ondersteuning voor downgrades.

Enkele van de punten waarop moet worden genoteerd:

- Deze migratie is bedoeld om te worden uitgevoerd, wat betekent dat bestaande Sender-en receiver-toepassingen **geen wijzigingen in code of configuratie nodig hebben**. De bestaande connection string wijst automatisch naar de nieuwe Premium-naam ruimte.
- De **Premium** -naam ruimte mag **geen entiteiten** bevatten voordat de migratie slaagt.
- Alle **entiteiten** in de standaard naam ruimte worden tijdens het migratie proces **gekopieerd** naar de Premium-naam ruimte.
- Migratie ondersteunt **1.000 entiteiten per Messa ging-eenheid** op de Premium-laag. Als u wilt weten hoeveel Messa ging-eenheden u nodig hebt, begint u met het aantal entiteiten dat u hebt in uw huidige standaard naam ruimte.
- U kunt niet rechtstreeks migreren van de **Basic-laag** naar de **Premium-laag**, maar dit kan ook indirect doen door de migratie eerst van Basic naar standaard en vervolgens van de standaard naar Premium in de volgende stap uit te voeren.

## <a name="migration-steps"></a>Migratiestappen

Sommige voor waarden zijn gekoppeld aan het migratie proces. Raadpleeg de volgende stappen om de kans op fouten te verminderen. In deze stappen wordt het migratie proces beschreven en worden de stapsgewijze details weer gegeven in de volgende secties.

1. Maak een nieuwe Premium-naam ruimte.
1. Koppel de standaard-en Premium-naam ruimten aan elkaar.
1. Synchroniseer entiteiten van de Standard-naar de Premium-naam ruimte.
1. Voer de migratie door.
1. Afvoer entiteiten in de standaard naam ruimte met behulp van de naam van de post-migratie van de naam ruimte.
1. Verwijder de standaard naam ruimte.

>[!IMPORTANT]
> Nadat de migratie is doorgevoerd, opent u de oude standaard naam ruimte en verwerkt u de wacht rijen en abonnementen. Nadat de berichten zijn ontslagen, kunnen ze worden verzonden naar de nieuwe Premium-naam ruimte die door de ontvangende toepassingen moet worden verwerkt. Nadat de wacht rijen en abonnementen zijn leeg gemaakt, wordt u aangeraden de oude standaard naam ruimte te verwijderen.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migreren met behulp van Azure CLI of Power shell

Als u uw Service Bus Standard-naam ruimte wilt migreren naar Premium met behulp van het Azure CLI-of Power shell-hulp programma, volgt u deze stappen.

1. Maak een nieuwe Service Bus Premium-naam ruimte. U kunt verwijzen naar de [Azure Resource Manager sjablonen](service-bus-resource-manager-namespace.md) of [de Azure Portal gebruiken](service-bus-create-namespace-portal.md). Zorg ervoor dat u **Premium** selecteert voor de para meter **serviceBusSku** .

1. Stel de volgende omgevings variabelen in om de migratie opdrachten te vereenvoudigen.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > De alias/naam (post_migration_dns_name) na de migratie wordt gebruikt voor toegang tot de oude standaard naam ruimte na migratie. Gebruik deze om de wacht rijen en de abonnementen af te zuigen en verwijder vervolgens de naam ruimte.

1. Koppel de standaard-en Premium-naam ruimten en start de synchronisatie met behulp van de volgende opdracht:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Controleer de status van de migratie met behulp van de volgende opdracht:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    De migratie wordt als voltooid beschouwd wanneer u de volgende waarden ziet:

    * MigrationState = "actief"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "geslaagd"

    Met deze opdracht wordt ook de migratie configuratie weer gegeven. Controleer of de waarden juist zijn ingesteld. Controleer ook de Premium-naam ruimte in de portal om te controleren of alle wacht rijen en onderwerpen zijn gemaakt en of ze overeenkomen met wat er in de standaard naam ruimte aanwezig is.

1. Voer de volgende volledige opdracht uit om de migratie door te voeren:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migreren met behulp van de Azure Portal

Migratie met behulp van de Azure Portal heeft dezelfde logische stroom als migratie met behulp van de opdrachten. Volg deze stappen om met behulp van de Azure Portal te migreren.

1. Selecteer **migreren naar Premium** in het **Navigatie** menu in het linkerdeel venster. Klik op de knop aan de **slag** om door te gaan naar de volgende pagina.
    ![Migratie landings pagina][]

1. Voltooi de **installatie**.
   ![Naam ruimte instellen][]
   1. Maak de Premium-naam ruimte en wijs deze toe om de bestaande standaard naam ruimte te migreren naar.
        ![Installatie naam ruimte-Premium-naam ruimte maken][]
   1. Kies een **naam** voor de migratie van de post. U gebruikt deze naam om toegang te krijgen tot de standaard naam ruimte nadat de migratie is voltooid.
        ![Installatie naam ruimte: de naam van de post migratie kiezen][]
   1. Selecteer **volgende** om door te gaan.
1. Entiteiten synchroniseren tussen de Standard-en Premium-naam ruimten.
    ![Setup-naam ruimte-entiteiten synchroniseren-starten][]

   1. Selecteer **begin synchronisatie** om te beginnen met het synchroniseren van de entiteiten.
   1. Selecteer **Ja** in het dialoog venster om de synchronisatie te bevestigen en te starten.
   1. Wacht totdat de synchronisatie is voltooid. De status is beschikbaar op de status balk.
        ![Naam ruimte instellen-entiteiten synchroniseren-voortgang][]
        >[!IMPORTANT]
        > Als u de migratie om een of andere reden moet afbreken, raadpleegt u de afbreek stroom in het gedeelte Veelgestelde vragen van dit document.
   1. Nadat de synchronisatie is voltooid, selecteert u **volgende** onder aan de pagina.

1. Bekijk de wijzigingen op de pagina samen vatting. Selecteer **volledige migratie** om te scha kelen tussen naam ruimten en de migratie te volt ooien.
    ![Switch naam ruimte: menu switch][]  
    De bevestigings pagina wordt weer gegeven wanneer de migratie is voltooid.
    ![Switch naam ruimte-geslaagd][]

## <a name="caveats"></a>Waarschuwingen

Sommige van de functies van Azure Service Bus Standard-laag worden niet ondersteund door Azure Service Bus Premium-laag. Dit zijn per ontwerp, aangezien de Premium-laag specifieke bronnen biedt voor voorspel bare door Voer en latentie.

Hier volgt een lijst met functies die niet worden ondersteund door Premium en de oplossing

### <a name="express-entities"></a>Express-entiteiten

   Express-entiteiten die geen bericht gegevens aan opslag door voeren, worden niet ondersteund in Premium. Toegewezen bronnen hebben aanzienlijke verbetering van de door Voer en zorgen ervoor dat de gegevens behouden blijven, zoals wordt verwacht van elk systeem voor bedrijfs berichten.

   Tijdens de migratie worden uw Express-entiteiten in uw standaard naam ruimte op de Premium-naam ruimte gemaakt als een niet-Express-entiteit.

   Als u Azure Resource Manager-sjablonen (ARM) gebruikt, moet u ervoor zorgen dat u de markering ' enableExpress ' verwijdert uit de implementatie configuratie zodat uw geautomatiseerde werk stromen zonder fouten worden uitgevoerd.

### <a name="partitioned-entities"></a>Gepartitioneerde entiteiten

   Gepartitioneerde entiteiten worden ondersteund in de laag standaard om een betere Beschik baarheid te bieden in een multi tenant-installatie. Met het inrichten van toegewezen resources die beschikbaar zijn per naam ruimte in de Premium-laag, is dit niet meer nodig.

   Tijdens de migratie wordt een gepartitioneerde entiteit in de Standard-naam ruimte gemaakt op de Premium-naam ruimte als een niet-gepartitioneerde entiteit.

   Als uw ARM-sjabloon ' enablePartitioning ' instelt op ' True ' voor een specifieke wachtrij of onderwerp, wordt deze door de Broker genegeerd.

## <a name="faqs"></a>Veelgestelde vragen

### <a name="what-happens-when-the-migration-is-committed"></a>Wat gebeurt er wanneer de migratie wordt doorgevoerd?

Nadat de migratie is doorgevoerd, wordt de connection string die naar de standaard naam ruimte wijst, naar de Premium-naam ruimte verwijzen.

De Sender-en receiver-toepassingen verbreekt de verbinding met de standaard naam ruimte en maken automatisch verbinding met de Premium-naam ruimte.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Wat moet ik doen nadat de standaard voor Premium-migratie is voltooid?

De standaard voor Premium-migratie zorgt ervoor dat de meta gegevens van de entiteit, zoals onderwerpen, abonnementen en filters, worden gekopieerd van de standaard naam ruimte naar de Premium-naam ruimte. De bericht gegevens die zijn vastgelegd in de standaard naam ruimte worden niet gekopieerd van de standaard naam ruimte naar de Premium-naam ruimte.

De standaard naam ruimte bevat mogelijk berichten die zijn verzonden en doorgevoerd tijdens de migratie. Voer deze berichten hand matig uit in de standaard naam ruimte en stuur deze hand matig naar de Premium-naam ruimte. Als u de berichten hand matig wilt verwijderen, gebruikt u een console-app of een script waarmee de standaard naam ruimte-entiteiten worden verwisseld met behulp van de post-migratie DNS-naam die u hebt opgegeven in de migratie opdrachten. Deze berichten verzenden naar de Premium-naam ruimte, zodat deze kunnen worden verwerkt door de ontvangers.

Nadat de berichten zijn leeg gemaakt, verwijdert u de standaard naam ruimte.

>[!IMPORTANT]
> Nadat de berichten uit de standaard naam ruimte zijn leeg gemaakt, verwijdert u de standaard naam ruimte. Dit is belang rijk omdat de connection string die in eerste instantie de Standard-naam ruimte wordt genoemd, nu verwijst naar de Premium-naam ruimte. U hebt de standaard naam ruimte niet meer nodig. Als u de standaard naam ruimte die u hebt gemigreerd verwijdert, vermindert u later Verwar ring.

### <a name="how-much-downtime-do-i-expect"></a>Hoeveel downtime moet ik verwachten?

Het migratie proces is bedoeld om de verwachte downtime voor de toepassingen te verminderen. Uitval tijd wordt gereduceerd met behulp van de connection string die de toepassingen van de afzender en de ontvanger gebruiken om naar de nieuwe Premium-naam ruimte te verwijzen.

De downtime die door de toepassing wordt ervaren, is beperkt tot de tijd die nodig is om de DNS-vermelding bij te werken zodat deze naar de Premium-naam ruimte wijst. Downtime is ongeveer 5 minuten.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Moet ik configuratie wijzigingen aanbrengen tijdens de migratie?

Nee, er zijn geen code-of configuratie wijzigingen nodig om de migratie uit te voeren. De connection string dat de afzender-en receiver-toepassingen gebruiken om toegang te krijgen tot de standaard naam ruimte wordt automatisch toegewezen om te fungeren als alias voor de Premium-naam ruimte.

### <a name="what-happens-when-i-abort-the-migration"></a>Wat gebeurt er wanneer ik de migratie afbreekt?

De migratie kan worden afgebroken met behulp van de `Abort` opdracht of met behulp van de Azure Portal.

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure Portal

![Stroom afbreken: afbreken van synchronisatie afgebroken ][]
 ![ stroom-afbreken voltooid][]

Wanneer het migratie proces wordt afgebroken, wordt het kopiëren van de entiteiten (onderwerpen, abonnementen en filters) van de standaard naar de Premium-naam ruimte afgebroken en wordt de koppeling verbroken.

De connection string is niet bijgewerkt zodat deze naar de Premium-naam ruimte wijst. Uw bestaande toepassingen blijven werken zoals ze waren voordat u de migratie begon.

De entiteiten in de Premium-naam ruimte worden echter niet verwijderd of de Premium-naam ruimte wordt verwijderd. Verwijder de entiteiten hand matig als u ervoor hebt gekozen om door te gaan met de migratie.

>[!IMPORTANT]
> Als u besluit de migratie af te breken, verwijdert u de Premium-naam ruimte die u voor de migratie hebt ingericht, zodat u niet in rekening wordt gebracht voor de resources.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Ik wil de berichten niet hoeven te verwerken. Wat moet ik doen?

Er zijn mogelijk berichten die door de toepassingen van de afzender worden verzonden en die zijn toegewezen aan de opslag in de standaard naam ruimte terwijl de migratie plaatsvindt en net voordat de migratie wordt doorgevoerd.

Tijdens de migratie worden de werkelijke bericht gegevens/Payload niet gekopieerd van de standaard naar de Premium-naam ruimte. De berichten moeten hand matig worden geleegd en vervolgens naar de Premium-naam ruimte worden verzonden.

Als u echter kunt migreren tijdens een gepland onderhouds-en housekeeping en u de berichten niet hand matig wilt verwerken en verzenden, voert u de volgende stappen uit:

1. Stop de toepassingen van de afzender. De receive-toepassingen verwerken de berichten die zich momenteel in de standaard naam ruimte bevinden en verwatert de wachtrij.
1. Nadat de wacht rijen en abonnementen in de standaard naam ruimte leeg zijn, volgt u de procedure die eerder is beschreven voor het uitvoeren van de migratie van de standaard naar de Premium-naam ruimte.
1. Nadat de migratie is voltooid, kunt u de toepassingen van de afzender opnieuw starten.
1. De afzenders en ontvangers zullen nu automatisch verbinding maken met de Premium-naam ruimte.

    >[!NOTE]
    > U hoeft de ontvangende toepassingen niet te stoppen voor de migratie.
    >
    > Nadat de migratie is voltooid, wordt de verbinding met de receiver-toepassingen met de standaard naam ruimte verbroken en wordt automatisch verbinding gemaakt met de Premium-naam ruimte.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [verschillen tussen de Standard-en Premium-berichten](./service-bus-premium-messaging.md).
* Meer informatie over de [herstel aspecten met hoge Beschik baarheid en Geo-Disaster voor service bus Premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Migratie landings pagina]: ./media/service-bus-standard-premium-migration/1.png
[Naam ruimte instellen]: ./media/service-bus-standard-premium-migration/2.png
[Installatie naam ruimte-Premium-naam ruimte maken]: ./media/service-bus-standard-premium-migration/3.png
[Installatie naam ruimte: de naam van de post migratie kiezen]: ./media/service-bus-standard-premium-migration/4.png
[Setup-naam ruimte-entiteiten synchroniseren-starten]: ./media/service-bus-standard-premium-migration/5.png
[Naam ruimte instellen-entiteiten synchroniseren-voortgang]: ./media/service-bus-standard-premium-migration/8.png
[Switch naam ruimte: menu switch]: ./media/service-bus-standard-premium-migration/9.png
[Switch naam ruimte-geslaagd]: ./media/service-bus-standard-premium-migration/12.png

[Stroom afbreken-synchronisatie afbreken]: ./media/service-bus-standard-premium-migration/abort1.png
[Stroom afbreken-afbreken voltooid]: ./media/service-bus-standard-premium-migration/abort3.png
