---
title: 'Zelfstudie voor installatie: fysiek Azure Stack Edge Pro-apparaat uitpakken, in een rek monteren en bekabelen | Microsoft Docs'
description: De tweede zelfstudie over het installeren van een GPU van Azure Stack Edge Pro gaat over het uitpakken, in een rek monteren en bekabelen van het fysieke apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 9aa02521d91d41380b1bdac3efe50ab3d196a856
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90894200"
---
# <a name="tutorial-install-azure-stack-edge-pro"></a>Zelfstudie: Azure Stack Edge Pro installeren

In deze zelfstudie wordt beschreven hoe u een fysiek Azure Stack Edge Pro-apparaat installeert. De installatieprocedure omvat het uitpakken, het in een rek monteren en het bekabelen van het apparaat. 

De installatie duurt ongeveer twee uur.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het apparaat uitpakken
> * Het apparaat in een rek monteren
> * Het apparaat bekabelen

## <a name="prerequisites"></a>Vereisten

De vereisten voor het installeren van een fysiek apparaat zijn als volgt:

### <a name="for-the-azure-stack-edge-resource"></a>Voor de Azure Stack Edge-resource

Zorg voordat u begint voor het volgende:

* U hebt alle stappen in [Implementatie van Azure Stack Edge Pro voorbereiden](azure-stack-edge-deploy-prep.md) uitgevoerd.
    * U hebt een Azure Stack Edge-resource gemaakt voor het implementeren van het apparaat.
    * U hebt de activeringscode gegenereerd om heet apparaat te activeren met de Azure Stack Edge-resource.

 
### <a name="for-the-azure-stack-edge-pro-physical-device"></a>Voor het fysieke Azure Stack Edge Pro-apparaat

Voordat u een apparaat implementeert, controleert u het volgende:

- Het apparaat staat veilig op een vlak, stabiel en horizontaal werkoppervlak.
- De locatie site waar u het apparaat wilt opstellen, voldoet aan deze voorwaarden:
    - Standaardnetstroom aanwezig van een onafhankelijke bron

        OF
    - Een PDU (Power Distribution Unit) aanwezig met een noodvoeding (UPS)
    - Een vrij 1U-slot in het rek waarin u het apparaat wilt monteren

### <a name="for-the-network-in-the-datacenter"></a>Voor het netwerk in het datacenter

Voordat u begint:

