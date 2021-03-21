---
title: Azure Lab Services gebruiken voor hackathon
description: In dit artikel wordt beschreven hoe u Azure Lab Services gebruikt voor het maken van Labs die u kunt gebruiken voor het uitvoeren van hackathons trappen.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d4648def48557d70547ef7d41d6c3e516671849e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452727"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>Azure Lab Services gebruiken voor uw volgende hackathon
Azure Lab Services is lichter ontworpen en eenvoudig te gebruiken, zodat u snel een nieuw Lab van virtuele machines (Vm's) kunt maken voor uw Hackathon.  Gebruik de volgende controle lijst om ervoor te zorgen dat uw hackathon zo soepel mogelijk verloopt. Deze controle lijst moet worden ingevuld door uw IT-afdeling of faculteit die verantwoordelijk is voor het maken en beheren van uw hackathon-Lab. 

Als u Lab-Services voor uw hackathon wilt gebruiken, moet u ervoor zorgen dat zowel een Lab-account als uw Lab ten minste enkele dagen vóór het begin van uw hackathon worden gemaakt. Volg ook de onderstaande instructies:

## <a name="guidance"></a>Hulp

- **Maak het lab in een regio of locatie die het dichtst bij de deel nemers** ligt. 

    Als u de latentie wilt beperken, maakt u uw Lab in een regio die het dichtst bij uw hackathon-deel nemers ligt.  Als uw deel nemers zich overal ter wereld bevinden, moet u uw beste arrest gebruiken om een lab te maken dat zich centraal bevindt.  Of Splits de hackathon om meerdere lessen te gebruiken op basis van de locaties waar uw deel nemers zich bevinden.
- **Kies een berekenings grootte die het meest geschikt is voor gebruiks behoeften**.

    Over het algemeen geldt dat hoe groter de reken grootte, des te sneller de virtuele machine wordt uitgevoerd. Als u de kosten echter wilt beperken, moet u de juiste reken grootte selecteren op basis van de behoeften van uw deel nemers. Zie [informatie over VM-grootte in de beheerders handleiding](administrator-guide.md#vm-sizing) voor meer informatie over de beschik bare reken grootten.
- **Configureer RDP\SSH voor verbinding met extern bureau blad met virtuele Linux-machines**.

    Als uw hackathon gebruikmaakt van Linux Vm's, moet u ervoor zorgen dat extern bureau blad is ingeschakeld zodat uw deel nemers RDP (Remote Desktop Protocol) of SSH (Secure Shell) kunnen gebruiken om verbinding te maken met hun virtuele machines. Deze stap is alleen vereist voor Linux Vm's en moet zijn ingeschakeld bij het maken van het lab. Daarnaast moet u voor RDP mogelijk de RDP-server en GUI-pakketten op de sjabloon-VM installeren en configureren voordat u deze publiceert.  Zie voor meer informatie de [hand leiding voor het inschakelen van extern bureau blad voor Linux](how-to-enable-remote-desktop-linux.md).

- **Windows-updates installeren en stoppen**. 

    Als u een Windows-installatie kopie gebruikt, raden we u aan om de meest recente Windows-updates op de VM van de Lab- [sjabloon](how-to-create-manage-template.md) te installeren voordat u deze publiceert om de vm's van Labs te maken. Het is om veiligheids redenen en om te voor komen dat deel nemers tijdens de hackathon worden onderbroken om updates te installeren, waardoor het mogelijk is dat de Vm's opnieuw worden opgestart. U kunt ook overwegen Windows-updates uit te scha kelen om toekomstige onderbrekingen te voor komen. Raadpleeg de [hand leiding voor het installeren en configureren van Windows-updates](how-to-prepare-windows-template.md#install-and-configure-updates).
- **Bepaal hoe studenten een back-up van hun werk moeten maken**. 

    Studenten krijgen elk een virtuele machine voor de levens duur van de Hackathon. Ze kunnen hun werk rechtstreeks op de machine opslaan, maar het wordt aangeraden om een back-up van hun werk te maken, zodat ze daar toegang toe hebben nadat de hackathon is afgelopen. Ze moeten bijvoorbeeld worden opgeslagen op een externe locatie, zoals OneDrive, GitHub, enzovoort. Als u OneDrive wilt gebruiken, kunt u dit automatisch configureren voor studenten op hun Lab-virtuele machines. Raadpleeg de [hand leiding voor het installeren en configureren van OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive).
- **Stel de capaciteit van de virtuele machine in op basis van het aantal deel nemers**. 

    Zorg ervoor dat de capaciteit van de virtuele machine van uw Lab is ingesteld op basis van het aantal deel nemers dat u verwacht op uw Hackathon. Wanneer u de virtuele machine van de sjabloon publiceert, kan het enkele uren duren om alle computers in het lab te maken. Daarom raden we u aan om dit vooraf te doen aan het begin van de Hackathon. Zie voor meer informatie de [hand leiding voor het bijwerken van Lab-capaciteit](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity).

- **Bepaal of u de toegang tot het Lab wilt beperken**. 

    Wanneer gebruikers aan het lab worden toegevoegd, is er een optie voor het beperken van toegang die standaard is ingeschakeld. Voor deze functie moet u alle e-mail berichten van uw hackathon-deel nemers aan de lijst toevoegen voordat ze het lab kunnen registreren en openen via de registratie koppeling. Als u een hackathon hebt waar u niet weet wie de deel nemers voor de gebeurtenis zullen hebben, kunt u ervoor kiezen de optie toegang beperken uit te scha kelen, waarmee iedereen zich bij het Lab kan registreren met de registratie koppeling. Zie voor meer informatie de [hand leiding voor het toevoegen van gebruikers](how-to-configure-student-usage.md).

- **Controleer de instellingen voor schema, quota** en automatisch afsluiten. 

    Lab-services bieden diverse kosten controles voor het beperken van het gebruik van Vm's. Als deze instellingen echter onjuist zijn geconfigureerd, kunnen ze ervoor zorgen dat de virtuele machines van uw Lab onverwacht worden afgesloten. Controleer de volgende instellingen om ervoor te zorgen dat deze instellingen op de juiste wijze zijn geconfigureerd voor uw hackathon:

    **Planning**: met een [schema](how-to-create-schedules.md) kunt u automatisch bepalen wanneer de computers van uw Labs worden gestart en afgesloten. Standaard wordt er geen schema geconfigureerd bij het maken van een nieuw lab. U moet er echter voor zorgen dat de planning van uw Lab is ingesteld op basis van wat zinvol is voor uw Hackathon.  Als uw hackathon bijvoorbeeld begint op zaterdag om 8:00 uur en eindigt op zondag om 5:00 uur: u kunt een planning maken waarmee de machine automatisch wordt gestart op 7:30 uur op zaterdag (ongeveer 30 minuten voor het begin van de hackathon) en deze op zondag wordt afgesloten op 5:00 uur. In plaats daarvan kunt u ook besluiten geen schema te gebruiken.

    **Quotum**: het [quotum](how-to-configure-student-usage.md#set-quotas-for-users) bepaalt het aantal uren dat deel nemers toegang hebben tot een virtuele machine buiten de geplande uren. Als het quotum is bereikt terwijl een deel nemer het gebruikt, wordt de computer automatisch afgesloten en kan de deel nemer deze niet opnieuw opstarten tenzij het quotum is verhoogd. Wanneer u een Lab maakt, wordt standaard het quotum ingesteld op 10 uur. Het is ook belang rijk dat u het quotum zo instelt dat het voldoende tijd heeft voor de Hackathon. Dit is met name het belangrijkst als u nog geen schema hebt gemaakt.

    Automatisch **Afsluiten**: wanneer deze optie is ingeschakeld, worden virtuele Windows-machines na een bepaalde periode in een [automatische afsluit modus](how-to-enable-shutdown-disconnect.md) afgesloten wanneer een student de verbinding met hun RDP-sessie heeft verbroken. Deze instelling is standaard uitgeschakeld.

- **Firewall instellingen configureren om verbindingen met virtuele lab-vm's toe te staan**. 

    Zorg ervoor dat de firewall instellingen van uw school of organisatie verbinding kunnen maken met Lab-Vm's met behulp van RDP\SSH. Raadpleeg voor meer informatie de [hand leiding voor het configureren van de firewall instellingen van uw netwerk](how-to-configure-firewall-settings.md).

- **Installeer RDP\SSH-client op de tablets, Macs, pc's,** enzovoort.

    Hackathon-deel nemers moeten een RDP-en/of SSH-client hebben geïnstalleerd op hun tablets of laptops die ze gebruiken om verbinding te maken met de virtuele machines van het lab. U kunt kiezen uit verschillende RDP-of SSH-clients, zoals:

    - De **verbinding met extern bureaublad** -app van micro soft voor RDP-verbindingen. De Verbinding met extern bureaublad-app wordt ondersteund op verschillende platformen, waaronder Chromebooks en [Mac](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162).
    - [Putty](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162) voor het gebruik van SSH om verbinding te maken met een virtuele Linux-machine.
- **Controleer de virtuele machines** van het lab. 

    Nadat u de Lab-Vm's hebt gepubliceerd, moet u controleren of deze juist zijn geconfigureerd. U hoeft deze verificatie alleen uit te voeren voor een van de virtuele machines van de deel nemer:

    1. Verbinding maken met behulp van RDP en/of SSH.
    2. Open elke extra toepassing en het hulp programma dat u hebt geïnstalleerd om de installatie kopie van de basis virtuele machine aan te passen.
    3. Door loop enkele basis scenario's die representatief zijn voor de activiteiten die deel nemers doen om ervoor te zorgen dat de prestaties van de virtuele machine adequaat zijn op basis van de geselecteerde reken grootte.

## <a name="on-the-day-of-hackathon"></a>Op de dag van hackathon
In deze sectie vindt u een overzicht van de stappen voor het volt ooien van de dag van uw Hackathon.

1. **Start de virtuele machines** van het lab.

    Afhankelijk van uw besturings systeem kan het starten van de test computer tot 30 minuten duren. Daarom is het belang rijk om machines te starten voordat de hackathon wordt gestart, zodat uw deel nemers niet hoeven te wachten. Als u een planning gebruikt, moet u ervoor zorgen dat de virtuele machines ook ten minste 30 minuten eerder worden gestart.
2. **Nodig studenten uit om hun Lab-virtuele machine te registreren en te openen**. 

    Geef uw deel nemers de volgende informatie zodat deel nemers toegang kunnen krijgen tot hun Lab-Vm's. 

    - De registratie koppeling van het lab. 
    - Referenties die moeten worden gebruikt om verbinding te maken met de computer. Deze stap is alleen van toepassing als uw Lab gebruikmaakt van een installatie kopie op basis van Windows en u alle Vm's hebt geconfigureerd voor het gebruik van hetzelfde wacht woord.
    - Instructies over hoe deel nemers SSH en/of RDP aan hun machines.

        Zie [instructies voor het verzenden van uitnodigingen voor gebruikers en het](how-to-configure-student-usage.md#send-invitations-to-users) [verbinden met virtuele Linux-machines](how-to-use-remote-desktop-linux-student.md)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen
Begin met het maken van een Lab-account in Labs door de volgende instructies te volgen in het artikel: [zelf studie: een Lab-account instellen met Azure Lab Services](tutorial-setup-lab-account.md).
