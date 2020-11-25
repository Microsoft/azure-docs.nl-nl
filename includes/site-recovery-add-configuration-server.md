---
title: bestand opnemen
description: bestand opnemen
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: include
ms.date: 02/28/2019
ms.author: mayg
ms.custom: include file
ms.openlocfilehash: f699ffe6d5a91e8ce3ae90c7e12249bbad0fff3e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001547"
---
1. Voer het installatiebestand voor de geïntegreerde Setup uit.
2. Selecteer bij **Voordat u begint** de optie **De configuratieserver en processerver installeren**.

    ![Schermopname van het scherm Voordat u begint in Uniforme installatie.](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. Klik bij **Licentievoorwaarden voor software van derden** op **Ik ga akkoord** om MySQL te downloaden en te installeren.

    ![Schermopname van het Third Party Software-licentiescherm in Uniforme installatie.](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. Selecteer bij **Registratie** de registratiesleutel die u hebt gedownload uit de kluis.

    ![Schermopname van het registratiescherm in Uniforme installatie.](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. Geef bij **Internetinstellingen** op hoe de provider die op de configuratieserver wordt uitgevoerd, via internet verbinding moet maken met Azure Site Recovery. Zorg ervoor dat u de vereiste URL's hebt.

    - Als u verbinding wilt maken met de proxy die momenteel op de computer is ingesteld, selecteert u **Verbinding maken met Azure Site Recovery via een proxyserver**.
    - Als u wilt dat de provider rechtstreeks verbinding maakt, selecteert u **Rechtstreeks verbinding maken met Azure Site Recovery zonder proxyserver**.
    - Als voor de bestaande proxy verificatie is vereist of als u voor de verbinding met de provider een aangepaste proxy wilt gebruiken, selecteert u **Verbinding maken met aangepaste proxyinstellingen** en geeft u het adres, de poort en referenties op.
     ![Schermopname van het scherm met internetinstellingen in Uniforme installatie.](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. Tijdens Setup wordt in **Controle op vereisten** gecontroleerd of de installatie kan worden uitgevoerd. Als er een waarschuwing wordt weergegeven over **Synchronisatiecontrole voor algemene tijd**, moet u controleren of de tijd op de systeemklok (instellingen voor **datum en tijd**) overeenkomt met de tijdzone.

    ![Schermopname van het scherm Controle van vereisten in Uniforme installatie.](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. Maak bij **MySQL-configuratie** referenties voor aanmelden bij de MySQL-serverinstantie die is geïnstalleerd.

    ![Schermopname van het MySQL-configuratiescherm in Uniforme installatie.](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. Selecteer in **Omgevingsdetails** de optie Nee als u Azure Stack-VM's of fysieke servers wilt repliceren. 
9. Selecteer bij **Installatielocatie** waar u de binaire bestanden wilt installeren en de cache wilt opslaan. Het station dat u selecteert, moet ten minste 5 GB vrije schijfruimte bevatten, maar wij raden u aan een cachestation te gebruiken met minstens 600 GB vrije ruimte.

    ![Schermopname van het installatielocatiescherm in Uniforme installatie.](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. Selecteer in **Netwerkselectie** eerst de NIC die de ingebouwde processerver gebruikt voor herstel na noodgeval en push installatie van de mobiliteitsservice op de bronmachines. Selecteer vervolgens de NIC die de configuratieserver gebruikt voor de connectiviteit met Azure. Poort 9443 is de standaardpoort voor het verzenden en ontvangen van replicatieverkeer, maar u kunt dit poortnummer aanpassen aan de vereisten van de omgeving. Naast poort 9443 wordt ook poort 443 geopend. Deze wordt door een webserver gebruikt om replicatiebewerkingen in te delen. Gebruik poort 443 niet voor het verzenden of ontvangen van replicatieverkeer.

    ![Schermopname van het netwerkselectiescherm in Uniforme installatie.](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. Lees de informatie bij **Samenvatting** en klik op **Installeren**. Wanneer de installatie is voltooid, wordt er een wachtwoordzin gegenereerd. U hebt deze nodig bij het inschakelen van de replicatie. Kopieer de wachtwoordzin daarom en bewaar deze op een veilige locatie.

    ![Schermopname van het overzichtsscherm in Uniforme installatie.](./media/site-recovery-add-configuration-server/combined-wiz10.png)

Na voltooiing van de registratie wordt de server weergegeven op de blade **Instellingen** > **Servers** in de kluis.
