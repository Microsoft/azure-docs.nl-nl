---
title: Beheerders toestemming voor LinkedIn-account verbindingen-Azure AD | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u verbindingen met LinkedIn-Integratie accounts in micro soft-apps in Azure Active Directory kunt in-of uitschakelen
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 158d8731965533b20917a25b72227a92c9638fa7
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95488450"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>LinkedIn-account verbindingen integreren in Azure Active Directory

U kunt gebruikers in uw organisatie in staat stellen om toegang te krijgen tot hun LinkedIn-verbindingen binnen sommige micro soft-apps. Er worden geen gegevens gedeeld wanneer gebruikers toestemming hebben om verbinding te maken met hun accounts. U kunt uw organisatie integreren in het Azure Active Directory- [beheer centrum](https://aad.portal.azure.com)(Azure AD).

> [!IMPORTANT]
> De instelling verbindingen van het LinkedIn-account wordt momenteel geïmplementeerd in azure AD-organisaties. Wanneer de implementatie is uitgevoerd naar uw organisatie, is deze standaard ingeschakeld.
>
> Uitzonderingen
>
> * De instelling is niet beschikbaar voor klanten die gebruikmaken van Microsoft Cloud voor Amerikaanse overheid, Microsoft Cloud Duitsland of Azure en Microsoft 365 die worden beheerd door 21Vianet in China.
> * De instelling is standaard uitgeschakeld voor Azure AD-organisaties die zijn ingericht in Duitsland. Houd er rekening mee dat de instelling niet beschikbaar is voor klanten die Microsoft Cloud Duitsland gebruiken.
> * De instelling is standaard uitgeschakeld voor organisaties die in Frank rijk zijn ingericht.
>
> Zodra de verbinding met het LinkedIn-account voor uw organisatie is ingeschakeld, werken de account verbindingen nadat gebruikers toestemming geven voor apps die namens hen toegang hebben tot Bedrijfs gegevens. Zie de [toegang van een gebruiker tot een toepassing verwijderen](../manage-apps/methods-for-removing-user-access.md)voor meer informatie over de instelling van toestemming voor gebruikers.

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>LinkedIn-account verbindingen inschakelen in de Azure Portal

U kunt LinkedIn-account verbindingen inschakelen voor alleen de gebruikers die u toegang wilt geven, vanuit uw hele organisatie tot alleen geselecteerde gebruikers in uw organisatie.

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com/) met een account dat een globale beheerder is voor de Azure AD-organisatie.
1. Selecteer **Gebruikers**.
1. Op de pagina **gebruikers** selecteert u **gebruikers instellingen**.
1. Onder **LinkedIn-account verbindingen** kunnen gebruikers hun accounts verbinden voor toegang tot hun LinkedIn-verbindingen binnen sommige micro soft-apps. Er worden geen gegevens gedeeld wanneer gebruikers toestemming hebben om verbinding te maken met hun accounts.

    * Selecteer **Ja** om de service in te scha kelen voor alle gebruikers in uw organisatie
    * Selecteer **geselecteerde groep** om de service alleen in te scha kelen voor een groep geselecteerde gebruikers in uw organisatie
    * Selecteer **Nee** om de toestemming van alle gebruikers in uw organisatie in te trekken

    ![LinkedIn-account verbindingen integreren in de organisatie](./media/linkedin-integration/linkedin-integration.png)

1. Wanneer u klaar bent, selecteert u **Opslaan** om uw instellingen op te slaan.

> [!Important]
> LinkedIn-integratie is niet volledig ingeschakeld voor uw gebruikers tot ze toestemming hebben om verbinding te maken met hun accounts. Er worden geen gegevens gedeeld wanneer u account verbindingen voor uw gebruikers inschakelt.

### <a name="assign-selected-users-with-a-group"></a>Geselecteerde gebruikers aan een groep toewijzen

We hebben de optie geselecteerd vervangen, waarmee een lijst met gebruikers wordt opgegeven met de optie om een groep gebruikers te selecteren, zodat u de mogelijkheid hebt om LinkedIn-en micro soft-accounts te verbinden voor één groep in plaats van veel afzonderlijke gebruikers. Als u geen LinkedIn-account verbindingen hebt ingeschakeld voor geselecteerde afzonderlijke gebruikers, hoeft u niets te doen. Als u eerder LinkedIn-account verbindingen hebt ingeschakeld voor geselecteerde afzonderlijke gebruikers, moet u het volgende doen:

1. De huidige lijst met afzonderlijke gebruikers ophalen
1. De momenteel ingeschakelde individuele gebruikers naar een groep verplaatsen
1. Gebruik de groep van de vorige als de geselecteerde groep in de instelling voor de verbinding met het LinkedIn-account in het Azure AD-beheer centrum.

> [!NOTE]
> Zelfs als u de momenteel geselecteerde afzonderlijke gebruikers niet naar een groep verplaatst, kunnen ze nog steeds LinkedIn-informatie zien in micro soft-apps.

### <a name="move-currently-selected-users-to-a-group"></a>Momenteel geselecteerde gebruikers naar een groep verplaatsen

1. Maak een CSV-bestand van de gebruikers die zijn geselecteerd voor de verbindingen van het LinkedIn-account.
1. Meld u aan bij Microsoft 365 met uw beheerders account.
1. Start PowerShell.
1. De Azure AD-module installeren door uit te voeren `Install-Module AzureAD`
1. Voer het volgende script uit:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Als u de groep uit stap 2 wilt gebruiken als de geselecteerde groep in de instelling verbindingen van het LinkedIn-account in het Azure AD-beheer centrum, raadpleegt u [LinkedIn-account verbindingen inschakelen in de Azure Portal](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>groepsbeleid gebruiken om LinkedIn-account verbindingen in te scha kelen

1. De [Office 2016-beheer sjabloon bestanden (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) downloaden
1. Pak de **ADMX** -bestanden uit en kopieer deze naar uw centrale opslag.
1. Open Groepsbeleidsbeheer.
1. Maak een Groepsbeleid-object met de volgende instelling: **gebruikers configuratie**  >  **Beheersjablonen**  >  **Microsoft Office 2016**  >  **diverse**  >  **LinkedIn-functies weer geven in Office-toepassingen**.
1. Selecteer **ingeschakeld** of **uitgeschakeld**.
  
   Staat | Effect
   ------ | ------
   **Ingeschakeld** | De instelling **LinkedIn-functies in Office-toepassingen weer geven** in Office 2016 opties is ingeschakeld. Gebruikers in uw organisatie kunnen LinkedIn-functies gebruiken in hun Office 2016-toepassingen.
   **Uitgeschakeld** | De instelling **LinkedIn-functies in Office-toepassingen weer geven** in Office 2016 opties is uitgeschakeld en eind gebruikers kunnen deze instelling niet wijzigen. Gebruikers in uw organisatie kunnen LinkedIn-functies niet gebruiken in hun Office 2016-toepassingen.

Dit groeps beleid is alleen van invloed op Office 2016-apps voor een lokale computer. Als gebruikers LinkedIn uitschakelen in hun Office 2016-apps, kunnen ze nog steeds LinkedIn-functies zien in Microsoft 365.

## <a name="next-steps"></a>Volgende stappen

* [Gebruikers toestemming en delen van gegevens voor LinkedIn](linkedin-user-consent.md)

* [LinkedIn-informatie en-functies in uw micro soft-apps](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn Help Center](https://www.linkedin.com/help/linkedin)

* [Bekijk uw huidige LinkedIn-Integratie-instelling in de Azure Portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)