---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76118092"
---
## <a name="about-the-user-delegation-sas"></a>Over de SAS voor gebruikers overdracht

Een SAS-token voor toegang tot een container of Blob kan worden beveiligd met behulp van de referenties voor Azure AD of een account sleutel. Een SAS die is beveiligd met Azure AD-referenties, wordt een SAS voor gebruikers overdracht genoemd, omdat het OAuth 2,0-token dat wordt gebruikt om de SA'S te ondertekenen, wordt aangevraagd namens de gebruiker.

Micro soft raadt u aan om Azure AD-referenties, indien mogelijk, te gebruiken als een beveiligings best practice, in plaats van de account sleutel te gebruiken, wat eenvoudiger kan worden aangetast. Wanneer het ontwerp van uw toepassing gedeelde toegangs handtekeningen vereist, gebruikt u Azure AD-referenties om een gebruikers delegering SA'S voor superieure beveiliging te maken. Zie [een gebruiker delegering Sa's maken](/rest/api/storageservices/create-user-delegation-sas)voor meer informatie over de sa's van de gebruikers overdracht.

> [!CAUTION]
> Elke client die beschikt over een geldige SAS heeft toegang tot gegevens in uw opslag account zoals toegestaan door die SAS. Het is belang rijk om een SAS te beschermen tegen kwaad aardig of onbedoeld gebruik. Gebruik de keuze voor het distribueren van een SAS en een plan voor het intrekken van een besmette SAS.

Zie voor meer informatie over gedeelde toegangs handtekeningen [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../articles/storage/common/storage-sas-overview.md).
