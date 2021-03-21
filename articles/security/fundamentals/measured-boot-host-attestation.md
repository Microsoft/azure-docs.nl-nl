---
title: Gemeten firmware-opstart-en host Attestation-Azure-beveiliging
description: Technisch overzicht van de gemeten opstart-en host attestatie van Azure firmware.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f4906d4e0590df047bac4ee15cb0e996a59c3d5b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498860"
---
# <a name="measured-boot-and-host-attestation"></a>Gemeten opstarten en host-Attestation
In dit artikel wordt beschreven hoe micro soft de integriteit en beveiliging van hosts garandeert door middel van gemeten opstarten en host Attestation.

## <a name="measured-boot"></a>Gemeten opstart

De [Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-top-node) (TPM) is een onrecht matige proef, cryptografisch beveiligd controle onderdeel met firmware geleverd door een vertrouwde derde partij. Het logboek met opstart configuratie bevat hash-aaneengeschakelde metingen die zijn vastgelegd in de platform configuratie registers (PCR) wanneer de host de laatste keer de Boots trapping sequentie heeft gestaan. In de volgende afbeelding ziet u dit proces. Incrementeel het toevoegen van een eerder gehashte meting aan de hash van de volgende meting en het uitvoeren van het hash-algoritme voor de samenvoeg bewerking maakt hash-koppeling.

![Diagram waarin de hash-koppeling van de Attestation-service van de host wordt weer gegeven.](./media/measured-boot-host-attestation/hash-chaining.png)

Attestation wordt uitgevoerd wanneer een host bewijst van de configuratie status met behulp van het opstart configuratie logboek (TCGLog). Vervalsing van een opstart logboek is lastig, omdat de bijbehorende PCR-waarden niet zichtbaar zijn voor de TPM. Daarnaast worden de referenties die zijn opgegeven door de host Attestation-service verzegeld met specifieke PCR-waarden. Het gebruik van hash-Chaining maakt het berekenen van het proces niet haalbaar voor het vervalsen of verzegelen van de referenties buiten de band.

## <a name="host-attestation-service"></a>Host Attestation-service

De host Attestation-service is een preventieve maat regel die controleert of de hostcomputers betrouwbaar zijn voordat ze kunnen communiceren met klant gegevens of workloads. Controles van de host Attestation-service door het valideren van een nalevings verklaring (Controleer bare bewijs van de naleving van de host) die door elke host wordt verzonden met een Attestation-beleid (definitie van de beveiligde status). De integriteit van dit systeem wordt gegarandeerd door een door een TPM verschafte [basis-vertrouwens relatie](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) .

De host Attestation-service is aanwezig in elk Azure-cluster binnen een gespecialiseerde, vergrendelde omgeving. De vergrendelde omgeving bevat andere gate keeper-services die deel nemen aan het Boots trap Protocol van de hostcomputer. Een open bare-sleutel infrastructuur (PKI) fungeert als een intermediair voor het valideren van de herkomst van attest aanvragen en als een identiteits verlener (afhankelijk van geslaagde Attestation van de host). De na Attestation-referenties die aan de attesting-host zijn verleend, worden verzegeld met de identiteit. Alleen de aangevraagde host kan de referenties onverzegel maken en gebruiken om incrementele machtigingen te verkrijgen. Dit voor komt het voor man-in-the-Middle-en spoofing-aanvallen.

Als een Azure-host van de fabriek arriveert met een onjuiste beveiligings configuratie of is geknoeid met het Data Center, bevat de TCGLog indica toren die door de host Attestation-service worden gemarkeerd bij de volgende Attestation-bewerking, waardoor een Attestation-fout wordt veroorzaakt. Door Attestation-fouten kan de Azure-vloot de onjuiste host niet vertrouwen. Deze preventie blokkeert effectief alle communicatie van en naar de host en activeert een incident werk stroom. Onderzoek en een gedetailleerde controle op de postmortemkeuring worden uitgevoerd om de hoofd oorzaken en mogelijke aanwijzingen van inbreuk te bepalen. Het is pas nadat de analyse is voltooid, en de kans bestaat dat een host aan de Azure-vloot wordt toegevoegd en op de werk belasting van de klant kan worden uitgevoerd.

Hieronder volgt een architectuur op hoog niveau van de host Attestation-service:

![Diagram waarin de architectuur van de Attestation-service van de host wordt weer gegeven.](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>Attestation-metingen

Hier volgen enkele voor beelden van de vele metingen die vandaag zijn vastgelegd.

### <a name="secure-boot-and-secure-boot-keys"></a>Secure boot-en Secure boot-sleutels
Door te valideren dat de data base-samen vattingen van de handtekening database en de ingetrokken hand tekeningen juist zijn, zorgt de host Attestation-service ervoor dat de client agent de juiste software betrouwt. Door de hand tekeningen te valideren van de data base van de inschrijvings sleutel voor de open bare sleutel en de sleutel van het open bare platform, wordt met de Attestation-service van de host bevestigd dat alleen vertrouwde partijen toestemming hebben om de definities van welke software als vertrouwd worden beschouwd. Ten slotte, door ervoor te zorgen dat beveiligd opstarten actief is, controleert de host Attestation-service of deze definities worden afgedwongen.

### <a name="debug-controls"></a>Besturings elementen voor fout opsporing
Fout opsporing zijn krachtige hulp middelen voor ontwikkel aars. De onbelemmerde toegang tot geheugen en andere opdrachten voor fout opsporing kan echter de gegevens bescherming en de systeem integriteit verzwakken als deze aan een niet-vertrouwde partij wordt gegeven. De host Attestation-service zorgt ervoor dat elke soort fout opsporing wordt uitgeschakeld bij het opstarten op productie machines.

### <a name="code-integrity"></a>Code-integriteit
UEFI [Secure boot](secure-boot.md) zorgt ervoor dat alleen betrouw bare software met een laag niveau kan worden uitgevoerd tijdens de opstart procedure. Dezelfde controles moeten echter ook worden toegepast in de omgeving na het opstarten naar stuur Programma's en andere uitvoer bare bestanden met toegang tot de kernelmodus. Daartoe wordt een beleid voor code-integriteit (CI) gebruikt om te definiëren welke Stuur Programma's, binaire bestanden en andere uitvoer bare bestanden worden beschouwd als vertrouwd door geldige en ongeldige hand tekeningen op te geven. Deze beleids regels worden afgedwongen. Schendingen van beleid genereren waarschuwingen voor het beveiligings incident antwoord team voor onderzoek.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de mogelijkheden van platform integriteit en-beveiliging:

- [Firmware-beveiliging](firmware.md)
- [Beveiligd opstarten](secure-boot.md)
- [Project Cerberus](project-cerberus.md)
- [Versleuteling 'at rest'](encryption-atrest.md)
- [Hypervisor-beveiliging](hypervisor.md)