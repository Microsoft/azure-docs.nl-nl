---
title: Afhankelijkheids analyse zonder agent instellen in de evaluatie van Azure Migrate server
description: Een afhankelijkheids analyse zonder agent instellen in de evaluatie van Azure Migrate server.
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 722ad4cc27132815c1416331aaf0691754ccee05
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500781"
---
# <a name="analyze-machine-dependencies-agentless"></a>Machine-afhankelijkheden analyseren (zonder agents)

In dit artikel wordt beschreven hoe u analyse van agentloze afhankelijkheden instelt in Azure Migrate: Server Assessment. [Afhankelijkheids analyse](concepts-dependency-visualization.md) helpt u bij het identificeren en begrijpen van afhankelijkheden tussen machines voor evaluatie en migratie naar Azure.


> [!IMPORTANT]
> Visualisatie van de afhankelijkheid van agents is momenteel beschikbaar als Preview voor VMware-Vm's die zijn gedetecteerd met het Azure Migrate: Server assessment tool.
> Functies zijn mogelijk beperkt of onvolledig.
> Deze preview wordt gedekt door de klant ondersteuning en kan worden gebruikt voor werk belastingen voor de productie.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="current-limitations"></a>Huidige beperkingen

- In de weer gave afhankelijkheids analyse kunt u op dit moment geen server toevoegen aan of verwijderen uit een groep.
- Een afhankelijkheids toewijzing voor een groep servers is momenteel niet beschikbaar.
- In een Azure Migrate project kan het verzamelen van afhankelijkheids gegevens gelijktijdig worden ingesteld voor 1000-servers. U kunt een hoger aantal servers analyseren door sequentiëren in batches van 1000.

## <a name="before-you-start"></a>Voordat u begint

- [Bekijk](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) ondersteunde besturings systemen en de vereiste machtigingen.
- Zorg ervoor dat:
    - Een Azure Migrate project hebben. Als u dit niet doet, [maakt](./create-manage-projects.md) u er nu een.
    - Controleer of u het Azure Migrate: Server Assessment Tool hebt [toegevoegd](how-to-assess.md) aan het project.
    - Stel een [Azure migrate apparaat](migrate-appliance.md) in om on-premises machines te detecteren. [Stel een apparaat](how-to-set-up-appliance-vmware.md) in voor VMware-vm's. Het apparaat detecteert on-premises machines en verstuurt meta gegevens en prestatie gegevens naar Azure Migrate: Server evaluatie.
- Controleer of VMware-Hulpprogram Ma's (hoger dan 10,2) is geïnstalleerd op elke virtuele machine die u wilt analyseren.


## <a name="create-a-user-account-for-discovery"></a>Een gebruikers account maken voor detectie

Stel een gebruikers account in zodat de server evaluatie toegang kan krijgen tot de virtuele machine om afhankelijkheden te detecteren. [Meer informatie](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) over de account vereisten voor virtuele Windows-en Linux-machines.


## <a name="add-the-user-account-to-the-appliance"></a>Het gebruikers account toevoegen aan het apparaat

Voeg het gebruikers account toe aan het apparaat.

