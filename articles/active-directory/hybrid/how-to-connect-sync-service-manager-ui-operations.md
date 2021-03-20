---
title: Synchronization Service Manager bewerkingen Azure AD Connect | Microsoft Docs
description: Meer informatie over het tabblad bewerkingen in de Synchronization Service Manager voor Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64ab5901ad54e37fc05d7da757ce7f31d42fe3b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85357100"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Het tabblad Synchronisatie Service Manager bewerkingen gebruiken

![Service Manager synchroniseren](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

Op het tabblad bewerkingen worden de resultaten van de meest recente bewerkingen weer gegeven. Dit tabblad is essentieel voor het begrijpen en oplossen van problemen.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Inzicht in de informatie die wordt weer gegeven op het tabblad bewerkingen
In het bovenste gedeelte worden alle uitvoeringen in chronologische volg orde weer gegeven. Standaard houdt het operations-logboek informatie over de afgelopen zeven dagen, maar deze instelling kan worden gewijzigd met de [scheduler](how-to-connect-sync-feature-scheduler.md). U wilt zoeken naar een wille keurige uitvoering waarvoor geen succes status wordt weer gegeven. U kunt de sorteer volgorde wijzigen door te klikken op de kopteksten.

De kolom **status** is de belangrijkste informatie en toont het ernstigste probleem voor een uitvoering. Hier volgt een korte samen vatting van de meest voorkomende statussen in volg orde van prioriteit om te onderzoeken (waarbij * duidt op meerdere mogelijke fout teken reeksen).

| Status | Opmerking |
| --- | --- |
| gestopt\* |De uitvoering is niet voltooid. Bijvoorbeeld, als het externe systeem niet actief is en er geen contact met kan worden opgenomen. |
| gestopt-fout limiet |Er zijn meer dan 5.000 fouten. De uitvoering is automatisch gestopt vanwege het grote aantal fouten. |
| voltooid- \* -fouten |De uitvoering is voltooid, maar er zijn fouten (minder dan 5.000) die moeten worden onderzocht. |
| voltooid- \* -waarschuwingen |De uitvoering is voltooid, maar sommige gegevens hebben niet de verwachte status. Als er fouten zijn, is dit bericht meestal alleen een symptoom. Als u fouten hebt opgelost, moet u geen waarschuwingen onderzoeken. |
| voltooid |Geen problemen. |

Wanneer u een rij selecteert, worden de onderste updates weer gegeven met de details van die uitvoering. Helemaal links van de onderkant hebt u mogelijk een lijst met de melding **stap #**. Deze lijst wordt alleen weer gegeven als u meerdere domeinen in uw forest hebt waarin elk domein door een stap wordt vertegenwoordigd. De domein naam kan worden gevonden onder de koptekst **partitie**. Onder **synchronisatie statistieken** vindt u meer informatie over het aantal wijzigingen dat is verwerkt. U kunt op de koppelingen klikken om een lijst met gewijzigde objecten op te halen. Als u objecten met fouten hebt, worden deze fouten weer gegeven onder **synchronisatie fouten**.

Zie [problemen oplossen met een object dat niet wordt gesynchroniseerd](tshoot-connect-object-not-syncing.md) voor meer informatie

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md) configuratie.

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
