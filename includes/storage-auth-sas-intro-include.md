---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75371770"
---
Met een Shared Access Signature (SAS) kunt u beperkte toegang verlenen tot containers en blobs in uw opslag account. Wanneer u een SAS maakt, geeft u de beperkingen op, waaronder Azure Storage bronnen die een client mag openen, welke machtigingen ze hebben op deze resources en hoe lang de SAS geldig is.

Elke SAS is ondertekend met een sleutel. U kunt op een van de volgende twee manieren een SAS ondertekenen:

- Met een sleutel die is gemaakt met behulp van Azure Active Directory-referenties (Azure AD). Een SAS die is ondertekend met Azure AD-referenties, is een *gebruikers delegering* sa's.
- Met de sleutel van het opslag account. Zowel een *service-SAS* als een *account-SAS* zijn ondertekend met de sleutel van het opslag account.

Een SAS voor gebruikers overdracht biedt superieure beveiliging voor een SAS die is ondertekend met de sleutel van het opslag account. Micro soft raadt u aan om, indien mogelijk, een SAS-gebruiker te gebruiken. Zie [beperkte toegang verlenen tot gegevens met Shared Access signatures (SAS)](../articles/storage/common/storage-sas-overview.md)voor meer informatie.
