---
title: Certificaten vereisten en probleem oplossing met Azure Stack Edge Pro | Microsoft Docs
description: Beschrijft de vereisten voor certificaten en het oplossen van problemen met certificaat fouten met Azure Stack Edge Pro-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: de41bd030ea73ac68bfac5fbfbd03ae14cf7980f
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874233"
---
# <a name="certificate-requirements"></a>Certificaatvereisten

In dit artikel worden de certificaat vereisten beschreven waaraan moet worden voldaan voordat certificaten kunnen worden geïnstalleerd op uw Azure Stack Edge Pro-apparaat. De vereisten zijn gerelateerd aan de PFX-certificaten, de uitgevende instantie, de onderwerpnaam van het certificaat en de alternatieve naam voor het onderwerp en ondersteunde certificaat algoritmen.

## <a name="certificate-issuing-authority"></a>Instantie voor het verlenen van certificaten

De volgende vereisten gelden voor het verlenen van certificaten:

* Certificaten moeten worden uitgegeven door een interne certificerings instantie of door een open bare certificerings instantie.

* Het gebruik van zelfondertekende certificaten wordt niet ondersteund.

* Het certificaat *dat is verleend aan:* , mag niet hetzelfde zijn als het veld *uitgereikt door:* , behalve basis-CA-certificaten.


## <a name="certificate-algorithms"></a>Certificaat algoritmen

Certificaat algoritmen moeten aan de volgende vereisten voldoen:

* Certificaten moeten gebruikmaken van de RSA-sleutel algoritme.

* Alleen RSA-certificaten met micro soft RSA/SChannel Cryptographic Provider worden ondersteund.

* Het algoritme voor certificaat handtekeningen kan geen SHA1 zijn.

* De minimale sleutel grootte is 4096.

## <a name="certificate-subject-name-and-subject-alternative-name"></a>Naam van certificaat houder en alternatieve onderwerpnaam

Certificaten moeten de volgende onderwerpnaam en alternatieve naam voor onderwerp hebben:

* U kunt één certificaat gebruiken voor alle naam ruimten in de velden alternatieve naam voor onderwerp (SAN) van het certificaat. U kunt ook afzonderlijke certificaten gebruiken voor elk van de naam ruimten. Bij beide benaderingen moet u Joker tekens gebruiken voor eind punten, waar vereist, zoals een binair large object (BLOB).

* Zorg ervoor dat de namen van de onderwerpen (algemene naam in de onderwerpnaam) deel uitmaken van alternatieve namen voor onderwerp in de extensie Alternatieve naam voor onderwerp.

* U kunt één certificaat met Joker tekens gebruiken voor alle naam ruimten in de SAN-velden van het certificaat.

* Gebruik de volgende tabel bij het maken van een eindpunt certificaat:

    |Type |Onderwerpnaam (SN)  |Alternatieve naam voor onderwerp (SAN)  |Voor beeld van onderwerpnaam |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Lokale gebruikers interface| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |Multi-SAN één certificaat voor beide eind punten|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |Knooppunt|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate is hardcoded.  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>PFX-certificaat

De PFX-certificaten die op uw Azure Stack Edge Pro-apparaat zijn geïnstalleerd, moeten voldoen aan de volgende vereisten:

* Wanneer u uw certificaten van de SSL-instantie ontvangt, zorgt u ervoor dat u de volledige handtekening keten voor de certificaten krijgt.

* Wanneer u een PFX-certificaat exporteert, moet u ervoor zorgen dat u de optie **alle certificaten in de keten toevoegen, indien mogelijk** hebt geselecteerd.

* Gebruik een PFX-certificaat voor eind punt, lokale gebruikers interface, knoop punt, VPN en Wi-Fi als de open bare en persoonlijke sleutels vereist zijn voor Azure Stack Edge Pro. Voor de persoonlijke sleutel moet het sleutel kenmerk van de lokale computer zijn ingesteld.

* De PFX-versleuteling van het certificaat moet 3DES zijn. Dit is de standaard versleuteling die wordt gebruikt bij het exporteren van een Windows 10-client of een Windows Server 2016-certificaat archief. Zie [Triple des](https://en.wikipedia.org/wiki/Triple_DES)voor meer informatie over 3DES.

* De PFX-bestanden van het certificaat moeten geldige waarden voor *digitale hand tekeningen* en *KeyEncipherment* bevatten in het veld *sleutel gebruik* .

* De PFX-bestanden van het certificaat moeten de waarden *server authenticatie (1.3.6.1.5.5.7.3.1)* en *client verificatie (1.3.6.1.5.5.7.3.2)* hebben in het veld *uitgebreid sleutel gebruik* .

* De wacht woorden voor alle PFX-bestanden van het certificaat moeten gelijk zijn op het moment van de implementatie als u het hulp programma Azure Stack Readiness Checker gebruikt. Zie [certificaten voor uw Azure stack Edge Pro maken met behulp van Azure stack hub-gereedheids controleprogramma](azure-stack-edge-j-series-create-certificates-tool.md)voor meer informatie.

* Het wacht woord voor het PFX van het certificaat moet een complex wacht woord zijn. Noteer dit wacht woord, omdat dit wordt gebruikt als een implementatie parameter.

* Gebruik alleen RSA-certificaten met de cryptografische micro soft RSA/SChannel-provider.

Zie [PFX-certificaten exporteren met de persoonlijke sleutel](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Certificaten gebruiken met Azure Stack Edge Pro](azure-stack-edge-j-series-manage-certificates.md)

[Certificaten maken voor uw Azure Stack Edge Pro met behulp van Azure Stack hub-gereedheids controleprogramma](azure-stack-edge-j-series-create-certificates-tool.md)

[PFX-certificaten exporteren met de persoonlijke sleutel](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[Certificaat fouten oplossen](azure-stack-edge-j-series-certificate-troubleshooting.md)