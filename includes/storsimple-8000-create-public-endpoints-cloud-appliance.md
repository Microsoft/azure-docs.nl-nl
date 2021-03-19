---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5a286753e438b7d65f3d33a82669c4f7e79a282
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86544775"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Openbare eindpunten op het cloudapparaat maken

1. Meld u aan bij Azure Portal.
2. Ga naar **Virtuele machines** en selecteer en klik op de virtuele machine die wordt gebruikt als uw cloudapparaat.
    
3. U moet een netwerkbeveiligingsgroepregel (NSG) voor het beheren van de verkeersstroom in en uit uw virtuele machine maken. Voer de volgende stappen uit voor het maken van een NSG-regel.
    1. Selecteer **Netwerkbeveiligingsgroep**.
        ![Scherm afbeelding van de pagina van de virtuele machine. In de sectie instellingen is de netwerk beveiligings groep gemarkeerd.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Klik op de standaardnetwerkbeveiligingsgroep die wordt weergegeven.
        ![Scherm afbeelding van de pagina netwerk beveiligings groep. De standaard netwerk beveiligings groep is gemarkeerd.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Selecteer **Inkomende beveiligingsregels zoeken**.
        ![Scherm afbeelding van een pagina met de eigenschappen van de standaard netwerk beveiligings groep. In het navigatie deel venster worden binnenkomende beveiligings regels gemarkeerd.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Klik op **Toevoegen** om een Inkomende beveiligingsregel te maken.
        ![Scherm afbeelding van de pagina inkomende beveiligings regels. Het plus teken en het woord toevoegen worden naast elkaar en gemarkeerd.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        Doe het volgende op de blade Inkomende beveiligingsregel toevoegen:

        1. Typ voor de **naam** de volgende naam voor het eind punt: WinRMHttps.
        
        2. Selecteer voor de **Prioriteit** een getal dat kleiner is dan 1000 (dit is de prioriteit voor de standaardregel). Hoe hoger de waarde, hoe lager de prioriteit.

        3. Stel de **Bron** in op **Willekeurig**.

        4. Voor de **Service** selecteert u **WinRM**. Het **Protocol** wordt automatisch ingesteld op **TCP** en het **Poortbereik** wordt ingesteld op **5986**.

        5. Klik op **OK** om de regel te maken.

            ![Scherm opname van de Blade binnenkomende beveiligings regel toevoegen. De waarden worden ingevuld zoals beschreven in de procedure en de knop OK is gemarkeerd.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. De laatste stap bestaat uit het koppelen van uw netwerkbeveiligingsgroep aan een subnet of een specifieke netwerkinterface. Voer de volgende stappen uit om uw netwerkbeveiligingsgroep te koppelen met een subnet.
    1. Ga naar **Subnetten**.
    2. Klik op **Koppelen**.
        ![Scherm afbeelding van de pagina subnetten. Het plus teken en het gekoppelde woord worden naast elkaar en gemarkeerd.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Selecteer het virtuele netwerk en selecteer vervolgens het juiste subnet.
    4. Klik op **OK** om de regel te maken.

        ![Scherm afbeelding van de pagina subnet koppelen. Het virtuele netwerk is geselecteerd en de knop OK is gemarkeerd.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Nadat de regel is gemaakt, kunt u de details van het eindpunt weergeven om het openbare VIP-adres (virtueel IP-adres) te bepalen. Noteer dit adres.


