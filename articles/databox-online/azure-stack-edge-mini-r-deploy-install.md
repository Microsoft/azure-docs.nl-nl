---
title: Zelfstudie voor het installeren van het fysieke Azure Stack Edge Mini R-apparaat | Microsoft Docs
description: De tweede zelfstudie over het installeren van Azure Stack Edge Mini R-apparaat gaat over het bekabelen van het fysieke apparaat aan het stroomnet en het netwerk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 8b154dabd6f672c6fdaf77c5f8d48f80fb40d5d8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060105"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>Zelfstudie: Azure Stack Edge Mini R installeren

In deze zelfstudie wordt beschreven hoe u een fysiek Azure Stack Edge Mini R-apparaat installeert. De installatieprocedure omvat het bekabelen van het apparaat.

Het installeren kan ongeveer dertig minuten in beslag nemen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het apparaat inspecteren
> * Het apparaat bekabelen

## <a name="prerequisites"></a>Vereisten

De vereisten voor het installeren van een fysiek apparaat zijn als volgt:

### <a name="for-the-azure-stack-edge-resource"></a>Voor de Azure Stack Edge-resource

Zorg voordat u begint voor het volgende:

* U hebt alle stappen in [Implementatie van Azure Stack Edge Mini R voorbereiden](azure-stack-edge-mini-r-deploy-prep.md) uitgevoerd.
    * U hebt een Azure Stack Edge-resource gemaakt voor het implementeren van het apparaat.
    * U hebt de activeringscode gegenereerd om heet apparaat te activeren met de Azure Stack Edge-resource.

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>Voor het fysieke Azure Stack Edge Mini R-apparaat

Voordat u een apparaat implementeert, controleert u het volgende:

- Het apparaat staat veilig op een vlak, stabiel en horizontaal werkoppervlak.
- De locatie site waar u het apparaat wilt opstellen, voldoet aan deze voorwaarden:
    - Standaardnetstroom aanwezig van een onafhankelijke bron, of
    - Een PDU-rek (Power Distribution Unit). 
    

### <a name="for-the-network-in-the-datacenter"></a>Voor het netwerk in het datacenter

Voordat u begint:

- Controleer de netwerkvereisten voor het implementeren van Azure Stack Edge Mini R en configureer het netwerk van het datacenter aan de hand van deze vereisten. Zie [Netwerkvereisten voor Azure Stack Edge](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements) voor meer informatie.

- Voor een optimale werking van het apparaat is een minimale internetbandbreedte van 20 Mbps nodig. <!-- engg TBC -->


## <a name="inspect-the-device"></a>Het apparaat inspecteren

Dit apparaat wordt verzonden als één eenheid. Voer de volgende stappen uit om het apparaat uit te pakken.

1. Leg de doos op een vlak, horizontaal oppervlak.
2. Inspecteer de koffer dat het apparaat bevat op beschadigingen. Open de koffer en inspecteer het apparaat. Indien u van mening bent dat de koffer of het apparaat is beschadigd, neemt u contact op met Microsoft Ondersteuning om vast te stellen of het apparaat in goede staat is.
3. Nadat de koffer is geopend, controleert u of het volgende aanwezig is:
    - Een draagbaar Azure Stack Edge Mini R-apparaat met aan de zijkant aangesloten bumpers
    - Eén accu en de op het apparaat gemonteerde afdekplaat aan de achterkant. 
    - Eén stroomkabel om de accu te verbinden met de voedingsbron 

Als een of meer van de bovenstaande items ontbreken, neemt u contact op met de Azure Stack Edge-ondersteuning. De volgende stap bestaat uit het bekabelen van het apparaat.


## <a name="cable-the-device"></a>Het apparaat bekabelen

In de volgende procedures wordt uitgelegd hoe uw Azure Stack Edge-apparaat kunt bekabelen voor stroomtoevoer en aansluiting op het netwerk.

Voordat u begint met de bekabeling van uw apparaat, zorgt u ervoor dat u over de volgende zaken beschikt:

- Het fysieke Azure Stack Edge Mini R-apparaat op de installatielocatie.
- Eén voedingskabel.
- Ten minste één 1-GbE RJ-45-netwerkkabel voor verbinding met de beheerinterface. Er zijn twee 1-GbE-netwerkinterfaces, één beheerinterface en één gegevensinterface, op het apparaat aanwezig.
- Eén koperen 10-GbE SFP+-kabel voor elke te configureren netwerkinterface. Ten minste één netwerkinterface (PORT 3 of PORT 4) moet worden verbonden met internet (en met Azure).  
- Toegang tot één Power Distribution Unit (aanbevolen).

