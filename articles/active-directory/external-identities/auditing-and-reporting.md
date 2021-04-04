---
title: Een B2B-samenwerkings gebruiker controleren en rapporteren-Azure AD
description: Eigenschappen van gast gebruikers kunnen worden geconfigureerd in Azure Active Directory B2B-samen werking
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a805f643e5f93270fe4721f527073baa9a1e4c8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87908762"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Een B2B-samenwerkings gebruiker controleren en rapporteren
Met gast gebruikers hebt u controle mogelijkheden die vergelijkbaar zijn met gebruikers van leden. 

## <a name="access-reviews"></a>Toegangsbeoordelingen
U kunt toegangs beoordelingen gebruiken om periodiek te controleren of gast gebruikers nog steeds toegang tot uw resources nodig hebben. De functie **toegangs beoordelingen** is beschikbaar in **Azure Active Directory** onder **externe identiteiten**  >  **toegangs beoordelingen**. U kunt ook zoeken naar ' toegangs beoordelingen ' van **alle services** in de Azure Portal. Zie [gast toegang beheren met Azure AD-toegangs beoordelingen](../governance/manage-guest-access-with-access-reviews.md)voor meer informatie over het gebruik van toegangs Beoordelingen.

## <a name="audit-logs"></a>Auditlogboeken

De audit logboeken van Azure AD bieden records van systeem-en gebruikers activiteiten, waaronder activiteiten die worden geïnitieerd door gast gebruikers. Als u de controle logboeken wilt openen, selecteert u in **Azure Active Directory** **controle logboeken** onder **bewaking**. Hier volgt een voor beeld van de uitnodiging en de geschiedenis van de genodigden van de SAM oogle:

![Scherm afbeelding met een voor beeld van een controle logboek uitvoer](./media/auditing-and-reporting/audit-log.png)

U kunt aan elk van deze gebeurtenissen beginnen om de details op te halen. Laten we bijvoorbeeld de acceptatie gegevens bekijken.

![Scherm afbeelding met een voor beeld van uitvoer van de activiteit Details](./media/auditing-and-reporting/activity-details.png)

U kunt deze logboeken ook exporteren vanuit Azure AD en het rapportage programma gebruiken om aangepaste rapporten op te halen.

### <a name="next-steps"></a>Volgende stappen

- [Eigenschappen van gebruikers van B2B-samenwerking](user-properties.md)

