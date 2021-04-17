---
author: baanders
description: bestand opnemen met een beschrijving van de beperking voor alle tenants met Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 16684f8c5947f8b6a09a9a785968dabf3e644c18
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589328"
---
Als gevolg hiervan is voor aanvragen naar Azure Digital Twins API's een gebruiker of service-principal vereist die deel uitmaakt van dezelfde tenant waarin de Azure Digital Twins zich bevindt. Om schadelijke scans van Azure Digital Twins eindpunten te voorkomen, krijgen aanvragen met toegangstokens van buiten de oorspronkelijke tenant het foutbericht '404 Sub-Domain niet gevonden'. Deze fout wordt  zelfs geretourneerd als de gebruiker of service-principal de rol Azure Digital Twins [](../articles/digital-twins/concepts-security.md) Gegevenseigenaar of Azure Digital Twins-gegevenslezer heeft gekregen via [Azure AD B2B-samenwerking.](../articles/active-directory/external-identities/what-is-b2b.md) 