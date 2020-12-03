---
title: Een Azure AD-organisatie (Tenant) verwijderen-Azure Active Directory | Microsoft Docs
description: Legt uit hoe u een Azure AD-organisatie (Tenant) voorbereidt voor verwijdering, met inbegrip van selfservice organisaties
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73210420f873729a10381b3cea0005d895c5163b
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547454"
---
# <a name="delete-a-tenant-in-azure-active-directory"></a>Een Tenant verwijderen in Azure Active Directory

Wanneer een Azure AD-organisatie (Tenant) wordt verwijderd, worden ook alle resources verwijderd die deel uitmaken van de organisatie. Bereid uw organisatie voor door de bijbehorende resources te minimaliseren voordat u verwijdert. Alleen een globale beheerder van een Azure Active Directory (Azure AD) kan een Azure AD-organisatie uit de Portal verwijderen.

## <a name="prepare-the-organization"></a>De organisatie voorbereiden

U kunt een organisatie in azure AD pas verwijderen als er verschillende controles worden door gegeven. Deze controles verminderen het risico dat het verwijderen van een Azure AD-organisatie een negatieve invloed heeft op de gebruikers toegang, zoals de mogelijkheid om zich aan te melden bij Microsoft 365 of om toegang te krijgen tot resources in Azure. Als de organisatie die is gekoppeld aan een abonnement, bijvoorbeeld per ongeluk is verwijderd, hebben gebruikers geen toegang tot de Azure-resources voor dat abonnement. Er wordt gecontroleerd of aan de volgende voorwaarden is voldaan:

* Er kunnen zich geen gebruikers in de Azure AD-organisatie (Tenant) bevinden, met uitzonde ring van één globale beheerder die de organisatie moet verwijderen. Andere gebruikers moeten worden verwijderd voordat de organisatie kan worden verwijderd. Als gebruikers van on-premises worden gesynchroniseerd, moet de synchronisatie eerst worden uitgeschakeld en moeten de gebruikers worden verwijderd uit de Cloud organisatie met behulp van de Azure Portal-of Azure PowerShell-cmdlets.
* Er kunnen geen toepassingen in de organisatie zijn. Alle toepassingen moeten worden verwijderd voordat de organisatie kan worden verwijderd.
* Er kunnen geen multi-factor Authentication-providers zijn gekoppeld aan de organisatie.
* Er kunnen geen abonnementen zijn voor micro soft Online Services, zoals Microsoft Azure, Microsoft 365 of Azure AD Premium die zijn gekoppeld aan de organisatie. Als er bijvoorbeeld een standaard Azure AD-organisatie is gemaakt in azure, kunt u deze organisatie niet verwijderen als uw Azure-abonnement voor verificatie nog steeds afhankelijk is van deze organisatie. Op dezelfde manier kunt u een organisatie niet verwijderen als een andere gebruiker er een abonnement aan heeft gekoppeld.

## <a name="delete-the-organization"></a>De organisatie verwijderen

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met een account dat de globale beheerder is voor uw organisatie.

2. Selecteer **Azure Active Directory**.

3. Schakel over naar de organisatie die u wilt verwijderen.
  
   ![Organisatie bevestigen vóór verwijderen](./media/directory-delete-howto/delete-directory-command.png)

4. Selecteer **Tenant verwijderen**.
  
   ![Selecteer de opdracht voor het verwijderen van de organisatie](./media/directory-delete-howto/delete-directory-list.png)

5. Als uw organisatie niet aan een of meer controles voldoet, krijgt u een koppeling naar meer informatie over hoe u kunt door geven. Nadat u alle controles hebt door lopen, selecteert u **verwijderen** om het proces te volt ooien.

## <a name="if-you-cant-delete-the-organization"></a>Als u de organisatie niet kunt verwijderen

Wanneer u uw Azure AD-organisatie hebt geconfigureerd, hebt u mogelijk ook op licenties gebaseerde abonnementen geactiveerd voor uw organisatie, zoals Azure AD Premium P2, Microsoft 365 Business Standard of Enterprise Mobility + Security E5. Om onbedoeld gegevens verlies te voor komen, kunt u een organisatie pas verwijderen als de abonnementen volledig zijn verwijderd. De abonnementen moeten een niet- **ingerichte** status hebben om het verwijderen van de organisatie toe te staan. Een **verlopen** of **geannuleerde** abonnement wordt verplaatst naar de **Uitgeschakelde** status en de laatste fase is de status provisioned. **Deprovisioned**

Zie de volgende tabel voor wat u kunt verwachten wanneer een proef Microsoft 365 abonnement verloopt (exclusief betaalde partner/CSP, Enterprise Agreement of volume licenties). Zie [Wat gebeurt er met mijn gegevens en toegang wanneer mijn Microsoft 365 for Business-abonnement wordt beëindigd?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)voor meer informatie over Microsoft 365 het bewaren van gegevens en de levens cyclus van een abonnement. 

