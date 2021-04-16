---
title: woordenlijst Windows Virtual Desktop MSIX-app koppelen - Azure
description: Een verklarende woordenlijst met msix-app-attach-termen en -concepten.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: c5c596735ad91f38d5ba4217135a9373d2856182
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538458"
---
# <a name="msix-app-attach-glossary"></a>Verklarende woordenlijst voor MSIX-app-koppelen

Dit artikel is een lijst met definities voor belangrijke termen en concepten met betrekking tot MSIX-app-attach.

## <a name="msix-container"></a>MSIX-container

In een MSIX-container worden MSIX-apps uitgevoerd. Zie [MSIX-containers voor meer informatie.](/windows/msix/msix-container)

## <a name="msix-application"></a>MSIX-toepassing 

Een toepassing die is opgeslagen in een . MSIX-bestand.

## <a name="msix-package"></a>MSIX-pakket 

Een MSIX-pakket is een MSIX-bestand of -toepassing.

## <a name="msix-share"></a>MSIX-share

Een MSIX-share is een netwerk share die uitgebreide MSIX-pakketten bevat. MSIX-shares moeten ondersteuning bieden voor SMB 3 of hoger. De shares moeten ook toegankelijk zijn voor de Virtual Machines (VM) in het systeemaccount van de hostgroep. MSIX-pakketten worden gefaseerd vanuit de MSIX-share zonder toepassingsbestanden naar het systeemstation te moeten verplaatsen. 

## <a name="msix-image"></a>MSIX-afbeelding

Een MSIX-afbeelding is een VHD-, VHDx- of CIM-bestand dat een of meer MSIX-verpakte toepassingen bevat. Elke toepassing wordt geleverd in de MSIX-afbeelding met behulp van het HULPPROGRAMMA MSIXMGR.

## <a name="repackage"></a>Ompakken

Bij het opnieuw verpakken wordt een niet-MSIX-toepassing ge converteert naar MSIX met behulp van de MSIX Packaging Tool (MPT). Zie overzicht van [MSIX Packaging Tool voor meer informatie.](/windows/msix/packaging-tool/tool-overview)

## <a name="expand-an-msix-package"></a>Een MSIX-pakket uitbreiden

Het uitbreiden van een MSIX-pakket is een proces met meerdere stappen. Uitbreiding neemt het MSIX-bestand en plaatst de inhoud ervan in een VHD(x) of CIM-bestand. 

Een MSIX-pakket uitbreiden:

