---
title: Een Lab instellen voor ArcMap\ArcGIS Desktop met Azure Lab Services | Microsoft Docs
description: Meer informatie over het instellen van een Lab voor klassen met behulp van ArcGIS.
author: nicolela
ms.topic: article
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: 530597a72b19afa1e80b5c7640b105d86479b1c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740100"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>Een Lab instellen voor ArcMap\ArcGIS Desktop

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) is een type geografisch informatie systeem (GIS).  ArcGIS wordt gebruikt voor het make\analyze van kaarten en het werken met geografische gegevens die worden verschaft door het [Environmental Institute](https://www.esri.com/en-us/home) (ESRI).  Hoewel ArcGIS Desktop meerdere toepassingen bevat, laat dit artikel zien hoe u Labs kunt instellen voor het gebruik van ArcMap.  [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) wordt gebruikt om 2D-kaarten te maken, te bewerken en te analyseren.

## <a name="lab-configuration"></a>Lab-configuratie

U hebt een Azure-abonnement en een Lab-account nodig om te beginnen met het instellen van een Lab voor het gebruik van ArcMap.  Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

Nadat u een Azure-abonnement hebt ontvangen, kunt u een nieuw Lab-account maken in Azure Lab Services.  Zie [een Lab-account instellen](tutorial-setup-lab-account.md)voor meer informatie over het maken van een nieuw Lab-account.  U kunt ook een bestaand Lab-account gebruiken.

### <a name="lab-account-settings"></a>Instellingen van Lab-account

Schakel de instellingen voor uw Lab-account in, zoals wordt beschreven in de volgende tabel.  Zie voor meer informatie over het inschakelen van installatie kopieën van Azure Marketplace [de Azure Marketplace-installatie kopieën opgeven die beschikbaar zijn voor Lab-makers](./specify-marketplace-images.md).

| Account instelling Lab | Instructies |
| ------------------- | ------------ |
|Marketplace-installatiekopie| Schakel de Windows 10 Pro-of Windows 10 Pro N-installatie kopie in voor gebruik binnen uw Lab-account.|

### <a name="licensing-server"></a>Licentie server

Eén type licentie voor ArcGIS Desktop-aanbiedingen is [gelijktijdig gebruik van licenties](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm).  Hiervoor moet u ArcGIS-licentie beheer installeren op uw licentie server.  De licentie beheerder houdt het aantal exemplaren van de software bij dat tegelijkertijd kan worden uitgevoerd.  Zie de [hand leiding voor licentie beheer](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm)voor meer informatie over het instellen van licentie beheer op uw server.

De licentie server bevindt zich doorgaans in uw on-premises netwerk of wordt gehost op een virtuele machine van Azure in een virtueel Azure-netwerk.  Nadat de licentie server is ingesteld, moet u [het virtuele netwerk](./how-to-connect-peer-virtual-network.md) koppelen aan uw [Lab-account](./tutorial-setup-lab-account.md).  U moet de netwerk peering uitvoeren voordat u het Lab maakt, zodat uw Lab-Vm's toegang hebben tot de licentie server en vice versa.

Zie [een licentie server instellen als een gedeelde bron](how-to-create-a-lab-with-shared-resource.md)voor meer informatie.

### <a name="lab-settings"></a>Lab-instellingen

De grootte van de virtuele machine (VM) die wordt aanbevolen voor ArcGIS bureau blad is afhankelijk van de toepassingen, uitbrei dingen en de specifieke versies die studenten zullen gebruiken.  De grootte van de virtuele machine is ook afhankelijk van de werk belastingen die studenten naar verwachting zullen uitvoeren.  Raadpleeg [ArcGIS Desktop System Requirements](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm) voor informatie over het identificeren van de VM-grootte.  Wanneer u de mogelijke VM-grootte hebt geïdentificeerd, raden we u aan de workloads van uw studenten te testen om te zorgen voor voldoende prestaties.

In dit artikel wordt aangeraden de [ **gemiddelde** VM-grootte](administrator-guide.md#vm-sizing) te gebruiken voor versie [10.7.1 van ArcMap](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm), ervan uitgaande dat er geen andere ArcGIS-desktop uitbreidingen worden gebruikt.  Afhankelijk van de behoeften van uw klasse, is het echter mogelijk dat u een **grote** of zelfs een VM-grootte van **Small\Medium GPU (visualisatie)** nodig hebt.  De extensie van de [spatiale analist](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm) die is opgenomen in ArcGIS Desktop biedt bijvoorbeeld ondersteuning voor een GPU voor betere prestaties, maar is niet vereist voor het gebruik van een GPU.

| Lab-instelling | Waarde en beschrijving |
| ------------ | ------------------ |
|Grootte van de virtuele machine| **Gemiddeld**.  Het meest geschikt voor relationele data bases, caching in het geheugen en analyse.|  

### <a name="template-machine"></a>Sjabloon machine

De stappen in deze sectie laten zien hoe u de sjabloon-VM kunt instellen:

1.  Start de sjabloon-VM en maak verbinding met de computer met behulp van RDP.

2.  Down load en installeer de ArcGIS-bureaublad onderdelen met behulp van instructies van door ESRI.  Deze stappen zijn onder andere het toewijzen van licentie Manager voor gelijktijdig gebruik van licenties: 
    - [Inleiding tot het installeren en configureren van ArcGIS Desktop](https://desktop.arcgis.com/en/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  Externe back-upopslag voor studenten instellen.  Studenten kunnen bestanden rechtstreeks opslaan op hun toegewezen VM, omdat alle wijzigingen die ze aanbrengen, worden opgeslagen in verschillende sessies.  We raden u echter aan om een aantal redenen om een back-up van hun werk te maken naar opslag die extern van de VM is:
    - Studenten in staat stellen om toegang te krijgen tot hun werk nadat de klas en het lab zijn beëindigd.  
    - Als de student de virtuele machine in een onjuiste staat krijgt en de installatie kopie opnieuw moet worden [ingesteld](how-to-set-virtual-machine-passwords.md#reset-vms).

    Met ArcGIS moet elke student een back-up maken van de volgende bestanden aan het einde van elke werk sessie:

    - MXD-bestand, waarin de lay-outinformatie voor een project wordt opgeslagen.
    - Geodata bases van een bestand, waarin alle gegevens worden opgeslagen die door ArcGIS worden geproduceerd.
    - Alle andere gegevens die de student kan gebruiken, zoals raster bestanden, ESRI, GeoTIFF, enzovoort.

    U kunt het beste OneDrive gebruiken voor back-upopslag.  Als u OneDrive wilt instellen op de sjabloon-VM, volgt u de stappen in het artikel [OneDrive installeren en configureren](how-to-prepare-windows-template.md#install-and-configure-onedrive). 

4.  [Publiceer](how-to-create-manage-template.md#publish-the-template-vm) ten slotte de VM van de sjabloon om de VM van de student te maken.

### <a name="auto-shutdown-and-disconnect-settings"></a>Instellingen voor automatisch afsluiten en verbreken

De instellingen voor [automatisch afsluiten en verbreken](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) van een Lab helpen ervoor te zorgen dat de VM van een student wordt afgesloten wanneer deze niet wordt gebruikt.  Deze instellingen moeten worden ingesteld op basis van de typen werk belastingen die uw studenten uitvoeren, zodat hun VM niet in het midden van hun werk wordt afgesloten.  Bijvoorbeeld: de instelling **gebruikers loskoppelen wanneer virtuele machines niet-actief zijn,** wordt de student losgekoppeld van hun RDP-sessie nadat er geen muis-of toetsenbord invoer is gedetecteerd gedurende een bepaalde periode.  Deze instelling moet voldoende tijd bieden voor werk belastingen waarbij de student niet actief is met de muis of het toetsen bord, zoals het uitvoeren van lange query's of wachten op rendering.

Voor ArcGIS raden we de volgende waarden voor deze instellingen aan:
- Gebruikers verbreken wanneer virtuele machines niet actief zijn
    - 30 minuten nadat de status niet-actief is gedetecteerd
- Virtuele machines uitschakelen wanneer gebruikers de verbinding verbreken
    - 15 minuten nadat de verbinding van de gebruiker is verbroken

## <a name="cost"></a>Kosten

Laten we een mogelijke schatting van de kosten voor deze klasse beslaan. Deze schatting omvat niet de kosten voor het uitvoeren van de licentie server. We gebruiken een klasse van 25 studenten. Er zijn 20 uur geplande tijd voor de klasse. Daarnaast krijgt elke student tien uur quota voor huis werk of toewijzingen buiten een geplande klasse tijd. Het formaat van de virtuele machine die we hebben gekozen is **gemiddeld**, dat wil zeggen 42 Lab-eenheden.

25 studenten \* (20 geplande uren + 10 quotum uren) \* 42 Lab-eenheden * 0,01 USD per uur = 315,00 USD

>[!IMPORTANT]
> Kosten raming is alleen bedoeld als voor beeld.  Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor actuele Details over prijzen.  

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van elk lab.

- [Een sjabloon maken en beheren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)