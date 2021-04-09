---
title: IBM DB2 pureScale implementeren op Azure
description: Meer informatie over het implementeren van een voorbeeld architectuur voor het migreren van een onderneming vanuit de IBM DB2-omgeving die wordt uitgevoerd op een z/O'S naar IBM DB2 pureScale op Azure.
author: njray
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 33ff6174d7e5107076dda177731c9daec7e57266
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104956142"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale implementeren op Azure

In dit artikel wordt beschreven hoe u een [voorbeeld architectuur](ibm-db2-purescale-azure.md) implementeert die een Enter prise-klant onlangs heeft gebruikt voor het migreren van de IBM DB2-omgeving die wordt uitgevoerd op een z/O'S naar IBM DB2 PureScale op Azure.

Zie de installatie scripts in de [DB2onAzure](https://aka.ms/db2onazure) -opslag plaats op github om de stappen te volgen die worden gebruikt voor de migratie. Deze scripts zijn gebaseerd op de architectuur voor een typische OLTP-werk belasting (online Trans Action processing) van gemiddelde grootte.

## <a name="get-started"></a>Aan de slag

Als u deze architectuur wilt implementeren, downloadt en voert u het script deploy.sh uit in de [DB2onAzure](https://aka.ms/db2onazure) -opslag plaats op github.

De opslag plaats bevat ook scripts voor het instellen van een Grafana-dash board. U kunt het dash board gebruiken om query's uit te Prometheus, het open source-bewakings-en waarschuwings systeem dat is opgenomen in DB2.

> [!NOTE]
> Het deploy.sh-script op de client maakt privé-SSH-sleutels en geeft deze door aan de implementatie sjabloon via HTTPS. Voor een betere beveiliging kunt u het beste [Azure Key Vault](../../../../key-vault/general/overview.md) gebruiken om geheimen, sleutels en wacht woorden op te slaan.

## <a name="how-the-deployment-script-works"></a>Hoe het implementatie script werkt

Met het script deploy.sh maakt en configureert u de Azure-resources voor deze architectuur. Het script vraagt u om het Azure-abonnement en de virtuele machines die worden gebruikt in de doel omgeving en voert de volgende bewerkingen uit:

-   Hiermee stelt u de resource groep, het virtuele netwerk en de subnetten op Azure in voor de installatie.

-   Hiermee stelt u de netwerk beveiligings groepen en SSH in voor de omgeving.

-   Hiermee stelt u meerdere Nic's in op zowel de gedeelde opslag als de pureScale van de DB2-virtuele machine.

-   Hiermee maakt u de virtuele machines van de gedeelde opslag. Als u Opslagruimten Direct of een andere opslag oplossing gebruikt, raadpleegt u [overzicht van opslagruimten direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview).

-   Hiermee maakt u de virtuele machine JumpBox.

-   Hiermee maakt u de DB2 pureScale-virtuele machines.

-   Hiermee maakt u de virtuele Witness-machine die door de DB2-pureScale wordt gepingd. Sla dit deel van de implementatie over als uw versie van de Db2-pureScale geen Witness vereist.

-   Hiermee maakt u een virtuele Windows-machine die moet worden gebruikt voor het testen, maar worden er geen items geïnstalleerd.

Vervolgens stelt de implementatie scripts een virtuele iSCSI-Storage Area Network (vSAN) in voor gedeelde opslag in Azure. In dit voor beeld maakt iSCSI verbinding met het gedeelde opslag cluster. In de oorspronkelijke klant oplossing is GlusterFS gebruikt. IBM biedt echter geen ondersteuning meer voor deze methode. Als u de ondersteuning van IBM wilt behouden, moet u een ondersteund bestands systeem dat compatibel is met iSCSI gebruiken. Micro soft biedt Opslagruimten Direct (S2D) als optie.

Deze oplossing biedt u ook de mogelijkheid om de iSCSI-doelen als één Windows-knoop punt te installeren. iSCSI biedt een gedeelde Block Storage-interface via TCP/IP waarmee de installatie procedure van de DB2-pureScale een apparaatinterface kan gebruiken om verbinding te maken met de gedeelde opslag.

De implementatie scripts voeren de volgende algemene stappen uit:

1.  Stel een gedeeld opslag cluster in op Azure. Deze stap omvat ten minste twee Linux-knoop punten.

2.  Stel een iSCSI direct-interface in op Linux-doel servers voor het gedeelde opslag cluster.

3.  De iSCSI-initiator instellen op de virtuele Linux-machines. De initiator heeft toegang tot het gedeelde opslag cluster met behulp van een iSCSI-doel. Zie [een iSCSI-doel en initiator configureren in Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) in de RootUsers-documentatie voor meer informatie over de installatie.

4.  Installeer de gedeelde opslaglaag voor de iSCSI-interface.

Nadat de scripts het iSCSI-apparaat hebben gemaakt, is de laatste stap om DB2 pureScale te installeren. Als onderdeel van de installatie van de DB2 pureScale wordt [IBM spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (voorheen bekend als GPFS) gecompileerd en geïnstalleerd op het glusterfs-cluster. Met dit geclusterde bestands systeem kan DB2 pureScale gegevens delen tussen de virtuele machines waarop de DB2 pureScale-engine wordt uitgevoerd. Zie de documentatie over [IBM spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) op de IBM-website voor meer informatie.

## <a name="db2-purescale-response-file"></a>PureScale-respons bestand van DB2

De GitHub-opslag plaats bevat DB2server. RSP, een antwoord bestand (. RSP) waarmee u een geautomatiseerd script kunt genereren voor de installatie van de DB2-pureScale. De volgende tabel bevat de opties voor de DB2-pureScale die het antwoord bestand gebruikt voor Setup. U kunt het antwoord bestand zo nodig aanpassen voor uw omgeving.

> [!NOTE]
> Een voor beeld van een antwoord bestand, DB2server. RSP, is opgenomen in de [DB2onAzure](https://aka.ms/db2onazure) -opslag plaats op github. Als u dit bestand gebruikt, moet u het bewerken voordat het in uw omgeving kan worden gebruikt.

| Scherm naam               | Veld                                        | Waarde                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Welkom                   |                                              | Nieuwe installatie                                                                                           |
| Kies een product          |                                              | DB2-versie 11.1.3.3. Server edities met DB2 pureScale                                              |
| Configuratie             | Directory                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Het installatie type selecteren                 | Standaard                                                                                               |
|                           | Ik ga akkoord met de IBM-voor waarden                     | Ingeschakeld                                                                                               |
| Eigenaar van exemplaar            | Bestaande gebruiker voor exemplaar, gebruikers naam        | DB2sdin1                                                                                              |
| Gebruiker met omheining               | Bestaande gebruiker, gebruikers naam                     | DB2sdfe1                                                                                              |
| Bestands systeem van cluster       | Pad naar gedeelde schijf partitie apparaat            | /dev/dm-2                                                                                             |
|                           | Koppel punt                                  | /DB2sd \_ 1804a                                                                                         |
|                           | Gedeelde schijf voor gegevens                         | /dev/dm-1                                                                                             |
|                           | Koppel punt (gegevens)                           | /DB2fs/datafs1                                                                                        |
|                           | Gedeelde schijf voor logboek                          | /dev/dm-0                                                                                             |
|                           | Koppel punt (logboek)                            | /DB2fs/logfs1                                                                                         |
|                           | Tiebreaker van de DB2-Cluster Services. Pad naar apparaat | /dev/dm-3                                                                                             |
| Hostlijst                 | D1 [eth1], D2 [eth1], CF1 [eth1], CF2 [eth1] |                                                                                                       |
|                           | Voor keur primair CF                         | cf1                                                                                                   |
|                           | Voor Keurs-secundair CF                       | cf2                                                                                                   |
| Antwoord bestand en samen vatting | eerste optie                                 | De versie van de DB2-Server installeren met de IBM DB2 pureScale-functie en mijn instellingen opslaan in een reactie bestand |
|                           | Naam van antwoord bestand                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Opmerkingen over deze implementatie

- De waarden voor/dev-dm0,/dev-DM1,/dev-dm2 en/dev-dm3 kunnen worden gewijzigd na het opnieuw opstarten van de virtuele machine waarop de installatie plaatsvindt (D0 in het geautomatiseerde script). U kunt de juiste waarden vinden door de volgende opdracht uit te voeren voordat het antwoord bestand wordt voltooid op de server waarop de installatie wordt uitgevoerd:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- De installatie scripts gebruiken aliassen voor de iSCSI-schijven zodat de werkelijke namen eenvoudig kunnen worden gevonden.

- Wanneer het installatie script wordt uitgevoerd op D0, zijn **de \* /dev/DM-** -waarden mogelijk anders op D1, cf0 en CF1. Het verschil in waarden heeft geen invloed op de instellingen van de DB2-pureScale.

## <a name="troubleshooting-and-known-issues"></a>Probleemoplossing en bekende problemen

De GitHub-opslag plaats bevat een Knowledge Base die de auteurs onderhouden. Hier vindt u mogelijke problemen die kunnen optreden en oplossingen die u kunt proberen. Bekende problemen kunnen bijvoorbeeld optreden wanneer:

-   U probeert het IP-adres van de gateway te bereiken.

-   U compileert de algemene open bare licentie (GPL).

-   De beveiligings-Handshake tussen hosts mislukt.

-   Het DB2-installatie programma detecteert een bestaand bestands systeem.

-   U installeert de IBM spectrum-schaal hand matig.

-   U installeert DB2 pureScale wanneer er al een IBM spectrum-schaal is gemaakt.

-   U verwijdert de pureScale van de DB2-en IBM-spectrum.

Zie het kb.md-bestand in de [DB2onAzure](https://aka.ms/DB2onAzure) -opslag plaats voor meer informatie over deze en andere bekende problemen.

## <a name="next-steps"></a>Volgende stappen

-   [De vereiste gebruikers voor een installatie van een DB2 pureScale-functie maken](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [De opdracht DB2icrt-instantie maken](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Gegevens oplossing voor DB2 pureScale-clusters](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Azure Virtual Data Center lift-en Shift-gids](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
