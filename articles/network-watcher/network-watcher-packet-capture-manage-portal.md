---
title: Pakket opnames beheren-Azure Portal
titleSuffix: Azure Network Watcher
description: Meer informatie over het beheren van de functie voor het vastleggen van pakketten van Network Watcher met behulp van de Azure Portal.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: d4112bd1d632ec21e2d8cb565027277eeb85452d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018256"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Pakket opnames beheren met Azure Network Watcher met behulp van de portal

Met Network Watcher-pakket opname kunt u opname sessies maken om verkeer van en naar een virtuele machine bij te houden. Er worden filters voor de opname sessie gegeven om ervoor te zorgen dat u alleen het gewenste verkeer vastlegt. Met pakket opname kunt u netwerk afwijkingen, zowel reactief als proactief, vaststellen. Andere gebruiken zijn onder andere het verzamelen van netwerk statistieken, het verkrijgen van informatie over inbreuken op het netwerk, het opsporen van fouten in client-server communicatie en nog veel meer. Als u pakket opnames op afstand wilt activeren, vereenvoudigt u de belasting van het hand matig uitvoeren van een pakket opname op een gewenste virtuele machine, waardoor kost bare tijd wordt bespaard.

In dit artikel leert u hoe u een pakket opname kunt starten, stoppen, downloaden en verwijderen. 

## <a name="before-you-begin"></a>Voordat u begint

Voor pakket opname zijn de volgende uitgaande TCP-verbindingen vereist:
- naar het gekozen opslag account via poort 443
- 169.254.169.254 via poort 80
- 168.63.129.16 via poort 8037

> [!NOTE]
> De poorten die in de bovenstaande laatste twee gevallen hierboven worden genoemd, zijn gebruikelijk voor alle Network Watcher-functies die betrekking hebben op de Network Watcher extensie en kunnen eventueel worden gewijzigd.


Als er een netwerk beveiligings groep is gekoppeld aan de netwerk interface of het subnet waarin de netwerk interface zich bevindt, moet u ervoor zorgen dat er regels bestaan die de vorige poorten toestaan. Op dezelfde manier kan het toevoegen van door de gebruiker gedefinieerde verkeers routes naar uw netwerk verhinderen dat de verbinding met de bovenstaande Ip's en poorten wordt verbroken. Controleer of ze bereikbaar zijn. 

## <a name="start-a-packet-capture"></a>Een pakket opname starten

