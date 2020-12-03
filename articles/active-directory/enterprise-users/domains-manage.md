---
title: Aangepaste domein namen toevoegen en verifiëren-Azure Active Directory | Microsoft Docs
description: Management-concepten en-procedures voor het beheren van een domein naam in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/20/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dd77b1d82fc427f53a251ef66230b450c1060fd
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547573"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Aangepaste domein namen in uw Azure Active Directory beheren

Een domein naam is een belang rijk onderdeel van de id voor veel Azure Active Directory-resources (Azure AD): het is onderdeel van een gebruikers naam of e-mail adres voor een gebruiker, een deel van het adres voor een groep en maakt deel uit van de App-ID-URI voor een toepassing. Een resource in azure AD kan een domein naam bevatten die het eigendom is van de organisatie die de resource bevat. Alleen een globale beheerder kan domeinen in azure AD beheren.

## <a name="set-the-primary-domain-name-for-your-azure-ad-organization"></a>De primaire domein naam voor uw Azure AD-organisatie instellen

Als uw organisatie is gemaakt, is de initiële domein naam, zoals ' contoso.onmicrosoft.com ', ook de primaire domein naam. Het primaire domein is de standaard domein naam voor een nieuwe gebruiker wanneer u een nieuwe gebruiker maakt. Het instellen van een primaire domein naam stroomlijnt het proces voor een beheerder om nieuwe gebruikers te maken in de portal. De primaire domein naam wijzigen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een account dat een globale beheerder voor de organisatie is.
2. Selecteer **Azure Active Directory**.
3. Selecteer **Namen van aangepaste domeinen**.
  
   ![De pagina gebruikers beheer openen](./media/domains-manage/add-custom-domain.png)
4. Selecteer de naam van het domein dat u als primair domein wilt maken.
5. Selecteer de opdracht **Primair maken** . Bevestig uw keuze wanneer u hierom wordt gevraagd.
  
   ![Een domein naam instellen als primair](./media/domains-manage/make-primary-domain.png)

U kunt de primaire domein naam voor uw organisatie wijzigen naar een geverifieerd aangepast domein dat niet federatief is. Het wijzigen van het primaire domein voor uw organisatie heeft geen invloed op de gebruikers naam voor bestaande gebruikers.

## <a name="add-custom-domain-names-to-your-azure-ad-organization"></a>Aangepaste domein namen toevoegen aan uw Azure AD-organisatie

U kunt Maxi maal 900 beheerde domein namen toevoegen. Als u al uw domeinen configureert voor Federatie met on-premises Active Directory, kunt u Maxi maal 450 domein namen toevoegen aan elke organisatie.

## <a name="add-subdomains-of-a-custom-domain"></a>Subdomeinen van een aangepast domein toevoegen

Als u een subdomeinnaam zoals ' europe.contoso.com ' aan uw organisatie wilt toevoegen, moet u eerst het hoofd domein, zoals contoso.com, toevoegen en verifiëren. Het subdomein wordt automatisch gecontroleerd door Azure AD. Vernieuw de lijst met domeinen in de browser om te zien dat het subdomein dat u hebt toegevoegd, is geverifieerd.

Als u al een contoso.com-domein aan één Azure AD-organisatie hebt toegevoegd, kunt u ook de europe.contoso.com van het subdomein in een andere Azure AD-organisatie controleren. Wanneer u het subdomein toevoegt, wordt u gevraagd een TXT-record toe te voegen aan de DNS-hosting provider.



## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Wat u moet doen als u de DNS-registratie functie voor uw aangepaste domein naam wijzigt

Als u de DNS-registratie service wijzigt, zijn er geen aanvullende configuratie taken in azure AD. U kunt de domein naam zonder onderbreking blijven gebruiken met Azure AD. Als u uw aangepaste domein naam gebruikt met Microsoft 365, intune of andere services die afhankelijk zijn van aangepaste domein namen in azure AD, raadpleegt u de documentatie voor deze services.

## <a name="delete-a-custom-domain-name"></a>Een aangepaste domein naam verwijderen

U kunt een aangepaste domein naam uit uw Azure AD verwijderen als uw organisatie niet langer gebruikmaakt van die domein naam of als u deze domein naam moet gebruiken met een andere Azure AD.

Als u een aangepaste domein naam wilt verwijderen, moet u er eerst voor zorgen dat geen resources in uw organisatie afhankelijk zijn van de domein naam. U kunt geen domein naam uit uw organisatie verwijderen als:

* Elke gebruiker heeft een gebruikers naam, e-mail adres of proxy adres dat de domein naam bevat.
* Elke groep heeft een e-mail adres of proxy adres dat de domein naam bevat.
* Elke toepassing in uw Azure AD heeft een app-ID-URI die de domein naam bevat.

U moet deze resource in uw Azure AD-organisatie wijzigen of verwijderen voordat u de aangepaste domein naam kunt verwijderen.

### <a name="forcedelete-option"></a>ForceDelete optie

