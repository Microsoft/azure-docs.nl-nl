---
title: 'Zelfstudie: Netwerkcommunicatie bewaken met behulp van Azure Portal'
description: Ontdek in deze zelfstudie hoe u de verbindingsmonitor-functie van Azure Network Watcher gebruikt om de netwerkcommunicatie tussen twee virtuele machines te bewaken.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: b734c4cdec022f2bf43ee7123b06331f67c10390
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570843"
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>Zelfstudie: Netwerkcommunicatie tussen twee virtuele machines bewaken met behulp van de Azure-portal

> [!NOTE]
> In deze zelfstudie wordt Verbindingsmonitor (klassiek) behandeld. Probeer de nieuwe en verbeterde [Verbindingsmonitor](connection-monitor-overview.md) voor verbeterde connectiviteitsbewaking

> [!IMPORTANT]
> Vanaf 1 juli 2021 kunt u geen nieuwe monitors voor verbinding toevoegen in de verbindings monitor (klassiek), maar bestaande verbindings monitors die zijn gemaakt vóór 1 juli 2021 blijven gebruiken. Als u de service onderbreking voor uw huidige workloads wilt minimaliseren, [migreert u van verbindings monitor (klassiek) naar de nieuwe verbindings monitor](migrate-to-connection-monitor-from-connection-monitor-classic.md) in azure Network Watcher vóór 29 februari 2024.

Geslaagde communicatie tussen een virtuele machine (VM) en een eindpunt, zoals een andere virtuele machine, kan zijn essentieel voor uw organisatie. Soms ontstaan configuratiewijzigingen die de communicatie kunnen verbreken. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Twee virtuele machines maken
> * Communicatie tussen virtuele machines bewaken met de verbindingsmonitor-functie van Network Watcher
> * Waarschuwingen genereren in metrische gegevens van de verbindingsmonitor
> * Een communicatieprobleem tussen twee virtuele machines vaststellen en meer informatie over hoe u het kunt oplossen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.



## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-vms"></a>Virtuele machines maken

Maak twee virtuele machines.

### <a name="create-the-first-vm"></a>De eerste VM maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Compute** en selecteer vervolgens een besturingssysteem. In deze zelfstudie wordt **Windows Server 2016 Datacenter** gebruikt.
3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **OK**:

    |Instelling|Waarde|
    |---|---|
    |Naam|myVm1|
    |Gebruikersnaam| Voer een gebruikersnaam naar keuze in.|
    |Wachtwoord| Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Abonnement| Selecteer uw abonnement.|
    |Resourcegroep| Selecteer **Nieuwe maken** en voer **myResourceGroup** in.|
    |Locatie| Selecteer **VS - oost**|

4. Selecteer een grootte voor de virtuele machine en selecteer **Selecteren**.
5. Selecteer onder **Instellingen** de optie **Extensies**. Selecteer **Extensie toevoegen** en selecteer **Network Watcher Agent voor Windows**, zoals wordt weergegeven in de volgende afbeelding:

    ![Extensie Network Watcher Agent](./media/connection-monitor/nw-agent-extension.png)

6. Selecteer onder **Network Watcher Agent voor Windows** de optie **Maken**, selecteer onder **Extensie installeren** de optie **OK** en selecteer vervolgens onder **Extensies** de optie **OK**.
7. Accepteer de standaardwaarden voor de overige **Instellingen** en selecteer **OK**.
8. Selecteer onder **Maken** bij **Samenvatting** de optie **Maken** om de implementatie van de virtuele machine te starten.

### <a name="create-the-second-vm"></a>De tweede VM maken

