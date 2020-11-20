---
title: StorSimple 8000-serie beveiliging | Microsoft Docs
description: Hierin worden de beveiligings-en privacyfuncties beschreven voor het beveiligen van uw StorSimple-service,-apparaat en-gegevens on-premises en in de Cloud.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: 72bf252488878c8b6fabb2ea2d61658a2cd87e14
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966204"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple beveiliging en gegevens bescherming

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht

Beveiliging is een grote bezorgdheid voor iedereen die een nieuwe technologie moet gebruiken, met name wanneer de technologie wordt gebruikt met vertrouwelijke of bedrijfs eigen gegevens. Als u verschillende technologieën evalueert, moet u rekening houden met verhoogde Risico's en kosten voor gegevens bescherming. Microsoft Azure StorSimple biedt een oplossing voor beveiliging en privacy voor gegevens bescherming, om ervoor te zorgen dat:

* **Vertrouwelijkheid** : alleen geautoriseerde entiteiten kunnen uw gegevens weer geven.
* **Integriteit** : alleen geautoriseerde entiteiten kunnen uw gegevens wijzigen of verwijderen.

De Microsoft Azure StorSimple oplossing bestaat uit vier hoofd onderdelen die met elkaar communiceren:

* **StorSimple Apparaatbeheer-service die in Microsoft Azure wordt gehost** : de beheer service die u gebruikt om het StorSimple-apparaat te configureren en in te richten.
* **StorSimple-apparaat** : een fysiek apparaat dat is geïnstalleerd in uw Data Center. Alle hosts en clients die gegevens genereren, maken verbinding met het StorSimple-apparaat en het apparaat beheert de gegevens en verplaatst die naar de Azure-Cloud.
* **Clients/hosts die zijn verbonden met het apparaat** : de clients in uw infra structuur die verbinding maken met het StorSimple-apparaat en gegevens genereren die moeten worden beveiligd.
* **Cloudopslag** – De locatie in de Azure-cloud waar gegevens worden opgeslagen.

In de volgende secties worden de StorSimple-beveiligings functies beschreven waarmee u elk van deze onderdelen en de gegevens die erop zijn opgeslagen, kunt beveiligen. Het bevat ook een lijst met vragen die u mogelijk hebt over Microsoft Azure StorSimple beveiliging en de bijbehorende antwoorden.

## <a name="storsimple-device-manager-service-protection"></a>StorSimple Apparaatbeheer service-beveiliging

De StorSimple-Apparaatbeheer service is een beheer service die wordt gehost in Microsoft Azure en die wordt gebruikt voor het beheren van alle StorSimple-apparaten die uw organisatie heeft aangeschaft. U hebt toegang tot de StorSimple-Apparaatbeheer service met behulp van de referenties van uw organisatie om u aan te melden bij de Azure Portal via een webbrowser.

Voor toegang tot de StorSimple Apparaatbeheer-service moet uw organisatie beschikken over een Azure-abonnement dat StorSimple bevat. Uw abonnement bepaalt tot welke functies u toegang hebt in de Azure-portal. Als uw organisatie geen Azure-abonnement heeft en u hierover meer wilt weten, raadpleegt u [registreren voor Azure als organisatie](../active-directory/fundamentals/sign-up-organization.md).

Omdat de StorSimple-Apparaatbeheer-service wordt gehost in azure, wordt deze beveiligd door de Azure-beveiligings functies. Ga naar het [Vertrouwenscentrum van Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/) voor meer informatie over de beveiligingsfuncties die worden geleverd door Microsoft Azure.

## <a name="storsimple-device-protection"></a>StorSimple

Het StorSimple-apparaat is een on-premises hybride opslag apparaat met Ssd's (Solid state drives) en harde schijven (Hdd's), samen met redundante controllers en automatische failover-mogelijkheden. De controllers beheren opslag lagen, op dit moment gebruikte (of dynamische) gegevens op lokale opslag plaatsen (in het StorSimple-apparaat of op on-premises servers), terwijl er minder vaak gebruikte gegevens naar de cloud worden verplaatst.

