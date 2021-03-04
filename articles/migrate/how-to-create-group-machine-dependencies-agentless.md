---
title: Afhankelijkheids analyse zonder agent instellen in de evaluatie van Azure Migrate server
description: Een afhankelijkheids analyse zonder agent instellen in de evaluatie van Azure Migrate server.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: c3aa2aea764af8469152b007e60427724fea398a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045850"
---
# <a name="analyze-server-dependencies-agentless"></a>Server afhankelijkheden analyseren (zonder agent)

In dit artikel wordt beschreven hoe u een afhankelijkheids analyse zonder agent instelt met behulp van Azure Migrate: Server Assessment. [Afhankelijkheids analyse](concepts-dependency-visualization.md) helpt u bij het identificeren en begrijpen van afhankelijkheden tussen servers voor evaluatie en migratie naar Azure.

> [!IMPORTANT]
> Er is momenteel een preview-versie van de afhankelijkheids analyse van agents beschikbaar voor servers die worden uitgevoerd in uw VMware-omgeving, gedetecteerd met het Azure Migrate: Server assessment tool.
> Deze preview wordt gedekt door de klant ondersteuning en kan worden gebruikt voor werk belastingen voor de productie.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="current-limitations"></a>Huidige beperkingen

- In de weer gave afhankelijkheids analyse kunt u momenteel geen server toevoegen aan of verwijderen uit een groep.
- Een afhankelijkheids toewijzing voor een groep servers is momenteel niet beschikbaar.
- In een Azure Migrate project kan het verzamelen van afhankelijkheids gegevens gelijktijdig worden ingeschakeld voor 1000-servers. U kunt een hoger aantal servers analyseren door sequentiëren in batches van 1000-servers.

## <a name="before-you-start"></a>Voordat u begint