Voer de stappen in [De eerste virtuele machine maken](#create-the-first-vm) opnieuw uit met de volgende wijzigingen:

|Stap|Instelling|Waarde|
|---|---|---|
| 1 | Selecteer een versie van **Ubuntu Server** |                                                                         |
| 3 | Name                                  | myVm2                                                                   |
| 3 | Verificatietype                   | Plak uw openbare SSH-sleutel of selecteer **Wachtwoord** en voer een wachtwoord in. |
| 3 | Resourcegroep                        | Selecteer **Bestaande gebruiken** en selecteer **myResourceGroup**.                 |
| 6 | Uitbreidingen                            | **Network Watcher Agent voor Linux**                                             |

Het implementeren van de VM duurt een paar minuten. Wacht tot de VM is geïmplementeerd voordat u doorgaat met de resterende stappen.

## <a name="create-a-connection-monitor"></a>Een verbindingsmonitor maken

Maak een verbindingsmonitor om communicatie via TCP-poort 22 van *myVm1* naar *myVm2* te bewaken.

1. Selecteer aan de linkerkant van de portal de optie **Alle services**.
2. Begin *network watcher* te typen in het vak **Filter**. Selecteer **Network Watcher** in de zoekresultaten.
3. Selecteer onder **CONTROLEREN** de optie **Verbindingsmonitor**.
4. Selecteer **+ Toevoegen**.
5. Typ of selecteer de gegevens voor de verbinding die u wilt bewaken en selecteer vervolgens **Toevoegen**. In het voorbeeld dat wordt weergegeven in de volgende afbeelding, wordt de verbinding bewaakt die afkomstig is vanaf de VM *myVm1* naar de VM *myVm2* via poort 22:

    | Instelling                  | Waarde               |
    | ---------                | ---------           |
    | Naam                     | myVm1-myVm2(22)     |
    | Bron                   |                     |
    | Virtuele machine          | myVm1               |
    | Doel              |                     |
    | Een virtuele machine selecteren |                     |
    | Virtuele machine          | myVm2               |
    | Poort                     | 22                  |

    ![Verbindingsmonitor toevoegen](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>Een verbindingsmonitor weergeven

1. Voltooi de stappen 1-3 in [Een verbindingsmonitor maken](#create-a-connection-monitor) om de bewaking van de verbinding weer te geven. U ziet een lijst met bestaande verbindingsmonitors, zoals in de volgende afbeelding:

    ![Verbindingsmonitors](./media/connection-monitor/connection-monitors.png)

2. Selecteer de monitor met de naam **myVm1-myVm2(22)**, zoals in de vorige afbeelding, om details te bekijken voor de monitor, zoals in de volgende afbeelding:

    ![Details van de monitor](./media/connection-monitor/vm-monitor.png)

    Let op de volgende informatie:

    | Item                     | Waarde                      | Details                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Status                   | Bereikbaar                  | Laat u weten of het eindpunt al dan niet bereikbaar is.|
    | GEM. RETOUR          | Laat u de retourtijd weten voor het maken van de verbinding, in milliseconden. De verbindingsmonitor test de verbinding elke 60 seconden, zodat u de latentie gedurende een bepaalde periode kunt bewaken.                                         |
    | Hops                     | Verbindingsmonitor laat u de hops tussen de twee eindpunten weten. In dit voorbeeld is de verbinding tussen twee virtuele machines in hetzelfde virtuele netwerk. Er is dus maar één hop, naar het IP-adres 10.0.0.5. Als een bestaand systeem of aangepaste routes verkeer routeren tussen de virtuele machines via een VPN-gateway, of virtueel netwerkapparaat, worden bijvoorbeeld aanvullende hops weergegeven.                                                                                                                         |
    | STATUS                   | De groene selectievakjes voor elk eindpunt laten u weten dat elk eindpunt in orde is.    ||

## <a name="generate-alerts"></a>Waarschuwingen genereren

Waarschuwingen worden gemaakt door regels voor waarschuwingen in Azure Monitor en kunnen automatisch opgeslagen query's uitvoeren of aangepaste zoekopdrachten in logboeken met regelmatige tussenpozen. Via een gegenereerde waarschuwing kunnen automatisch een of meer acties worden uitgevoerd, zoals het op de hoogte stellen van iemand of het starten van een ander proces. Als u een waarschuwingsregel instelt, wordt de lijst met beschikbare metrische gegevens die u kunt gebruiken om waarschuwingen te genereren, bepaalt met de doelresource.

1. Selecteer in de Azure-portal de service **Monitor** en selecteer vervolgens **Waarschuwingen** > **Nieuwe waarschuwingsregel**.
2. Klik op **Doel selecteren** en selecteer vervolgens de resources die u als doel wilt instellen. Selecteer het **Abonnement** en stel **Resourcetype** in om te filteren op de verbindingsmonitor die u wilt gebruiken.

    ![waarschuwingsscherm met een geselecteerd doel](./media/connection-monitor/set-alert-rule.png)
1. Nadat u een doelresource hebt geselecteerd, selecteert u **Criteria toevoegen**. Network Watcher heeft [metrische gegevens over welke waarschuwingen u kunt maken](../azure-monitor/alerts/alerts-metric-near-real-time.md#metrics-and-dimensions-supported). Stel **Beschikbare signalen** in op de metrische gegevens ProbesFailedPercent en AverageRoundtripMs:

    ![waarschuwingspagina met geselecteerde signalen](./media/connection-monitor/set-alert-signals.png)
1. Vul de waarschuwingsdetails in, zoals de naam, beschrijving en ernst van de waarschuwingsregel. U kunt ook een actiegroep toevoegen aan de waarschuwing om het waarschuwingsantwoord te automatiseren en aan te passen.

## <a name="view-a-problem"></a>Een probleem weergeven

Standaard staat Azure communicatie toe over alle poorten tussen virtuele machines in hetzelfde virtuele netwerk. U of iemand in uw organisatie kan mogelijk de standaardregels van Azure overschrijven, waardoor per ongeluk een communicatiefout ontstaat. Voltooi de volgende stappen om een communicatieprobleem te veroorzaken en geef dan de verbindingsmonitor nogmaals weer:

1. Voer *myResourceGroup* in het zoekvak bovenin de portal in. Selecteer de resourcegroep **myResourceGroup** wanneer deze wordt weergegeven in zoekresultaten.
2. Selecteer de netwerkbeveiligingsgroep **myVm2-nsg**.
3. Selecteer **Inkomende beveiligingsregels** en selecteer vervolgens **Toevoegen**, zoals in de volgende afbeelding:

    ![Inkomende beveiligingsregels](./media/connection-monitor/inbound-security-rules.png)

4. De standaardregel die communicatie tussen alle VM's in een virtueel netwerk mogelijk maakt, is de regel met de naam **AllowVnetInBound**. Maak een regel met een hogere prioriteit (lager nummer) dan de regel **AllowVnetInBound** die binnenkomende communicatie via poort 22 weigert. Selecteer de volgende informatie, of voer deze in, accepteer voor het overige de standaardwaarden en selecteer dan **Toevoegen**:

    | Instelling                 | Waarde          |
    | ---                     | ---            |
    | Poortbereiken van doel | 22             |
    | Actie                  | Weigeren           |
    | Prioriteit                | 100            |
    | Naam                    | DenySshInbound |

5. Aangezien verbindingsmonitor test met intervallen van 60 seconden, wacht u een paar minuten. Selecteer vervolgens aan de linkerkant van de portal **Network Watcher**, dan **Verbindingsmonitor** en selecteer vervolgens de monitor **myVm1-myVm2(22)** opnieuw. De resultaten zijn nu anders, zoals u ziet in de volgende afbeelding:

    ![Fouten in monitordetails](./media/connection-monitor/vm-monitor-fault.png)

    U ziet dat er een rood uitroepteken staat in de statuskolom voor de netwerkinterface **myvm2529**.

6. Voor meer informatie over waarom de status is gewijzigd, selecteert u 10.0.0.5, in de vorige afbeelding. De verbindingsmonitor meldt de reden voor de communicatiefout: *Verkeer geblokkeerd vanwege de volgende regel voor netwerkbeveiligingsgroep: UserRule_DenySshInbound*.

    Als u niet wist dat iemand de beveiligingsregel die u hebt gemaakt in stap 4 had geïmplementeerd, zou u via de verbindingsmonitor te weten komen dat het communicatieprobleem wordt veroorzaakt door de regel. U kunt vervolgens de regel wijzigen, overschrijven of verwijderen om de communicatie tussen de virtuele machines te herstellen.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep en alle gerelateerde resources die deze bevat verwijderen wanneer u deze niet meer nodig hebt:

1. Voer *myResourceGroup* in het vak **Zoeken** bovenaan de portal in. Wanneer u **myResourceGroup** ziet in de zoekresultaten, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een verbinding tussen twee virtuele machines bewaakt. U hebt geleerd dat een regel voor de netwerkbeveiligingsgroep communicatie met een virtuele machine heeft verhinderd. Zie [antwoordtypen](network-watcher-connectivity-overview.md#response)voor meer informatie over alle andere verschillende antwoorden die verbindingsmonitor kan retourneren. U kunt ook een verbinding tussen een virtuele machine, een FQDN-naam, een uniform resource-id of een IP-adres bewaken.

Op een bepaald moment merkt u dat resources in een virtueel netwerk niet kunnen communiceren met resources in andere netwerken die zijn verbonden via een virtuele Azure-netwerkgateway. Ga naar de volgende zelfstudie voor informatie over een probleem met een virtuele netwerkgateway.

> [!div class="nextstepaction"]
> [Communicatieproblemen tussen netwerken vaststellen](diagnose-communication-problem-between-networks.md)
