---
title: StorSimple Cloud-cloudapparaat Update 3| Microsoft Docs
description: Informatie over het maken, implementeren en beheren van een StorSimple-cloudapparaat in een Microsoft Azure Virtual Network. (Van toepassing op StorSimple Update 3 en hoger).
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2017
ms.author: alkohli
ms.openlocfilehash: d78051c1a5af82a986152c8244d25b68dd65d552
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968839"
---
# <a name="deploy-and-manage-a-storsimple-cloud-appliance-in-azure-update-3-and-later"></a>Een StorSimple-cloudapparaat implementeren en beheren in Azure (Update 3 en hoger)

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht

De cloudapparaten van de serie StorSimple 8000 dienen ter aanvulling op de Microsoft Azure StorSimple-oplossing. Het StorSimple-cloudapparaat wordt uitgevoerd op een virtuele machine in een Microsoft Azure Virtual Network. U kunt het gebruiken om back-ups te maken van gegevens en om gegevens van uw hosts te klonen.

In dit artikel wordt stapsgewijs beschreven hoe u een StorSimple-cloudapparaat implementeert en beheert in Azure. Wanneer u dit artikel hebt gelezen:

* Begrijpt u waarin het cloudapparaat verschilt van het fysieke apparaat.
* Kunt u het cloudapparaat maken en configureren.
* Kunt u verbinding maken met het cloudapparaat.
* Kunt u met het cloudapparaat werken.

Deze zelfstudie is van toepassing op alle StorSimple-cloudapparaten met Update 3 en hoger.

#### <a name="cloud-appliance-model-comparison"></a>Vergelijking van cloudapparaatmodellen

Het StorSimple-cloudapparaat is beschikbaar in twee modellen: de Standard 8010 (voorheen bekend als de 1100) en de Premium 8020 (geïntroduceerd bij Update 2). De volgende tabel geeft een vergelijking van de twee modellen.

