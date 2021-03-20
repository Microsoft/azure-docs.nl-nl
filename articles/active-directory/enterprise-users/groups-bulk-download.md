---
title: Een lijst met groepen in de Azure Active Directory portal downloaden | Microsoft Docs
description: Down load groeps eigenschappen in bulk in het Azure-beheer centrum in Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98c61679fed04c0a696b60bb7ee53009a8a530e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547675"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Bulksgewijs downloaden van een lijst met groepen in Azure Active Directory

Met Azure Active Directory-Portal (Azure AD) kunt u de lijst met alle groepen in uw organisatie bulksgewijs downloaden naar een bestand met door komma's gescheiden waarden (CSV).

## <a name="to-download-a-list-of-groups"></a>Een lijst met groepen downloaden

1. Meld u aan bij [de Azure Portal](https://portal.azure.com) met een beheerders account in de organisatie.
1. Selecteer **groepen**  >  **Download groepen** in azure AD.
1. Selecteer op de pagina **groepen downloaden** de optie **Start** om een CSV-bestand te ontvangen waarin uw groepen worden weer gegeven.

   ![De opdracht groepen downloaden bevindt zich op de pagina alle groepen](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Download status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking** .

[![Controleer de status op de pagina resultaten van bulk bewerking.](./media/groups-bulk-download/bulk-center.png)](./media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Service limieten bulksgewijs downloaden

Elke bulk activiteit voor het downloaden van een groeps lijst kan Maxi maal één uur worden uitgevoerd. Hiermee kunt u een lijst met ten minste 300.000 groepen downloaden.

## <a name="next-steps"></a>Volgende stappen

- [Groeps leden bulksgewijs verwijderen](groups-bulk-remove-members.md)
- [Leden van een groep downloaden](groups-bulk-download-members.md)
