---
title: Woorden lijst voor het toevoegen van de MSIX-app van Windows virtueel bureau blad-Azure
description: Een woorden lijst met voor waarden en concepten voor het koppelen van MSIX-apps.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7132eae073f3d53a104536076ae801ec9ff93e5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96518665"
---
# <a name="msix-app-attach-glossary"></a>Woorden lijst voor het toevoegen van MSIX-apps

Dit artikel bevat een lijst met definities voor belang rijke termen en concepten die betrekking hebben op MSIX app attach.

## <a name="msix-container"></a>MSIX-container

Een MSIX-container is waar MSIX-apps worden uitgevoerd. Zie [MSIX-containers](/windows/msix/msix-container)voor meer informatie.

## <a name="msix-application"></a>MSIX-toepassing 

Een toepassing die is opgeslagen in een. MSIX-bestand.

## <a name="msix-package"></a>MSIX-pakket 

Een MSIX-pakket is een MSIX-bestand of-toepassing.

## <a name="msix-share"></a>MSIX-share

Een MSIX-share is een netwerk share die uitgevouwen MSIX-pakketten bevat. MSIX-shares bieden ondersteuning voor SMB 3 of hoger. Toepassingen worden klaargezet van deze MSIX-share zonder dat ze toepassings bestanden naar het systeem station hoeven te verplaatsen.

## <a name="msix-image"></a>MSIX-installatie kopie

Een MSIX-installatie kopie is een VHD-, VHDx-of CIM-bestand dat een of meer MSIX-verpakte toepassingen bevat. Elke toepassing wordt geleverd in de MSIX-installatie kopie met behulp van het MSIXMGR-hulp programma.

## <a name="repackage"></a>Opnieuw verpakken

Bij het opnieuw inpakken wordt een niet-MSIX toepassing gebruikt en omgezet in MSIX met behulp van het MSIX-verpakkings programma (MPT). Zie het overzicht van het [MSIX-verpakkings programma](/windows/msix/packaging-tool/tool-overview)voor meer informatie.

## <a name="expand-an-msix-package"></a>Een MSIX-pakket uitbreiden

Het uitbreiden van een MSIX-pakket is een proces dat uit meerdere stappen bestaat. Uitbrei ding neemt het MSIX-bestand op en plaatst de inhoud in een VHD (x) of een CIM-bestand. 

Een MSIX-pakket uitbreiden:

