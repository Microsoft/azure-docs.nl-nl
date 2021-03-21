---
title: Bulksgewijs uploaden om leden van een groep toe te voegen of te maken-Azure Active Directory | Microsoft Docs
description: Voeg groeps leden in bulk toe in het Azure Active Directory-beheer centrum.
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
ms.openlocfilehash: 22d39a2ee66f2c63612ad2cb3cf9ae61f1660de3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547743"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>Groeps leden bulksgewijs toevoegen in Azure Active Directory

Met Azure Active Directory-Portal (Azure AD) kunt u een groot aantal leden toevoegen aan een groep met behulp van een bestand met door komma's gescheiden waarden (CSV) voor het bulk importeren van groeps leden.

## <a name="understand-the-csv-template"></a>Inzicht in de CSV-sjabloon

Down load en vul de CSV-sjabloon bulksgewijs uploaden in om Azure AD-groeps leden bulksgewijs toe te voegen. Uw CSV-sjabloon kan er als volgt uitzien:

![Werkblad voor uploaden en aanroepen, waarin het doel en de waarden voor elke rij en kolom worden uitgelegd](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>CSV-sjabloonstructuur

De rijen in een gedownloade CSV-sjabloon zijn als volgt:

- **Versienummer**: De eerste rij met het versienummer moet worden opgenomen in het CSV-uploadbestand.
- **Kolomkoppen**: De indeling van de kolomkoppen is &lt;*itemnaam*&gt; [eigenschapsnaam] &lt;*Required of leeg*&gt;. Bijvoorbeeld `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Sommige oudere versies van de sjabloon kunnen iets afwijken. Voor wijzigingen in groepslid maatschappen hebt u de mogelijkheid om te gebruiken id: lid object-ID of user principal name.
- **Rij met voorbeelden**: We hebben in de sjabloon een rij met voorbeelden van acceptabele waarden voor elke kolom opgenomen. U moet de rij met voorbeelden verwijderen en vervangen door uw eigen invoerwaarden.

### <a name="additional-guidance"></a>Aanvullende richtlijnen

- De eerste twee rijen van de uploadsjabloon mogen niet worden verwijderd of gewijzigd, anders kan de upload niet worden verwerkt.
- De vereiste kolommen worden het eerst weergegeven.
- Het is niet raadzaam om nieuwe kolommen aan de sjabloon toe te voegen. Eventuele extra kolommen die u toevoegt, worden genegeerd en niet verwerkt.
- U wordt aangeraden altijd de meest recente versie van de CSV-sjabloon te downloaden.
- Voeg ten minste twee Upn's of object-Id's van gebruikers toe om het bestand te uploaden.

## <a name="to-bulk-import-group-members"></a>Groeps leden bulksgewijs importeren

1. Meld u aan bij [de Azure Portal](https://portal.azure.com) met een Administrator-account van de gebruiker in de organisatie. Groeps eigenaren kunnen ook leden van groepen waarvan ze eigenaar zijn, bulksgewijs importeren.
1. Selecteer **groepen**  >  **alle groepen** in azure AD.
1. Open de groep waaraan u leden wilt toevoegen en selecteer vervolgens **leden**.
1. Selecteer **leden importeren** op de pagina **leden** .
1. Selecteer op de pagina **groeps leden Bulk Import** de optie **downloaden** om de CSV-bestands sjabloon met de vereiste eigenschappen voor de groeps leden op te halen.

    ![De opdracht leden importeren bevindt zich op de profiel pagina voor de groep](./media/groups-bulk-import-members/import-panel.png)

1. Open het CSV-bestand en voeg een regel toe voor elk groepslid dat u wilt importeren in de groep (de vereiste waarden zijn een **Member object-id** of **User Principal name**). Sla het bestand op.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="Het CSV-bestand bevat de namen en Id's van de leden die moeten worden geïmporteerd":::

1. Blader op de pagina **groeps leden Bulk Import** onder **uw CSV-bestand uploaden** naar het bestand. Wanneer u het bestand selecteert, wordt de validatie van het CSV-bestand gestart.
1. Wanneer de bestands inhoud is gevalideerd, wordt het **bestand dat is geüpload** weer gegeven op de pagina Bulk Import. Als er fouten zijn, moet u deze corrigeren voordat u de taak kunt verzenden.
1. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om de Azure bulk bewerking te starten waarmee de groeps leden in de groep worden geïmporteerd.
1. Wanneer de import bewerking is voltooid, ziet u een melding dat de bulk bewerking is geslaagd.

## <a name="check-import-status"></a>Import status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking** .

[![Controleer de status op de pagina resultaten van bulk bewerking.](./media/groups-bulk-import-members/bulk-center.png)](./media/groups-bulk-import-members/bulk-center.png#lightbox)

Selecteer de waarden onder de kolommen **# geslaagd**, **# fout** of **Totaal aantal aanvragen** voor meer informatie over elk regel item in de bulk bewerking. Als er fouten zijn opgetreden, worden de redenen voor de fouten weergegeven.

## <a name="bulk-import-service-limits"></a>Service limieten voor bulk import

Elke bulk activiteit voor het importeren van een lijst met groeps leden kan Maxi maal één uur worden uitgevoerd. Hiermee kan een lijst van Maxi maal 40.000 leden worden ingevoerd.

## <a name="next-steps"></a>Volgende stappen

- [Groeps leden bulksgewijs verwijderen](groups-bulk-remove-members.md)
- [Leden van een groep downloaden](groups-bulk-download-members.md)
- [Een lijst met alle groepen downloaden](groups-bulk-download.md)
