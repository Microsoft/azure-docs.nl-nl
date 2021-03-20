---
title: Gebruikers beheren in azure FarmBeats
description: In dit artikel wordt beschreven hoe u gebruikers beheert in azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-ummehabiba
ms.openlocfilehash: de9a11cac9856417422bd72579cbca44233ab422
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179898"
---
# <a name="manage-users"></a>Gebruikers beheren

Azure FarmBeats bevat gebruikers beheer voor personen die deel uitmaken van uw Azure Active Directory-exemplaar (Azure AD). U kunt gebruikers toevoegen aan uw Azure FarmBeats-exemplaar om toegang te krijgen tot de Api's, de gegenereerde kaarten bekijken en telemetrie van de sensor openen vanuit de farm.

## <a name="prerequisites"></a>Vereisten

- Installatie van Azure FarmBeats is vereist. Zie [Azure FarmBeats installeren](install-azure-farmbeats.md)voor meer informatie.
- De e-mail-Id's van de gebruikers die u wilt toevoegen aan of verwijderen uit uw Azure FarmBeats-exemplaar.

## <a name="manage-azure-farmbeats-users"></a>Azure FarmBeats-gebruikers beheren

Azure FarmBeats maakt gebruik van Azure AD voor verificatie, Toegangs beheer en rollen. U kunt gebruikers toevoegen aan de Azure AD-Tenant als gebruikers in azure FarmBeats.

> [!NOTE]
> Als een gebruiker geen Azure AD-Tenant gebruiker is, volgt u de instructies in het gedeelte **Azure AD-gebruikers toevoegen** om de installatie te volt ooien.

Azure FarmBeats ondersteunt twee typen gebruikers rollen:

 - **Beheerder**: volledige toegang tot Azure FarmBeats Datahub-api's. Gebruikers met deze rol kunnen alle Azure FarmBeats Datahub-objecten opvragen en alle bewerkingen van de FarmBeats-Accelerator uitvoeren.
 - **Alleen-lezen**: alleen-lezen toegang tot FarmBeats Datahub-api's. Gebruikers kunnen de Datahub-Api's, de Accelerator dashboards en de Maps weer geven. Gebruikers met alleen-lezen toegang kunnen geen bewerkingen uitvoeren zoals het genereren van Maps, het koppelen van apparaten of het maken van farms.

## <a name="add-users-to-azure-farmbeats"></a>Gebruikers toevoegen aan Azure FarmBeats

Gebruikers toevoegen aan Azure FarmBeats:

1. Meld u aan bij Accelerator en selecteer vervolgens het pictogram **instellingen** .
2. Selecteer **Access Control**.

    ![Het deel venster instellingen van Farm](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Voer de e-mail-ID in van de gebruiker aan wie u toegang wilt verlenen.
4. Selecteer de gewenste rol, **beheerder** of **alleen-lezen**.
5. Selecteer **rol toevoegen**.

De toegevoegde gebruiker heeft nu toegang tot Azure FarmBeats (zowel Datahub als Accelerator).

## <a name="delete-users-from-azure-farmbeats"></a>Gebruikers verwijderen uit Azure FarmBeats

Gebruikers verwijderen uit het Azure FarmBeats-systeem:

1. Meld u aan bij Accelerator en selecteer vervolgens het pictogram **instellingen** .
2. Selecteer **Access Control**.
3. Selecteer **Verwijderen**.

   De gebruiker wordt verwijderd uit het systeem. U ontvangt het volgende bevestigings bericht:

   ![Azure FarmBeats-bevestigings bericht](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Azure AD-gebruikers toevoegen

> [!NOTE]
> Azure FarmBeats-gebruikers moeten aanwezig zijn in de Azure AD-Tenant voordat ze aan toepassingen en rollen kunnen worden toegewezen. Als een gebruiker niet aanwezig is in de Azure AD-Tenant, volgt u de instructies in deze sectie. Sla de instructies over als een gebruiker al aanwezig is in de Azure AD-Tenant.

Volg de stappen om gebruikers toe te voegen aan Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer rechtsboven uw account en schakel vervolgens over naar de Azure AD-Tenant die is gekoppeld aan FarmBeats.
3. Selecteer **Azure Active Directory**  >  **gebruikers**.

    Er wordt een lijst met Azure AD-gebruikers weer gegeven.

4. Selecteer **nieuwe gebruiker** om een gebruiker toe te voegen aan de Directory. Als u een externe gebruiker wilt toevoegen, selecteert u **nieuwe gast gebruiker**.

    ![Het deel venster alle gebruikers](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Selecteer de naam van de nieuwe gebruiker en vul vervolgens de vereiste velden voor die gebruiker in.
6. Selecteer **Maken**.

Zie [gebruikers toevoegen of verwijderen in azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md)voor meer informatie over het beheren van Azure AD-gebruikers.

## <a name="next-steps"></a>Volgende stappen

U hebt gebruikers toegevoegd aan uw Azure FarmBeats-exemplaar. Nu leert u hoe u [Farms kunt maken en beheren](manage-farms-in-azure-farmbeats.md#create-farms).