> [!NOTE]
> - Als u slechts één netwerkinterface verbindt, wordt u aangeraden een 10-GbE netwerkinterface, zoals PORT 3 of PORT 4, te gebruiken om gegevens naar Azure te verzenden. 
> - Voor de beste prestaties en voor het verwerken van grote gegevensvolumes, kunt u eventueel alle gegevenspoorten verbinden.
> - Het Azure Stack Edge-apparaat moet worden aangesloten op het netwerk van het datacenter, zodat het gegevens kan opnemen van gegevensbronservers. <!-- engg TBC -->

Op uw Azure Stack Edge-apparaat:

- Het paneel aan de voorzijde heeft een SSD-drager. 

    - Het apparaat heeft één SSD-schijf in de sleuf. 
    - Het apparaat heeft ook een CFx-kaart die fungeert als opslag voor de schijf van het besturingssysteem.
    
- Het voorpaneel heeft netwerkinterfaces en toegang tot Wi-Fi.

    - 2 X 1 GbE RJ 45-netwerkinterfaces. Dit zijn PORT 1 en PORT 2 op de lokale gebruikersinterface van het apparaat.
    - 2 X 10 GbE SFP+-netwerkinterfaces. Dit zijn PORT 3 en PORT 4 op de lokale gebruikersinterface van het apparaat. 
    - Een Wi-Fi-poort waaraan een Wi-Fi-zendontvanger is gekoppeld.

- Het voorpaneel heeft ook een aan/uit-knop. 

- Op het achterpaneel bevinden zich de accu en de afdekplaat die op het apparaat zijn gemonteerd. 


Voer de volgende stappen uit om uw apparaat te bekabelen voor stroom en verbinding met het netwerk.

1. Let op de verschillende netwerk- en opslagonderdelen op het voorpaneel van het apparaat.

    ![Netwerk- en opslaginterfaces op het apparaat](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. De aan/uit-knop bevindt zich linksonder aan de voorzijde van het apparaat. 

    ![Voorpaneel van een apparaat met de aan/uit-knop op het apparaat](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. De accu is verbonden met het achterpaneel van het apparaat. Let op de tweede aan/uit-knop op de accu. 

    ![Voorpaneel van een apparaat met de aan/uit-knop op de accu](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    Sluit één uiteinde van de stroomkabel aan op de accu en de andere op het stopcontact. 

    ![Verbinding van stroomkabel](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    Als u alleen de accu gebruikt (de accu is niet aangesloten op de netstroom), moet zowel de aan/uit-knop aan de voorzijde als de aan/uit-knop op de accu op de stand ON staan. Wanneer de accu is aangesloten op netstroom, hoeft alleen de aan/uit-knop aan de voorzijde van het apparaat op de stand ON te staan. 

4. Druk op de aan/uit-knop aan de voorzijde om het apparaat in te schakelen. 
    
    > [!NOTE]
    > U schakelt de stroom naar het apparaat uit door op de zwarte knop boven op de aan/uit-knop te drukken en de aan/uit-knop op OFF te zetten. 

5. Als u Wi-Fi op dit apparaat configureert, gebruikt u het volgende bekabelingsdiagram:

    ![Bekabeling voor wifi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - Verbind PORT 1 van de 1-GbE netwerkinterface met de computer die wordt gebruikt voor het configureren van het fysieke apparaat. PORT 1 is de gereserveerde beheerinterface.


    Als u een bedrade configuratie voor dit apparaat gebruikt, gebruikt u het volgende diagram:
     
    ![Bekabeling voor bedraad](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - Verbind PORT 1 van de 1-GbE netwerkinterface met de computer die wordt gebruikt voor het configureren van het fysieke apparaat. PORT 1 is de gereserveerde beheerinterface.
    - Verbind één of meer interfaces, bijvoorbeeld PORT 2, PORT 3 of PORT 4 met het netwerk van het datacentrum of internet.
    
        - Gebruik de RJ-45-netwerkkabel als u PORT 2 verbindt.
        - Gebruik de koperen SFP+-kabels voor de 10-GbE-netwerkinterfaces.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Stack Edge, zoals:

> [!div class="checklist"]
> * Het apparaat uitpakken
> * Het apparaat bekabelen

Ga naar de volgende zelfstudie voor informatie over het verbinden, instellen en activeren van uw apparaat.

> [!div class="nextstepaction"]
> [Azure Stack Edge verbinden en instellen](./azure-stack-edge-mini-r-deploy-connect.md)
