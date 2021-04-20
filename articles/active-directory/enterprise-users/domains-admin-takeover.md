---
title: Beheerdersovername van een niet-beheerdirectory - Azure AD-| Microsoft Docs
description: Een DNS-domeinnaam overnemen in een niet-beherenDe Azure AD-organisatie (schaduw-tenant).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 04/18/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 816f4645626675ae19a462ac8707e995c3b4045e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739364"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Als beheerder in Azure Active Directory een niet-beheerde directory overnemen

In dit artikel worden twee manieren beschreven om een DNS-domeinnaam in een niet-beheerde map in Azure Active Directory (Azure AD) over te nemen. Wanneer een selfservice-gebruiker zich registreert voor een cloudservice die gebruikmaakt van Azure AD, wordt deze toegevoegd aan een niet-beheerde Azure AD-adreslijst op basis van het e-maildomein. Zie Wat is selfservice-aanmelding voor een Azure Active Directory? voor meer informatie over [selfservice](directory-self-service-signup.md) of 'virale' aanmelding voor een Azure Active Directory

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Bepalen hoe u een niet-beheerde map wilt overnemen
Tijdens het proces van overname door een beheerder kunt u eigendom bewijzen, zoals beschreven in [Een aangepaste domeinnaam toevoegen aan Azure AD](../fundamentals/add-custom-domain.md). In de volgende secties wordt de ervaring voor de beheerder gedetailleerder uitgelegd, maar hier volgt een samenvatting:

