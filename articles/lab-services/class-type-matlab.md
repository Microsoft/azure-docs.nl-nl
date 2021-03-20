---
title: Stel een lab in om MATLAB te leren met Azure Lab Services | Microsoft Docs
description: Meer informatie over het instellen van een lab om MATLAB te leren met Azure Lab Services.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 137959f51b08dceee150962f77110ee2ac1dc193
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85444995"
---
# <a name="setup-a-lab-to-teach-matlab"></a>Een Lab instellen om MATLAB te leren

[MATLAB](https://www.mathworks.com/products/matlab.html), die staat voor matrix laboratorium, bevindt zich in het programmeer platform van [MathWorks](https://www.mathworks.com/).  Het combineert reken kracht en visualisatie die het populaire hulp programma maakt in de velden van wiskunde, techniek, natuur kunde en schei kunde.

Als u een licentie voor [universiteits](https://www.mathworks.com/academia/tah-support-program/administrators.html)gebruik gebruikt, raadpleegt u instructies bij het [downloaden van MATLAB-installatie bestanden](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine) om de MATLAB-installatie bestanden op de sjabloon machine te downloaden.  

In dit artikel laten we zien hoe u een klasse instelt die gebruikmaakt van MATLAB-client software met een licentie server.

## <a name="license-server"></a>Licentie server

Voordat u de sjabloon machine voor uw Lab wijzigt, moet u de server instellen voor het uitvoeren van de software voor [netwerk licentie beheer](https://www.mathworks.com/help/install/administer-network-licenses.html) .  Deze instructies zijn alleen van toepassing op instellingen die de netwerk licentie optie voor MATLAB kiezen, waarmee gebruikers een groep licentie sleutels kunnen delen.  U moet ook het licentie bestand en de installatie sleutel voor het bestand opslaan voor later.  Zie voor gedetailleerde instructies voor het downloaden van een licentie bestand de eerste stap in het artikel [netwerk licentie beheer installeren met Internet verbinding](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html) .

Gedetailleerde instructies voor het installeren van een licentie server zijn beschikbaar op [netwerk licentie beheer installeren met Internet verbinding](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html).  Als u lenen wilt inschakelen, raadpleegt u het artikel over de [lenende licentie](https://www.mathworks.com/help/install/license/borrow-licenses.html) .

Als de licentie server zich in een on-premises netwerk of in een particulier netwerk bevindt in azure, vergeet dan niet om [het virtuele netwerk](how-to-connect-peer-virtual-network.md) te koppelen aan uw [Lab-account](tutorial-setup-lab-account.md).  De netwerk peering moet worden uitgevoerd voordat u het Lab maakt, zodat de virtuele machines van het lab toegang hebben tot de licentie server.

## <a name="lab-configuration"></a>Lab-configuratie

Als u dit Lab wilt instellen, hebt u een Azure-abonnement nodig om aan de slag te gaan.  Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. Zodra u een Azure-abonnement hebt ontvangen, kunt u een nieuw Lab-account maken in Azure Lab Services of een bestaand account gebruiken.  Als u een nieuw Lab-account wilt maken, raadpleegt u de [zelf studie een Lab-account instellen](tutorial-setup-lab-account.md).

Als u een nieuw Lab wilt maken, volgt u [de zelf studie Stel een klas lokaal](tutorial-setup-classroom-lab.md)in.  Pas de volgende instellingen toe:

| Grootte van de virtuele machine | Installatiekopie |
| -------------------- | ----- |
| Normaal | Windows 10 |

MATLAB wordt ondersteund op meer besturings systemen.  Zie [MATLAB-systeem vereisten](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) voor meer informatie.

> [!WARNING]
> Vergeet niet om het [virtuele netwerk](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) voor het lab-account te koppelen aan het virtuele netwerk van de licentie server voordat u het Lab maakt.

## <a name="template-machine"></a>Sjabloon machine

Nadat de sjabloon machine is gemaakt, start u de machine en maakt u er verbinding mee om de volgende belang rijke taken uit te voeren.

1. Down load de installatie bestanden voor de MATLAB-client software.
2. Installeer MATLAB met de bestands installatie sleutel.

Het installeren van MATLAB is een proces met meerdere onderdelen.  Met het eerste deel worden de bestanden gedownload voor MATLAB en eventuele andere producten die u wilt installeren.  Voor het gebruik van een bestands installatie sleutel moeten alle installatie bestanden voor te installeren producten vooraf worden gedownload.  Het tweede deel wordt de MATLAB-software geïnstalleerd op de sjabloon-VM en de software te activeren.  Als de sjabloon-VM is geconfigureerd om te activeren met behulp van de licentie server, worden de student-Vm's hetzelfde.

### <a name="download-installation-files"></a>Installatie bestanden downloaden

U moet een licentie beheerder zijn om de installatie bestanden te downloaden en het licentie bestand en de installatie sleutel voor het bestand op te halen.  Hieronder vindt u de stappen voor het downloaden van de installatie bestanden.

1. Meld u aan bij uw account voor [https://www.mathworks.com](https://www.mathworks.com) .
2. Kies **Mijn account**.
3. Klik in de sectie **mijn software** van de pagina account op de licentie die is gekoppeld aan de installatie van het netwerk licentie beheer voor het lab.
4. Klik op de pagina licentie details op **producten downloaden**.
5. Wacht totdat het installatie programma zelf is uitgepakt.
6. Start het installatieprogramma.  
7. Voer uw MathWorks-account in op de pagina **Aanmelden bij uw MathWorks-account** .
8. Accepteer de voor waarden op de pagina **gebruiksrecht overeenkomst** van de MathWorks en klik op de knop **volgende** .
9. Klik op de vervolg keuzelijst **Geavanceerde opties** en kies **Ik wil downloaden zonder te installeren**.
10. Klik op **volgende** in de **doelmap selecteren**.
11. Selecteer **Windows** als het platform van de computer waarop u MATLAB wilt installeren.
12. Zorg ervoor dat op de pagina **product selecteren de optie** MATLAB is geselecteerd samen met alle andere MathWorks-producten die u wilt installeren.
13. Klik op de pagina **selecties bevestigen en downloaden** op **downloaden**.  
14. Wacht tot de geselecteerde producten zijn gedownload.  Klik op **Voltooien**.

U kunt ook een ISO-installatie kopie downloaden van de MathWorks-website.

1. Meld u aan bij uw account voor [https://www.mathworks.com](https://www.mathworks.com) .
2. Ga naar [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads).
3. Selecteer de release van MATLAB die u wilt installeren.
4. Klik op de koppeling {version}. ISO-installatie kopie ophalen onder de gerelateerde koppelingen waarbij {version} iets als R2020a is.
5. Klik op de koppeling Blue **down load release** voor Windows.

### <a name="run-installer"></a>Installatie programma uitvoeren

Zodra de bestanden zijn gedownload, wordt de tweede stap uitgevoerd om het installatie programma uit te voeren. Eenmaal opnieuw moet u een licentie beheerder zijn om deze stap te volt ooien.  Alleen licentie beheerders kunnen MATLAB installeren met een bestands installatie sleutel.

1. Controleer het gedownloade licentie bestand en controleer of de SERVER regel de licentie server correct vermeldt.  Zie voor meer informatie over het format teren van het licentie bestand, de [Update netwerk licentie](https://www.mathworks.com/help/install/ug/network-license-files.html), het [lenen van licenties](https://www.mathworks.com/help/install/license/borrow-licenses.html)en het [vinden van host-id-](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license) artikelen.
2. Start het MATLAB-installatie programma.
3. Voer uw MathWorks-account in op de pagina **Aanmelden bij uw MathWorks-account** .
4. Accepteer de voor waarden op de pagina **gebruiksrecht overeenkomst** van de MathWorks en klik op de knop **volgende** .
5. Klik op de vervolg keuzelijst **Geavanceerde opties** en kies **Ik heb een installatie sleutel voor het bestand**.
6. Voer op de pagina **installatie met behulp van bestands installatie** de bestands installatie sleutel in voor de licentie server.   Klik op **Volgende**.
7. Ga op de pagina **licentie bestand selecteren** naar het licentie bestand dat is opgeslagen bij het downloaden van de installatie bestanden die u eerder hebt gedownload.
8. Klik op de pagina **doelmap selecteren** op **volgende**.
9. Klik op de pagina **producten selecteren** op **volgende**.
10. Klik op de pagina **opties selecteren** op **volgende**.
11. Klik op de pagina **selecties bevestigen en installatie** op **installatie starten**.
12. Controleer op de pagina **installatie voltooid** de optie **MATLAB activeren** is ingeschakeld.  Klik op **Voltooien**.

## <a name="cost-estimate"></a>Kosten raming

Laten we een mogelijke schatting van de kosten voor deze klasse beslaan.  Deze schatting omvat niet de kosten voor het uitvoeren van de licentie server.  We gebruiken een klasse van 25 studenten.  Er zijn 20 uur geplande tijd voor de klasse.  Daarnaast krijgt elke student tien uur quota voor huis werk of toewijzingen buiten een geplande klasse tijd.  Het formaat van de virtuele machine die we hebben gekozen is gemiddeld, dat wil zeggen 55 Lab-eenheden.

Hier volgt een voor beeld van een mogelijke schatting van de kosten voor deze klasse:

25 studenten \* (20 geplande uren + 10 quotum uren) \* 55 Lab-eenheden \*  0,01 USD per uur = 412,50 USD

>[!IMPORTANT]
> Kosten raming is alleen bedoeld als voor beeld.  Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor actuele Details over prijzen.  

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van elk lab.

- [Een sjabloon maken, beheren en publiceren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)