Alleen geautoriseerde StorSimple-apparaten mogen lid worden van de StorSimple Apparaatbeheer-service die u hebt gemaakt in uw Azure-abonnement. Als u een apparaat wilt autoriseren, moet u dit registreren bij de StorSimple-Apparaatbeheer-service door de service registratie sleutel op te geven. De service registratie sleutel is een 128-bits wille keurige sleutel die wordt gegenereerd in de Azure Portal.

![Service registratie sleutel](./media/storsimple-security/ServiceRegistrationKey.png)

Ga naar [stap 2: de service registratie sleutel ophalen](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)voor meer informatie over het ophalen van een service registratie sleutel.

De service registratie sleutel is een lange sleutel die 100 + tekens bevat. U kunt de sleutel kopiëren en opslaan in een tekst bestand op een veilige locatie, zodat u deze kunt gebruiken om indien nodig extra apparaten te autoriseren. Als de service registratie sleutel verloren gaat nadat u uw eerste apparaat hebt geregistreerd, kunt u een nieuwe sleutel genereren op basis van de StorSimple-Apparaatbeheer service. Dit heeft geen invloed op de werking van bestaande apparaten.

Nadat een apparaat is geregistreerd, worden tokens gebruikt om met Microsoft Azure te communiceren. De service registratie sleutel wordt niet gebruikt na registratie van het apparaat.

> [!NOTE]
> U wordt aangeraden de service registratie sleutel na elk gebruik opnieuw te genereren.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Uw StorSimple-oplossing beveiligen via wacht woorden

Wacht woorden zijn een belang rijk aspect van computer beveiliging en worden uitgebreid gebruikt in de StorSimple-oplossing om ervoor te zorgen dat uw gegevens alleen toegankelijk zijn voor gemachtigde gebruikers. Met StorSimple kunt u de volgende wacht woorden configureren:

* Beheerders wachtwoord StorSimple
* Challenge Handshake Authentication Protocol (CHAP) initiator-en doel wachtwoorden
* Wachtwoord StorSimple Snapshot Manager

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>StorSimple en het beheerders wachtwoord voor het apparaat Windows PowerShell voor StorSimple

Windows PowerShell voor StorSimple is een opdracht regel interface die u kunt gebruiken om het StorSimple-apparaat te beheren. Windows PowerShell voor StorSimple beschikt over functies waarmee u uw apparaat kunt registreren, de netwerk interface op het apparaat kunt configureren, bepaalde soorten updates kunt installeren, het apparaat moet oplossen door de ondersteunings sessie te openen en de status van het apparaat te wijzigen. U kunt toegang krijgen tot Windows PowerShell voor StorSimple door verbinding te maken met de seriële console op het apparaat of door middel van externe communicatie van Windows Power shell.

Externe communicatie met Power shell kan worden uitgevoerd via HTTPS of HTTP. Als extern beheer via HTTPS is ingeschakeld, moet u het certificaat voor extern beheer downloaden van het apparaat en op de externe client installeren. Ga naar [extern verbinding maken met uw StorSimple-apparaat](storsimple-8000-remote-connect.md)voor meer informatie over externe communicatie met Power shell.

Nadat u Windows PowerShell voor StorSimple hebt gebruikt om verbinding te maken met het apparaat, moet u het beheerders wachtwoord voor het apparaat opgeven om u aan te melden bij het apparaat.

![Wachtwoord apparaatbeheerder](./media/storsimple-security/DeviceAdminPW.png)

Houd de volgende aanbevolen procedures in acht:

* Extern beheer is standaard uitgeschakeld. U kunt de StorSimple Apparaatbeheer-service gebruiken om deze functie in te scha kelen. Als een beveiligings best practice, moet externe toegang alleen worden ingeschakeld gedurende de periode dat deze werkelijk nodig is.
* Als u het wacht woord wijzigt, moet u alle RAS-gebruikers hiervan op de hoogte stellen zodat er geen onverwacht connectiviteits verlies optreedt.
* De StorSimple-Apparaatbeheer service kan de bestaande wacht woorden niet ophalen: deze kan alleen opnieuw worden ingesteld. We raden u aan om alle wacht woorden op een veilige locatie op te slaan, zodat u een wacht woord niet opnieuw hoeft in te stellen als het is verg eten. Als u een wacht woord opnieuw wilt instellen, moet u alle gebruikers hiervan op de hoogte stellen voordat u het opnieuw instelt.

