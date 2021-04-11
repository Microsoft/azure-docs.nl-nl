---
title: 'Azure AD Connect: naadloze enkelvoudige Sign-On-Veelgestelde vragen | Microsoft Docs'
description: Antwoorden op veelgestelde vragen over Azure Active Directory naadloze eenmalige aanmelding.
services: active-directory
keywords: Wat is Azure AD Connect, installeer Active Directory, vereiste onderdelen voor Azure AD, SSO, eenmalige aanmelding
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52b450ecc8aff379dbdb8d58f9b7609cf730ad27
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731663"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory naadloze eenmalige aanmelding: veelgestelde vragen

In dit artikel sturen we veelgestelde vragen over Azure Active Directory naadloze single Sign-On (naadloze SSO). Blijf op de hoogte van nieuwe inhoud.

**V: welke aanmeldings methoden maken naadloze SSO mogelijk met**

Naadloze SSO kan worden gecombineerd met de aanmeldings methoden voor [wachtwoord-hash](how-to-connect-password-hash-synchronization.md) of [Pass-Through-verificatie](how-to-connect-pta.md) . Deze functie kan echter niet worden gebruikt met Active Directory Federation Services (ADFS).

**V: is naadloze SSO een gratis functie?**

Naadloze SSO is een gratis functie en u hebt geen betaalde versies van Azure AD nodig om deze te gebruiken.