Abonnements status | Gegevens | Toegang tot gegevens
----- | ----- | -----
Actief (30 dagen voor proef versie) | Gegevens die toegankelijk zijn voor alle | Gebruikers hebben normale toegang tot Microsoft 365-bestanden of apps<br>Beheerders hebben normale toegang tot Microsoft 365 beheer centrum en bronnen 
Verlopen (30 dagen) | Gegevens die toegankelijk zijn voor alle| Gebruikers hebben normale toegang tot Microsoft 365-bestanden of apps<br>Beheerders hebben normale toegang tot Microsoft 365 beheer centrum en bronnen
Uitgeschakeld (30 dagen) | Gegevens die alleen toegankelijk zijn voor beheerders | Gebruikers hebben geen toegang tot Microsoft 365-bestanden of apps<br>Beheerders hebben toegang tot het Microsoft 365-beheer centrum, maar kunnen geen licenties toewijzen of gebruikers bijwerken
Inrichting ongedaan gemaakt (30 dagen na uitgeschakeld) | Gegevens verwijderd (automatisch verwijderd als er geen andere services worden gebruikt) | Gebruikers hebben geen toegang tot Microsoft 365-bestanden of apps<br>Beheerders hebben toegang tot het Microsoft 365-beheer centrum om andere abonnementen te kopen en te beheren

## <a name="delete-a-subscription"></a>Een abonnement verwijderen

Met het Microsoft 365-beheer centrum kunt u een abonnement op de **onvoorziene** status laten verwijderen in drie dagen.

