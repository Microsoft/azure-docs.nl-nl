---
title: Cookie-instellingen voor toepassings proxy-Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) heeft toegang en sessie cookies voor toegang tot on-premises toepassingen via toepassings proxy. In dit artikel vindt u informatie over het gebruik en de configuratie van de cookie-instellingen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 01/16/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62afe97b44f45bc0b7aa12b33b6a65dd94ecf095
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99252199"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Cookie-instellingen voor toegang tot on-premises toepassingen in Azure Active Directory

Azure Active Directory (Azure AD) heeft toegang en sessie cookies voor toegang tot on-premises toepassingen via toepassings proxy. Meer informatie over het gebruik van de cookie-instellingen van de toepassings proxy. 

## <a name="what-are-the-cookie-settings"></a>Wat zijn de cookie-instellingen?

[Toepassings proxy](application-proxy.md) gebruikt de volgende instellingen voor toegang en sessie cookies.

| Cookie-instelling | Standaard | Beschrijving | Aanbevelingen |
| -------------- | ------- | ----------- | --------------- |
| Alleen-HTTP-cookies gebruiken | **Nee** | Met **Ja** kan toepassings proxy de vlag HTTPOnly in http-antwoord headers bevatten. Deze vlag biedt bijvoorbeeld extra beveiligings voordelen, waardoor het voor komt dat de cookies worden gekopieerd of gewijzigd door client scripts.<br></br><br></br>Voordat we de HTTP-Only-instelling hebben ondersteund, versleutelde en verzonden toepassings proxy-cookies via een beveiligd TLS-kanaal om tegen wijzigingen te beveiligen. | Gebruik **Ja** vanwege de extra beveiligings voordelen.<br></br><br></br>Gebruik **Nee** voor clients of gebruikers agenten waarvoor toegang tot de sessie cookie is vereist. Gebruik bijvoorbeeld **Nee** voor een RDP-of MTSC-client die via toepassings proxy verbinding maakt met een extern bureaublad-gateway server.|
| Beveiligde cookies gebruiken | **Nee** | Met **Ja** kan toepassings proxy de beveiligde vlag in http-antwoord headers bevatten. Beveiligde cookies verbeteren de beveiliging door het verzenden van cookies via een TLS-kanaal, zoals HTTPS. Zo wordt voor komen dat cookies door onbevoegden worden waargenomen als gevolg van het verzenden van de cookie als Lees bare tekst. | Gebruik **Ja** vanwege de extra beveiligings voordelen.|
| Permanente cookies gebruiken | **Nee** | Met **Ja** kan de toepassings proxy de toegangs cookies instellen op niet verlopen wanneer de webbrowser is gesloten. De persistentie loopt door totdat het toegangs token is verlopen of totdat de gebruiker de permanente cookies hand matig verwijdert. | Gebruik **Nee** als gevolg van het beveiligings risico dat is gekoppeld aan het houden van geverifieerde gebruikers.<br></br><br></br>We raden u aan alleen **Ja** te gebruiken voor oudere toepassingen die geen cookies tussen processen kunnen delen. Het is beter om uw toepassing bij te werken voor het afhandelen van het delen van cookies tussen processen in plaats van permanente cookies te gebruiken. Zo hebt u mogelijk permanente cookies nodig om een gebruiker toe te staan Office-documenten in de Verkenner-weer gave te openen vanaf een share point-site. Zonder permanente cookies kan deze bewerking mislukken als de toegangs cookies niet worden gedeeld tussen de browser, het Explorer-proces en het Office-proces. |

## <a name="samesite-cookies"></a>SameSite cookies
Vanaf versie Chrome 80 en uiteindelijk in browsers die gebruikmaken van chroom, worden cookies waarbij het kenmerk [SameSite](https://web.dev/samesite-cookies-explained) niet wordt opgegeven, behandeld alsof ze zijn ingesteld op **SameSite = slordig**. Het kenmerk SameSite declareert hoe cookies moeten worden beperkt tot een context van dezelfde site. Wanneer deze waarde is ingesteld op slordig, wordt de cookie alleen verzonden naar dezelfde site aanvragen of navigatie op het hoogste niveau. Toepassings proxy vereist echter dat deze cookies worden bewaard in de context van derden, zodat gebruikers tijdens hun sessie op de juiste wijze kunnen worden aangemeld. Als gevolg hiervan worden de toegangs-en sessie cookies van de toepassings proxy bijgewerkt om nadelige gevolgen van deze wijziging te voor komen. De updates zijn onder andere:

* Het kenmerk **SameSite** wordt ingesteld op **none**. Hierdoor kunnen toepassings proxy-en sessie cookies op de juiste manier worden verzonden in de context van derden.
* Instelling van **beveiligde cookie gebruiken** als standaard instelling **gebruiken.** Chrome vereist ook dat de cookies de beveiligde vlag opgeven of worden geweigerd. Deze wijziging is van toepassing op alle bestaande toepassingen die zijn gepubliceerd via toepassings proxy. Houd er rekening mee dat toepassings proxy-toegangs cookies altijd zijn ingesteld op beveiligd en alleen via HTTPS worden verzonden. Deze wijziging is alleen van toepassing op de sessie cookies.

Deze wijzigingen in toepassings proxy cookies worden in de loop van de volgende weken vóór de release datum van het Chrome 80 geïmplementeerd.

Als uw back-endtoepassing cookies heeft die beschikbaar moeten zijn in een context van derden, moet u er ook voor kiezen om uw toepassing expliciet in te stellen op het gebruik van SameSite = none voor deze cookies. Toepassings proxy vertaalt de Set-Cookie-header naar de bijbehorende URL'S en respecteert de instellingen voor deze cookies die door de back-endtoepassing zijn ingesteld.



## <a name="set-the-cookie-settings---azure-portal"></a>De cookie-instellingen instellen-Azure Portal
De cookie-instellingen instellen met behulp van de Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Ga naar **Azure Active Directory**   >  **Enter prise-toepassingen**   >  **alle toepassingen**.
3. Selecteer de toepassing waarvoor u een cookie-instelling wilt inschakelen.
4. Klik op **toepassings proxy**.
5. Stel onder **extra instellingen** de instelling cookie in op **Ja** of **Nee**.
6. Klik op **Opslaan** om uw wijzigingen toe te passen. 

## <a name="view-current-cookie-settings---powershell"></a>Huidige cookie-instellingen weer geven-Power shell

Als u de huidige cookie-instellingen voor de toepassing wilt bekijken, gebruikt u deze Power shell-opdracht:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Cookie-instellingen instellen-Power shell

In de volgende Power shell-opdrachten ```<ObjectId>``` is de ObjectId van de toepassing. 

**Cookie met alleen http** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Beveiligde cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Permanente cookies**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