| Apparaatmodel | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Maximale capaciteit** |30 TB |64 TB |
| **Azure VM** |Standard_A3 (4 kerngeheugens, 7 GB geheugen)| Standard_DS3 (4 kerngeheugens, 14 GB geheugen)|
| **Beschikbaarheid in regio’s** |Alle Azure-regio's |Azure-regio's waar ondersteuning wordt geboden voor Premium Storage en Azure-VM’s met DS3<br></br>Gebruik [deze lijst](https://azure.microsoft.com/regions/services/) om te kijken of zowel **Virtuele Machines > DS-serie** als **Opslag > Schijfruimte** beschikbaar is in uw regio. |
| **Opslagtype** |Maakt gebruik van Azure Standard-opslag voor lokale schijven<br></br> Ontdek hoe u [een Standard-opslagaccount maakt](../storage/common/storage-account-create.md) |Maakt gebruik van Azure Premium Storage voor lokale schijven<sup>2</sup> <br></br> |
| **Richtlijnen voor de workload** |Bestanden ophalen uit back-ups op itemniveau |Ontwikkelings- en testscenario’s voor cloudapparaten <br></br>Lage latentie en workloads met hogere prestaties<br></br>Secundair apparaat voor herstel na noodgevallen |

<sup>1</sup> *Voorheen bekend als de 1100*.

<sup>2</sup> *zowel de 8010 als de 8020 gebruiken de Azure Standard-opslag voor de Cloud-laag. Het verschil bestaat alleen in de lokale laag in het apparaat*.

## <a name="how-the-cloud-appliance-differs-from-the-physical-device"></a>Hoe het cloudapparaat verschilt van het fysieke apparaat

Het StorSimple-cloudapparaat is een versie van StorSimple die alleen uit software bestaat en wordt uitgevoerd op één knooppunt in een virtuele Microsoft Azure-machine. Het cloudapparaat biedt ondersteuning voor herstel na noodgevallen waarbij uw fysieke apparaat niet beschikbaar is. Het cloudapparaat kan worden gebruikt om op itemniveau bestanden te herstellen van back-ups, voor on-premises herstel na noodgevallen, en voor cloudontwikkelings- en testscenario’s.

#### <a name="differences-from-the-physical-device"></a>Verschillen met het fysieke apparaat

In de volgende tabel ziet u enkele belangrijke verschillen tussen het StorSimple-cloudapparaat en het fysieke StorSimple-apparaat.

|  | Fysiek apparaat | Cloudapparaat |
| --- | --- | --- |
| **Locatie** |Bevindt zich in het datacenter. |Wordt uitgevoerd in Azure. |
| **Netwerkinterfaces** |Heeft zes netwerkinterfaces: DATA 0 t/m DATA 5. |Heeft slechts één netwerkinterface: DATA 0 |
| **Registratie** |Geregistreerd tijdens de initiële configuratiestap. |Registratie is een afzonderlijke taak. |
| **Versleutelings sleutel voor service gegevens** |Genereer de sleutel opnieuw op het fysieke apparaat en werk het cloudapparaat daarna bij met de nieuwe sleutel. |Het is niet mogelijk om sleutels opnieuw te genereren op het cloudapparaat. |
| **Ondersteunde volumetypen** |Ondersteunt zowel lokaal vastgemaakte als gelaagde volumes. |Ondersteunt alleen gelaagde volumes. |

## <a name="prerequisites-for-the-cloud-appliance"></a>Vereisten voor het cloudapparaat

In de volgende gedeelten worden de configuratievereisten voor het StorSimple-cloudapparaat toegelicht. Voordat u een cloudapparaat implementeert, moet u de beveiligingsoverwegingen voor het gebruik van een cloudapparaat controleren.

[!INCLUDE [StorSimple Cloud Appliance security](../../includes/storsimple-8000-cloud-appliance-security.md)]

#### <a name="azure-requirements"></a>Azure-vereisten

Voordat u het cloudapparaat inricht, moet u de volgende voorbereidingen treffen in uw Azure-omgeving:

* Zorg ervoor dat er een fysiek StorSimple-apparaat uit de 8000-serie (model 8100 of 8600) is geïmplementeerd en wordt uitgevoerd in uw datacenter. Registreer dit apparaat bij dezelfde StorSimple-apparaatbeheerservice als waarvoor u een StorSimple-Cloud-apparaat wilt maken.
* Voor het cloudapparaat [configureert u een virtueel netwerk in Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Als u Premium-opslag gebruikt, moet u een virtueel netwerk maken in een Azure-regio die ondersteuning biedt voor Premium-opslag. De Premium Storage-regio's zijn regio's die overeenkomen met de rij voor Schijfruimte in de [lijst met Azure Services per regio](https://azure.microsoft.com/regions/services/).
* Het is raadzaam om de standaard-DNS-server van Azure te gebruiken. Geef liever geen eigen naam op voor de DNS-server. Als uw DNS-servernaam niet geldig is of als de DNS-server IP-adressen niet correct kan omzetten, mislukt het maken van het cloudapparaat.
* Punt-naar-site en site-naar-site zijn optioneel, maar niet vereist. Als u wilt, kunt u deze opties configureren in meer geavanceerde scenario's.
* U kunt [virtuele Azure-machines](../virtual-machines/windows/quick-create-portal.md) (hostservers) maken in het virtuele netwerk; deze maken gebruik van de volumes die beschikbaar worden gesteld door het cloudapparaat. Deze servers moeten aan de volgende vereisten voldoen:

  * Het moeten virtuele Windows- of Linux-machines zijn waarop de iSCSI-initiatorsoftware is geïnstalleerd.
  * Ze moeten in hetzelfde virtuele netwerk worden uitgevoerd als het cloudapparaat.
  * Ze moeten in staat zijn om verbinding te maken met het iSCSI-doel van het cloudapparaat via het interne IP-adres van het cloudapparaat.
  * Zorg ervoor dat u ondersteuning voor iSCSI en cloudverkeer hebt geconfigureerd in hetzelfde virtuele netwerk.

#### <a name="storsimple-requirements"></a>StorSimple-vereisten

Breng de volgende updates aan in uw StorSimple-apparaatbeheerservice voordat u een cloudapparaat maakt:

* Voeg [Access Control Records](storsimple-8000-manage-acrs.md) toe voor de virtuele machines die gebruikt gaan worden als hostservers voor uw cloudapparaat.
* Gebruik een [opslagaccount](storsimple-8000-manage-storage-accounts.md#add-a-storage-account) in dezelfde regio als het cloudapparaat. Als u opslagaccounts in andere regio's gebruikt, kan dat leiden tot slechte prestaties. U kunt met het cloudapparaat een Standard- of Premium-opslagaccount gebruiken. Meer informatie over hoe u een [Standard Storage-account](../storage/common/storage-account-create.md) maakt.
* Gebruik een ander opslagaccount voor het maken van het cloudapparaat dan voor het apparaat dat u gebruikt voor uw gegevens. Als u hetzelfde opslagaccount gebruikt, kan dat leiden tot slechte prestaties.

Zorg ervoor dat u over de volgende informatie beschikt voordat u begint:

* Uw account voor Azure Portal met referenties voor toegang.
* Een kopie van de versleutelingssleutel voor servicegegevens van uw fysieke apparaat wordt geregistreerd bij de StorSimple-apparaatbeheerservice.

## <a name="create-and-configure-the-cloud-appliance"></a>Het cloudapparaat maken en configureren

Zorg er voordat u deze procedures uitvoert voor dat u voldoet aan de [vereisten voor het cloudapparaat](#prerequisites-for-the-cloud-appliance).

Voer de volgende stappen uit om het StorSimple-cloudapparaat te maken.

### <a name="step-1-create-a-cloud-appliance"></a>Stap 1: Een cloudapparaat maken

Voer de volgende stappen uit om het StorSimple-cloudapparaat te maken.

[!INCLUDE [Create a cloud appliance](../../includes/storsimple-8000-create-cloud-appliance-u2.md)]

Als het maken van het cloudapparaat in deze stap mislukt, hebt u mogelijk geen verbinding met internet. Ga voor meer informatie naar [Problemen met internetverbinding oplossen](#troubleshoot-internet-connectivity-errors) wanneer u een cloudapparaat maakt.

### <a name="step-2-configure-and-register-the-cloud-appliance"></a>Stap 2: het cloudapparaat configureren en registreren

Voordat u aan deze procedure begint, zorgt u ervoor dat u een kopie van de gegevensversleutelingssleutel van de service hebt. De versleutelingssleutel voor servicegegevens wordt gemaakt wanneer u uw eerste fysieke StorSimple-apparaat hebt geregistreerd bij de StorSimple-apparaatbeheerservice. U krijgt de instructie deze op een veilige locatie op te slaan. Als u geen kopie van de gegevensversleutelingssleutel van de service hebt, moet u contact opnemen met Microsoft Ondersteuning voor hulp.

Voer de volgende stappen uit om het StorSimple-cloudapparaat te configureren en te registreren.

[!INCLUDE [Configure and register a cloud appliance](../../includes/storsimple-8000-configure-register-cloud-appliance.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Stap 3: (optioneel) de configuratie-instellingen van het apparaat wijzigen

In het volgende gedeelte wordt beschreven welke apparaatconfiguratie-instellingen vereist zijn voor het StorSimple-cloudapparaat als u CHAP of StorSimple Snapshot Manager wilt gebruiken of als u het beheerderswachtwoord van het apparaat wilt wijzigen.

#### <a name="configure-the-chap-initiator"></a>De CHAP-initiator configureren

Deze parameter bevat de referenties die uw cloudapparaat (doel) verwacht te ontvangen van de initiators (servers) die proberen de volumes te openen. De initiators bieden een CHAP-gebruikersnaam en een CHAP-wachtwoord om zichzelf tijdens de verificatie te identificeren bij uw apparaat. Zie [CHAP configureren voor uw apparaat](storsimple-8000-configure-chap.md#unidirectional-or-one-way-authentication) voor gedetailleerde stappen.

#### <a name="configure-the-chap-target"></a>Het CHAP-doel configureren

Deze parameter bevat de referenties die uw cloudapparaat gebruikt wanneer een CHAP-initiator wederzijdse verificatie of verificatie in twee richtingen aanvraagt. Uw cloudapparaat maakt gebruik van een omgekeerde CHAP-gebruikersnaam en een omgekeerd CHAP-wachtwoord om zich tijdens het verificatieproces bij de initiator te identificeren.

> [!NOTE]
> De CHAP-doelinstellingen zijn algemene instellingen. Wanneer deze instellingen worden toegepast, wordt CHAP-verificatie gebruikt voor alle volumes die zijn verbonden met het cloudapparaat.

Zie [CHAP configureren voor uw apparaat](storsimple-8000-configure-chap.md#bidirectional-or-mutual-authentication) voor gedetailleerde stappen.

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Het wachtwoord voor StorSimple Snapshot Manager configureren

De StorSimple Snapshot Manager-software bevindt zich op uw Windows-host. Met deze software kunnen beheerders de back-ups van uw StorSimple-apparaat beheren. De back-ups zijn in feite momentopnamen die lokaal en in de cloud worden opgeslagen.

> [!NOTE]
> Voor het cloudapparaat is uw Windows-host een virtuele Azure-machine.

Wanneer u in StorSimple Snapshot Manager een apparaat configureert, wordt u gevraagd om het IP-adres en het wachtwoord van het StorSimple-apparaat op te geven voor verificatie van uw opslagapparaat. Zie [Het wachtwoord voor StorSimple Snapshot Manager configureren](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password) voor gedetailleerde stappen.

#### <a name="change-the-device-administrator-password"></a>Het beheerderswachtwoord voor het apparaat wijzigen

Als u de Windows PowerShell-interface gebruikt voor toegang tot het cloudapparaat, moet u een beheerderswachtwoord voor het apparaat invoeren. Voor de beveiliging van uw gegevens moet u dit wachtwoord wijzigen voordat u het cloudapparaat kunt gebruiken. Zie [Het beheerderswachtwoord voor het apparaat configureren](../storsimple/storsimple-8000-change-passwords.md#change-the-device-administrator-password) voor gedetailleerde stappen.

## <a name="connect-remotely-to-the-cloud-appliance"></a>Extern verbinding maken met het cloudapparaat

Externe toegang tot uw cloudapparaat via de Windows PowerShell-interface is niet standaard ingeschakeld. U moet eerst extern beheer inschakelen op het cloudapparaat en vervolgens op de client die wordt gebruikt voor toegang tot het cloudapparaat.

De volgende procedure in twee stappen beschrijft hoe u op afstand verbinding maakt met uw cloudapparaat.

### <a name="step-1-configure-remote-management"></a>Stap 1: extern beheer configureren

Voer de volgende stappen uit om extern beheer te configureren voor uw StorSimple-cloudapparaat.

[!INCLUDE [Configure remote management via HTTP for cloud appliance](../../includes/storsimple-8000-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-cloud-appliance"></a>Stap 2: extern toegang verkrijgen tot het cloudapparaat

Nadat u extern beheer op het cloudapparaat hebt ingeschakeld, gebruikt u Windows PowerShell op afstand om verbinding maken met het apparaat vanaf een andere virtuele machine binnen hetzelfde virtuele netwerk. U kunt bijvoorbeeld verbinding maken vanaf de virtuele hostmachine die u hebt geconfigureerd en gebruikt om verbinding maken met iSCSI. Bij de meeste implementaties hebt u al een openbaar eindpunt geopend voor toegang tot uw host-VM; deze virtuele machine kunt u vervolgens gebruiken voor toegang tot het cloudapparaat.

> [!WARNING]
> **Voor een betere beveiliging wordt aangeraden HTTPS te gebruiken bij het verbinden met de eindpunten en om de eindpunten te verwijderen nadat u uw externe PowerShell-sessie hebt voltooid.**

U moet de procedures in [Op afstand verbinding maken met uw StorSimple-apparaat](storsimple-8000-remote-connect.md) volgen voor het instellen van externe toegang tot uw cloudapparaat.

## <a name="connect-directly-to-the-cloud-appliance"></a>Rechtstreeks verbinding maken met het cloudapparaat

U kunt ook rechtstreeks verbinding maken met het cloudapparaat. Als u rechtstreeks verbinding wilt maken met het cloudapparaat vanaf een andere computer buiten het virtuele netwerk of buiten de Microsoft Azure-omgeving, moet u extra eindpunten maken.

Voer de volgende stappen uit om een openbaar eindpunt te maken op het cloudapparaat.

[!INCLUDE [Create public endpoints on a cloud appliance](../../includes/storsimple-8000-create-public-endpoints-cloud-appliance.md)]

U doet er verstandig aan om verbinding te maken via een andere virtuele machine in hetzelfde virtuele netwerk, omdat er dan in uw virtuele netwerk een minimaal aantal openbare eindpunten nodig is. Maak in dit geval verbinding met de virtuele machine via een extern bureaublad. Daarna configureert u de virtuele machine voor gebruik, net zoals u dat bij andere Windows-clients zou doen in een lokaal netwerk. U hoeft het openbare-poortnummer niet toe te voegen omdat de poort al bekend is.

## <a name="get-private-ip-for-the-cloud-appliance"></a>Privé IP-adres voor het cloudapparaat ophalen

U hebt het interne of privé IP-adres van het cloudapparaat nodig om dit apparaat te verbinden met de hostserver in hetzelfde virtuele netwerk. Voer de volgende stappen uit om het privé IP-adres van het cloudapparaat op te halen

1. Ga naar de onderliggende virtuele machine voor het cloudapparaat. De virtuele machine heeft dezelfde naam als het cloudapparaat. Ga naar **Alle resources**, geef de naam van het cloudapparaat en abonnement op, en selecteer virtuele machines als type. Selecteer in de getoonde lijst met virtuele machines de virtuele machine die overeenkomt met het cloudapparaat en klik erop.

     ![De virtuele machine voor het cloudapparaat selecteren](./media/storsimple-8000-cloud-appliance-u2/sca-vm.png)

2. Ga naar **Instellingen > Netwerken**. In het rechterdeelvenster ziet u het privé IP-adres van het cloudapparaat. Noteer deze naam.

    ![Het privé IP-adres voor het cloudapparaat ophalen](./media/storsimple-8000-cloud-appliance-u2/sca-private-ip-vm-networking.png)

## <a name="work-with-the-storsimple-cloud-appliance"></a>Werken met het StorSimple-cloudapparaat

Nu u het StorSimple-cloudapparaat hebt gemaakt en geconfigureerd, kunt u ermee aan de slag. U kunt werken met volumecontainers, volumes en back-upbeleid op een cloudapparaat net zoals u op een fysiek StorSimple-apparaat zou doen. Het enige verschil is dat u moet controleren of u het cloudapparaat in uw lijst met apparaten hebt gekozen. Zie [De StorSimple Manager-apparaatbeheerservice gebruiken voor het beheren van een cloudapparaat](storsimple-8000-manager-service-administration.md) voor stapsgewijze instructies voor de verschillende beheertaken voor het cloudapparaat.

In de volgende gedeelten worden enkele van de verschillen besproken die u tegenkomt wanneer u met een cloudapparaat werkt.

### <a name="maintain-a-storsimple-cloud-appliance"></a>Een StorSimple-cloudapparaat onderhouden

In vergelijking met het fysieke apparaat is voor het cloudapparaat slechts weinig onderhoud vereist, omdat het apparaat alleen uit software bestaat.

Een cloudapparaat kan niet worden bijgewerkt. Gebruik de meest recente versie van de software om een nieuw cloudapparaat te maken.


### <a name="storage-accounts-for-a-cloud-appliance"></a>Opslagaccounts voor een cloudapparaat

Opslagaccounts worden gemaakt voor gebruik door de StorSimple-apparaatbeheerservice, het cloudapparaat en het fysieke apparaat. Wanneer u uw opslagaccounts maakt, wordt u aangeraden een regio-id te gebruiken in de beschrijvende naam. Dit zorgt ervoor dat de regio in alle systeemonderdelen consistent is. Voor een cloudapparaat is het van belang dat alle onderdelen zich in dezelfde regio bevinden. Dit voorkomt prestatieproblemen.

Zie [Een opslagaccount toevoegen](storsimple-8000-manage-storage-accounts.md#add-a-storage-account) voor stapsgewijze instructies.

### <a name="deactivate-a-storsimple-cloud-appliance"></a>Een StorSimple-cloudapparaat deactiveren

Als u een cloudapparaat deactiveert, worden de virtuele machine en de resources verwijderd die zijn gemaakt tijdens het inrichten van het apparaat. Wanneer het cloudapparaat is gedeactiveerd, kan het niet meer worden hersteld naar de oorspronkelijke staat. Voordat u het cloudapparaat deactiveert, beëindigt of verwijdert u de clients en hosts die er afhankelijk van zijn.

Als u een cloudapparaat deactiveert, resulteert dat in de volgende acties:

* Het cloudapparaat wordt verwijderd.
* De besturingssysteemschijf en gegevensschijven die voor het cloudapparaat zijn gemaakt, worden verwijderd.
* De gehoste service en het virtuele netwerk die zijn gemaakt tijdens het inrichten, blijven behouden. Als u deze niet gebruikt, moet u ze handmatig verwijderen.
* De cloudmomentopnamen die voor het cloudapparaat zijn gemaakt, blijven behouden.

Zie [Uw StorSimple-apparaat deactiveren en verwijderen](storsimple-8000-deactivate-and-delete-device.md) voor stapsgewijze instructies.

Zodra het cloudapparaat op de StorSimple Manager-serviceblade wordt weergegeven als gedeactiveerd, kunt u het cloudapparaat uit de apparatenlijst op de blade **Apparaten** verwijderen.

### <a name="start-stop-and-restart-a-cloud-appliance"></a>Een cloudapparaat starten, stoppen en opnieuw opstarten
Op het fysieke StorSimple-apparaat zit een aan/uit-knop, maar dat is bij een StorSimple-cloudapparaat niet het geval. Er kunnen zich echter situaties voordoen waarin u het cloudapparaat wilt stoppen om het opnieuw op te starten.

De eenvoudigste manier om een cloudapparaat te starten, te stoppen en opnieuw op te starten, is via de blade voor de service voor virtuele machines. Ga naar de service voor virtuele machines. Zoek in de lijst met virtuele machines de virtuele machine op die overeenkomt met uw cloudapparaat (dezelfde naam) en klik op de naam van de virtuele machine. Als u op de blade voor virtuele machines kijkt, is de status van het cloudapparaat **Actief**, omdat het standaard wordt gestart nadat het is gemaakt. U kunt virtuele machines te allen tijde starten, stoppen en opnieuw opstarten.

[!INCLUDE [Stop and restart cloud appliance](../../includes/storsimple-8000-stop-restart-cloud-appliance.md)]

### <a name="reset-to-factory-defaults"></a>De fabrieksinstellingen terugzetten
Als u besluit dat u helemaal opnieuw wilt beginnen met uw cloudapparaat, deactiveert en verwijdert u het oude apparaat en maakt u een nieuw apparaat.

## <a name="fail-over-to-the-cloud-appliance"></a>Failover uitvoeren naar het cloudapparaat
Het StorSimple-cloudapparaat is onder meer ontworpen voor herstel na noodgevallen. In noodgevallen is het fysieke StorSimple-apparaat of zelfs het gehele datacenter mogelijk niet beschikbaar. In dergelijke gevallen kunt u een cloudapparaat gebruiken om de werkzaamheden op een andere locatie voort te zetten. Tijdens het herstel na een noodgeval krijgen de volumecontainers van het bronapparaat een andere eigenaar en worden ze overgedragen naar het cloudapparaat.

De vereisten voor herstel na noodgevallen zijn:

* Het cloudapparaat is gemaakt en geconfigureerd.
* Alle volumes in de volumecontainer zijn offline.
* De volumecontainer waarvoor u een failover uitvoert, heeft een bijbehorende cloud-momentopname.

> [!NOTE]
> * Als u een cloudapparaat als secundair apparaat gebruikt voor herstel na noodgevallen, moet u er rekening mee houden dat de 8010 30 TB aan Standard-opslag biedt en de 8020 64 TB aan Premium-opslag. Het 8020-cloudapparaat met een hogere capaciteit is mogelijk beter geschikt voor herstel na noodgevallen.

Zie [Failover naar een cloudapparaat](storsimple-8000-device-failover-cloud-appliance.md) voor een stapsgewijze procedure.

## <a name="delete-the-cloud-appliance"></a>Het cloudapparaat verwijderen
Als u al eerder een StorSimple-cloudapparaat hebt geconfigureerd en gebruikt, maar u geen rekenkosten meer wilt betalen voor het gebruik hiervan, moet u het cloudapparaat stoppen. Wanneer het cloudapparaat wordt gestopt, wordt de toewijzing van de virtuele machine ongedaan gemaakt. Met deze actie wordt voorkomen dat de kosten voor uw abonnement oplopen. De kosten voor opslag voor het besturingssysteem en gegevensschijven blijven echter.

Als u alle kosten wilt stoppen, moet u het cloudapparaat verwijderen. Als u de back-ups wilt verwijderen die zijn gemaakt door het cloudapparaat, kunt u het apparaat deactiveren of verwijderen. Zie [Een StorSimple-apparaat deactiveren en verwijderen](storsimple-8000-deactivate-and-delete-device.md) voor meer informatie.

[!INCLUDE [Delete a cloud appliance](../../includes/storsimple-8000-delete-cloud-appliance.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>Problemen met internetverbinding oplossen
Als er tijdens het maken van een cloudapparaat geen verbinding is met internet, mislukt deze stap. Als u problemen met de internetverbinding wilt oplossen, voert u de volgende stappen uit in de klassieke Azure-portal:

1. [Een virtuele Windows-machine maken in de Azure-portal](../virtual-machines/windows/quick-create-portal.md). Deze virtuele machine moet hetzelfde opslagaccount, VNet en subnet gebruiken als die worden gebruikt door uw cloudapparaat. Als er al een bestaande Windows Server-host in Azure is die hetzelfde opslagaccount, VNet en subnet gebruikt, kunt u deze ook gebruiken om het probleem met de internetverbinding op te lossen.
2. Meld u extern aan bij de virtuele machine die u in de vorige stap hebt gemaakt.
3. Open een opdrachtvenster in de virtuele machine (Win + R en typ vervolgens `cmd`).
4. Voer de volgende opdracht uit bij de prompt.

    `nslookup windows.net`
5. Als `nslookup` mislukt, zorgt het probleem met de internetverbinding ervoor dat het cloudapparaat zich niet kan registreren bij de StorSimple-apparaatbeheerservice.
6. Breng de benodigde wijzigingen aan in uw virtuele netwerk om ervoor te zorgen dat het cloudapparaat toegang kan krijgen tot Azure-sites zoals _windows.net_.

## <a name="next-steps"></a>Volgende stappen
* Ontdek hoe u [de StorSimple Manager-apparaatbeheerservice gebruikt om een cloudapparaat te beheren](storsimple-8000-manager-service-administration.md).
* Ontdek hoe u [een StorSimple-volume herstelt op basis van een back-upset](storsimple-8000-restore-from-backup-set-u2.md).