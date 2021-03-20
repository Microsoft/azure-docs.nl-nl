---
title: Problemen met grafische prestaties diagnosticeren Extern bureaublad-Azure
description: In dit artikel wordt beschreven hoe u grafische items van RemoteFX in extern bureau blad-protocol sessies gebruikt voor het vaststellen van prestatie problemen met afbeeldingen in Windows virtueel bureau blad.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: dc5c6499fa47a1e32a517032d5cc2a97b3f2677f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88007163"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Problemen met grafische prestaties in Extern bureaublad diagnosticeren

Als u problemen met de kwaliteit van uw externe sessies wilt vaststellen, zijn er tellers gegeven in de sectie RemoteFX graphics van de prestatie meter. Dit artikel helpt u bij het lokaliseren en oplossen van problemen met grafische prestaties tijdens Remote Desktop Protocol (RDP)-sessies met behulp van deze prestatie meter items.

## <a name="find-your-remote-session-name"></a>Uw externe sessie naam zoeken

U hebt uw externe sessie naam nodig om de grafische prestatie meter items te identificeren. Volg de instructies in deze sectie om uw exemplaar van elk prestatie meter item te identificeren.

1. Open de Windows-opdracht prompt vanuit uw externe sessie.
2. Voer de opdracht **qwinsta** uit en zoek de naam van uw sessie.
    - Als uw sessie wordt gehost op een virtuele machine met meerdere sessies (VM): uw exemplaar van elk prestatie meter item is een achtervoegsel dat overeenkomt met de naam van uw sessie, zoals ' RDP-TCP 37 '.
    - Als uw sessie wordt gehost in een VM die virtuele grafische verwerkings eenheden (vGPU) ondersteunt: uw exemplaar van elk item wordt opgeslagen op de server in plaats van in uw VM. De item instanties bevatten de naam van de virtuele machine in plaats van het nummer in de sessie naam, zoals ' Win8 Enter prise VM '.

>[!NOTE]
> Hoewel tellers RemoteFX in hun namen hebben, bevatten ze ook extern bureau blad-afbeeldingen in vGPU-scenario's.

## <a name="access-performance-counters"></a>Prestatie meter items voor toegang

Nadat u de naam van uw externe sessie hebt bepaald, volgt u deze instructies voor het verzamelen van de prestatie meter items voor RemoteFX graphics voor uw externe sessie.

1. Selecteer **Start**  >  **systeem beheer**  >  **prestatie meter**.
2. Vouw in het dialoog venster **prestatie meter** het onderdeel **controle hulpprogramma's** uit, selecteer **prestatie meter** en selecteer vervolgens **toevoegen**.
3. Vouw in het dialoog venster **items toevoegen** vanuit de lijst **beschik bare items** de sectie uit voor RemoteFX-afbeeldingen.
4. Selecteer de prestatie meter items die moeten worden bewaakt.
5. Selecteer in de lijst **instanties van geselecteerd object** de specifieke exemplaren die moeten worden bewaakt voor de geselecteerde items en selecteer vervolgens **toevoegen**. Selecteer **alle exemplaren** om alle beschik bare item instanties te selecteren.
6. Nadat u de items hebt toegevoegd, selecteert u **OK**.

De geselecteerde prestatie meter items worden weer gegeven op het scherm prestatie meter.

>[!NOTE]
>Elke actieve sessie op een host heeft een eigen exemplaar van elk prestatie meter item.

## <a name="diagnose-issues"></a>Problemen diagnosticeren

Problemen met betrekking tot grafische prestaties zijn in het algemeen onderverdeeld in vier categorieën:

- Lage frame frequentie
- Wille keurige hokjes
- Hoge invoer latentie
- Slechte kwaliteit van frames

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Lage frame frequentie, wille keurige hokjes en hoge invoer latentie adresseren

Controleer eerst de uitvoer frames per seconde teller. Hiermee wordt het aantal frames gemeten dat beschikbaar wordt gesteld aan de client. Als deze waarde lager is dan het item invoer frames/seconde, worden frames overgeslagen. Als u het knel punt wilt identificeren, gebruikt u de tellers voor overgeslagen items/seconde.

Er zijn drie soorten frames overgeslagen/seconde tellers:

