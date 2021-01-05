---
title: Activiteiten van de Privécloud controleren
titleSuffix: Azure VMware Solution by CloudSimple
description: Beschrijft de beschik bare informatie over activiteiten in de Azure VMware-oplossing op CloudSimple omgeving, met inbegrip van waarschuwingen, gebeurtenissen, taken en controle.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb597cc71d228a1daaaa4e52d76b301764ff9292
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899300"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>VMware-oplossing op CloudSimple-activiteit bewaken

CloudSimple-activiteiten logboeken bieden inzicht in de bewerkingen die worden uitgevoerd op de CloudSimple-Portal.  De lijst bevat waarschuwingen, gebeurtenissen, taken en controle.  Gebruik de activiteiten Logboeken om te bepalen wie, wanneer en welke bewerkingen zijn uitgevoerd.  De activiteiten logboeken bevatten geen lees bewerkingen die door een gebruiker worden uitgevoerd.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Toegang tot de CloudSimple-portal

Toegang tot de [CloudSimple-Portal](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Activiteit gegevens

Selecteer **Activity** in het menu aan de zijkant om toegang te krijgen tot de activiteiten pagina's.

![Overzicht van de activiteit pagina](media/activity-page-overview.png)

Als u details over een van de activiteiten op de pagina activiteit wilt weer geven, selecteert u de activiteit. Aan de rechter kant wordt een detail venster geopend. De acties in het deel venster zijn afhankelijk van het type activiteit. Klik op **X** om het deel venster te sluiten.

Klik op de kolomkop om de weer gave te sorteren.  U kunt kolommen filteren op specifieke waarden om weer te geven.  Down load het activiteiten rapport door te klikken op **downloaden als CSV** -pictogram.

## <a name="alerts"></a>Waarschuwingen

Waarschuwingen zijn meldingen van belang rijke activiteiten in uw CloudSimple-omgeving.  Waarschuwingen bevatten gebeurtenissen die van invloed zijn op facturering of gebruikers toegang.

Als u waarschuwingen wilt bevestigen en wilt verwijderen uit de lijst, selecteert u een of meer in de lijst en klikt u op **bevestigen**.

De volgende kolommen met informatie zijn beschikbaar voor waarschuwingen. Klik op **kolommen bewerken** en selecteer de kolommen die u wilt weer geven.

| Kolom | Beschrijving |
------------ | ------------- |
| Waarschuwings type | Categorie van de waarschuwing.|
| Tijd | Tijdstip waarop de waarschuwing is opgetreden. |
| Severity | Significantie van de waarschuwing.|
| Resourcenaam | De naam die is toegewezen aan de resource, zoals de naam van de Privécloud. |
| Resourcetype | Categorie van resource: Privécloud, Cloud rack. |
| Resource-id | De id van de resource. |
| Beschrijving | Beschrijving van de trigger van de waarschuwing. |
| Bevestigd | Indicatie of de waarschuwing wordt bevestigd. |

## <a name="events"></a>Gebeurtenissen

Gebeurtenissen tonen gebruikers-en systeem activiteiten in de CloudSimple-Portal. Op de pagina gebeurtenissen wordt een lijst weer gegeven met de activiteit die is gekoppeld aan een specifieke resource en de ernst van de impact.

De volgende kolommen met informatie zijn beschikbaar voor waarschuwingen. Klik op **kolommen bewerken** en selecteer de kolommen die u wilt weer geven.

| Kolom | Beschrijving |
------------ | ------------- |
| Tijd | De datum en tijd waarop de gebeurtenis heeft plaatsgevonden. |
| Gebeurtenistype | Numerieke code waarmee de gebeurtenis wordt aangeduid. |
| Severity | Ernst van gebeurtenis.|
| Resourcenaam | De naam die is toegewezen aan de resource, zoals de naam van de Privécloud. |
| Resourcetype | Categorie van resource: Privécloud, Cloud rack. |
| Beschrijving | Beschrijving van de trigger van de waarschuwing. |

## <a name="tasks"></a>Taken

Taken zijn activiteiten in particuliere Clouds waarvan wordt verwacht dat deze 30 seconden of langer duren. (Activiteiten die naar verwachting minder dan 30 seconden duren, worden alleen gerapporteerd als gebeurtenissen.) Open de pagina taken om de voortgang van taken voor uw Privécloud bij te houden.

De volgende kolommen met informatie zijn beschikbaar voor waarschuwingen. Klik op **kolommen bewerken** en selecteer de kolommen die u wilt weer geven.

| Kolom | Beschrijving |
------------ | ------------- |
| Taak-ID | De unieke id voor de taak. |
| Bewerking | De actie die door de taak wordt uitgevoerd. |
| Gebruiker | De gebruiker is toegewezen om de taak te volt ooien. |
| Resourcenaam | De naam die is toegewezen aan de resource. |
| Resourcetype | Categorie van resource: Privécloud, Cloud rack. |
| Resource-id | De id van de resource. |
| Starten | Begin tijd voor de taak. |
| Beëindigen | De eind tijd voor de taak. |
| Status | Huidige taak status. |
| Verstreken tijd | De tijd die nodig is om de taak te volt ooien (indien voltooid) of op dit moment (als deze wordt uitgevoerd). |
| Beschrijving | Taak beschrijving. |

## <a name="audit"></a>Controleren

Controle logboeken houden de activiteiten van gebruikers bij. U kunt controle Logboeken gebruiken om de gebruikers activiteit voor alle gebruikers te bewaken.

De volgende kolommen met informatie zijn beschikbaar voor waarschuwingen. Klik op **kolommen bewerken** en selecteer de kolommen die u wilt weer geven.

| Kolom | Beschrijving |
------------ | ------------- |
| Tijd | Tijd van de controle vermelding. |
| Bewerking | De actie die door de taak wordt uitgevoerd. |
| Gebruiker | De gebruiker is toegewezen aan de taak. |
| Resourcenaam | De naam die is toegewezen aan de resource. |
| Resourcetype | Categorie van resource: Privécloud, Cloud rack. |
| Resource-id | De id van de resource. |
| Resultaat | Resultaat van de activiteit, zoals **geslaagd**. |
| Gebruikte tijd | Tijd om de taak te volt ooien. |
| Beschrijving | Beschrijving van de actie. |

## <a name="next-steps"></a>Volgende stappen

* [VMware-VM's in Azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* Meer informatie over [persoonlijke Clouds](cloudsimple-private-cloud.md)