**V: is naadloze SSO beschikbaar in de [Microsoft Azure Duitsland Cloud](https://www.microsoft.de/cloud-deutschland) en de [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/)?**

Naadloze SSO is beschikbaar voor de Azure Government Cloud. Bekijk voor meer informatie [hybride identiteits overwegingen voor Azure Government](./reference-connect-government-cloud.md).

**V: welke toepassingen profiteren van de `domain_hint` of `login_hint` parameter mogelijkheden van naadloze SSO?**

Hieronder vindt u een niet-limitatieve lijst met toepassingen die deze para meters naar Azure AD kunnen verzenden, en waarmee gebruikers zich op de achtergrond aanmelden met naadloze SSO (dat wil zeggen dat uw gebruikers geen gebruikers namen of wacht woorden hoeven in te voeren):

| De naam van de toepassing | URL van de toepassing die moet worden gebruikt |
| -- | -- |
| Toegangsvenster | https: \/ /MyApps.Microsoft.com/contoso.com |
| Outlook op Internet | https: \/ /Outlook.office365.com/contoso.com |
| Office 365-portals | https: \/ /Portal.Office.com? domain_hint = contoso. com, https: \/ /www.office.com? domain_hint = contoso. com |

Daarnaast krijgen gebruikers een stille aanmeldings ervaring als een toepassing aanmeldings aanvragen verzendt naar de eind punten van Azure AD die als tenants zijn ingesteld, dat wil zeggen, https: \/ /login.microsoftonline.com/contoso.com/<.. > of https: \/ /login.microsoftonline.com/<tenant_ID>/<. >-in plaats van het gemeen schappelijke eind punt van Azure AD, dat wil zeggen, https: \/ /login.microsoftonline.com/common/<... >. Hieronder vindt u een niet-limitatieve lijst van toepassingen die deze typen aanmeldings aanvragen maken.

| De naam van de toepassing | URL van de toepassing die moet worden gebruikt |
| -- | -- |
| SharePoint Online | https: \/ /contoso.SharePoint.com |
| Azure Portal | https: \/ /Portal.Azure.com/contoso.com |

Vervang in de bovenstaande tabellen ' contoso.com ' door de domein naam om naar de juiste toepassings-Url's voor uw Tenant te gaan.

Als u wilt dat andere toepassingen gebruikmaken van de aanmeldings ervaring op de achtergrond, laat het ons weten in het gedeelte feedback.

**V: biedt naadloze SSO `Alternate ID` -ondersteuning als de gebruikers naam, in plaats van `userPrincipalName` ?**

Ja. Naadloze SSO ondersteunt `Alternate ID` als de gebruikers naam wanneer deze is geconfigureerd in azure AD Connect, zoals [hier](how-to-connect-install-custom.md)wordt weer gegeven. Niet alle Microsoft 365-toepassingen worden ondersteund `Alternate ID` . Raadpleeg de documentatie van de specifieke toepassing voor de ondersteunings verklaring.

**V: wat is het verschil tussen de eenmalige aanmelding van [Azure AD](../devices/overview.md) en naadloze SSO?**

[Azure AD-deelname](../devices/overview.md) levert SSO aan gebruikers als hun apparaten zijn geregistreerd bij Azure AD. Deze apparaten hoeven geen lid te zijn van een domein. SSO wordt met behulp van *primaire vernieuwings tokens* of *PRTs*, en niet met Kerberos. De gebruikers ervaring is het meest optimaal op Windows 10-apparaten. SSO wordt automatisch uitgevoerd in de micro soft Edge-browser. Het werkt ook op Chrome met het gebruik van een browser extensie.

U kunt zowel Azure AD-deelname als naadloze SSO gebruiken voor uw Tenant. Deze twee functies zijn complementair. Als beide functies zijn ingeschakeld, heeft SSO van Azure AD-deelname voor rang op naadloze SSO.

**V: Ik wil niet-Windows 10-apparaten registreren bij Azure AD zonder AD FS te gebruiken. Kan ik in plaats daarvan naadloze SSO gebruiken?**

Ja, voor dit scenario is versie 2,1 of hoger van de [werk plek-client](https://www.microsoft.com/download/details.aspx?id=53554)vereist.

**V: hoe kan ik de Kerberos-ontsleutelings sleutel van het `AZUREADSSO` computer account inrollen?**

Het is belang rijk dat u de Kerberos-ontsleutelings sleutel voor het `AZUREADSSO` computer account (dat staat voor Azure AD) die is gemaakt in uw on-premises AD-forest regel matig wilt door lopen.

>[!IMPORTANT]
>We raden u ten zeerste aan de Kerberos-ontsleutelingssleutel ten minste elke 30 dagen uit te voeren.

Volg deze stappen op de on-premises server waarop u Azure AD Connect:

   > [!NOTE]
   >U hebt de referenties domein beheerder en globale beheerder nodig om de volgende stappen uit te voeren.
   >Als u geen domein beheerder bent en u machtigingen hebt toegewezen door de domein beheerder, moet u `Update-AzureADSSOForest -OnPremCredentials $creds -PreserveCustomPermissionsOnDesktopSsoAccount`

   **Stap 1. Lijst met AD-forests ophalen waar naadloze SSO is ingeschakeld**

   1. Down load en installeer eerst [Azure AD Power shell](/powershell/azure/active-directory/overview).
   2. Navigeer naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importeer de naadloze SSO Power shell-module met de volgende opdracht: `Import-Module .\AzureADSSO.psd1` .
   4. Voer Power shell uit als beheerder. Bel in Power shell `New-AzureADSSOAuthenticationContext` . Met deze opdracht geeft u een pop-up om de globale beheerders referenties van uw Tenant in te voeren.
   5. Aanroep `Get-AzureADSSOStatus | ConvertFrom-Json` . Met deze opdracht geeft u de lijst met AD-forests weer (Bekijk de lijst ' domeinen ') waarop deze functie is ingeschakeld.

   **Stap 2. De Kerberos-ontsleutelings sleutel bijwerken op elk AD-forest waarop deze is ingesteld**

   1. Aanroep `$creds = Get-Credential` . Wanneer u hierom wordt gevraagd, voert u de referenties voor de domein beheerder in voor het beoogde AD-forest.

   > [!NOTE]
   >De gebruikers naam van de domein beheerder referenties moet worden opgegeven in de indeling SAM-account naam (contoso\johndoe of contoso. com\johndoe). We gebruiken het domein gedeelte van de gebruikers naam voor het zoeken van de domein controller van de domein beheerder met behulp van DNS.

   >[!NOTE]
   >Het domein beheerders account dat wordt gebruikt, mag geen lid zijn van de groep met beveiligde gebruikers. Als dit het geval is, mislukt de bewerking.

   2. Aanroep `Update-AzureADSSOForest -OnPremCredentials $creds` . Met deze opdracht wordt de Kerberos-ontsleutelings sleutel voor het `AZUREADSSO` computer account in dit specifieke AD-forest bijgewerkt en bijgewerkt in azure AD.
   
   3. Herhaal de voor gaande stappen voor elk AD-forest waarop u de functie hebt ingesteld.
   
  >[!NOTE]
   >Als u een forest bijwerkt, met uitzonde ring van de Azure AD Connect, moet u ervoor zorgen dat de verbinding met de globale-catalogus server (TCP 3268 en TCP 3269) beschikbaar is.

   >[!IMPORTANT]
   >Zorg ervoor dat u de opdracht _niet_ `Update-AzureADSSOForest` meer dan één keer uitvoert. Anders werkt de functie niet meer wanneer de Kerberos-tickets van uw gebruikers verlopen en opnieuw worden uitgegeven door uw on-premises Active Directory.

**V: hoe kan ik naadloze SSO uitschakelen?**

   **Stap 1. De functie op uw Tenant uitschakelen**

   **Optie A: uitschakelen met Azure AD Connect**
    
   1. Voer Azure AD Connect uit, kies **pagina gebruikers aanmelding wijzigen** en klik op **volgende**.
   2. Schakel de optie **eenmalige aanmelding inschakelen** uit. Ga door met de wizard.

   Na het volt ooien van de wizard wordt naadloze SSO uitgeschakeld voor uw Tenant. Er wordt echter een bericht weer gegeven op het scherm dat er als volgt uitziet:

   Eenmalige aanmelding is nu uitgeschakeld, maar er zijn aanvullende hand matige stappen die moeten worden uitgevoerd om de opschoon bewerking te volt ooien. [Meer informatie](tshoot-connect-sso.md#step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature)'

   Volg de stappen 2 en 3 op de on-premises server waarop u Azure AD Connect hebt om het opschonings proces te volt ooien.

   **Optie B: uitschakelen met behulp van Power shell**

   Voer de volgende stappen uit op de on-premises server waarop u Azure AD Connect uitvoert:

   1. Down load en installeer eerst [Azure AD Power shell](/powershell/azure/active-directory/overview).
   2. Navigeer naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importeer de naadloze SSO Power shell-module met de volgende opdracht: `Import-Module .\AzureADSSO.psd1` .
   4. Voer Power shell uit als beheerder. Bel in Power shell `New-AzureADSSOAuthenticationContext` . Met deze opdracht geeft u een pop-up om de globale beheerders referenties van uw Tenant in te voeren.
   5. Aanroep `Enable-AzureADSSO -Enable $false` .
   
   Op dit punt is naadloze SSO uitgeschakeld, maar de domeinen blijven geconfigureerd voor het geval u naadloze eenmalige aanmelding mogelijk wilt maken. Als u de domeinen volledig wilt verwijderen uit de naadloze SSO-configuratie, roept u de volgende cmdlet aan nadat u stap 5 hebt uitgevoerd: `Disable-AzureADSSOForest -DomainFqdn <fqdn>` .

   >[!IMPORTANT]
   >Door naadloze SSO uit te scha kelen met behulp van Power shell, wordt de status in Azure AD Connect niet gewijzigd. Naadloze SSO wordt weer gegeven als ingeschakeld op de aanmeldings pagina van de **gebruiker wijzigen** .

   **Stap 2. Lijst met AD-forests ophalen waar naadloze SSO is ingeschakeld**

   Volg de onderstaande taken 1 tot en met 4 als u naadloze SSO hebt uitgeschakeld met Azure AD Connect. Als u naadloze SSO hebt uitgeschakeld met behulp van Power shell, gaat u verder met taak 5 hieronder.

   1. Down load en installeer eerst [Azure AD Power shell](/powershell/azure/active-directory/overview).
   2. Navigeer naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importeer de naadloze SSO Power shell-module met de volgende opdracht: `Import-Module .\AzureADSSO.psd1` .
   4. Voer Power shell uit als beheerder. Bel in Power shell `New-AzureADSSOAuthenticationContext` . Met deze opdracht geeft u een pop-up om de globale beheerders referenties van uw Tenant in te voeren.
   5. Aanroep `Get-AzureADSSOStatus | ConvertFrom-Json` . Met deze opdracht geeft u de lijst met AD-forests weer (Bekijk de lijst ' domeinen ') waarop deze functie is ingeschakeld.

   **Stap 3. Verwijder het `AZUREADSSO` computer account hand matig uit elk AD-forest dat wordt weer gegeven.**

## <a name="next-steps"></a>Volgende stappen

- [**Quick**](how-to-connect-sso-quick-start.md) start: krijg Azure AD naadloze SSO.
- [**Technisch diep gaande**](how-to-connect-sso-how-it-works.md) kennis van de werking van deze functie.
- [**Problemen oplossen**](tshoot-connect-sso.md) : informatie over het oplossen van veelvoorkomende problemen met de functie.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -voor het indienen van nieuwe functie aanvragen.
