---
title: Wijzigingen bijhouden en inventaris in Azure Automation beheren
description: In dit artikel leest u hoe u Wijzigingen bijhouden en inventaris kunt gebruiken om wijzigingen in de software en micro soft-Services in uw omgeving bij te houden.
services: automation
ms.subservice: change-inventory-management
ms.date: 12/10/2020
ms.topic: conceptual
ms.openlocfilehash: dff314f3c9fb72c565a7c2d522694d533c487895
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572643"
---
# <a name="manage-change-tracking-and-inventory"></a>Wijzigingen bijhouden en inventaris beheren

Wanneer u een nieuw bestand of register sleutel toevoegt om bij te houden, Azure Automation dit voor [Wijzigingen bijhouden en inventaris](overview.md)inschakelen. In dit artikel wordt beschreven hoe u tracering configureert, traceer resultaten controleert en hoe u waarschuwingen afhandelt wanneer er wijzigingen worden gedetecteerd.

Voordat u de procedures in dit artikel gebruikt, moet u ervoor zorgen dat u Wijzigingen bijhouden en inventaris op uw Vm's hebt ingeschakeld met een van de volgende technieken:

* [Wijzigingen bijhouden en inventaris inschakelen vanuit een Automation-account](enable-from-automation-account.md)
* [Wijzigingen bijhouden en inventaris inschakelen door te bladeren door de Azure Portal](enable-from-portal.md)
* [Wijzigingen bijhouden en inventaris inschakelen vanuit een runbook](enable-from-runbook.md)
* [Wijzigingen bijhouden en inventaris inschakelen vanaf een virtuele Azure-machine](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Het bereik voor de implementatie beperken

Wijzigingen bijhouden en inventarisatie gebruikt een scope configuratie in de werk ruimte om de computers te richten op het ontvangen van wijzigingen. Zie [beperken wijzigingen bijhouden en inventarisatie bereik](manage-scope-configurations.md)voor meer informatie.

## <a name="track-files"></a>Bestanden bijhouden

U kunt Wijzigingen bijhouden en inventaris gebruiken om wijzigingen in bestanden en mappen/mappen bij te houden. In deze sectie wordt beschreven hoe u bestands tracering configureert in Windows en op Linux.

### <a name="configure-file-tracking-on-windows"></a>Bestands tracering configureren in Windows

Gebruik de volgende stappen om bestands tracering op Windows-computers te configureren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer in de Azure-portal de optie **Alle services**. Typ in de lijst met resources **Automation**. Wanneer u begint te typen, worden in de lijst suggesties weer geven op basis van uw invoer. Selecteer **Automation-accounts**.

3. Selecteer in de lijst met Automation-accounts het account dat u hebt gekozen bij het inschakelen van Wijzigingen bijhouden en inventarisatie.

4. Selecteer in uw Automation-account de optie **Wijzigingen bijhouden** onder **configuratie beheer**.

5. Selecteer **Instellingen bewerken** (het tandwiel symbool).

6. Selecteer op de pagina werkruimte configuratie de optie **Windows-bestanden** en klik vervolgens op **toevoegen** om een nieuw bestand toe te voegen om bij te houden.

7. Voer in het deel venster Windows-bestand voor Wijzigingen bijhouden toevoegen de informatie in voor het bestand of de map die u wilt bijhouden en klik op **Opslaan**. In de volgende tabel worden de eigenschappen gedefinieerd die u kunt gebruiken voor de informatie.

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Ingeschakeld     | Waar als de instelling wordt toegepast en anders false.        |
    |Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden.        |
    |Groep     | Een groeps naam voor het logisch groeperen van bestanden.        |
    |Pad invoeren     | Het pad om het bestand te controleren, bijvoorbeeld: **c:\Temp \\ \* . txt**. U kunt ook omgevings variabelen gebruiken, zoals `%winDir%\System32\\\*.*` .       |
    |Padtype     | Het type pad. Mogelijke waarden zijn bestand en map.        |    
    |Recursie     | Waar als recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden, en ONWAAR anders onwaar.        |    
    |Bestands inhoud uploaden | True voor het uploaden van bestands inhoud op bijgehouden wijzigingen en ONWAAR, anders onwaar.|

    Als u van plan bent om de bewaking van bestanden en mappen met behulp van joker tekens te configureren, moet u rekening houden met het volgende:

    - Joker tekens zijn vereist voor het bijhouden van meerdere bestanden.
    - Joker tekens kunnen alleen worden gebruikt in het laatste segment van een pad, zoals *C:\folder\file* of */etc/*. conf *
    - Als een omgevings variabele een pad bevat dat niet geldig is, wordt de validatie uitgevoerd, maar het pad mislukt wanneer de inventarisatie wordt uitgevoerd.
    - Bij het instellen van het pad vermijdt u algemene paden zoals *c:*. * *. Dit leidt ertoe dat er te veel mappen worden gepasseerd.

8. Zorg ervoor dat u waar opgeeft voor het **uploaden van bestands inhoud**. Met deze instelling schakelt u het bijhouden van bestands inhoud in voor het opgegeven bestandspad.

### <a name="configure-file-tracking-on-linux"></a>Bestands tracering configureren in Linux

Gebruik de volgende stappen om bestands tracering op Linux-computers te configureren:

1. Selecteer **Instellingen bewerken** (het tandwiel symbool).

2. Selecteer op de pagina werkruimte configuratie de optie **Linux-bestanden** en selecteer vervolgens **+ toevoegen** om een nieuw bestand toe te voegen om bij te houden.

3. Voer op de pagina **Linux-bestand voor wijzigingen bijhouden toevoegen** de informatie in voor het bestand of de map die u wilt bijhouden en selecteer vervolgens **Opslaan**. In de volgende tabel worden de eigenschappen gedefinieerd die u kunt gebruiken voor de informatie.

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Ingeschakeld     | Waar als de instelling wordt toegepast en anders false.        |
    |Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden.        |
    |Groep     | Een groeps naam voor het logisch groeperen van bestanden.        |
    |Pad invoeren     | Het pad om het bestand te controleren, bijvoorbeeld **/etc/*. conf**.       |
    |Padtype     | Het type pad. Mogelijke waarden zijn bestand en Directory.        |
    |Recursie     | Waar als recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden, en ONWAAR anders onwaar.        |
    |Sudo gebruiken     | True om sudo te gebruiken bij het controleren op het item en ONWAAR, anders false.         |
    |Koppelingen     | Instelling die bepaalt hoe symbolische koppelingen moeten worden verwerkt bij het door lopen van directory's. Mogelijke waarden zijn:<br> Negeren: Hiermee worden symbolische koppelingen genegeerd en worden de bestanden/mappen waarnaar wordt verwezen, niet meegenomen.<br>Follow-volgt de symbolische koppelingen tijdens recursie en bevat ook de bestanden/mappen waarnaar wordt verwezen.<br>Beheren: volgt de symbolische koppelingen en staat de wijziging van geretourneerde inhoud toe.<br>**Opmerking:** De optie beheren wordt niet aanbevolen, omdat deze geen ondersteuning biedt voor het ophalen van bestands inhoud.    |
    |Bestands inhoud uploaden | True voor het uploaden van bestands inhoud op bijgehouden wijzigingen en ONWAAR, anders onwaar. |

4. Zorg ervoor dat u **waar** opgeeft voor het **uploaden van bestands inhoud**. Met deze instelling schakelt u het bijhouden van bestands inhoud in voor het opgegeven bestandspad.

   ![Linux-bestand toevoegen](./media/manage-change-tracking/add-linux-file.png)

## <a name="track-file-contents"></a>Bestands inhoud bijhouden

Met het bijhouden van bestands inhoud kunt u de inhoud van een bestand voor en na een bijgehouden wijziging bekijken. De functie slaat de inhoud van het bestand op in een [opslag account](../../storage/common/storage-account-overview.md) nadat elke wijziging is doorgevoerd. Hier volgen enkele regels om de inhoud van het bestand bij te houden:

* Een standaard opslag account met het Resource Manager-implementatie model is vereist voor het opslaan van bestands inhoud.
* Standaard accepteren opslagaccounts verbindingen van clients in elk netwerk. Als u uw opslag account hebt beveiligd om alleen bepaalde verkeer toe te staan, moet u de configuratie regels aanpassen zodat uw Automation-account er verbinding mee kan maken. Zie [Azure Storage firewalls en virtuele netwerken configureren](../../storage/common/storage-network-security.md).
* Gebruik geen Premium-en klassieke implementatie model opslag accounts. Zie [over Azure Storage-accounts](../../storage/common/storage-account-create.md).
* U kunt het opslag account koppelen aan slechts één Automation-account.
* Wijzigingen bijhouden en inventaris moeten zijn ingeschakeld in uw Automation-account.

### <a name="enable-tracking-for-file-content-changes"></a>Bijhouden van wijzigingen in bestands inhoud inschakelen

Voer de volgende stappen uit om het bijhouden van wijzigingen in de bestands inhoud in te scha kelen:

1. Selecteer **Instellingen bewerken** (het tandwiel symbool).

2. Selecteer **Bestands inhoud** en selecteer vervolgens **koppeling**. Met deze selectie wordt de pagina **inhouds locatie toevoegen voor wijzigingen bijhouden** geopend.

   ![Locatie van inhoud toevoegen](./media/manage-change-tracking/enable.png)

3. Selecteer het abonnement en het opslag account dat moet worden gebruikt voor het opslaan van de bestands inhoud.

5. Als u het bijhouden van bestands inhoud voor alle bestaande bijgehouden bestanden wilt inschakelen, selecteert u **aan** voor **uploaden van bestands inhoud voor alle instellingen**. U kunt deze instelling voor elk bestandspad later wijzigen.

   ![Opslag account instellen](./media/manage-change-tracking/storage-account.png)

6. In Wijzigingen bijhouden en inventaris worden Uri's van het opslag account en de Shared Access Signature (SAS) weer gegeven wanneer het bijhouden van bestands inhoud wordt ingeschakeld. De hand tekeningen verlopen na 365 dagen en u kunt ze opnieuw maken door **opnieuw genereren** te selecteren.

   ![Lijst met account sleutels](./media/manage-change-tracking/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>De inhoud van een bijgehouden bestand weer geven

Zodra Wijzigingen bijhouden en de inventaris een wijziging voor een bijgehouden bestand detecteert, kunt u de inhoud van het bestand bekijken in het deel venster Details wijzigen.  

![Wijzigingen weer geven](./media/manage-change-tracking/change-list.png)

1. Kies op de pagina wijzigingen **bijhouden** van uw Automation-account een bestand in de lijst met wijzigingen en selecteer vervolgens **Bestands inhoud weer geven wijzigingen** om de inhoud van het bestand weer te geven. In het deel venster Details wijzigen ziet u de standaard voor en na de bestands informatie voor elke eigenschap.

   ![Details wijzigen](./media/manage-change-tracking/change-details.png)

2. U bekijkt de bestands inhoud in een weer gave naast elkaar. U kunt **inline** selecteren om een inline weer gave van de wijzigingen weer te geven.

## <a name="track-registry-keys"></a>Register sleutels bijhouden

Gebruik de volgende stappen om het bijhouden van register sleutels op Windows-computers te configureren:

1. Op de pagina **Wijzigingen bijhouden** van uw Automation-account selecteert u **Instellingen bewerken** (het tandwiel symbool).

2. Selecteer op de pagina werkruimte configuratie de optie **Windows-REGI ster**.

3. Selecteer **+ toevoegen** om een nieuwe register sleutel aan de tracering toe te voegen.

4. Voer op de pagina **Windows-REGI ster toevoegen voor wijzigingen bijhouden** de gegevens in voor de sleutel die u wilt bijhouden en selecteer vervolgens **Opslaan**. In de volgende tabel worden de eigenschappen gedefinieerd die u kunt gebruiken voor de informatie. Wanneer u een registerpad opgeeft, moet dit een sleutel zijn en geen waarde.

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Ingeschakeld     | Waar als een instelling wordt toegepast en anders false.        |
    |Itemnaam     | Beschrijvende naam van de register sleutel die moet worden gevolgd.        |
    |Groep     | Groeps naam voor het logisch groeperen van register sleutels.        |
    |Windows-registersleutel   | Sleutel naam met pad, bijvoorbeeld `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` .      |

## <a name="search-logs-for-change-records"></a>Zoek logboeken voor wijzigings records

U kunt verschillende zoek acties uitvoeren op de Azure Monitor logboeken voor wijzigings records. Open de pagina wijzigingen bijhouden en klik op **log Analytics** om de pagina logboeken te openen. De volgende tabel bevat voor beelden van zoek opdrachten in Logboeken voor wijzigings records.

|Query’s uitvoeren  |Description  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Toont de meest recente inventaris records voor micro soft-services die zijn ingesteld op auto, maar die zijn gerapporteerd als gestopt. De resultaten zijn beperkt tot de meest recente record voor de opgegeven software naam en computer.    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|Geeft wijzigings records weer voor verwijderde software.|

## <a name="next-steps"></a>Volgende stappen

* Zie [limiet voor wijzigingen bijhouden en inventarisatie bereik](manage-scope-configurations.md)voor meer informatie over Scope configuraties.
* Als u logboeken wilt zoeken die zijn opgeslagen in Azure Monitor logboeken, raadpleegt u [Zoek opdrachten in Logboeken in azure monitor logboeken](../../azure-monitor/logs/log-query-overview.md).
* Zie [Wijzigingen bijhouden en inventaris verwijderen](remove-feature.md)als u klaar bent met implementaties.
* Zie [Vm's verwijderen uit wijzigingen bijhouden en inventaris](remove-vms-from-change-tracking.md)om uw vm's uit wijzigingen bijhouden en inventaris te verwijderen.
* Zie [problemen met wijzigingen bijhouden-en inventaris problemen oplossen](../troubleshoot/change-tracking.md)voor informatie over het oplossen van functie fouten.
