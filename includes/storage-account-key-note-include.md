---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ba715d510dc296ffa8f9c0ee58841f284416a118
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86027325"
---
## <a name="protect-your-access-keys"></a>Uw toegangs sleutels beveiligen

De toegangs sleutels voor uw opslag account zijn vergelijkbaar met een basis wachtwoord voor uw opslag account. Zorg ervoor dat u altijd uw toegangs sleutels beveiligt. Gebruik Azure Key Vault om uw sleutels veilig te beheren en te draaien. Vermijd het distribueren van toegangs sleutels naar andere gebruikers, het afwijzen van de code of het opslaan van berichten naar een wille keurige locatie in tekst zonder opmaak die voor anderen toegankelijk is. Roteer uw sleutels als u denkt dat ze mogelijk zijn aangetast.

> [!NOTE]
> Micro soft raadt u aan Azure Active Directory (Azure AD) te gebruiken om aanvragen te autoriseren tegen Blob-en wachtrij gegevens, indien mogelijk, in plaats van een gedeelde sleutel. Azure AD biedt een superieure beveiliging en gebruiks vriendelijk gebruik over gedeelde sleutels. Zie [toegang tot Azure-blobs en-wacht rijen toestaan met Azure Active Directory](../articles/storage/common/storage-auth-aad.md)voor meer informatie over het machtigen van toegang tot gegevens met Azure AD.
