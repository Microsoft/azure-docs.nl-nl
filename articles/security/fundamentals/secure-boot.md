---
title: Veilig opstarten met firmware-Azure-beveiliging
description: Technisch overzicht van de Secure boot van Azure-firmware.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: feb28b1d448d0146046ed789d1389a3a42f344de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94557664"
---
# <a name="secure-boot"></a>Secure Boot

Secure boot is een functie van de [Unified Extensible Firmware Interface](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI) dat vereist dat alle firmware en software onderdelen op laag niveau worden gecontroleerd voordat ze worden geladen. Tijdens het opstarten controleert UEFI Secure boot de hand tekening van elk onderdeel van de opstart software, met inbegrip van UEFI-firmware Stuur programma's (ook wel bekend als optie-ROMs), Extensible Firmware Interface (EFI)-toepassingen en de Stuur Programma's en binaire bestanden van het besturings systeem. Als de hand tekeningen geldig zijn of worden vertrouwd door de OEM (Original Equipment Manufacturer), start de computer op en de firmware biedt controle over het besturings systeem.

## <a name="components-and-process"></a>Onderdelen en proces

Beveiligd opstarten is afhankelijk van de volgende essentiële onderdelen:

- Platform Key (PK): brengt een vertrouwens relatie tot stand tussen de eigenaar van het platform (micro soft) en de firmware. De open bare helft is PKpub en de persoonlijke helft is PKpriv.
- Key Data Base voor sleutel inschrijving (KEK): Hiermee wordt een vertrouwens relatie tussen het besturings systeem en de platform firmware tot stand gebracht. De open bare helft is KEKpub en de persoonlijke helft is KEKpriv.
- Signature data base (DB): bevat de samen vattingen voor vertrouwde ondertekeners (open bare sleutels en certificaten) van de firmware-en software code modules die zijn gemachtigd om te communiceren met platform firmware.
- Data Base met ingetrokken hand tekeningen (dbx): bevat ingetrokken samen vattingen van code modules die zijn geïdentificeerd als schadelijk, kwetsbaar, aangetast of niet-vertrouwd. Als een hash zich in de handtekening database bevindt en de ingetrokken hand tekeningen-data base, neemt de ingetrokken handtekeningen database een bron.

In de volgende afbeelding en het proces wordt uitgelegd hoe deze onderdelen worden bijgewerkt:

![Diagram waarin de onderdelen voor beveiligd opstarten worden weer gegeven.](./media/secure-boot/secure-boot.png)

De OEM slaat de beveiligd-boot-samen vattingen op in het niet-vluchtige RAM-geheugen (NV-RAM) van de computer op het moment van productie.

1. De Signature data base (DB) is gevuld met de Onderteken-of afbeeldings-hashes van UEFI-toepassingen, de laad Programma's van het besturings systeem (zoals het micro soft-besturings systeem of de opstart beheer) en UEFI-stuur programma 's die worden vertrouwd.
2. De data base met ingetrokken hand tekeningen (dbx) is gevuld met samen vattingen van modules die niet meer worden vertrouwd.
3. De sleutel registratie sleutel (KEK) wordt ingevuld met ondertekeningssleutel die kunnen worden gebruikt voor het bijwerken van de handtekening database en de ingetrokken handtekeningen database. De data bases kunnen worden bewerkt via updates die zijn ondertekend met de juiste sleutel of via updates door een fysiek aanwezige geautoriseerde gebruiker met behulp van firmware-menu's.
4. Nadat de data bases DB, dbx en KEK zijn toegevoegd en de definitieve validatie van de firmware is voltooid, wordt de firmware door de OEM vergrendeld om deze te bewerken en wordt een platform sleutel (PK) gegenereerd. De PK kan worden gebruikt voor het ondertekenen van updates voor de KEK of voor het uitschakelen van beveiligd opstarten.

Tijdens elke fase in het opstart proces worden de samen vattingen van de firmware, de bootloader, het besturings systeem, de kernel-Stuur Programma's en andere opstart keten artefacten berekend en vergeleken met acceptabele waarden. Firmware en software waarvan wordt ontdekt dat ze niet worden vertrouwd, mogen niet worden geladen. Zo kunt u malware-injectie op laag niveau of malware-aanvallen voorafgaand aan het opstarten blok keren.

## <a name="secure-boot-on-the-azure-fleet"></a>Beveiligd opstarten op de Azure-vloot
Vandaag de dag is elke computer die is gedistribueerd en geïmplementeerd in azure Compute-werk belastingen, afkomstig van fabrieks vloeren met beveiligd opstarten ingeschakeld. Beoogde hulp middelen en processen zijn aanwezig in elke fase van de hardware-buildout en de integratie pijplijn, om ervoor te zorgen dat de activering van beveiligd opstarten niet per ongeluk of kwaad aardig opzet wordt teruggedraaid.

Controleren of de data base-en dbx-samen vatting juist zijn, zorgt ervoor dat:

- De bootloader is aanwezig in een van de DB-vermeldingen
- De hand tekening van de bootloader is geldig
- Host wordt opgestart met vertrouwde software

 Door de hand tekeningen van KEKpub en PKpub te valideren, kunnen we bevestigen dat alleen vertrouwde partijen toestemming hebben om de definities te wijzigen van welke software als vertrouwd wordt beschouwd. Ten slotte kunnen we controleren of beveiligd opstarten actief is, kan worden gevalideerd of deze definities worden afgedwongen.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de mogelijkheden van platform integriteit en-beveiliging:

- [Firmware-beveiliging](firmware.md)
- [Gemeten opstarten en host-Attestation](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Versleuteling 'at rest'](encryption-atrest.md)
- [Hypervisor-beveiliging](hypervisor.md)