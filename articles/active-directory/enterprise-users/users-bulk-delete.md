---
title: Gebruikers bulksgewijs verwijderen in de Azure Active Directory Portal | Microsoft Docs
description: Gebruikers bulksgewijs verwijderen in het Azure-beheer centrum in Azure Active Directory
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
ms.openlocfilehash: b57d675003c1ebeb29927b86338f95cf7dd68090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574460"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Gebruikers bulksgewijs verwijderen in Azure Active Directory

Met Azure Active Directory-Portal (Azure AD) kunt u een groot aantal leden verwijderen uit een groep met behulp van een bestand met door komma's gescheiden waarden (CSV) om gebruikers bulksgewijs te verwijderen.

## <a name="understand-the-csv-template"></a>Inzicht in de CSV-sjabloon

Down load en vul de CSV-sjabloon in om Azure AD-gebruikers bulksgewijs te kunnen verwijderen. De CSV-sjabloon die u downloadt, kan eruitzien als in dit voorbeeld:

![Werkblad voor uploaden en aanroepen, waarin het doel en de waarden voor elke rij en kolom worden uitgelegd](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>CSV-sjabloonstructuur

De rijen in een gedownloade CSV-sjabloon zijn als volgt:

- **Versienummer**: De eerste rij met het versienummer moet worden opgenomen in het CSV-uploadbestand.
- **Kolomkoppen**: De indeling van de kolomkoppen is &lt;*itemnaam*&gt; [eigenschapsnaam] &lt;*Required of leeg*&gt;. Bijvoorbeeld `User name [userPrincipalName] Required`. Sommige oudere versies van de sjabloon kunnen iets afwijken.
- **Rij met voorbeelden**: We hebben in de sjabloon een rij met voorbeelden van acceptabele waarden voor elke kolom opgenomen. U moet de rij met voorbeelden verwijderen en vervangen door uw eigen invoerwaarden.

### <a name="additional-guidance"></a>Aanvullende richtlijnen

- De eerste twee rijen van de uploadsjabloon mogen niet worden verwijderd of gewijzigd, anders kan de upload niet worden verwerkt.
- De vereiste kolommen worden het eerst weergegeven.
- Het is niet raadzaam om nieuwe kolommen aan de sjabloon toe te voegen. Eventuele extra kolommen die u toevoegt, worden genegeerd en niet verwerkt.
- U wordt aangeraden altijd de meest recente versie van de CSV-sjabloon te downloaden.

## <a name="to-bulk-delete-users"></a>Gebruikers bulksgewijs verwijderen

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een account dat een gebruikers beheerder in de organisatie is.
1. Selecteer in azure AD de optie **gebruikers**  >  **bulksgewijs verwijderen**.
1. Selecteer op de pagina **bulksgewijs verwijderen gebruiker** **downloaden** om een geldig CSV-bestand met gebruikers eigenschappen te ontvangen.

   ![Selecteer een lokaal CSV-bestand waarin de gebruikers die u wilt verwijderen, worden vermeld](./media/users-bulk-delete/bulk-delete.png)

1. Open het CSV-bestand en voeg een regel toe voor elke gebruiker die u wilt verwijderen. De enige vereiste waarde is **User Principal name**. Sla het bestand op.

   ![Het CSV-bestand bevat de namen en Id's van de gebruikers die u wilt verwijderen](./media/users-bulk-delete/delete-csv-file.png)

1. Blader op de pagina **bulksgewijs verwijderen** , onder **uw CSV-bestand uploaden**, naar het bestand. Wanneer u het bestand selecteert en op verzenden klikt, wordt de validatie van het CSV-bestand gestart.
1. Wanneer de bestandsinhoud is gevalideerd, ziet u **Het bestand is geüpload**. Als er fouten zijn, moet u deze corrigeren voordat u de taak kunt verzenden.
1. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om de Azure bulk bewerking te starten waarmee de gebruikers worden verwijderd.
1. Wanneer de verwijderings bewerking is voltooid, ziet u een melding dat de bulk bewerking is geslaagd.

Als er fouten zijn, kunt u het bestand met resultaten downloaden en weer geven op de pagina **resultaten van bulk bewerking** . Het bestand bevat de reden voor elke fout.

## <a name="check-status"></a>Status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking** .

   [![Controleer de verwijderings status op de pagina resultaten van bulk bewerking.](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

Vervolgens kunt u controleren of de gebruikers die u hebt verwijderd, bestaan in de Azure AD-organisatie in de Azure Portal of met behulp van Power shell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verwijderde gebruikers verifiëren in de Azure Portal

1. Meld u bij Azure Portal aan met een account met beheerdersrechten in de organisatie.
1. Selecteer in het navigatiedeelvenster de service **Azure Active Directory**.
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Onder **weer geven** selecteert u alleen **alle gebruikers** en controleert u of de gebruikers die u hebt verwijderd, niet meer worden weer gegeven.

### <a name="verify-deleted-users-with-powershell"></a>Verwijderde gebruikers controleren met Power shell

Voer de volgende opdracht uit:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Controleer of de gebruikers die u hebt verwijderd, niet meer worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers bulksgewijs toevoegen](users-bulk-add.md)
- [Lijst met gebruikers downloaden](users-bulk-download.md)
- [Gebruikers bulksgewijs herstellen](users-bulk-restore.md)