1. Open de app voor het beheren van apparaten. 
2. Navigeer naar het deel venster **vCenter-gegevens opgeven** .
3. Klik in **toepassing en afhankelijkheden van Vm's detecteren** op **referenties toevoegen**
3. Kies het **besturings systeem**, geef een beschrijvende naam op voor het account en het **User name** / **wacht woord** voor de gebruikers naam
6. Klik op **Opslaan**.
7. Klik op **opslaan en detectie starten**.

    ![VM-gebruikers account toevoegen](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Detectie van afhankelijkheid starten

Kies de computers waarop u detectie van afhankelijkheden wilt inschakelen. 

1. Klik in **Azure migrate: Server evaluatie** op **gedetecteerde servers**.
2. Klik op het pictogram **afhankelijkheids analyse** .
3. Klik op **servers toevoegen**.
4. Kies op de pagina **servers toevoegen** het apparaat dat de relevante computers detecteert.
5. Selecteer de machines in de lijst met computers.
6. Klik op **servers toevoegen**.

    ![Detectie van afhankelijkheid starten](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

U kunt afhankelijkheden ongeveer zes uur na het starten van de detectie van afhankelijkheden visualiseren. Als u meerdere computers wilt inschakelen, kunt u [Power shell](#start-or-stop-dependency-discovery-using-powershell) gebruiken om dit te doen.

## <a name="visualize-dependencies"></a>Afhankelijkheden visualiseren

1. Klik in **Azure migrate: Server evaluatie** op **gedetecteerde servers**.
2. Zoek naar de computer die u wilt weer geven.
3. Klik in de kolom **afhankelijkheden** op **afhankelijkheden weer geven**
4. Wijzig de tijds periode waarvoor u de kaart wilt weer geven met de vervolg keuzelijst **tijd duur** .
5. Vouw de **client** groep uit om de machines met een afhankelijkheid op de geselecteerde computer weer te geven.
6. Vouw de **poort** groep uit om de computers weer te geven die een afhankelijkheid hebben van de geselecteerde computer.
7. Als u wilt navigeren naar de kaart weergave van een van de afhankelijke machines, klikt u op de computer naam > **Server toewijzing laden**

    ![Server poort groep uitvouwen en server toewijzing laden](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Client groep uitvouwen ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Vouw de geselecteerde machine uit om details op proces niveau weer te geven voor elke afhankelijkheid.

    ![Server uitvouwen om processen weer te geven](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Proces informatie voor een afhankelijkheid is niet altijd beschikbaar. Als deze niet beschikbaar is, wordt de afhankelijkheid weer gegeven met het proces dat is gemarkeerd als ' onbekend proces '.

## <a name="export-dependency-data"></a>Afhankelijkheids gegevens exporteren

1. Klik in **Azure migrate: Server evaluatie** op **gedetecteerde servers**.
2. Klik op het pictogram **afhankelijkheids analyse** .
3. Klik op **toepassings afhankelijkheden exporteren**.
4. Kies op de pagina **toepassings afhankelijkheden exporteren** het apparaat dat de relevante computers detecteert.
5. Selecteer de begin tijd en eind tijd. Houd er rekening mee dat u de gegevens alleen voor de afgelopen 30 dagen kunt downloaden.
6. Klik op **afhankelijkheid exporteren**.

De afhankelijkheids gegevens worden geëxporteerd en gedownload in CSV-indeling. Het gedownloade bestand bevat de afhankelijkheids gegevens op alle machines die zijn ingeschakeld voor afhankelijkheids analyse. 

![Afhankelijkheden exporteren](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>Afhankelijkheids gegevens

Elke rij in het geëxporteerde CSV komt overeen met een afhankelijkheid in de opgegeven tijds periode. 

De volgende tabel bevat een overzicht van de velden in het geëxporteerde CSV-bestand. Houd er rekening mee dat Server naam-, toepassings-en proces velden alleen worden ingevuld voor servers waarop de afhankelijkheids analyse zonder agent is ingeschakeld.

**Veldnaam** | **Details**
--- | --- 
Timeslot | De timeslot waarin de afhankelijkheid is waargenomen. <br/> Er worden momenteel afhankelijkheids gegevens vastgelegd in sleuven van zes uur.
Naam van bron server | Naam van de bron machine 
Bron toepassing | Naam van de toepassing op de bron machine 
Bron proces | De naam van het proces op de bron machine 
Naam van de doel server | Naam van de doel computer
Doel-IP | IP-adres van de doel computer
Doel toepassing | Naam van de toepassing op de doel computer
Doel proces | De naam van het proces op de doel computer 
Doelpoort | Poort nummer op de doel computer


## <a name="stop-dependency-discovery"></a>Detectie van afhankelijkheid stoppen

Kies de computers waarop u de detectie van afhankelijkheden wilt stoppen. 

1. Klik in **Azure migrate: Server evaluatie** op **gedetecteerde servers**.
2. Klik op het pictogram **afhankelijkheids analyse** .
3. Klik op **servers verwijderen**.
3. Kies op de pagina **servers verwijderen** het **apparaat** dat de vm's detecteert waarop u de detectie van de afhankelijkheid wilt stoppen.
4. Selecteer de machines in de lijst met computers.
5. Klik op **servers verwijderen**.

Als u de afhankelijkheid op meerdere computers wilt stoppen, kunt u [Power shell](#start-or-stop-dependency-discovery-using-powershell) gebruiken om dit te doen.


## <a name="start-or-stop-dependency-discovery-using-powershell"></a>Detectie van afhankelijkheden starten of stoppen met Power shell

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

1. Bekijk de lijst met gedetecteerde virtuele VMware-machines in uw Azure Migrate-project met behulp van de volgende opdrachten. In het onderstaande voor beeld is de project naam FabrikamDemoProject en de resource groep waartoe deze behoort, is FabrikamDemoRG. De lijst met computers wordt opgeslagen in FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    In het bestand ziet u de weergave naam van de virtuele machine, de huidige status van de afhankelijkheids verzameling en de ARM-ID van alle gedetecteerde Vm's. 

2. Als u afhankelijkheden wilt in-of uitschakelen, maakt u een CSV-invoer bestand. Het bestand moet een kolom met de header ' ARM-ID ' hebben. Eventuele aanvullende headers in het CSV-bestand worden genegeerd. U kunt het CSV maken met het bestand dat u in de vorige stap hebt gegenereerd. Maak een kopie van het bestand waarin de Vm's worden bewaard waarvoor u afhankelijkheden wilt in-of uitschakelen. 

    In het volgende voor beeld wordt afhankelijkheids analyse ingeschakeld op de lijst met Vm's in het invoer bestand FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    In het volgende voor beeld wordt de afhankelijkheids analyse uitgeschakeld in de lijst met virtuele machines in het invoer bestand FabrikamDemo_VMs_Disable.csv.

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

4. Voer de volgende opdracht uit. Met deze opdracht worden de afhankelijkheids gegevens in een CSV gedownload en wordt deze verwerkt voor het genereren van een lijst met unieke afhankelijkheden die kunnen worden gebruikt voor visualisatie in Power BI. In het voor beeld hieronder is de project naam FabrikamDemoProject en de resource groep waartoe deze behoort, is FabrikamDemoRG. De afhankelijkheden worden gedownload voor computers die zijn gedetecteerd door FabrikamAppliance. De unieke afhankelijkheden worden opgeslagen in FabrikamDemo_Dependencies.csv

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

[Groep machines](how-to-create-a-group.md) voor evaluatie.