1. Een MSIX-pakket (MSIX-bestand) downloaden.
2. Wijzig de naam van het MSIX-bestand in een ZIP-bestand.
3. Het resulterende ZIP-bestand uit een map uitmaken.
4. Maak een VHD met dezelfde grootte als de map.
5. Bevestig de VHD.
6. Initialiseer een schijf.
7. Maak een partitie.
8. Maak de partitie op.
9. Kopieer de uitgepakte inhoud naar de VHD.
10. Gebruik het hulpprogramma MSIXMGR om ACL's toe te passen op de inhoud van de VHD.
11. Ontkoppel de VHD(x) of [CIM](#cim).

## <a name="upload-an-msix-package"></a>Een MSIX-pakket uploaden 

Het uploaden van een MSIX-pakket omvat het uploaden van de VHD(x) of [CIM](#cim) die een uitgebreid MSIX-pakket bevat naar de MSIX-share.

In Windows Virtual Desktop worden uploads eenmaal per MSIX-share geüpload. Nadat u een pakket hebt geüpload, kunnen alle hostgroepen in hetzelfde abonnement hier naar verwijzen.

## <a name="add-an-msix-package"></a>Een MSIX-pakket toevoegen

Als Windows Virtual Desktop MSIX-pakket toevoegt, wordt dit aan een hostgroep toegevoegd.

## <a name="publish-an-msix-package"></a>Een MSIX-pakket publiceren 

In Windows Virtual Desktop moet een gepubliceerd MSIX-pakket worden toegewezen aan een Active Directory-domein-service (AD DS) of Azure Active Directory-gebruikersgroep (Azure AD).

## <a name="staging"></a>Faseren

Fasering omvat twee dingen:

- De VHD(x) of [CIM aan de](#cim) VM te monteren.
- Het besturingssysteem melden dat het MSIX-pakket beschikbaar is voor registratie.

## <a name="registration"></a>Registratie

Registratie maakt een gefaseerd MSIX-pakket beschikbaar voor uw gebruikers. Registreren gebeurt per gebruiker. Als u niet expliciet een app hebt geregistreerd voor die specifieke gebruiker, kunnen ze de app niet uitvoeren.

Er zijn twee soorten registratie: regelmatig en vertraagd.

### <a name="regular-registration"></a>Reguliere registratie

In de normale registratie is elke toepassing die aan een gebruiker is toegewezen, volledig geregistreerd. Registratie vindt plaats tijdens de aanmelding van de gebruiker bij de sessie, wat van invloed kan zijn op de tijd die nodig is voor het gebruik van Windows Virtual Desktop.

### <a name="delayed-registration"></a>Vertraagde registratie

Bij vertraagde registratie wordt elke toepassing die aan de gebruiker is toegewezen slechts gedeeltelijk geregistreerd. Gedeeltelijke registratie betekent dat de Startmenu tegel en dubbelklikken bestands associations zijn geregistreerd. De registratie vindt plaats wanneer de gebruiker zich bij de sessie aanmeldt. Dit heeft dus minimale invloed op de tijd die nodig is om het gebruik van Windows Virtual Desktop. De registratie wordt alleen voltooid wanneer de gebruiker de toepassing in het MSIX-pakket heeft uitgevoerd.

Vertraagde registratie is momenteel de standaardconfiguratie voor msix-app-attach.

## <a name="deregistration"></a>Uitschrijving

Met Deregistration wordt een geregistreerd maar niet-actief MSIX-pakket voor een gebruiker verwijderd. De registratie vindt plaats wanneer de gebruiker zich afmeldt bij de sessie. Tijdens de registratie pusht de MSIX-app toepassingsgegevens die specifiek zijn voor de gebruiker naar het lokale gebruikersprofiel.

## <a name="destage"></a>Faseer de fase

Destaging waarschuwt het besturingssysteem dat een MSIX-pakket of -toepassing die momenteel niet wordt uitgevoerd en die niet is gefaseerd voor een gebruiker, kan worden ontkoppeld. Hiermee verwijdert u alle verwijzingen in het besturingssysteem.

## <a name="cim"></a>Cim

. CIM is een nieuwe bestandsextensie die is gekoppeld aan CimFS (Composite Image Files System). CIM-bestanden kunnen sneller worden bevestigd en ontkoppeld dan VHD-bestanden. CIM verbruikt ook minder CPU en geheugen dan VHD.

Een CIM-bestand is een bestand met een . CIM-extensie met metagegevens en ten minste zes extra bestanden die werkelijke gegevens bevatten. De bestanden in het CIM-bestand hebben geen extensies. De volgende tabel is een lijst met voorbeeldbestanden die u in een CIM zou vinden:

| Bestandsnaam | Toestelnummer | Grootte |
|-----------|-----------|------|
| Vsc | Cim | 1 kB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 27 kB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 20 kB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 42 kB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 428 kB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 217 kB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 264.132 kB |

De volgende tabel is een vergelijking van de prestaties tussen VHD en CimFS. Deze getallen zijn het resultaat van een test met 500 bestanden van 300 MB in elke indeling die worden uitgevoerd op een DSv4-computer.

|  Specificaties                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| Gemiddelde tijd voor de aaneensteltijd     | 356 ms                     | 255 ms      |
| Gemiddelde ontkoppeltijd   | 1615 ms                    | 36 ms       |
| Geheugenverbruik | 6% (van 8 GB)                      | 2% (van 8 GB)       |
| CPU (piek aantal)          | Maximum aantal keren uit | Geen impact |

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over het koppelen van de MSIX-app, raadpleegt u ons [overzicht en](what-is-app-attach.md) de [veelgestelde vragen.](app-attach-faq.md) Ga anders aan de slag met [App koppelen instellen.](app-attach.md)