U kunt de Windows Power shell-interface openen met behulp van een seriële verbinding met het apparaat. U kunt deze ook op afstand openen door HTTP of HTTPS te gebruiken, die extra beveiliging biedt. HTTPS biedt een hoger beveiligings niveau dan een seriële of HTTP-verbinding. Als u echter HTTPS wilt gebruiken, moet u eerst een certificaat installeren op de client computer die toegang heeft tot het apparaat. U kunt het RAS-certificaat downloaden via de pagina apparaatconfiguratie in de StorSimple-Apparaatbeheer service. Als het certificaat voor externe toegang is verbroken, moet u een nieuw certificaat downloaden en dit door geven aan alle clients die zijn gemachtigd om extern beheer te gebruiken.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Challenge Handshake Authentication Protocol (CHAP) initiator-en doel wachtwoorden

CHAP is een verificatie schema dat door het StorSimple-apparaat wordt gebruikt om de identiteit van externe clients te valideren. De verificatie is gebaseerd op een gedeeld wacht woord. CHAP kan één richting (unidirectioneel) of wederzijds (bidirectioneel) zijn. Met eenrichtings-CHAP verifieert het doel (het StorSimple-apparaat) een initiator (host). Voor wederzijdse of omgekeerde CHAP moet het doel de initiator verifiëren en vervolgens het doel door de initiator verifiëren. Uw StorSimple kan worden geconfigureerd voor het gebruik van beide methoden.

Houd rekening met het volgende wanneer u CHAP configureert:

* De CHAP-gebruikers naam moet minder dan 233 tekens bevatten.
* Het CHAP-wacht woord moet tussen de 12 en 16 tekens lang zijn. Als u een langere gebruikers naam of wacht woord probeert te gebruiken, treedt er een verificatie fout op de Windows-host op.
* U kunt niet hetzelfde wacht woord gebruiken voor zowel de CHAP-initiator als het CHAP-doel.
* Nadat u het wacht woord hebt ingesteld, kan het worden gewijzigd, maar het kan niet worden opgehaald. Als het wacht woord wordt gewijzigd, moet u alle RAS-gebruikers hiervan op de hoogte stellen zodat ze verbinding kunnen maken met het StorSimple-apparaat.