1. Ga in uw browser naar de [Azure Portal](https://portal.azure.com) en selecteer **alle services**, en selecteer vervolgens **Network Watcher** in het **gedeelte netwerken**.
2. Selecteer **pakket opname** onder **Diagnostische hulpprogram Ma's** voor het netwerk. Bestaande pakket opnames worden weer gegeven, ongeacht hun status.
3. Selecteer **toevoegen** om een pakket opname te maken. U kunt waarden selecteren voor de volgende eigenschappen:
   - **Abonnement**: het abonnement van de virtuele machine waarvoor u de pakket opname wilt maken, is in.
   - **Resource groep**: de resource groep van de virtuele machine.
   - **Doel-virtuele machine**: de virtuele machine waarvoor u de pakket opname wilt maken.
   - **Naam van pakket opname**: een naam voor het vastleggen van pakketten.
   - **Opslag account of-bestand**: Selecteer een **opslag account**, een **bestand** of beide. Als u **bestand** selecteert, wordt de vastleg ging geschreven naar een pad binnen de virtuele machine.
   - **Lokaal** bestandspad: het lokale pad op de virtuele machine waarop de pakket opname wordt opgeslagen (alleen geldig wanneer het *bestand* is geselecteerd). Het pad moet een geldig pad zijn. Als u een virtuele Linux-machine gebruikt, moet het pad beginnen met */var/captures*.
   - **Opslag accounts**: Selecteer een bestaand opslag account als u een *opslag account* hebt geselecteerd. Deze optie is alleen beschikbaar als u **opslag** hebt geselecteerd.
   
     > [!NOTE]
     > Premium Storage-accounts worden momenteel niet ondersteund voor het opslaan van pakket opnames.

   - **Maximum aantal bytes per pakket**: het aantal bytes van elk pakket dat is vastgelegd. Als dit veld leeg blijft, worden alle bytes vastgelegd.
   - **Maximum aantal bytes per sessie**: het aantal bytes dat is vastgelegd. Zodra de waarde is bereikt, stopt het vastleggen van pakketten.
   - **Tijds limiet (seconden)**: de tijds limiet voordat het vastleggen van pakketten wordt gestopt. De standaard waarde is 18.000 seconden.
   - Filteren (optioneel). Selecteer **+ filter toevoegen**
     - **Protocol**: het protocol dat moet worden gefilterd voor de pakket opname. De beschik bare waarden zijn TCP, UDP en any.
     - **Lokaal IP-adres**: Hiermee wordt de pakket opname gefilterd op pakketten waarbij het lokale IP-adres overeenkomt met deze waarde.
     - **Lokale poort**: Hiermee wordt de pakket opname gefilterd op pakketten waarbij de lokale poort overeenkomt met deze waarde.
     - **Extern IP-adres**: Hiermee wordt de pakket opname gefilterd op pakketten waarbij het externe IP-adres overeenkomt met deze waarde.
     - **Externe poort**: Hiermee wordt de pakket opname gefilterd op pakketten waarbij de externe poort overeenkomt met deze waarde.
    
     > [!NOTE]
     > Poort-en IP-adres waarden kunnen bestaan uit één waarde, waarden bereik of een bereik, zoals 80-1024, voor poort. U kunt zoveel filters definiëren als u nodig hebt.

4. Selecteer **OK**.

Nadat de ingestelde tijds limiet voor het vastleggen van pakketten is verlopen, wordt de pakket opname gestopt en kan deze worden gecontroleerd. U kunt ook een pakket opname sessie hand matig stoppen.

> [!NOTE]
> De portal wordt automatisch:
>  * Hiermee maakt u een Network Watcher in dezelfde regio als de regio waarin de geselecteerde virtuele machine zich bevindt, als de regio nog geen netwerk-Watcher heeft.
>  * Voegt de *AzureNetworkWatcherExtension* [Linux](../virtual-machines/extensions/network-watcher-linux.md) -of [Windows](../virtual-machines/extensions/network-watcher-windows.md) -extensie voor virtuele machines toe aan de virtuele machine, als deze nog niet is geïnstalleerd.

## <a name="delete-a-packet-capture"></a>Een pakket opname verwijderen

1. Selecteer in de weer gave pakket opname de optie **...** aan de rechter kant van de pakket opname of klik met de rechter muisknop op een bestaande pakket opname en selecteer **verwijderen**.
2. U wordt gevraagd om te bevestigen dat u de pakket opname wilt verwijderen. Selecteer **Ja**.

> [!NOTE]
> Als u een pakket opname verwijdert, wordt het vastleg bestand niet verwijderd uit het opslag account of de virtuele machine.

## <a name="stop-a-packet-capture"></a>Een pakket opname stoppen

Selecteer in de weer gave pakket opname de optie **...** aan de rechter kant van de pakket opname of klik met de rechter muisknop op een bestaande pakket opname en selecteer **stoppen**.

## <a name="download-a-packet-capture"></a>Pakket opname downloaden

Zodra de pakket opname sessie is voltooid, wordt het vastleg bestand geüpload naar Blob-opslag of naar een lokaal bestand op de virtuele machine. De opslag locatie van de pakket opname wordt gedefinieerd tijdens het maken van de pakket opname. Een handig hulp middel om toegang te krijgen tot opname bestanden die zijn opgeslagen in een opslag account, is Microsoft Azure Storage Explorer, dat u kunt [downloaden](https://storageexplorer.com/).

Als er een opslag account is opgegeven, worden pakket opname bestanden opgeslagen in een opslag account op de volgende locatie:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Als u tijdens het maken van de opname **het bestand hebt** geselecteerd, kunt u het bestand weer geven of downloaden vanuit het pad dat u hebt geconfigureerd op de virtuele machine.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het automatiseren van pakket opnames met waarschuwingen voor virtuele machines [een waarschuwing een gegenereerde pakket opname maken](network-watcher-alert-triggered-packet-capture.md).
- Zie [een probleem met het netwerk verkeer van een virtuele machine vaststellen](diagnose-vm-network-traffic-filtering-problem.md)als u wilt weten of het verkeer is toegestaan in of uit een virtuele machine.