U kunt een domein naam in het [Azure AD-beheer centrum](https://aad.portal.azure.com) **ForceDelete** of gebruikmaken van [Microsoft Graph-API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true). Deze opties gebruiken een asynchrone bewerking en werken alle verwijzingen van de aangepaste domein naam zoals " user@contoso.com " bij naar de oorspronkelijke standaard domein naam, zoals " user@contoso.onmicrosoft.com ."

Als u **ForceDelete** wilt aanroepen in de Azure Portal, moet u ervoor zorgen dat er minder dan 1000 verwijzingen naar de domein naam zijn en alle verwijzingen waarbij Exchange de inrichtings service is, moeten worden bijgewerkt of verwijderd in het [Exchange-beheer centrum](https://outlook.office365.com/ecp/). Dit omvat Exchange Mail-Enabled-beveiligings groepen en gedistribueerde lijsten. Zie [beveiligings groepen met e-mail beveiliging verwijderen](/Exchange/recipients/mail-enabled-security-groups?view=exchserver-2019#Remove%20mail-enabled%20security%20groups&preserve-view=true)voor meer informatie. De **ForceDelete** -bewerking kan ook niet worden uitgevoerd als aan een van de volgende voor waarden wordt voldaan:

* U hebt een domein aangeschaft via Microsoft 365-abonnements services van het domein
* U bent een partner die u beheert namens een andere klant organisatie

De volgende acties worden uitgevoerd als onderdeel van de **ForceDelete** -bewerking:

* De naam van de UPN, Emailnaam en proxyAddress attribuut van gebruikers met verwijzingen naar de aangepaste domein naam naar de oorspronkelijke standaard domein naam.
* De naam van het EmailAddress van groepen met verwijzingen naar de aangepaste domein naam wordt gewijzigd naar de naam van het oorspronkelijke standaard domein.
* De naam van de identifierUris van toepassingen met verwijzingen naar de aangepaste domein naam wordt gewijzigd in de naam van het oorspronkelijke standaard domein.

Er wordt een fout geretourneerd wanneer:

* Het aantal objecten waarvan de naam moet worden gewijzigd, is groter dan 1000
* Een van de toepassingen waarvan u de naam wilt wijzigen, is een app met meerdere tenants

### <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: waarom mislukt het verwijderen van het domein met een fout die aangeeft dat ik Gemastered groups heb op deze domein naam?** <br>
**A:** Tegenwoordig worden bepaalde groepen, zoals Mail-Enabled-beveiligings groepen en gedistribueerde lijsten, door Exchange ingericht en moeten ze hand matig worden opgeschoond in het [Exchange-beheer centrum (SBV)](https://outlook.office365.com/ecp/). Er is mogelijk een achtergebleven ProxyAddresses die afhankelijk is van de aangepaste domein naam en moet hand matig worden bijgewerkt naar een andere domein naam. 

**V: Ik ben aangemeld als beheerder \@ contoso.com, maar ik kan de domein naam ' contoso.com ' niet verwijderen?**<br>
**A:** U kunt niet verwijzen naar de aangepaste domein naam die u probeert te verwijderen in de naam van uw gebruikers account. Zorg ervoor dat het account van de globale beheerder de oorspronkelijke standaard domein naam (. onmicrosoft.com) gebruikt, zoals admin@contoso.onmicrosoft.com . Meld u aan met een ander account voor globale beheerders, zoals een admin@contoso.onmicrosoft.com andere aangepaste domein naam, zoals ' fabrikam.com ', waarbij het account is admin@fabrikam.com .

**V: Ik heb op de knop domein verwijderen geklikt en Zie `In Progress` status voor de Verwijder bewerking. Hoe lang duurt het? Wat gebeurt er als dit mislukt?**<br>
**A:** De bewerking domein verwijderen is een asynchrone achtergrond taak waarmee alle verwijzingen naar de domein naam worden hernoemd. Deze moet binnen een minuut of twee worden voltooid. Als het verwijderen van het domein mislukt, zorg er dan voor dat u niet beschikt over:

* Apps die zijn geconfigureerd op de domein naam met de appIdentifierURI
* Elke e-mail groep die verwijst naar de aangepaste domein naam
* Meer dan 1000 verwijzingen naar de domein naam

Als u merkt dat aan een van de voor waarden niet is voldaan, moet u de verwijzingen hand matig opschonen en proberen het domein opnieuw te verwijderen.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Power shell of de Microsoft Graph-API gebruiken voor het beheren van domein namen

De meeste beheer taken voor domein namen in Azure Active Directory kunnen ook worden voltooid met behulp van micro soft power shell of via een programma met behulp van de Microsoft Graph-API.

* [Power shell gebruiken voor het beheren van domein namen in azure AD](/powershell/module/azuread/?view=azureadps-2.0#domains&preserve-view=true)
* [Bron type van het domein](/graph/api/resources/domain?view=graph-rest-1.0&preserve-view=true)

## <a name="next-steps"></a>Volgende stappen

* [Aangepaste domeinnamen toevoegen](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
* [Beveiligings groepen met Exchange-e-mail verwijderen in Exchange-beheer centrum op een aangepaste domein naam in azure AD](/Exchange/recipients/mail-enabled-security-groups?view=exchserver-2019#Remove%20mail-enabled%20security%20groups&preserve-view=true)
* [Een aangepaste domein naam ForceDelete met Microsoft Graph-API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)