Voor meer informatie over CHAP en hoe u deze kunt configureren voor uw StorSimple-oplossing, gaat u naar [CHAP configureren voor uw StorSimple-apparaat](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Wachtwoord StorSimple Snapshot Manager

StorSimple Snapshot Manager is een MMC-module (micro soft Management Console) die gebruikmaakt van volume groepen en de Windows-Volume Shadow Copy Service voor het genereren van toepassings consistente back-ups. Daarnaast kunt u StorSimple Snapshot Manager gebruiken om back-upschemas te maken en volumes te klonen of te herstellen.

Wanneer u een apparaat configureert voor het gebruik van StorSimple Snapshot Manager, moet u het wacht woord voor StorSimple Snapshot Manager opgeven. Dit wacht woord wordt voor het eerst ingesteld in Windows PowerShell voor StorSimple tijdens de registratie. Het wacht woord kan ook worden ingesteld en gewijzigd vanuit de StorSimple-Apparaatbeheer service. Met dit wacht woord wordt het apparaat geverifieerd met StorSimple Snapshot Manager.

![Wachtwoord StorSimple Snapshot Manager](./media/storsimple-security/SnapshotMgrPassword.png)

Het wacht woord voor de StorSimple Snapshot Manager moet 14 tot 15 tekens lang zijn en moet drie of meer van een combi natie van hoofd letters, kleine letters, cijfers en speciale tekens bevatten. Nadat u het wacht woord voor StorSimple Snapshot Manager hebt ingesteld, kan het worden gewijzigd, maar het kan niet worden opgehaald. Als u het wacht woord wijzigt, moet u alle externe gebruikers hiervan op de hoogte stellen.

Ga naar [Wat is StorSimple Snapshot Manager](storsimple-what-is-snapshot-manager.md) voor meer informatie over StorSimple-Snapshot Manager?

### <a name="password-best-practices"></a>Aanbevolen procedures voor wachtwoorden

U kunt het beste de volgende richt lijnen gebruiken om ervoor te zorgen dat StorSimple-wacht woorden sterk en goed beveiligd zijn:

* Wijzig uw wacht woord om de drie maanden. Het wijzigen van de wacht woorden wordt jaarlijks afgedwongen.
* Gebruik sterke wacht woorden. Ga naar [sterkere wacht woorden maken en beveiligen](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/)voor meer informatie.
* Altijd verschillende wacht woorden gebruiken voor verschillende toegangs mechanismen; elk wacht woord dat u opgeeft, moet uniek zijn.
* Deel geen wacht woorden met iedereen die geen toegang heeft tot het StorSimple-apparaat.
* Praat niet met een wacht woord voor anderen of hints met de indeling van een wacht woord.
* Als u vermoedt dat een account of wacht woord is aangetast, meldt u het incident aan uw gegevens beveiligings afdeling.
* Alle wacht woorden behandelen als gevoelige, vertrouwelijke informatie. 

## <a name="storsimple-data-protection"></a>StorSimple-gegevens beveiliging

In deze sectie worden de StorSimple-beveiligings functies beschreven voor het beveiligen van gegevens die onderweg zijn en opgeslagen gegevens.

Zoals beschreven in andere secties, worden wacht woorden gebruikt om gebruikers te autoriseren en te verifiëren voordat ze toegang kunnen krijgen tot uw StorSimple-oplossing. Een andere beveiligings overweging is het beschermen van gegevens van niet-geautoriseerde gebruikers terwijl deze worden overgedragen tussen opslag systemen en terwijl deze worden opgeslagen. In de volgende secties worden de functies voor gegevens beveiliging beschreven die worden meegeleverd met StorSimple.

> [!NOTE]
> Ontdubbeling biedt extra beveiliging voor gegevens die zijn opgeslagen op het StorSimple-apparaat en in Microsoft Azure opslag. Wanneer gegevens worden ontdubbeld, worden de gegevens objecten afzonderlijk opgeslagen van de meta gegevens die worden gebruikt om ze toe te wijzen en te openen: er is geen beschik bare context op opslag niveau om de gegevens te reconstrueren op basis van de volume structuur, het bestands systeem of de bestands naam.


## <a name="protect-data-flowing-through-the-service"></a>Gegevens die via de service stromen, beveiligen

Het primaire doel van de StorSimple-Apparaatbeheer service is het beheren en configureren van het StorSimple-apparaat. De StorSimple-Apparaatbeheer-service wordt uitgevoerd in Microsoft Azure. U gebruikt de Azure Portal om configuratie gegevens voor het apparaat in te voeren en vervolgens Microsoft Azure gebruikt de StorSimple Apparaatbeheer-service om de gegevens naar het apparaat te verzenden. StorSimple maakt gebruik van een systeem van asymmetrische sleutel paren om ervoor te zorgen dat een inbreuk van de Azure-service niet leidt tot een inbreuk op de opgeslagen gegevens.

![Gegevens versleuteling in Flight](./media/storsimple-security/DataEncryption.png)

Het asymmetrische-sleutel systeem helpt bij het beveiligen van de gegevens die door de service als volgt worden door gegeven:

1. Een certificaat voor gegevens versleuteling dat gebruikmaakt van een asymmetrische combi natie van open bare en persoonlijke sleutels wordt gegenereerd op het apparaat en wordt gebruikt om de gegevens te beveiligen. De sleutels worden gegenereerd wanneer het eerste apparaat wordt geregistreerd.
2. De sleutels voor gegevens versleutelings certificaten worden geëxporteerd naar een pfx-bestand (Personal Information Exchange) dat wordt beveiligd door de versleutelings sleutel voor service gegevens. Dit is een sterke 128-bits sleutel die wille keurig door het eerste apparaat wordt gegenereerd tijdens de registratie.
3. De open bare sleutel van het certificaat wordt veilig beschikbaar gesteld aan de StorSimple-Apparaatbeheer service en de persoonlijke sleutel blijft bij het apparaat.
4. Gegevens die de service binnenkomen, worden versleuteld met behulp van de open bare sleutel en ontsleuteld met de persoonlijke sleutel die op het apparaat is opgeslagen, waardoor de Azure-service de gegevens stroom niet kan ontsleutelen naar het apparaat.

De versleutelings sleutel voor service gegevens wordt alleen gegenereerd op het eerste apparaat dat is geregistreerd bij de service. Op alle volgende apparaten die zijn geregistreerd bij de service moet dezelfde versleutelings sleutel voor service gegevens worden gebruikt.

> [!IMPORTANT]
> Het is belang rijk om een kopie te maken van de versleutelings sleutel voor service gegevens en deze op een veilige locatie op te slaan. Een kopie van de versleutelings sleutel voor de service gegevens moet zodanig worden opgeslagen dat deze toegankelijk is voor een geautoriseerd persoon en gemakkelijk kan worden gecommuniceerd naar de beheerder van het apparaat.
> 
> Als de versleutelings sleutel voor de service gegevens is verdwenen, kan een ondersteunings medewerker van micro soft u helpen om deze op te halen, op voor waarde dat u ten minste één apparaat in een online status hebt. U wordt aangeraden de versleutelings sleutel van de service gegevens te wijzigen nadat deze is opgehaald.

Als u de versleutelings sleutel voor service gegevens en het bijbehorende certificaat voor gegevens versleuteling wilt wijzigen, volgt u de stappen in [de versleutelings sleutel voor service gegevens wijzigen voor uw StorSimple-Apparaatbeheer service](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). Voor het wijzigen van de versleutelings sleutels moeten alle apparaten worden bijgewerkt met de nieuwe sleutel. Daarom wordt u aangeraden de sleutel te wijzigen wanneer alle apparaten online zijn. Als apparaten offline zijn, kunnen hun sleutels op een ander tijdstip worden gewijzigd. De apparaten met verouderde sleutels kunnen nog steeds back-ups uitvoeren, maar ze kunnen geen gegevens herstellen totdat de sleutel is bijgewerkt.

De versleutelings sleutel voor de service gegevens en het certificaat voor gegevens versleuteling verlopen niet. We raden u echter aan om de versleutelings sleutel voor service gegevens jaarlijks te wijzigen om te voor komen dat de sleutel wordt gekraakt.

## <a name="protect-data-at-rest"></a>Data-at-rest beschermen

Het StorSimple-apparaat beheert gegevens door deze in lagen lokaal en in de Cloud op te slaan, afhankelijk van de frequentie van het gebruik. Alle hostcomputers die zijn verbonden met het apparaat verzenden gegevens naar het apparaat, waarna gegevens naar de cloud worden verplaatst, indien van toepassing. Gegevens worden veilig via internet overgedragen van het apparaat naar de Cloud. Elk apparaat heeft één iSCSI-doel dat alle gedeelde volumes op dat apparaat Opper vlakken. Alle gegevens worden versleuteld voordat ze worden verzonden naar de Cloud opslag. 

![Versleutelings sleutel voor Cloud opslag](./media/storsimple-security/CloudStorageEncryption.png)

Om ervoor te zorgen dat de veiligheid en integriteit van gegevens die naar de cloud worden verplaatst, kunt u met StorSimple de volgende versleutelings sleutels voor de Cloud opslag definiëren:

* U geeft de versleutelings sleutel voor de Cloud opslag op wanneer u een volume container maakt. De sleutel kan niet worden gewijzigd of later worden toegevoegd.
* Alle volumes in een volume container delen dezelfde versleutelings sleutel. Als u een andere vorm van versleuteling wilt voor een specifiek volume, raden we u aan een nieuwe volume container te maken om dat volume te hosten.
* Wanneer u de versleutelings sleutel voor de Cloud opslag in de StorSimple Apparaatbeheer-service invoert, wordt de sleutel versleuteld met behulp van het open bare gedeelte van de versleutelings sleutel voor service gegevens en vervolgens naar het apparaat verzonden.
* De versleutelings sleutel voor de Cloud opslag wordt nergens in de service opgeslagen en is alleen bekend bij het apparaat.
* Het opgeven van een versleutelings sleutel voor Cloud opslag is optioneel. U kunt gegevens die op de host zijn versleuteld naar het apparaat verzenden.

### <a name="additional-security-best-practices"></a>Aanvullende aanbevolen procedures voor beveiliging

* Gesplitst verkeer: Isoleer uw iSCSI-SAN van gebruikers verkeer in een bedrijfs-LAN door een volledig gescheiden netwerk te implementeren en VLAN'S te gebruiken waarbij fysieke isolatie geen optie is. Een speciaal netwerk voor iSCSI-opslag zorgt voor de veiligheid en prestaties van uw bedrijfs kritieke gegevens. Het combi neren van opslag en gebruikers verkeer via een bedrijfs-LAN wordt niet aanbevolen en kan de latentie verhogen en netwerk fouten veroorzaken.
* Gebruik voor netwerk beveiliging aan de host zijde netwerk interfaces die ondersteuning bieden voor TCP/IP Offload Engine (TOE). TOE vermindert CPU-belasting door TCP op de netwerk adapter te verwerken.

## <a name="protect-data-via-storage-accounts"></a>Gegevens beveiligen via opslag accounts

Elk Microsoft Azure-abonnement kan een of meer opslag accounts maken. (Een opslag account biedt een unieke naam ruimte voor het werken met gegevens die zijn opgeslagen in de Azure-Cloud.) Toegang tot een opslag account wordt bepaald door het abonnement en de toegangs sleutels die aan dat opslag account zijn gekoppeld.

Wanneer u een opslag account maakt, genereert Microsoft Azure 2 512-bits toegangs sleutels voor opslag, waarvan een van de voor verificatie wordt gebruikt wanneer het StorSimple-apparaat toegang heeft tot het opslag account. Houd er rekening mee dat slechts één van deze sleutels in gebruik is. De andere sleutel wordt in de reserve ring bewaard, zodat u de sleutels periodiek kunt draaien. Als u sleutels wilt draaien, maakt u de secundaire sleutel actief en verwijdert u vervolgens de primaire sleutel. U kunt vervolgens tijdens de volgende rotatie een nieuwe sleutel maken voor gebruik. (Om veiligheids redenen is voor veel data centers het draaien van sleutels vereist.)

U wordt aangeraden deze aanbevolen procedures te volgen voor het draaien van sleutels:

* U moet regel matig Storage-account sleutels draaien om ervoor te zorgen dat uw opslag account niet wordt gebruikt door onbevoegde gebruikers.
* De Azure-beheerder moet de primaire of secundaire sleutel periodiek wijzigen of opnieuw genereren met behulp van de sectie opslag van de Azure Portal om rechtstreeks toegang te krijgen tot het opslag account.

## <a name="protect-data-via-encryption"></a>Gegevens beveiligen via versleuteling

StorSimple maakt gebruik van de volgende versleutelings algoritmen voor het beveiligen van gegevens die zijn opgeslagen in of reizen tussen de onderdelen van uw StorSimple-oplossing.

| Algoritme | Sleutellengte | Protocollen/toepassingen/opmerkingen |
| --- | --- | --- |
| RSA |2048 |RSA PKCS 1 v 1.5 wordt gebruikt door de Azure Portal om de configuratie gegevens te versleutelen die naar het apparaat worden verzonden: bijvoorbeeld de referenties van het opslag account, de StorSimple en de versleutelings sleutels voor de Cloud opslag. |
| AES |256 |AES met CBC wordt gebruikt voor het versleutelen van het open bare gedeelte van de versleutelings sleutel voor service gegevens voordat deze wordt verzonden naar de Azure Portal van het StorSimple-apparaat. Het wordt ook gebruikt door het StorSimple-apparaat om gegevens te versleutelen voordat de gegevens naar het Cloud-opslag account worden verzonden. |

## <a name="storsimple-cloud-appliance-security"></a>StorSimple Cloud Appliance beveiliging

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Persoonlijke gegevens beheren

Met de StorSimple-Apparaatbeheer voor zowel fysieke als virtuele Series worden persoonlijke gegevens in de volgende sleutel instanties verzameld:

- Gebruikers instellingen voor waarschuwingen waarbij e-mail adres van gebruikers zijn geconfigureerd. Deze informatie kan worden weer gegeven en gewist door de beheerder. Dit geldt voor zowel de StorSimple 8000-serie apparaten als de virtuele StorSimple-matrices.
  * Als u de instellingen voor de StorSimple 8000-serie wilt bekijken en wissen, volgt u de stappen in [StorSimple-waarschuwingen weer geven en beheren](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Volg de stappen in [StorSimple-waarschuwingen weer geven en beheren](storsimple-virtual-array-manage-alerts.md#configure-alert-settings) om de instellingen voor de virtuele StorSimple-matrix weer te geven en te wissen
- Gebruikers die toegang hebben tot de gegevens die zich op de shares bevinden. Een lijst met gebruikers die toegang hebben tot de gegevens van de share, worden weer gegeven en kunnen worden weer gegeven. Deze lijst wordt ook verwijderd wanneer de shares worden verwijderd. Dit geldt alleen voor virtuele StorSimple-matrices.
  * Als u de lijst wilt weer geven met gebruikers die een share kunnen openen of verwijderen, volgt u de stappen in [shares beheren op de virtuele StorSimple-matrix](storsimple-virtual-array-manage-shares.md)

Bekijk het Microsoft-privacybeleid in het [Vertrouwenscentrum](https://www.microsoft.com/trustcenter) voor meer informatie.

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

Hier volgen enkele vragen en antwoorden over beveiliging en Microsoft Azure StorSimple.

**V:** Mijn service is aangetast. Wat moet ik doen met mijn volgende stappen?

**A:** U moet onmiddellijk de versleutelings sleutel voor service gegevens en de sleutels van het opslag account voor het opslag account dat wordt gebruikt voor het bijhouden van gegevens wijzigen. Ga voor instructies naar:

* [De versleutelings sleutel voor service gegevens wijzigen](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Sleutel rotatie van opslag accounts](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**V:** Ik heb een nieuw StorSimple-apparaat dat vraagt naar de service registratie sleutel. Hoe kan ik ophalen?

**A:** Deze sleutel is gemaakt toen u de StorSimple-Apparaatbeheer service voor het eerst hebt gemaakt. Wanneer u de StorSimple Apparaatbeheer-service gebruikt om verbinding te maken met het apparaat, kunt u de service-Quick Start-pagina gebruiken om de service registratie sleutel weer te geven of opnieuw te genereren. Het genereren van een nieuwe service registratie sleutel heeft geen invloed op de bestaande geregistreerde apparaten. Ga voor instructies naar:

* [De service registratie sleutel weer geven of opnieuw genereren](storsimple-8000-manage-service.md#regenerate-the-service-registration-key)

**V:** Ik ben mijn versleutelings sleutel voor service gegevens verloren. Wat moet ik doen?

**A:** Neem contact op met Microsoft Ondersteuning. Ze kunnen zich aanmelden bij een ondersteunings sessie op uw apparaat en u helpen bij het ophalen van de sleutel (ten minste één apparaat is online). Onmiddellijk nadat u de versleutelings sleutel voor de service gegevens hebt verkregen, moet u deze wijzigen om ervoor te zorgen dat de nieuwe sleutel alleen bij u bekend is. Ga voor instructies naar:

* [De versleutelings sleutel voor service gegevens wijzigen](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**V:**  Ik heb een apparaat geautoriseerd voor een wijziging van de versleutelings sleutel van de service gegevens, maar het wijzigings proces van de sleutel is niet gestart. Wat moet ik doen?

**A:** Als de time-outperiode is verlopen, moet u het apparaat opnieuw autoriseren voor het wijzigen van de versleutelings sleutel van de service gegevens en het proces opnieuw starten.

**V:**  Ik heb de versleutelings sleutel voor de service gegevens gewijzigd, maar ik kon de andere apparaten niet binnen vier uur bijwerken. Moet ik het opnieuw starten?

**A:** De periode van 4 uur is alleen voor het initiëren van de wijziging. Nadat u het update proces op het geautoriseerde StorSimple-apparaat hebt gestart, is de autorisatie geldig totdat alle apparaten zijn bijgewerkt.

**V:** Onze StorSimple-beheerder heeft het bedrijf verlaten. Wat moet ik doen?

**A:** Wijzig de wacht woorden en stel deze opnieuw in voor toegang tot het StorSimple-apparaat en wijzig de versleutelings sleutel van de service gegevens om ervoor te zorgen dat de nieuwe informatie niet bekend is bij niet-geautoriseerd personeel. Ga voor instructies naar:

* [De StorSimple Apparaatbeheer-service gebruiken om uw StorSimple-wacht woorden te wijzigen](storsimple-8000-change-passwords.md)
* [De versleutelings sleutel voor service gegevens wijzigen](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [CHAP configureren voor uw StorSimple-apparaat](storsimple-8000-configure-chap.md)

**V:** Ik wil de StorSimple Snapshot Manager wacht woord opgeven voor een host die verbinding maakt met het StorSimple-apparaat, maar het wacht woord is niet beschikbaar. Wat kan ik doen?

**A:** Als u het wacht woord bent verg eten, kunt u een nieuw account maken. Informeer vervolgens alle bestaande gebruikers dat het wacht woord is gewijzigd en dat de clients moeten worden bijgewerkt om het nieuwe wacht woord te gebruiken. Ga voor instructies naar:

* [Het wacht woord voor StorSimple Snapshot Manager wijzigen](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Een apparaat verifiëren](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**V:** Het certificaat voor externe toegang tot de Windows PowerShell voor StorSimple is gewijzigd op het apparaat. Mijn RAS-clients Hoe kan ik bijwerken?

**A:** U kunt het nieuwe certificaat downloaden van de StorSimple Apparaatbeheer-service en deze vervolgens installeren om te worden geïnstalleerd in het certificaat archief van uw RAS-clients. Ga voor instructies naar:

* [Import-certificaat-cmdlet](/powershell/module/pkiclient/import-certificate)

**V:** Worden mijn gegevens beschermd als de StorSimple-Apparaatbeheer-service is aangetast?

**A:** Service configuratie gegevens worden altijd versleuteld met uw open bare sleutel wanneer u deze in een webbrowser bekijkt. Omdat de service geen toegang heeft tot de persoonlijke sleutel, kunnen er geen gegevens worden weer gegeven in de service. Als de StorSimple-Apparaatbeheer-service is aangetast, is er geen impact, omdat er geen sleutels zijn opgeslagen in de StorSimple Apparaatbeheer-service.

**V:** Als iemand toegang krijgt tot het certificaat voor gegevens versleuteling, worden mijn gegevens aangetast?

**A:** Microsoft Azure slaat de gegevens versleutelings sleutel (. pfx-bestand) van de klant op in een versleutelde indeling. Omdat het pfx-bestand is versleuteld en de StorSimple-service niet beschikt over de versleutelings sleutel voor service gegevens voor het ontsleutelen van het pfx-bestand, is het niet mogelijk om met toegang tot het pfx-bestand geheimen te maken.

**V:** Wat gebeurt er als een overheids entiteit micro soft voor mijn gegevens vraagt?

**A:** Omdat alle gegevens zijn versleuteld op de service en de persoonlijke sleutel wordt bewaard met het apparaat, moet de overheids instantie de klant vragen voor de gegevens.



## <a name="next-steps"></a>Volgende stappen

[Implementeer uw StorSimple-apparaat](storsimple-8000-deployment-walkthrough-u2.md).