1. Een MSIX-pakket (MSIX-bestand) ophalen.
2. Wijzig de naam van het MSIX-bestand in een zip-bestand.
3. Pak het resulterende zip-bestand uit in een map.
4. Maak een VHD die even groot is als de map.
5. Koppel de VHD.
6. Initialiseer een schijf.
7. Maak een partitie.
8. Format teer de partitie.
9. Kopieer de uitgepakte inhoud naar de VHD.
10. Gebruik het hulp programma MSIXMGR om Acl's toe te passen op de inhoud van de VHD.
11. Ontkoppel de VHD (x) of [CIM](#cim).

## <a name="upload-an-msix-package"></a>Een MSIX-pakket uploaden 

Als u een MSIX-pakket uploadt, moet u de VHD (x) of [CIM](#cim) met een uitgebreid MSIX-pakket uploaden naar de MSIX-share.

In Windows Virtual Desktop worden uploads eenmaal per MSIX-share uitgevoerd. Wanneer u een pakket uploadt, kunnen alle hostgroepen in hetzelfde abonnement hiernaar verwijzen.

## <a name="add-an-msix-package"></a>Een MSIX-pakket toevoegen

In Windows Virtual Desktop voegt een MSIX-pakket toe aan een hostgroep.

## <a name="publish-an-msix-package"></a>Een MSIX-pakket publiceren 

In Windows Virtual Desktop moet een gepubliceerd MSIX-pakket worden toegewezen aan een gebruikers-of gebruikers groep van een Active Directory-domein-service (AD DS) of Azure Active Directory (Azure AD).

## <a name="staging"></a>Faseren

Fase ring omvat twee dingen:

- De VHD (x) of [CIM](#cim) koppelen aan de virtuele machine.
- Het besturings systeem op de hoogte stellen dat het MSIX-pakket beschikbaar is voor registratie.

## <a name="registration"></a>Registratie

Registratie maakt een klaargezet MSIX-pakket beschikbaar voor uw gebruikers. Registratie is per gebruiker. Als u een app niet expliciet hebt geregistreerd voor die specifieke gebruiker, kunnen ze de app niet meer uitvoeren.

Er zijn twee soorten registratie: normaal en vertraagd.

### <a name="regular-registration"></a>Normale registratie

Bij een normale registratie wordt elke toepassing die is toegewezen aan een gebruiker, volledig geregistreerd. Registratie vindt plaats wanneer de gebruiker zich aanmeldt bij de sessie. Dit kan van invloed zijn op de tijd die ze nodig hebben om te beginnen met het gebruik van Windows virtueel bureau blad.

### <a name="delayed-registration"></a>Vertraagde registratie

Bij een vertraagde registratie wordt elke toepassing die aan de gebruiker is toegewezen, slechts gedeeltelijk geregistreerd. Gedeeltelijke registratie betekent dat de tegel in het menu Start en bestands koppelingen voor dubbel klikken zijn geregistreerd. De registratie wordt uitgevoerd terwijl de gebruiker zich aanmeldt bij de sessie, zodat deze een minimale impact heeft op de tijd die nodig is om te beginnen met het gebruik van Windows virtueel bureau blad. De registratie is alleen voltooid wanneer de gebruiker de toepassing uitvoert in het MSIX-pakket.

De vertraagde registratie is momenteel de standaard configuratie voor het koppelen van de MSIX-app.

## <a name="deregistration"></a>Registratie opheffen

Bij het opheffen van de registratie wordt een geregistreerd, maar niet-uitgevoerde MSIX-pakket voor een gebruiker verwijderd. De registratie vindt plaats wanneer de gebruiker zich afmeldt bij de sessie. Tijdens het opheffen van de registratie pusht MSIX app attach toepassings gegevens die specifiek zijn voor de gebruiker naar het lokale gebruikers profiel.

## <a name="destage"></a>Destage

Met destaging wordt het besturings systeem gewaarschuwd dat een MSIX-pakket of-toepassing die momenteel niet wordt uitgevoerd en niet kan worden gekoppeld aan een wille keurige gebruiker. Hiermee wordt de verwijzing naar het bestand in het besturings systeem verwijderd.

## <a name="cim"></a>VRACHT

. CIM is een nieuwe bestands extensie die is gekoppeld aan een samengesteld installatie kopie bestand systeem (CimFS). Het koppelen en ontkoppelen van CIM-bestanden verloopt sneller dan die VHD-bestanden. CIM verbruikt ook minder CPU en geheugen dan VHD.

Een CIM-bestand is een bestand met een. CIM-uitbrei ding die meta gegevens en ten minste zes extra bestanden bevat die actuele gegevens bevatten. De bestanden in het CIM-bestand hebben geen extensies. De volgende tabel bevat een lijst met voorbeeld bestanden die u in een CIM zou zoeken:

| Bestandsnaam | Toestelnummer | Grootte |
|-----------|-----------|------|
| VSC | VRACHT | 1 kB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 27 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 20 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 42 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 428 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 217 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 264.132 KB |

De volgende tabel is een prestatie vergelijking tussen VHD en CimFS. Deze getallen zijn het resultaat van een test uitvoering met 500 300 MB aan bestanden in elke indeling die wordt uitgevoerd op een DSv4-machine.

|  Specificaties                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| Gemiddelde koppelings tijd     | 356 MS                     | 255 MS      |
| Gemiddelde ontkoppelings tijd   | 1615 MS                    | 36 MS       |
| Geheugenverbruik | 6% (van 8 GB)                      | 2% (van 8 GB)       |
| CPU (aantal pieken)          | Meerdere keren benut | Geen invloed |

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over het toevoegen van MSIX-apps, raadpleegt u ons [overzicht](what-is-app-attach.md) en [Veelgestelde vragen](app-attach-faq.md). U kunt ook aan de slag met het [instellen van een app-koppeling](app-attach.md).