- Overgeslagen frames/seconde (onvoldoende Server bronnen)
- Overgeslagen frames/seconde (onvoldoende netwerk bronnen)
- Overgeslagen frames/seconde (onvoldoende client bronnen)

Een hoge waarde voor een van de overgeslagen frames/seconde tellers impliceert dat het probleem betrekking heeft op de bron die door het item wordt bijgehouden. Als de client bijvoorbeeld niet in staat is om frames te decoderen en te presen teren met dezelfde snelheid, levert de server de frames op. het aantal frames dat/seconde is overgeslagen (onvoldoende client bronnen) is hoog.

Als het item uitvoer frames/seconde overeenkomt met het item invoer frames per seconde, maar u nog steeds ongebruikelijk vertraging of Stallion ondervindt, kan de gemiddelde coderings tijd de culprit zijn. Encoding is een synchroon proces dat op de server plaatsvindt in het scenario met één sessie en op de virtuele machine in het scenario met meerdere sessies. De gemiddelde coderings tijd moet minder dan 33 MS zijn. Als de gemiddelde coderings tijd minder is dan 33 MS, maar u nog steeds prestatie problemen ondervindt, is er mogelijk een probleem met de app of het besturings systeem dat u gebruikt.

Zie voor meer informatie over het vaststellen van problemen met betrekking tot een app de [prestatie meter items voor gebruikers invoer vertraging](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

Omdat RDP een gemiddelde coderings tijd van 33 MS ondersteunt, wordt een invoer frame frequentie van Maxi maal 30 frames per seconde ondersteund. Houd er rekening mee dat 33 MS de Maxi maal ondersteunde frame frequentie is. In veel gevallen is de frame frequentie die de gebruiker heeft ervaren lager, afhankelijk van hoe vaak een frame aan RDP door de bron wordt door gegeven. Taken zoals het bekijken van een video vereisen bijvoorbeeld een volledige invoer frame frequentie van 30 frames/seconde, maar minder computerintensieve taken zoals het niet regel matig bewerken van een document resulteren in een veel lagere waarde voor invoer frames per seconde zonder degradatie in de ervaring van de gebruiker.

### <a name="addressing-poor-frame-quality"></a>Slechte frame kwaliteit aanpakken

Gebruik het prestatie meter item voor frames om problemen met de frame kwaliteit op te sporen. Dit item drukt de kwaliteit van het uitvoer frame af als een percentage van de kwaliteit van het bron frame. Het kwaliteits verlies kan worden veroorzaakt door RemoteFX of het is inherent aan de grafische bron. Als RemoteFX het kwaliteits verlies heeft veroorzaakt, is het probleem mogelijk een tekort aan netwerk-of Server bronnen voor het verzenden van inhoud met een hogere kwaliteit.

## <a name="mitigation"></a>Oplossing

Als Server bronnen het knel punt veroorzaken, kunt u een van de volgende benaderingen proberen om de prestaties te verbeteren:

- Verminder het aantal sessies per host.
- Verg root de hoeveelheid geheugen en reken resources op de-server.
- Verwijder de resolutie van de verbinding.

Als netwerk bronnen het knel punt veroorzaken, kunt u een van de volgende benaderingen proberen om de netwerk beschikbaarheid per sessie te verbeteren:

- Verminder het aantal sessies per host.
- Gebruik een netwerk met een hogere band breedte.
- Verwijder de resolutie van de verbinding.

Als client bronnen het knel punt veroorzaken, kunt u een van de volgende benaderingen proberen om de prestaties te verbeteren:

- Installeer de meest recente Extern bureaublad-client.
- Verg root de hoeveelheid geheugen en reken resources op de client computer.

> [!NOTE]
> De bron frames/seconde tellers worden momenteel niet ondersteund. Voor nu wordt altijd 0 weer gegeven in de bron frames per seconde teller.

## <a name="next-steps"></a>Volgende stappen

- Zie [Configure graphics processing unit (GPU) Acceleration (Engelstalig) voor virtuele Windows-desktop omgevingen](configure-vm-gpu.md)om een door GPU geoptimaliseerde virtuele Azure-machine te maken.
- Zie [probleemoplossings overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van het oplossen van problemen en escalatie sporen.
- Zie [Windows Desktop Environment](environment-setup.md)(Engelstalig) voor meer informatie over de service.