- Zorg ervoor dat u [een Azure migrate-project hebt gemaakt](./create-manage-projects.md) met het Azure migrate: Server assessment tool is toegevoegd.
- Raadpleeg de [VMware-vereisten](migrate-support-matrix-vmware.md#vmware-requirements) voor het uitvoeren van afhankelijkheids analyse.
- Controleer de [vereisten voor apparaten](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) voordat u het apparaat instelt.
- [Bekijk vereisten voor afhankelijkheids analyse](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) voordat u afhankelijkheids analyses op servers inschakelt.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Het Azure Migrate apparaat implementeren en configureren

1. [Bekijk](migrate-appliance.md#appliance---vmware) de vereisten voor het implementeren van het Azure migrate apparaat.
2. Bekijk de Azure-Url's die het apparaat nodig heeft voor toegang tot de [open bare](migrate-appliance.md#public-cloud-urls) en [overheids Clouds](migrate-appliance.md#government-cloud-urls).
3. [Bekijk gegevens](migrate-appliance.md#collected-data---vmware) die door het apparaat worden verzameld tijdens de detectie en evaluatie.
4. [Noteer](migrate-support-matrix-vmware.md#port-access-requirements) de toegangs vereisten voor poorten voor het apparaat.
5. [Implementeer het Azure migrate apparaat om de](how-to-set-up-appliance-vmware.md) detectie te starten. Als u het apparaat wilt implementeren, downloadt en importeert u een eicellen-sjabloon in VMware om een server te maken die wordt uitgevoerd in uw vCenter Server. Nadat u het apparaat hebt geïmplementeerd, moet u dit registreren bij het Azure Migrate-project en het configureren om de detectie te initiëren.
6. Wanneer u het apparaat configureert, moet u het volgende opgeven in de configuratie beheer van het apparaat:
    - De details van de vCenter Server waarmee u verbinding wilt maken.
    - vCenter Server het bereik van referenties voor het detecteren van de servers in uw VMware-omgeving.
    - Server referenties die domein-/Windows-(niet-domein)/Linux-referenties (niet-domein) kunnen zijn. Meer [informatie](add-server-credentials.md) over hoe u referenties kunt opgeven en hoe u deze kunt afhandelen.

## <a name="verify-permissions"></a>Machtigingen controleren

- U moet [een vCenter Server alleen-lezen-account maken](./tutorial-discover-vmware.md#prepare-vmware) voor detectie en evaluatie. Voor de alleen-lezen-account zijn bevoegdheden nodig die zijn ingeschakeld voor **virtual machines**  >  -**gast bewerkingen**, om te kunnen communiceren met de servers om afhankelijkheids gegevens te verzamelen.
- U hebt een gebruikers account nodig zodat de server evaluatie toegang kan krijgen tot de server om afhankelijkheids gegevens te verzamelen. [Meer informatie](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) over de account vereisten voor Windows-en Linux-servers.

### <a name="add-credentials-and-initiate-discovery"></a>Referenties toevoegen en detectie starten

1. Open de configuratie van het apparaatconfiguratie-beheer, voer de vereiste controles en registratie van het apparaat uit.
2. Navigeer naar het paneel **referenties en detectie bronnen beheren** .
1.  In **stap 1: geef vCenter Server referenties** op en klik op **referenties toevoegen** om referenties op te geven voor het vCenter Server account dat het apparaat gebruikt om servers te detecteren die worden uitgevoerd op de vCenter Server.
1. In **stap 2: geef vCenter Server Details** op, klik op **detectie bron toevoegen** om de beschrijvende naam voor referenties in de vervolg keuzelijst te selecteren, geef het **IP-adres/de FQDN** van de vCenter Server instantie :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="paneel 3 op Apparaatbeheer voor vCenter Server Details op"::: .
1. In **stap 3: Server referenties opgeven voor het uitvoeren van software-inventaris, afhankelijkheids analyse zonder agent en detectie van SQL Server instanties en data bases**, klikt u op **referenties toevoegen** om meerdere Server referenties op te geven om software-inventaris te initiëren.
1. Klik op **detectie starten** om te beginnen met de detectie van vCenter Server.

 Nadat de vCenter Server detectie is voltooid, start het apparaat de detectie van geïnstalleerde toepassingen, functies en onderdelen (software-inventarisatie). Tijdens software-inventarisatie worden de referenties van de toegevoegde servers herhaald op servers en gevalideerd voor de afhankelijkheids analyse zonder agent. U kunt afhankelijkheids analyse zonder agent inschakelen voor servers vanuit de portal. Alleen de servers waarop de validatie slaagt, kunnen worden geselecteerd voor het inschakelen van afhankelijkheids analyse zonder agent.

## <a name="start-dependency-discovery"></a>Detectie van afhankelijkheid starten

Selecteer de servers waarop u detectie van afhankelijkheden wilt inschakelen.

1. Klik in **Azure migrate: Server evaluatie** op **gedetecteerde servers**.
2. Kies de **naam** van het apparaat waarvan u de detectie wilt controleren.
1. U kunt de validatie status van de servers bekijken onder **afhankelijkheden (zonder agent)** kolom.
1. Klik op de vervolg keuzelijst **afhankelijkheids analyse** .
1. Klik op **servers toevoegen**.
1. Selecteer op de pagina **servers toevoegen** de servers waarop u de afhankelijkheids analyse wilt inschakelen. U kunt afhankelijkheids toewijzing alleen inschakelen op de servers waarop validatie is geslaagd. De volgende validatie cyclus wordt 24 uur na de laatste tijds tempel van de validatie uitgevoerd.
1. Klik na het selecteren van de servers op **servers toevoegen**.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="Afhankelijkheids analyse starten":::

U kunt afhankelijkheden ongeveer zes uur na het inschakelen van afhankelijkheids analyse op servers visualiseren. Als u meerdere servers tegelijk wilt inschakelen voor afhankelijkheids analyse, kunt u [Power shell](#start-or-stop-dependency-analysis-using-powershell) gebruiken om dit te doen.

## <a name="visualize-dependencies"></a>Afhankelijkheden visualiseren

1. Klik in **Azure migrate: Server evaluatie** op **gedetecteerde servers**.
1. Kies de **naam** van het apparaat waarvan u de detectie wilt controleren.
1. Zoek de server waarvan u de afhankelijkheden wilt controleren.
1. Klik onder de kolom **afhankelijkheden (zonder agent)** op **afhankelijkheden weer geven**
1. Wijzig de tijds periode waarvoor u de kaart wilt weer geven met de vervolg keuzelijst **tijd duur** .
1. Vouw de **client** groep uit om de servers met een afhankelijkheid op de geselecteerde server weer te geven.
1. Vouw de **poort** groep uit om de servers weer te geven die een afhankelijkheid hebben van de geselecteerde server.
1. Als u wilt navigeren naar de kaart weergave van een van de afhankelijke servers, klikt u op de server naam > **Load Server-map** server- 
 :::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="poort groep uitbreiden en server toewijzing laden":::
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="Client groep uitvouwen":::

8. Vouw de geselecteerde server uit om details op proces niveau weer te geven voor elke afhankelijkheid.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="Server uitvouwen om processen weer te geven":::

> [!NOTE]
> Proces informatie voor een afhankelijkheid is niet altijd beschikbaar. Als deze niet beschikbaar is, wordt de afhankelijkheid weer gegeven met het proces dat is gemarkeerd als ' onbekend proces '.

## <a name="export-dependency-data"></a>Afhankelijkheids gegevens exporteren

1. Klik in **Azure migrate: Server evaluatie** op **gedetecteerde servers**.
2. Klik op de vervolg keuzelijst **afhankelijkheids analyse** .
3. Klik op **toepassings afhankelijkheden exporteren**.
4. Kies op de pagina **toepassings afhankelijkheden exporteren** de naam van het apparaat dat de gewenste servers detecteert.
5. Selecteer de begin tijd en eind tijd. Houd er rekening mee dat u de gegevens alleen voor de afgelopen 30 dagen kunt downloaden.
6. Klik op **afhankelijkheid exporteren**.

De afhankelijkheids gegevens worden geëxporteerd en gedownload in CSV-indeling. Het gedownloade bestand bevat de afhankelijkheids gegevens op alle servers die zijn ingeschakeld voor afhankelijkheids analyse. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="Afhankelijkheden exporteren":::

### <a name="dependency-information"></a>Afhankelijkheids gegevens

Elke rij in het geëxporteerde CSV komt overeen met een afhankelijkheid in de opgegeven tijds periode.

De volgende tabel bevat een overzicht van de velden in het geëxporteerde CSV-bestand. Houd er rekening mee dat Server naam-, toepassings-en proces velden alleen worden ingevuld voor servers waarop de afhankelijkheids analyse zonder agent is ingeschakeld.

**Veldnaam** | **Details**
--- | --- 
Timeslot | De timeslot waarin de afhankelijkheid is waargenomen. <br/> Er worden momenteel afhankelijkheids gegevens vastgelegd in sleuven van zes uur.
Naam van bron server | Naam van de bron server 
Bron toepassing | Naam van de toepassing op de bron server  
Bron proces | De naam van het proces op de bron server  
Naam van de doel server | Naam van de doel server
Doel-IP | IP-adres van de doel server
Doel toepassing | Naam van de toepassing op de doel server
Doel proces | De naam van het proces op de doel server  
Doelpoort | Poort nummer op de doel server

## <a name="stop-dependency-discovery"></a>Detectie van afhankelijkheid stoppen

Selecteer de servers waarop u de detectie van afhankelijkheden wilt stoppen.

1. Klik in **Azure migrate: Server evaluatie** op **gedetecteerde servers**.
1. Kies de **naam** van het apparaat waarvan u de detectie wilt controleren.
1. Klik op de vervolg keuzelijst **afhankelijkheids analyse** .
1. Klik op **servers verwijderen**.
1. Selecteer op de pagina **servers verwijderen** de server die u wilt stoppen voor afhankelijkheids analyse.
1. Klik na het selecteren van de servers op **servers verwijderen**.

Als u de afhankelijkheid op meerdere servers tegelijk wilt stoppen, kunt u [Power shell](#start-or-stop-dependency-analysis-using-powershell) gebruiken om dit te doen.

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>Afhankelijkheids analyse starten of stoppen met Power shell

Down load de Power shell-module van [Azure PowerShell samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) opslag plaats op github.

### <a name="log-in-to-azure"></a>Meld u aan bij Azure.

1. Meld u aan bij uw Azure-abonnement met behulp van de cmdlet Connect-AzAccount.

    ```PowerShell
    Connect-AzAccount
    ```
    Gebruik de volgende opdracht als u Azure Government gebruikt.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Selecteer het abonnement waarin u het Azure Migrate project hebt gemaakt 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. De gedownloade AzMig_Dependencies Power shell-module importeren

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Verzamelen van afhankelijkheids gegevens in-of uitschakelen

1. Bekijk de lijst met gedetecteerde servers in uw Azure Migrate-project met behulp van de volgende opdrachten. In het onderstaande voor beeld is de project naam FabrikamDemoProject en de resource groep waartoe deze behoort, is FabrikamDemoRG. De lijst met servers wordt opgeslagen in FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    In het bestand ziet u de weergave naam van de server, de huidige status van de afhankelijkheids verzameling en de ARM-ID van alle gedetecteerde servers. 

2. Als u afhankelijkheden wilt in-of uitschakelen, maakt u een CSV-invoer bestand. Het bestand moet een kolom met de header ' ARM-ID ' hebben. Eventuele aanvullende headers in het CSV-bestand worden genegeerd. U kunt het CSV maken met het bestand dat u in de vorige stap hebt gegenereerd. Maak een kopie van het bestand waarin de servers worden bewaard waarvoor u afhankelijkheden wilt in-of uitschakelen. 

    In het volgende voor beeld wordt afhankelijkheids analyse ingeschakeld op de lijst met servers in het invoer bestand FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    In het volgende voor beeld wordt de afhankelijkheids analyse uitgeschakeld op de lijst met servers in het invoer bestand FabrikamDemo_VMs_Disable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Netwerk verbindingen in Power BI visualiseren

Azure Migrate biedt een Power BI sjabloon die u kunt gebruiken om netwerk verbindingen van veel servers tegelijk te visualiseren en te filteren op proces en server. Als u wilt visualiseren, laadt u de Power BI met afhankelijkheids gegevens volgens de onderstaande instructies.

1. Down load de Power shell-module en de Power BI sjabloon vanuit [Azure PowerShell samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) opslag plaats op github.

2. Meld u aan bij Azure met behulp van de onderstaande instructies: 
    - Meld u aan bij uw Azure-abonnement met behulp van de cmdlet Connect-AzAccount.

        ```PowerShell
        Connect-AzAccount
        ```

    - Gebruik de volgende opdracht als u Azure Government gebruikt.

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - Selecteer het abonnement waarin u het Azure Migrate project hebt gemaakt

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. De gedownloade AzMig_Dependencies Power shell-module importeren

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Voer de volgende opdracht uit. Met deze opdracht worden de afhankelijkheids gegevens in een CSV gedownload en wordt deze verwerkt voor het genereren van een lijst met unieke afhankelijkheden die kunnen worden gebruikt voor visualisatie in Power BI. In het voor beeld hieronder is de project naam FabrikamDemoProject en de resource groep waartoe deze behoort, is FabrikamDemoRG. De afhankelijkheden worden gedownload voor servers die worden gedetecteerd door FabrikamAppliance. De unieke afhankelijkheden worden opgeslagen in FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. De gedownloade Power BI-sjabloon openen

6. Laad de gedownloade afhankelijkheids gegevens in Power BI.
    - Open de sjabloon in Power BI.
    - Klik op **gegevens ophalen** op de werk balk. 
    - Kies **tekst/CSV** in algemene gegevens bronnen.
    - Kies het bestand met afhankelijkheden dat is gedownload.
    - Klik op **laden**.
    - U ziet dat er een tabel wordt geïmporteerd met de naam van het CSV-bestand. U ziet de tabel in de velden balk aan de rechter kant. Wijzig de naam in AzMig_Dependencies
    - Klik op Vernieuwen in de werk balk.

    De tabel netwerk verbindingen en de naam van de bron server, de naam van de doel server, de naam van het bron proces, de Slicers van de doel proces naam moeten licht worden gemaakt met de geïmporteerde gegevens.

7. Visualiseer de kaart van netwerk verbindingen die worden gefilterd op servers en processen. Sla het bestand op.

## <a name="next-steps"></a>Volgende stappen

[Groeps servers](how-to-create-a-group.md) voor evaluatie.