* Wanneer u een ['interne' beheerdersovername](#internal-admin-takeover) van een niet-beheerde Azure-adreslijst uitvoert, wordt u als globale beheerder van de niet-beheerde adreslijst toegevoegd. Er worden geen gebruikers, domeinen of serviceabonnementen gemigreerd naar een andere adreslijst die u beheert.

* Wanneer u een ['externe' beheerdersovername](#external-admin-takeover) uitvoert van een niet-beheerde Azure-adreslijst, voegt u de naam van het DNS-domein van de niet-beheerde adreslijst toe aan uw beheerde Azure-adreslijst. Wanneer u de domeinnaam toevoegt, wordt een toewijzing van gebruikers aan bronnen gemaakt in uw beheerde Azure-adreslijst, zodat gebruikers zonder onderbreking toegang houden tot services. 

## <a name="internal-admin-takeover"></a>Interne overname door beheerder

Sommige producten die SharePoint en OneDrive bevatten, zoals Microsoft 365, bieden geen ondersteuning voor externe overname. Als dat uw scenario is, of als u een beheerder bent en een niet-beheerde of 'schaduw' Azure AD-organisatie wilt overnemen die is gemaakt door gebruikers die selfservice-aanmelding hebben gebruikt, kunt u dit doen met een interne overname door de beheerder.

1. Maak een gebruikerscontext in de onmanaged organisatie door u aan te melden voor Power BI. Voor het gemak gaan deze stappen uit van dat pad.

2. Open de [Power BI en](https://powerbi.com) selecteer **Gratis starten.** Voer een gebruikersaccount in dat gebruikmaakt van de domeinnaam voor de organisatie; bijvoorbeeld `admin@fourthcoffee.xyz` . Nadat u de verificatiecode hebt invoeren, controleert u uw e-mail op de bevestigingscode.

3. Selecteer In het bevestigings-e-Power BI selecteert **u Ja, dat ben ik.**

4. Meld u aan bij [de Microsoft 365-beheercentrum](https://portal.office.com/admintakeover) met het Power BI gebruikersaccount. U ontvangt een bericht met  de instructie om beheerder te worden van de domeinnaam die al is geverifieerd in de niet-beheerde organisatie. Selecteer **Ja, ik wil de beheerder zijn.**
  
   ![eerste schermopname voor Beheerder worden](./media/domains-admin-takeover/become-admin-first.png)
  
5. Voeg de TXT-record toe om te bewijzen dat u eigenaar bent van de domeinnaam **fourthcoffee.xyz** uw domeinnaamregistrar. In dit voorbeeld is dit GoDaddy.com.
  
   ![Een txt-record toevoegen voor de domeinnaam](./media/domains-admin-takeover/become-admin-txt-record.png)

Wanneer de DNS TXT-records worden geverifieerd bij uw domeinnaamregistrar, kunt u de Azure AD-organisatie beheren.

Wanneer u de voorgaande stappen hebt voltooid, bent u nu de globale beheerder van de vierde koffieorganisatie in Microsoft 365. Als u de domeinnaam wilt integreren met uw andere Azure-services, kunt u deze verwijderen Microsoft 365 toevoegen aan een andere beheerde organisatie in Azure.

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>De domeinnaam toevoegen aan een beheerde organisatie in Azure AD

1. Open de [Microsoft 365-beheercentrum](https://admin.microsoft.com).
2. Selecteer **het** tabblad Gebruikers en maak een nieuw gebruikersaccount met een naam zoals *\@ fourthcoffeexyz.onmicrosoft.com* die geen gebruik maakt van de aangepaste domeinnaam. 
3. Zorg ervoor dat het nieuwe gebruikersaccount globale beheerdersbevoegdheden heeft voor de Azure AD-organisatie.
4. Open **het tabblad** Domeinen in Microsoft 365-beheercentrum, selecteer de domeinnaam en selecteer **Verwijderen.** 
  
   ![Verwijder de domeinnaam uit Microsoft 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Als er gebruikers of groepen in de Microsoft 365 die verwijzen naar de verwijderde domeinnaam, moeten ze worden hernoemd naar het domein .onmicrosoft.com. Als u de domeinnaam geforceerd verwijdert, wordt de naam van alle gebruikers in dit voorbeeld automatisch gewijzigd in *\@ fourthcoffeexyz.onmicrosoft.com*.
  
6. Meld u aan bij [het Azure AD-beheercentrum](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met een account dat de globale beheerder is voor de Azure AD-organisatie.
  
7. Selecteer **Aangepaste domeinnamen en** voeg vervolgens de domeinnaam toe. U moet de DNS TXT-records invoeren om het eigendom van de domeinnaam te controleren. 
  
   ![domein geverifieerd als toegevoegd aan Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Alle gebruikers van Power BI- of Azure Rights Management-service aan wie licenties zijn toegewezen in de Microsoft 365-organisatie, moeten hun dashboards opslaan als de domeinnaam wordt verwijderd. Ze moeten zich aanmelden met een gebruikersnaam zoals *\@ fourthcoffeexyz.onmicrosoft.com* in plaats *van fourthcoffee.xyz. \@*

## <a name="external-admin-takeover"></a>Externe overname door beheerder

Als u al een organisatie met Azure-services of Microsoft 365, kunt u geen aangepaste domeinnaam toevoegen als deze al is geverifieerd in een andere Azure AD-organisatie. Vanuit uw beheerde organisatie in Azure AD kunt u echter een niet-beheerde organisatie overnemen als een externe beheerdersovername. De algemene procedure volgt het artikel [Een aangepast domein toevoegen aan Azure AD.](../fundamentals/add-custom-domain.md)

Wanneer u het eigendom van de domeinnaam verifieert, verwijdert Azure AD de domeinnaam uit de niet-beherende organisatie en verplaatst deze naar uw bestaande organisatie. Voor het overnemen van een externe beheerder van een niet-beheermap is hetzelfde DNS TXT-validatieproces vereist als voor interne overname door de beheerder. Het verschil is dat het volgende ook wordt verplaatst met de domeinnaam:

- Gebruikers
- Abonnementen
- Licentietoewijzingen

### <a name="support-for-external-admin-takeover"></a>Ondersteuning voor externe beheerdersovername
Externe overname door beheerders wordt ondersteund door de volgende onlineservices:

- Azure Rights Management
- Exchange Online

De ondersteunde serviceplannen zijn onder andere:

- Gratis PowerApps
- Gratis PowerFlow
- RMS voor personen
- Microsoft Stream
- Gratis proefversie van Dynamics 365

Externe overname door beheerders wordt niet ondersteund voor een service met serviceplannen die SharePoint, OneDrive of Skype voor Bedrijven bevatten; bijvoorbeeld via een gratis Office-abonnement. 

U kunt eventueel de [ **optie ForceTakeover**](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) gebruiken om de domeinnaam te verwijderen uit de niet-beherende organisatie en deze te verifiëren in de gewenste organisatie. 

#### <a name="more-information-about-rms-for-individuals"></a>Meer informatie over RMS voor personen

Voor [RMS](/azure/information-protection/rms-for-individuals)voor personen worden de automatisch gemaakte Azure Information Protection-organisatiesleutel en standaardbeveiligingssjablonen ook verplaatst met de [](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) domeinnaam wanneer de niet-beherende organisatie zich in dezelfde regio als de organisatie van u [belandt.](/azure/information-protection/plan-implement-tenant-key)

De sleutel en sjablonen worden niet verplaatst wanneer de niet-beherende organisatie zich in een andere regio heeft. Als de onmanagede organisatie zich bijvoorbeeld in Europa en de organisatie die u bezit, zich in de Noord-Amerika.

Hoewel RMS voor personen is ontworpen ter ondersteuning van Azure AD-verificatie om beveiligde inhoud te openen, voorkomt dit niet dat gebruikers ook inhoud beveiligen. Als gebruikers inhoud hebben beschermd met het abonnement RMS voor personen en de sleutel en sjablonen niet zijn verplaatst, is die inhoud na de overname van het domein niet meer toegankelijk.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD PowerShell-cmdlets voor de optie ForceTakeover
U kunt deze cmdlets zien die worden gebruikt in [PowerShell-voorbeeld.](#powershell-example)

cmdlet | Gebruik
------- | -------
`connect-msolservice` | Meld u aan bij uw beheerde organisatie wanneer u hier om wordt gevraagd.
`get-msoldomain` | Geeft de domeinnamen weer die zijn gekoppeld aan de huidige organisatie.
`new-msoldomain –name <domainname>` | Voegt de domeinnaam aan de organisatie toe als Niet geverifieerd (er is nog geen DNS-verificatie uitgevoerd).
`get-msoldomain` | De domeinnaam is nu opgenomen in de lijst met domeinnamen die zijn gekoppeld aan uw beheerde organisatie, maar wordt vermeld als **Niet geverifieerd.**
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Bevat de informatie die moet worden gebruikt in een nieuwe DNS TXT-record voor het domein (MS=xxxxx). Verificatie vindt mogelijk niet onmiddellijk plaats omdat het enige tijd duurt voordat de TXT-record is doorgegeven. Wacht daarom enkele minuten voordat u de optie **-ForceTakeover overweegt.** 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Als uw domeinnaam nog steeds niet is geverifieerd, kunt u doorgaan met de **optie -ForceTakeover.** Het controleert of de TXT-record is gemaakt en start het overnameproces.<li>De **optie -ForceTakeover** moet alleen worden toegevoegd aan de cmdlet wanneer een externe beheerdersovername wordt geforceerd, bijvoorbeeld wanneer de niet-Microsoft 365 services de overname blokkeren.
`get-msoldomain` | In de lijst met domeinen wordt nu de domeinnaam geverifieerd **weergegeven.**

> [!NOTE]
> De niet-mande Azure AD-organisatie wordt tien dagen nadat u de optie voor externe overname force hebt gebruikt, verwijderd.

### <a name="powershell-example"></a>PowerShell-voorbeeld

1. Maak verbinding met Azure AD met behulp van de referenties die zijn gebruikt om te reageren op de selfserviceaanbieding:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Een lijst met domeinen op te halen:
  
   ```powershell
   Get-MsolDomain
   ```
3. Voer de Get-MsolDomainVerificationDns cmdlet uit om een uitdaging te maken:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Bijvoorbeeld:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Kopieer de waarde (de uitdaging) die wordt geretourneerd met deze opdracht. Bijvoorbeeld:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Maak in uw openbare DNS-naamruimte een DNS txt-record die de waarde bevat die u in de vorige stap hebt gekopieerd. De naam voor deze record is de naam van het bovenliggende domein. Als u deze resourcerecord maakt met behulp van de DNS-rol van Windows Server, laat u recordnaam leeg en plakt u de waarde in het tekstvak.
6. Voer de Confirm-MsolDomain cmdlet uit om de uitdaging te controleren:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Bijvoorbeeld:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Een geslaagde uitdaging retourneert u naar de prompt zonder een fout.

## <a name="next-steps"></a>Volgende stappen

* [Een aangepaste domeinnaam toevoegen in Azure AD](../fundamentals/add-custom-domain.md)
* [Azure PowerShell installeren en configureren](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure-cmdlet-naslaginformatie](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0&preserve-view=true)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
