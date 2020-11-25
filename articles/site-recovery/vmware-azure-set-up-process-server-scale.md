---
title: Een scale-out proces server instellen tijdens het herstel na nood gevallen van virtuele VMware-machines en fysieke servers met Azure Site Recovery | Microsoft Docs '
description: In dit artikel wordt beschreven hoe u een scale-out proces server instelt tijdens het herstel na nood gevallen van virtuele VMware-machines en fysieke servers.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 40f912122e6ffb9cccbd32a747f6f0d46fd6c330
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019129"
---
# <a name="scale-with-additional-process-servers"></a>Schalen met extra proces servers

Wanneer u virtuele VMware-machines of fysieke servers naar Azure repliceert met behulp van [site Recovery](site-recovery-overview.md), wordt er standaard een proces server geïnstalleerd op de configuratie server machine en wordt deze gebruikt voor het coördineren van de gegevens overdracht tussen site Recovery en uw on-premises infra structuur. Als u de capaciteit wilt verg Roten en de implementatie van uw replicatie wilt uitbreiden, kunt u extra zelfstandige proces servers toevoegen. In dit artikel wordt beschreven hoe u een scale-out proces server kunt instellen.

## <a name="before-you-start"></a>Voordat u begint

### <a name="capacity-planning"></a>Capaciteitsplanning

Zorg ervoor dat u de [capaciteits planning](site-recovery-plan-capacity-vmware.md) voor VMware-replicatie hebt uitgevoerd. Zo kunt u bepalen hoe en wanneer u extra proces servers moet implementeren.

Vanuit 9,24-versie wordt hulp toegevoegd tijdens het selecteren van de proces server voor nieuwe replicaties. De proces server wordt als in orde, waarschuwing en kritiek gemarkeerd op basis van bepaalde criteria. Bekijk de waarschuwingen van de [proces server](vmware-physical-azure-monitor-process-server.md#process-server-alerts)voor meer informatie over verschillende scenario's die de status van de proces server kunnen beïnvloeden.

> [!NOTE]
> Het gebruik van een gekloond proces Server onderdeel wordt niet ondersteund. Volg de stappen in dit artikel voor elke PS-uitschalen.

### <a name="sizing-requirements"></a>Grootte vereisten 

Controleer de grootte vereisten die in de tabel worden samenvatten. Als u in het algemeen uw implementatie moet schalen naar meer dan 200 bron machines of als u een totaal dagelijks verloop van meer dan 2 TB hebt, hebt u extra proces servers nodig om het verkeers volume af te handelen.

| **Aanvullende proces server** | **Schijf grootte van cache** | **Frequentie van gegevenswijzigingen** | **Beveiligde machines** |
| --- | --- | --- | --- |
|4 Vcpu's (2 sockets * 2 kernen \@ 2,5 GHz), 8 GB geheugen |300 GB |250 GB of minder |Repliceren 85 of minder machines. |
|8 Vcpu's (2 sockets * 4 kernen \@ 2,5 GHz), 12 GB geheugen |600 GB |250 GB tot 1 TB |Repliceren tussen 85-150 machines. |
|12 Vcpu's (2 sockets * 6 kernen \@ 2,5 GHz) 24 GB geheugen |1 TB |1 TB tot 2 TB |Repliceren tussen 150-225 machines. |

Waarbij elke beveiligde bron machine is geconfigureerd met 3 schijven van 100 GB.

### <a name="prerequisites"></a>Vereisten

De vereisten voor de extra proces server worden in de volgende tabel samenvatten.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Installatie bestand downloaden

Down load het installatie bestand voor de proces server als volgt:

1. Meld u aan bij de Azure Portal en blader naar uw Recovery Services kluis.
2. Open **site Recovery Infrastructure**  >  -configuratie servers voor **VMware en fysieke machines**  >  **Configuration Servers** (onder voor VMware & fysieke machines).
3. Selecteer de configuratie server om in te zoomen op de server details. Klik vervolgens op **+ proces server**.
4. In **proces server toevoegen**  >   **kiest u waar u uw proces server wilt implementeren**, selecteert u **een scale-out proces server on-premises implementeren**.

   ![Pagina servers toevoegen](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Klik op **down load de Microsoft Azure site Recovery Unified Setup**. Hiermee downloadt u de meest recente versie van het installatie bestand.

   > [!WARNING]
   > De installatie versie van de proces server moet gelijk zijn aan of lager zijn dan de versie van de configuratie server die u gebruikt. Een eenvoudige manier om ervoor te zorgen dat versie compatibiliteit hetzelfde installatie programma gebruikt, dat u het meest recent hebt gebruikt om de configuratie server te installeren of bij te werken.

## <a name="install-from-the-ui"></a>Installeren via de gebruikers interface

Installeer dit als volgt. Nadat u de server hebt ingesteld, migreert u de bron machines om deze te gebruiken.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Installeren vanaf de opdracht regel

Installeer door de volgende opdracht uit te voeren:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMware/NonVMware>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Waarbij de opdracht regel parameters als volgt zijn:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Bijvoorbeeld:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMware" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Een bestand met proxy-instellingen maken

Als u een proxy moet instellen, neemt de para meter ProxySettingsFilePath een bestand als invoer. U kunt het bestand als volgt maken en dit door geven als para meter invoer ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het beheren van de instellingen van de [proces server](vmware-azure-manage-process-server.md)