- Controleer de netwerkvereisten voor het implementeren van Azure Stack Edge Pro en configureer het netwerk van het datacenter aan de hand van deze vereisten. Zie [Netwerkvereisten voor Azure Stack Edge Pro](azure-stack-edge-system-requirements.md#networking-port-requirements) voor meer informatie.

- Voor een optimale werking van het apparaat heeft internet een minimale bandbreedte van 20 Mbps nodig.


## <a name="unpack-the-device"></a>Het apparaat uitpakken

Dit apparaat wordt verzonden in één doos. Voer de volgende stappen uit om het apparaat uit te pakken. 

1. Leg de doos op een vlak, horizontaal oppervlak.
2. Controleer de doos en het verpakkingsmateriaal op deuken, scheuren, waterschade of andere duidelijk zichtbare schade. Als de doos of het verpakkingsmateriaal ernstig beschadigd is, moet u de doos niet openen. Neem contact op met Microsoft Support om in overleg te bepalen of het apparaat nog in goede staat zal zijn.
3. Pak de doos uit. Controleer of de inhoud van de doos bestaat uit de volgende items:
    - Eén Azure Stack Edge Pro-apparaat met enkele behuizing
    - Twee netsnoeren
    - Eén railkitassembly
    - Het boekje Informatie over veiligheid, milieu en regelgeving

Als een of meer van de bovenstaande items ontbreken, neemt u contact op met de ondersteuning van Azure Stack Edge Pro. De volgende stap is het in het rek monteren van uw apparaat.


## <a name="rack-the-device"></a>Het apparaat in het rek monteren

Het apparaat moet worden geïnstalleerd in een standaard 19-inch rek. Gebruik de volgende procedure om het apparaat te monteren in een standaard 19-inch rek.

> [!IMPORTANT]
> Azure Stack Edge Pro-apparaten moeten voor een goede werking in een rek worden gemonteerd.


### <a name="prerequisites"></a>Vereisten

- Lees voordat u begint de veiligheidsinstructies in het boekje Informatie over Veiligheid, milieu en regelgeving. Dit boekje is geleverd bij het apparaat.
- Begin met het installeren van de rails in de toegewezen ruimte die zich het dichtst bij de onderkant van de rekbehuizing bevindt.
- Voor het monteren van de rails met gereedschap:
    -  U hebt acht schroeven nodig: #10-32, #12-24, #M5 of #M6. De kopdiameter van de schroeven moet kleiner zijn dan 10 mm (0,4").
    -  U hebt een vlakke schroevendraaier nodig.

### <a name="identify-the-rail-kit-contents"></a>De inhoud van de railkit identificeren

Zoek de onderdelen voor het installeren van de railkit-assembly:
1. Twee A7 Dell ReadyRails II sliding rail-assembly's
2. Twee Hook and Loop-riemen

    ![Inhoud van de railkit identificeren](./media/azure-stack-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Gereedschapsloze rails installeren en verwijderen (rekken met vierkante of ronde gaten)

> [!TIP]
> Deze optie is gereedschapsloos omdat geen gereedschap nodig is om de rails te installeren in en te verwijderen uit de vierkante of ronde gaten zonder schroefdraad in de rekken.

1. Plaats de eindstukken van de linker- en rechterrails met het label **VOORZIJDE** naar binnen, en plaats elk eindstuk in een gat aan de voorzijde van de verticale rekrichels.
2. Lijn elk eindstuk uit in de onderste en bovenste gaten van de gewenste U-ruimten.
3. Beweeg de achterzijde van de rail totdat deze volledig rust op de verticale rekrichel. De grendel klikt nu vast. Herhaal deze stappen om het voorste gedeelte op de verticale rekrichel te positioneren en te laten rusten.
4. Als u de rails wilt verwijderen, trek u de knop uit om de grendel los te maken op het eindpunt van het gedeelte en maakt u elke rail los.

    ![Gereedschapsloze rails installeren en verwijderen](./media/azure-stack-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Rails met gereedschap installeren en verwijderen (rekken met gaten met schroefdraad)

> [!TIP]
> Voor deze optie is gereedschap nodig (_een vlakke schroevendraaier_) om de rails te installeren in en te verwijderen uit de vierkante of ronde gaten met schroefdraad in de rekken.

1. Verwijder de pinnen van de monteerhaakjes voor- en achter met een vlakke schroevendraaier.
2. Trek en draai de subassembly's van de railgrendel om ze te verwijderen van de monteerhaken.
3. Bevestig de linker- en rechtermontagerails aan de verticale rekrichels aan de voorzijde met behulp van twee paar schroeven.
4. Schuif de linker- en rechterhaakjes naar voren tegen de verticale rekrichel en voeg deze toe met behulp van twee paar schroeven.

    ![Rails met gereedschap installeren en verwijderen](./media/azure-stack-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Het systeem in een rek installeren

1. Trek de binnenste schuifrails uit het rek totdat ze zijn vergrendeld.
2. Zoek de achterste railpinnen aan elke zijde van het systeem en laat deze in de eerste J-sleuven op de schuifassembly zakken. Draai het systeem omlaag totdat alle railpinnen in de J-sleuven rusten.
3. Duw het systeem naar binnen totdat de vergrendelingen vastklikken.
4. Druk op de vergrendelingsknoppen van de schuif op beide rails en schuif het systeem in het rek.

    ![Systeem in een rek installeren](./media/azure-stack-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Het systeem uit het rek verwijderen

1. Zoek de vergrendelingen aan de zijkanten van de binnenkant van de rails.
2. Ontgrendel elke hendel door deze naar de vrijgavepositie te verplaatsen.
3. Grijp de zijkanten van het systeem stevig vast en trek het naar voren, totdat de railpinnen vooraan in de J-sleuven staan. Til het systeem omhoog en weg van het rek en plaats het op een vlakke oppervlakte.

    ![Systeem uit het rek verwijderen](./media/azure-stack-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Het slagslot bewegen en loslaten

> [!NOTE]
> Voor systemen zonder slagsloten: bevestig het systeem met schroeven, zoals wordt beschreven in stap 3 van deze procedure.

1. Zoek vanaf de voorzijde het slagslot aan beide zijden van het systeem.
2. De grendels bewegen automatisch wanneer het systeem in het rek wordt geduwd en laten los wanneer u de grendels omhoog trekt.
3. Als u het systeem voor verzending - of in een ander instabiele omgeving - in het rek wilt bevestigen, draait u de klemschroeven onder elke grendel vast met een #2-schroevendraaier van Philips.

    ![Slagslot bewegen en loslaten](./media/azure-stack-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Het apparaat bekabelen

Leg de kabels op hun plek en bekabel het apparaat. In de volgende procedures wordt uitgelegd hoe uw Azure Stack Edge Pro-apparaat kunt bekabelen voor stroomtoevoer en aansluiting op het netwerk.

Voordat u begint met de bekabeling van uw apparaat, zorgt u ervoor dat u over de volgende zaken beschikt:

- Uw fysieke Azure Stack Edge Pro-apparaat, uitgepakt en in het rek gemonteerd.
- Twee netsnoeren.
- Ten minste één 1-GbE RJ-45-netwerkkabel voor verbinding met de beheerinterface. Er zijn twee 1-GbE-netwerkinterfaces, één beheerinterface en één gegevensinterface, op het apparaat aanwezig.
- Eén koperen 25-GbE SFP+-kabel voor elke te configureren netwerkinterface. Ten minste één netwerkinterface (PORT 2, PORT 3, PORT 4, PORT 5 of PORT 6) moet worden verbonden met internet (en met Azure).  
- Toegang tot twee Power Distribution Units (aanbevolen).

> [!NOTE]
> - Als u slechts één netwerkinterface verbindt, wordt u aangeraden een 25/10-GbE netwerkinterface, zoals PORT 3, PORT 4, PORT 5 of PORT 6, te gebruiken om gegevens naar Azure te verzenden. 
> - Voor de beste prestaties en voor het verwerken van grote gegevensvolumes, kunt u eventueel alle gegevenspoorten verbinden.
> - Het Azure Stack Edge Pro-apparaat moet worden aangesloten op het netwerk van het datacenter, zodat het gegevens kan opnemen van gegevensbronservers.

Op uw Azure Stack Edge Pro-apparaat:

- Het voorpaneel heeft schijfstations en een aan/uit-knop.

    - Er zijn 10 schijfsleuven aan de voorzijde van het apparaat.
    - Sleuf 0 heeft een 240-GB SATA-schijf die wordt gebruikt als een besturingssysteemschijf. Sleuf 1 is leeg, en sleuven 2 tot en met 9 zijn NVMe SSD's die worden gebruikt als gegevensschijven.
- Het achterpaneel is voorzien van redundante PSU’s (Power Supply Units).
- Het achterpaneel heeft zes netwerkinterfaces:

    - Twee 1-Gbps-interfaces.
    - Vier 25-Gbps-interfaces die ook kunnen fungeren als 10-Gbps-interfaces.
    - Een BMC (basiskaartbeheercontroller).

- Het achterpaneel heeft twee netwerkkaarten die horen bij de 6 poorten:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

Voor een volledige lijst met ondersteunde kabels, schakelaars en ontvangers voor deze netwerkkaarten gaat u naar [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Voer de volgende stappen uit om uw apparaat te bekabelen voor stroom en verbinding met het netwerk.

1. Hieronder ziet u de verschillende poorten en aansluitingen op het achterpaneel van het apparaat.

    ![Achterpaneel van een bekabeld apparaat](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

2. Zoek de schijfsleuven en de aan/uit-knop aan de voorzijde van het apparaat.

    ![Voorpaneel van een apparaat](./media/azure-stack-edge-deploy-install/device-front-plane-labeled-1.png)

3. Sluit de netsnoeren aan op de PSU's in de behuizing. Voor een hoge beschikbaarheid moet u beide PSU's verbinden met verschillende stopcontacten.
4. Sluit de netsnoeren aan op de PDU's van het rek. Zorg ervoor dat de twee PSU's op verschillende stopcontacten zijn aangesloten.
5. Druk op de aan/uit-knop om het apparaat in te schakelen.
6. Verbind PORT 1 van de 1-GbE netwerkinterface met de computer die wordt gebruikt voor het configureren van het fysieke apparaat. PORT 1 is de gereserveerde beheerinterface.
7. Verbind één of meer interfaces, bijvoorbeeld PORT 2, PORT 3, PORT 4, PORT 5 of PORT 6 aan het netwerk van het datacenter of internet.

    - Gebruik de RJ-45-netwerkkabel als u PORT 2 verbindt.
    - Gebruik de koperen SFP+-kabels voor de 10/25-GbE-netwerkinterfaces.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Stack Edge Pro, zoals:

> [!div class="checklist"]
> * Het apparaat uitpakken
> * Het apparaat in het rek monteren
> * Het apparaat bekabelen

Ga naar de volgende zelfstudie voor informatie over het verbinden, instellen en activeren van uw apparaat.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro verbinden en instellen](./azure-stack-edge-deploy-connect-setup-activate.md)