1. Meld u aan bij het [Microsoft 365-beheer centrum](https://admin.microsoft.com) met een account dat een globale beheerder is in uw organisatie. Als u de organisatie Contoso wilt verwijderen die de oorspronkelijke standaard domein contoso.onmicrosoft.com heeft, meldt u zich aan met een UPN, zoals admin@contoso.onmicrosoft.com .

2. Bekijk een voor beeld van het nieuwe Microsoft 365-beheer centrum door ervoor te zorgen dat de nieuwe schakel optie voor het **beheer centrum** is ingeschakeld.

   ![Bekijk een voor beeld van de nieuwe M365-beheer centrum-ervaring](./media/directory-delete-howto/preview-toggle.png)

3. Zodra het nieuwe beheer centrum is ingeschakeld, moet u een abonnement annuleren voordat u het kunt verwijderen. Selecteer **facturering** en selecteer **producten & Services** en selecteer vervolgens **abonnement annuleren** voor het abonnement dat u wilt annuleren. U wordt naar een feedback pagina geleid.

   ![Kies een abonnement dat u wilt annuleren](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Vul het feedback formulier in en selecteer **abonnement annuleren** om het abonnement te annuleren.

   ![Opdracht annuleren in de preview van het abonnement](./media/directory-delete-howto/cancel-command.png)

5. U kunt nu het abonnement verwijderen. Selecteer **verwijderen** voor het abonnement dat u wilt verwijderen. Als u het abonnement niet kunt vinden op de pagina **Products & Services** , controleert u of u de **abonnements status** hebt ingesteld op **alle**.

   ![Koppeling verwijderen voor het verwijderen van het abonnement](./media/directory-delete-howto/delete-command.png)

6. Selecteer **abonnement verwijderen** om het abonnement te verwijderen en de voor waarden te accepteren. Alle gegevens worden binnen drie dagen definitief verwijderd. Als u van gedachten verandert, kunt u [het abonnement opnieuw activeren](/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) tijdens de periode van drie dagen.
  
   ![voor waarden zorgvuldig lezen](./media/directory-delete-howto/delete-terms.png)

7. Nu is de status van het abonnement gewijzigd en is het abonnement gemarkeerd voor verwijdering. Het abonnement treedt de **status van** 72 uur later op.

8. Zodra u een abonnement in uw organisatie hebt verwijderd en 72 uur is verstreken, kunt u zich opnieuw aanmelden bij het Azure AD-beheer centrum en moeten er geen vereiste acties worden uitgevoerd en mogen er geen abonnementen worden genomen om de verwijdering van uw organisatie te blok keren. U moet uw Azure AD-organisatie kunnen verwijderen.
  
   ![controle van het abonnement door geven op het scherm van verwijderen](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Ik heb een proef abonnement dat verwijderen blokkeert

Er zijn [self-service-aanmeldingen](/office365/admin/misc/self-service-sign-up?view=o365-worldwide) , zoals micro soft Power BI, Rights Management Services, micro soft power apps of Dynamics 365, afzonderlijke gebruikers kunnen zich registreren via Microsoft 365, waarmee ook een gast gebruiker voor verificatie in uw Azure AD-organisatie wordt gemaakt. Met deze self-service producten worden verwijderde mappen geblokkeerd totdat de producten volledig zijn verwijderd uit de organisatie, om gegevens verlies te voor komen. Ze kunnen alleen worden verwijderd door de Azure AD-beheerder, ongeacht of de gebruiker zich afzonderlijk heeft aangemeld of het product heeft toegewezen.

Er zijn twee soorten self-service registratie producten die worden toegewezen: 

* Toewijzing op organisatie niveau: een Azure AD-beheerder wijst het product toe aan de hele organisatie en een gebruiker kan actief gebruikmaken van de service met deze toewijzing op organisatie niveau, zelfs als ze niet afzonderlijk een licentie hebben.
* Toewijzing op gebruikers niveau: een afzonderlijke gebruiker tijdens een self-service-aanmelding wijst het product zonder beheerder toe aan zichzelf. Zodra de organisatie wordt beheerd door een beheerder (Zie [beheerders overname van een onbeheerde organisatie](domains-admin-takeover.md), kan de beheerder het product rechtstreeks aan gebruikers toewijzen zonder selfservice registratie.  

Wanneer u het verwijderen van het self-service-aanmeldings product start, worden de gegevens door de actie permanent verwijderd en wordt alle gebruikers toegang tot de service verwijderd. Gebruikers aan wie de aanbieding afzonderlijk of op het niveau van de organisatie is toegewezen, kunnen zich niet aanmelden of toegang tot bestaande gegevens krijgen. Als u gegevens verlies wilt voor komen met het self-service registratie product zoals [micro soft power bi Dash boards](/power-bi/service-export-to-pbix) of [Rights Management Services-beleids configuratie](/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), moet u ervoor zorgen dat er een back-up van de gegevens wordt gemaakt en op een andere locatie worden opgeslagen.

Zie [beschik bare selfservice Program ma's](/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)voor meer informatie over momenteel beschik bare self-service voor het aanmelden van services.

Zie de volgende tabel voor wat u kunt verwachten wanneer een proef Microsoft 365 abonnement verloopt (exclusief betaalde partner/CSP, Enterprise Agreement of volume licenties). Zie [Wat gebeurt er met mijn gegevens en toegang wanneer mijn Microsoft 365 for Business-abonnement wordt beëindigd?](/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)voor meer informatie over Microsoft 365 het bewaren van gegevens en de levens cyclus van een abonnement.

Product status | Gegevens | Toegang tot gegevens
------------- | ---- | --------------
Actief (30 dagen voor proef versie) | Gegevens die toegankelijk zijn voor alle | Gebruikers hebben normale toegang tot een self-service voor het registreren van een product, bestanden of apps<br>Beheerders hebben normale toegang tot Microsoft 365 beheer centrum en bronnen
Verwijderd | Gegevens verwijderd | Gebruikers kunnen geen toegang krijgen tot een self-service voor het aanmelden, bestanden of apps<br>Beheerders hebben toegang tot het Microsoft 365-beheer centrum om andere abonnementen te kopen en te beheren

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Hoe kan ik een self-service-aanmeld product verwijderen in de Azure Portal?

U kunt een self-service registratie product, zoals micro soft Power BI of Azure Rights Management Services, in een **verwijderings** status plaatsen zodat ze onmiddellijk worden verwijderd in de Azure AD-Portal.

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met een account dat een globale beheerder in de organisatie is. Als u de organisatie Contoso wilt verwijderen die de oorspronkelijke standaard domein contoso.onmicrosoft.com heeft, meldt u zich aan met een UPN, zoals admin@contoso.onmicrosoft.com .

2. Selecteer **licenties** en selecteer vervolgens **self-service registratie producten**. U kunt alle Self-service-aanmeldings producten afzonderlijk van de op seat gebaseerde abonnementen bekijken. Kies het product dat u definitief wilt verwijderen. Hier volgt een voor beeld van micro soft Power BI:

    ![Scherm opname van de pagina licenties-self-service-registratie producten.](./media/directory-delete-howto/licenses-page.png)

3. Selecteer **verwijderen** om het product te verwijderen en de voor waarden te accepteren waarmee gegevens onmiddellijk en onherroepelijk worden verwijderd. Met deze Verwijder actie worden alle gebruikers verwijderd en wordt de organisatie toegang tot het product verwijderd. Klik op Ja om door te gaan met de verwijdering.  

    ![Scherm opname van de pagina licenties-self-service-aanmeldingen, met het venster ' self-service voor aanmelden verwijderen ' geopend.](./media/directory-delete-howto/delete-product.png)

4. Wanneer u **Ja** selecteert, wordt de verwijdering van het self-service product gestart. Er is een melding waarin wordt aangegeven dat de verwijdering wordt uitgevoerd.  

    ![Scherm opname van de pagina licenties-self-service-aanmeldingen, met de melding ' bezig met verwijderen ' wordt weer gegeven.](./media/directory-delete-howto/progress-message.png)

5. Nu is de status van de self-service voor aanmelden gewijzigd in **verwijderd**. Wanneer u de pagina vernieuwt, moet het product worden verwijderd van de pagina met **self-service registratie producten** .  

    ![Scherm opname van de pagina licenties-self-service-aanmeldingen, met het deel venster ' self-service-product verwijderd ' aan de rechter kant.](./media/directory-delete-howto/product-deleted.png)

6. Zodra u alle producten hebt verwijderd, kunt u zich opnieuw aanmelden bij het Azure AD-beheer centrum en moeten er geen vereiste acties worden uitgevoerd en hoeven er geen producten te worden verwijderd. U moet uw Azure AD-organisatie kunnen verwijderen.

    ![de gebruikersnaam is onjuist getypt of niet gevonden](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Volgende stappen

[Documentatie voor Azure Active Directory](../index.yml)