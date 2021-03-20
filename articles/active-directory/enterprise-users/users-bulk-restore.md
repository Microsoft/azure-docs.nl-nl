---
title: Verwijderde gebruikers in de Azure Active Directory Portal bulksgewijs herstellen | Microsoft Docs
description: Verwijderde gebruikers bulksgewijs herstellen in het Azure AD-beheer centrum in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: be3947e3de18f8ccaf47382c4035e187521ac710
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96571501"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Verwijderde gebruikers bulksgewijs herstellen in Azure Active Directory

Azure Active Directory (Azure AD) ondersteunt het herstellen van bewerkingen voor bulk gebruikers en ondersteunt het downloaden van lijsten van gebruikers, groepen en groeps leden.

## <a name="understand-the-csv-template"></a>Inzicht in de CSV-sjabloon

Down load en vul de CSV-sjabloon in om Azure AD-gebruikers bulksgewijs te herstellen. De CSV-sjabloon die u downloadt, kan eruitzien als in dit voorbeeld:

![Werkblad voor uploaden en aanroepen, waarin het doel en de waarden voor elke rij en kolom worden uitgelegd](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>CSV-sjabloonstructuur

De rijen in een gedownloade CSV-sjabloon zijn als volgt:

- **Versienummer**: De eerste rij met het versienummer moet worden opgenomen in het CSV-uploadbestand.
- **Kolomkoppen**: De indeling van de kolomkoppen is &lt;*itemnaam*&gt; [eigenschapsnaam] &lt;*Required of leeg*&gt;. Bijvoorbeeld `Object ID [objectId] Required`. Sommige oudere versies van de sjabloon kunnen iets afwijken.
- **Rij met voorbeelden**: We hebben in de sjabloon een rij met voorbeelden van acceptabele waarden voor elke kolom opgenomen. U moet de rij met voorbeelden verwijderen en vervangen door uw eigen invoerwaarden.

### <a name="additional-guidance"></a>Aanvullende richtlijnen

- De eerste twee rijen van de uploadsjabloon mogen niet worden verwijderd of gewijzigd, anders kan de upload niet worden verwerkt.
- De vereiste kolommen worden het eerst weergegeven.
- Het is niet raadzaam om nieuwe kolommen aan de sjabloon toe te voegen. Eventuele extra kolommen die u toevoegt, worden genegeerd en niet verwerkt.
- U wordt aangeraden altijd de meest recente versie van de CSV-sjabloon te downloaden.

## <a name="to-bulk-restore-users"></a>Gebruikers bulksgewijs herstellen

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een account dat een gebruikers beheerder is in de Azure AD-organisatie.
1. Selecteer in azure AD de optie **gebruikers**  >  **verwijderd**.
1. Op de pagina **Verwijderde gebruikers** selecteert u **bulk herstel** om een geldig CSV-bestand met eigenschappen te uploaden van de gebruikers die u wilt herstellen.

    ![Selecteer de opdracht bulk herstel op de pagina verwijderde gebruikers](./media/users-bulk-restore/bulk-restore.png)

1. Open de CSV-sjabloon en voeg een regel toe voor elke gebruiker die u wilt herstellen. De enige vereiste waarde is **ObjectID**. Sla het bestand op.

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="Selecteer een lokaal CSV-bestand waarin de gebruikers worden vermeld die u wilt toevoegen":::

1. Blader op de pagina **bulk herstel** onder **uw CSV-bestand uploaden** naar het bestand. Wanneer u het bestand selecteert en op **verzenden** klikt, wordt de validatie van het CSV-bestand gestart.
1. Wanneer de bestandsinhoud is gevalideerd, ziet u **Het bestand is geüpload**. Als er fouten zijn, moet u deze corrigeren voordat u de taak kunt verzenden.
1. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om de bulk bewerking van Azure te starten waarmee de gebruikers worden hersteld.
1. Wanneer de herstel bewerking is voltooid, ziet u een melding dat de bulk bewerking is geslaagd.

Als er fouten zijn, kunt u het bestand met resultaten downloaden en weer geven op de pagina **resultaten van bulk bewerking** . Het bestand bevat de reden voor elke fout.

## <a name="check-status"></a>Status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking** .

[![Controleer de status op de pagina resultaten van bulk bewerking.](./media/users-bulk-restore/bulk-center.png)](./media/users-bulk-restore/bulk-center.png#lightbox)

Vervolgens kunt u controleren of de gebruikers die u hebt hersteld, bestaan in de Azure AD-organisatie in de Azure Portal of met behulp van Power shell.

## <a name="view-restored-users-in-the-azure-portal"></a>Herstelde gebruikers weer geven in de Azure Portal

1. [Meld u aan bij het Azure AD-beheer centrum](https://aad.portal.azure.com) met een account dat een gebruikers beheerder in de organisatie is.
1. Selecteer in het navigatiedeelvenster de service **Azure Active Directory**.
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Selecteer onder **weer geven** **alle gebruikers** en controleer of de gebruikers die u hebt hersteld, worden weer gegeven.

### <a name="view-users-with-powershell"></a>Gebruikers weer geven met Power shell

Voer de volgende opdracht uit:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

U ziet dat de gebruikers die u hebt hersteld, worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers bulksgewijs importeren](users-bulk-add.md)
- [Gebruikers bulksgewijs verwijderen](users-bulk-delete.md)
- [Lijst met gebruikers downloaden](users-bulk-download.md)
