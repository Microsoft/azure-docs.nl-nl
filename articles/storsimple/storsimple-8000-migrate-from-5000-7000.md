---
title: Gegevens migreren op StorSimple 5000-7000-serie naar 8000-serie apparaat | Microsoft Docs
description: Meer informatie over het migreren van gegevens over de StorSimple 5000-7000-serie naar het apparaat 8000 Series en wat de vereisten zijn voor het migratie proces.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 58b7b67842e9ba385b34ea4d8fdbcac190076218
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428163"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Gegevens migreren van de StorSimple 5000-7000-serie naar het 8000-serie apparaat

> [!IMPORTANT]
> - Op 31 juli 2019 de StorSimple-serie van 5000/7000 om de status van het end of support (EOS) te bereiken. We raden aan dat klanten van de StorSimple 5000/7000-serie migreren naar een van de alternatieven die in het document worden beschreven.
> - Migratie is momenteel een ondersteunings bewerking. Als u van plan bent om gegevens te migreren van uw StorSimple 5000-7000 series-apparaat naar een 8000-serie apparaat, moet u de migratie plannen met Microsoft Ondersteuning. Microsoft Ondersteuning wordt vervolgens uw abonnement voor migratie ingeschakeld. Zie [een ondersteunings ticket openen](storsimple-8000-contact-microsoft-support.md)voor meer informatie.
> - Nadat u de service aanvraag hebt ingepland, kan het enkele weken duren om het migratie plan uit te voeren en de migratie daad werkelijk te starten.
> - Voordat u contact opneemt met Microsoft Ondersteuning, moet u de [vereiste migratie vereisten](#migration-prerequisites) in het artikel controleren en volt ooien.

## <a name="overview"></a>Overzicht

In dit artikel wordt de migratie functie geïntroduceerd waarmee klanten van de StorSimple 5000-7000-serie hun gegevens kunnen migreren naar een fysiek apparaat met StorSimple 8000 Series of een 8010/8020 Cloud Appliance. In dit artikel vindt u ook koppelingen naar een download bare stapsgewijze uitleg van de stappen die nodig zijn om gegevens te migreren van een verouderd apparaat van de 5000-7000-serie naar een fysiek of Cloud apparaat van een 8000-serie.

Dit artikel is van toepassing op zowel het on-premises 8000 Series-apparaat als de StorSimple Cloud Appliance.


## <a name="migration-feature-versus-host-side-migration"></a>Migratie functie versus migratie aan de host-zijde

U kunt uw gegevens verplaatsen met behulp van de migratie functie of door een migratie op de host uit te voeren. In deze sectie worden de specifieke voor delen van elke methode beschreven, met inbegrip van de voor-en nadelen. Gebruik deze informatie om te achterhalen welke methode u wilt gebruiken voor het migreren van uw gegevens.

De migratie functie simuleert een herstel na nood gevallen (DR) van de 7000/5000-serie tot 8000-serie. Met deze functie kunt u de gegevens van de 5000/7000-serie-indeling naar de 8000-indeling van Azure migreren. Het migratie proces wordt gestart met het hulp programma StorSimple Migration. Het hulp programma start het downloaden en de conversie van back-upmetagegevens op het apparaat van de 8000-serie, waarna de meest recente back-up wordt gebruikt om de volumes op het apparaat beschikbaar te stellen.

| Voordelen                                                                                                                                     |Nadelen                                                                                                                                                              |
|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Het migratie proces behoudt de geschiedenis van back-ups die zijn gemaakt op de 5000/7000-serie.                                               | Wanneer gebruikers toegang proberen te krijgen tot de gegevens, downloadt deze migratie de gegevens van Azure, waardoor de kosten voor het downloaden van gegevens worden bespaard.                                     |
| Er worden geen gegevens gemigreerd naar de host.                                                                                                     | Het proces moet downtime hebben tussen het begin van de back-up en de laatste back-up die wordt weer gegeven in de 8000-serie (kan worden geschat met het migratie hulpprogramma). |
| Met dit proces worden alle beleids regels, bandbreedte sjablonen, versleuteling en andere instellingen op apparaten uit de 8000-serie bewaard.                      | Gebruikers toegang brengt alleen de gegevens die toegankelijk zijn voor gebruikers, en de volledige gegevensset worden niet opnieuw gehydrateerd.                                                  |
| Dit proces vereist extra tijd voor het converteren van alle oudere back-ups in azure die asynchroon worden uitgevoerd, zonder dat dit van invloed is op de productie | Migratie kan alleen worden uitgevoerd op een Cloud configuratie niveau.  Afzonderlijke volumes in een Cloud configuratie kunnen niet afzonderlijk worden gemigreerd                       |

Met een migratie aan de kant van een host kan de 8000-serie onafhankelijk van elkaar worden ingesteld en worden de gegevens van het apparaat van de 5000/7000-serie naar 8000 Series gekopieerd. Dit komt overeen met het migreren van gegevens van het ene opslag apparaat naar het andere. Diverse hulpprogram ma's, zoals Diskboss, Robocopy, worden gebruikt voor het kopiëren van de gegevens.

| Voordelen                                                                                                                      |Nadelen                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Migratie kan op basis van het volume per volume worden geaanpakd.                                               | Eerdere back-ups (gemaakt op 5000/7000 serie) zijn niet beschikbaar op het apparaat met de 8000-serie.                                                                                                       |
| Hiermee wordt het samen voegen van gegevens in één opslag account in azure toegestaan.                                                       | De eerste back-up naar de Cloud op 8000 Series neemt langer tijd in beslag omdat alle gegevens op de 8000-serie moeten worden opgeslagen in Azure.                                                                     |
| Na een geslaagde migratie worden alle gegevens op het apparaat lokaal opgeslagen. Er zijn geen vertragingen bij het openen van de gegevens. | Het gebruik van Azure-opslag neemt toe totdat de gegevens worden verwijderd van het 5000/7000-apparaat.                                                                                                        |
|                                                                                                                           | Als het 7000/5000 Series-apparaat een grote hoeveelheid gegevens heeft, moet deze gegevens tijdens de migratie worden gedownload van Azure, waardoor kosten en latenties zijn gerelateerd aan het downloaden van gegevens van Azure |

Dit artikel richt zich alleen op de migratie functie van 5000/7000 naar 8000 Series-apparaat. Ga voor meer informatie over migratie aan de host-zijde naar [migratie van andere opslag apparaten](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Migratie vereisten

Hier vindt u de migratie vereisten voor uw oude 5000-of 7000-serie apparaat en het StorSimple-apparaat van de 8000-serie.

> [!IMPORTANT]
> Controleer de vereisten voor migratie en Voltooi deze voordat u een service aanvraag met Microsoft Ondersteuning.

### <a name="for-the-50007000-series-device-source"></a>Voor het 5000/7000-serie apparaat (bron)

Voordat u met de migratie begint, moet u ervoor zorgen dat:

* U hebt uw 5000-of 7000-serie bron apparaat; het apparaat kan live of down zijn.

    > [!IMPORTANT]
    > U wordt aangeraden seriële toegang tot dit apparaat te krijgen tijdens het migratie proces. Als er problemen zijn met apparaten, kan seriële toegang helpen bij het oplossen van problemen.

* Op het bron apparaat van de 5000-of 7000-serie wordt software versie v 2.1.1.518 of hoger uitgevoerd. Eerdere versies worden niet ondersteund.
* Als u wilt controleren of de versie van de 5000-of 7000-serie wordt uitgevoerd, kijkt u in de rechter bovenhoek van de webinterface van uw web. Hiermee wordt de software versie weer gegeven die op uw apparaat wordt uitgevoerd. Voor de migratie moet uw 5000-of 7000-serie de v-2.1.1.518 uitvoeren.

    ![Controleer de software versie op een verouderd apparaat](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Als op uw Live-apparaat geen v 2.1.1.518 of hoger wordt uitgevoerd, voert u een upgrade van uw systeem uit naar de vereiste minimale versie. U moet mogelijk met Microsoft Ondersteuning werken om u te helpen bij het uitvoeren van de upgrade.
    * Als u v 2.1.1.518 uitvoert, gaat u naar de Web-UI om te zien of er meldingen zijn voor het herstellen van fouten in het REGI ster. Als het herstellen van het REGI ster is mislukt, voert u de register herstel bewerking uit. U moet mogelijk met Microsoft Ondersteuning werken om uw REGI ster te herstellen.
    * Als u een lager apparaat hebt waarop geen v-2.1.1.518 werd uitgevoerd, voert u een failover uit naar een vervangend apparaat met v 2.1.1.518. Raadpleeg DR van uw 5000/7000 series StorSimple-apparaat voor gedetailleerde instructies.
    * Maak een back-up van de gegevens voor uw apparaat door een Cloud momentopname te maken.
    * Controleer op andere actieve back-uptaken die worden uitgevoerd op het bron apparaat. Dit omvat de taken op de StorSimple Data Protection-host van de console. Wacht totdat de huidige taken zijn voltooid.


### <a name="for-the-8000-series-physical-device-target"></a>Voor het fysieke apparaat van de 8000-serie (doel)

Voordat u met de migratie begint, moet u ervoor zorgen dat:

* Het apparaat van de doel 8000 serie is geregistreerd en wordt uitgevoerd. Zie [uw StorSimple-apparaat implementeren met StorSimple Manager service](storsimple-8000-deployment-walkthrough-u2.md)voor meer informatie.
* Op het apparaat met de 8000-serie is de meest recente StorSimple 8000 Series Update 5 geïnstalleerd en wordt 6.3.9600.17845 of hoger uitgevoerd. Als de nieuwste updates niet op uw apparaat zijn geïnstalleerd, moet u de meest recente updates installeren voordat u kunt door gaan met de migratie. Zie [de nieuwste update installeren op uw 8000-serie apparaat](storsimple-8000-install-update-5.md)voor meer informatie.
* Uw Azure-abonnement is ingeschakeld voor migratie. Als uw abonnement niet is ingeschakeld, neemt u contact op met Microsoft Ondersteuning om uw abonnement voor migratie in te scha kelen.

### <a name="for-the-80108020-cloud-appliance-target"></a>Voor het 8010/8020-Cloud apparaat (doel)

Voordat u met de migratie begint, moet u het volgende controleren:

* Uw doel-Cloud apparaat is geregistreerd en wordt uitgevoerd. Zie [StorSimple Cloud Appliance implementeren en beheren](storsimple-8000-cloud-appliance-u2.md)voor meer informatie.
* Uw Cloud apparaat voert de meest recente StorSimple 8000 Series Update 5-software versie 6.3.9600.17845. Als op uw Cloud apparaat Update 5 niet wordt uitgevoerd, maakt u een nieuw update 5-Cloud apparaat voordat u doorgaat met de migratie. Zie How to [Create a 8010/8020 Cloud Appliance](storsimple-8000-cloud-appliance-u2.md)(Engelstalig) voor meer informatie.

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Voor de computer met het hulp programma StorSimple Migration

Het hulp programma StorSimple Migration is een UI-hulp programma waarmee u gegevens kunt migreren van een StorSimple 5000-7000-serie naar een 8000 Series-apparaat. Als u het hulp programma StorSimple Migration wilt installeren, gebruikt u een computer die voldoet aan de volgende vereisten.

De computer heeft Internet verbinding en:

* Voert het volgende besturings systeem uit
    * Windows 10.
    * Windows Server 2012 R2 (of hoger) om het hulp programma StorSimple Migration te installeren.
* .NET 4.5.2 is geïnstalleerd.
* Er is mini maal 5 GB beschik bare ruimte beschikbaar voor de installatie en het gebruik van het hulp programma.

> [!TIP]
> Als uw StorSimple-apparaat is verbonden met een Windows Server-host, kunt u het hulp programma voor migratie op de Windows Server-hostcomputer installeren.

#### <a name="to-install-storsimple-migration-tool"></a>Het hulp programma StorSimple Migration installeren

Voer de volgende stappen uit om het hulp programma StorSimple Migration op uw computer te installeren.

1. Kopieer de map _StorSimple8000SeriesMigrationTool_ naar uw Windows-computer. Zorg ervoor dat er voldoende ruimte is op het station waar de software is gekopieerd.

    Open het configuratie bestand van het hulp programma _StorSimple8000SeriesMigrationTool.exe.config_ in de map. Dit is het fragment van het bestand.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Bewerk de waarden die overeenkomen met de sleutels en vervang door:

    * `UserName` – Gebruikers naam om u aan te melden bij Azure Portal.
    * `SubscriptionName and SubscriptionId` – Naam en ID voor uw Azure-abonnement. Klik in de StorSimple Apparaatbeheer service landings pagina onder **Algemeen** op **Eigenschappen**. Kopieer de naam van het abonnement en de abonnements-ID die is gekoppeld aan uw service.
    * `ResourceName` : De naam van uw StorSimple-Apparaatbeheer-service in de Azure Portal. Ook weer gegeven onder service-eigenschappen.
    * `ResourceGroup` : De naam van de resource groep die is gekoppeld aan uw StorSimple-Apparaatbeheer service in de Azure Portal. Ook weer gegeven onder service-eigenschappen.
    ![Service-eigenschappen voor doel apparaat controleren](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` -Azure Active Directory Tenant-ID in Azure Portal. Meld u als beheerder aan bij Microsoft Azure. Klik in het Microsoft Azure-portal op **Azure Active Directory**. Klik onder **Beheren** op **Eigenschappen**. De Tenant-ID wordt weer gegeven in het vak **Directory-id** .
    ![Tenant-ID controleren voor Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Sla de wijzigingen in het configuratie bestand op.
4.  Voer de _StorSimple8000SeriesMigrationTool.exe_ uit om het hulp programma te starten. Wanneer u om referenties wordt gevraagd, geeft u de referenties op die zijn gekoppeld aan uw abonnement in Azure Portal. 
5.  De gebruikers interface van het hulp programma voor migratie van StorSimple wordt weer gegeven.
  

## <a name="next-steps"></a>Volgende stappen
Down load de stapsgewijze instructies voor het [migreren van gegevens uit een StorSimple 5000-7000-serie naar een 8000 Series-apparaat](https://databoxbuilds.blob.core.windows.net/storsimpledocs/MicrosoftAzureStorSimple_Migration_Guide_20171115.pdf).
