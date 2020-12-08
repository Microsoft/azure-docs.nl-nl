---
title: Zelfstudie voor het installeren van het fysieke Azure Stack Edge Pro R-apparaat | Microsoft Docs
description: De tweede zelfstudie over het installeren van Azure Stack Edge Pro R gaat over het bekabelen van het fysieke apparaat aan het stroomnet en het netwerk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro R in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: b67da2607d206424f69f53645eda148495ea58ec
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464001"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>Zelfstudie: Azure Stack Edge Pro R installeren

In deze zelfstudie wordt beschreven hoe u een fysiek Azure Stack Edge Pro R-apparaat installeert. De installatieprocedure omvat het bekabelen van het apparaat.

Het installeren kan ongeveer dertig minuten in beslag nemen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het apparaat inspecteren
> * Het apparaat bekabelen

## <a name="prerequisites"></a>Vereisten

De vereisten voor het installeren van een fysiek apparaat zijn als volgt:

### <a name="for-the-azure-stack-edge-resource"></a>Voor de Azure Stack Edge-resource

Zorg voordat u begint voor het volgende:

* U hebt alle stappen in [Implementatie van Azure Stack Edge Pro R voorbereiden](azure-stack-edge-pro-r-deploy-prep.md) uitgevoerd.
    * U hebt een Azure Stack Edge-resource gemaakt voor het implementeren van het apparaat.
    * U hebt de activeringscode gegenereerd om heet apparaat te activeren met de Azure Stack Edge-resource.

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>Voor het fysieke Azure Stack Edge Pro R-apparaat

Voordat u een apparaat implementeert, controleert u het volgende:

- Het apparaat staat veilig op een vlak, stabiel en horizontaal werkoppervlak.
- De locatie site waar u het apparaat wilt opstellen, voldoet aan deze voorwaarden:
    - Standaardnetstroom aanwezig van een onafhankelijke bron

        OF
    - Een PDU-rek (Power Distribution Unit). Het apparaat wordt geleverd met een noodvoeding (UPS)
    

### <a name="for-the-network-in-the-datacenter"></a>Voor het netwerk in het datacenter

Voordat u begint:

- Controleer de netwerkvereisten voor het implementeren van Azure Stack Edge Pro R en configureer het netwerk van het datacentrum aan de hand van deze vereisten. Zie [Netwerkvereisten voor Azure Stack Edge Pro R](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements) voor meer informatie.

- Voor een optimale werking van het apparaat heeft internet een minimale bandbreedte van 20 Mbps nodig.


## <a name="inspect-the-device"></a>Het apparaat inspecteren

Dit apparaat wordt verzonden als één eenheid. Voer de volgende stappen uit om het apparaat uit te pakken.

1. Leg de doos op een vlak, horizontaal oppervlak.
2. Inspecteer de koffer dat het apparaat bevat op beschadigingen. Open de koffer en inspecteer het apparaat. Indien u van mening bent dat de koffer of het apparaat is beschadigd, neemt u contact op met Microsoft Ondersteuning om vast te stellen of het apparaat in goede staat is.
3. Nadat de koffer is geopend, controleert u of het volgende aanwezig is:
    - Eén Azure Stack Edge Pro R-apparaat met enkele behuizing
    - Noodvoeding (UPS)
    - Twee korte stroomkabels om het apparaat aan te sluiten op de noodvoeding
    - Eén stroomkabel om de noodvoeding aan te sluiten op een voedingsbron

Als een of meer van de bovenstaande items ontbreken, neemt u contact op met de ondersteuning van Azure Stack Edge Pro R. De volgende stap bestaat uit het bekabelen van het apparaat.


## <a name="cable-the-device"></a>Het apparaat bekabelen

In de volgende procedures wordt uitgelegd hoe u het Azure Stack Edge Pro R-apparaat kunt bekabelen voor stroomtoevoer en aansluiting op het netwerk.

Voordat u begint met de bekabeling van uw apparaat, zorgt u ervoor dat u over de volgende zaken beschikt:

- Het fysieke Azure Stack Edge Pro R-apparaat op de installatielocatie.
- Eén voedingskabel.
- Ten minste één 1-GbE RJ-45-netwerkkabel voor verbinding met de beheerinterface. Er zijn twee 1-GbE-netwerkinterfaces, één beheerinterface en één gegevensinterface, op het apparaat aanwezig.
- Eén koperen 10/25-GbE SFP+-kabel voor elke te configureren netwerkinterface. Ten minste één netwerkinterface (PORT 3 of PORT 4) moet worden verbonden met internet (en met Azure).  
- Toegang tot één Power Distribution Unit (aanbevolen).

> [!NOTE]
> - Als u slechts één netwerkinterface verbindt, wordt u aangeraden een 25/10-GbE netwerkinterface, zoals PORT 3 of PORT 4, te gebruiken om gegevens naar Azure te verzenden. 
> - Voor de beste prestaties en voor het verwerken van grote gegevensvolumes, kunt u eventueel alle gegevenspoorten verbinden.
> - Het Azure Stack Edge Pro R-apparaat moet worden aangesloten op het netwerk van het datacentrum, zodat het gegevens kan opnemen van gegevensbronservers.

Op het Azure Stack Edge Pro R-apparaat:

- Het voorpaneel heeft schijfstations en een aan/uit-knop.

    - Er zijn acht schijfsleuven aan de voorzijde van het apparaat.
    - Het apparaat bevat ook 2 X M.2 SATA-schijven die als besturingssysteemschijven fungeren. 

- Het achterpaneel is voorzien van redundante PSU’s (Power Supply Units).
- Het achterpaneel heeft vier netwerkinterfaces:

    - Twee 1-Gbps-interfaces.
    - Twee 25-Gbps-interfaces, die ook als 10-Gbps-interfaces kunnen fungeren.
    - Een BMC (basiskaartbeheercontroller).

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
Voer de volgende stappen uit om uw apparaat te bekabelen voor stroom en verbinding met het netwerk.

1. Hieronder ziet u de verschillende poorten en aansluitingen op het achterpaneel van het apparaat.

    ![Achterpaneel van een bekabeld apparaat](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. Zoek de schijfsleuven en de aan/uit-knop aan de voorzijde van het apparaat.

    ![Voorpaneel van een apparaat](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. Sluit één uiteinde van de stroomkabel aan op de noodvoeding. Sluit het andere uiteinde van de stroomkabel aan op de PDU's van het rek. 
5. Druk op de aan/uit-knop om het apparaat in te schakelen.
6. Verbind PORT 1 van de 1-GbE netwerkinterface met de computer die wordt gebruikt voor het configureren van het fysieke apparaat. PORT 1 is de gereserveerde beheerinterface.
7. Verbind één of meer interfaces, bijvoorbeeld PORT 2, PORT 3 of PORT 4 met het netwerk van het datacentrum of internet.

    - Gebruik de RJ-45-netwerkkabel als u PORT 2 verbindt.
    - Gebruik de koperen SFP+-kabels voor de 10/25-GbE-netwerkinterfaces.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Stack Edge Pro R, zoals:

> [!div class="checklist"]
> * Het apparaat uitpakken
> * Het apparaat bekabelen

Ga naar de volgende zelfstudie voor informatie over het verbinden van uw apparaat.

> [!div class="nextstepaction"]
> [Verbinding maken met Azure Stack Edge Pro R](./azure-stack-edge-pro-r-deploy-connect.md)
