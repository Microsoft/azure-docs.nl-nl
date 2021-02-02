---
title: Aangepaste start pagina voor gepubliceerde apps-Azure AD-toepassingsproxy
description: Bestrijkt de basis beginselen van Azure AD-toepassingsproxy-connectors
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebef93455414c114fb60df9af8ebc629769a33e6
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254425"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Een aangepaste start pagina instellen voor gepubliceerde apps met behulp van Azure AD-toepassingsproxy

In dit artikel wordt beschreven hoe u een app kunt configureren om een gebruiker naar een aangepaste start pagina te sturen. Wanneer u een app met toepassings proxy publiceert, stelt u een interne URL in, maar soms is dat niet de pagina die een gebruiker eerst moet zien. Stel een aangepaste start pagina zo in dat een gebruiker de juiste pagina krijgt wanneer hij of zij toegang tot de app heeft. Een gebruiker ziet de aangepaste start pagina die u hebt ingesteld, ongeacht of ze toegang hebben tot de app vanuit de Azure Active Directory mijn apps of het start programma voor apps van Microsoft 365.

Wanneer een gebruiker de app start, wordt deze standaard omgeleid naar de hoofd domein-URL voor de gepubliceerde app. De landings pagina wordt doorgaans ingesteld als de URL van de start pagina. Gebruik de Azure AD Power shell-module om een aangepaste URL voor de start pagina te definiëren wanneer u wilt dat een app-gebruiker op een specifieke pagina in de app terechtkomt.

Hier volgt een scenario waarin wordt uitgelegd waarom uw bedrijf een aangepaste start pagina zou instellen:

- In het bedrijfs netwerk gaat een gebruiker `https://ExpenseApp/login/login.aspx` zich aanmelden en toegang krijgen tot uw app.
- Omdat u andere assets (zoals installatie kopieën) hebt die door de toepassings proxy moeten worden geopend op het hoogste niveau van de mappen structuur, publiceert u de app met `https://ExpenseApp` als de interne URL.
- De standaard externe URL is `https://ExpenseApp-contoso.msappproxy.net` , waarmee geen externe gebruiker naar de aanmeldings pagina gaat.
- U wilt `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` in plaats daarvan instellen als de URL van de start pagina, zodat een externe gebruiker de aanmeldings pagina eerst ziet.

> [!NOTE]
> Wanneer u gebruikers toegang geeft tot gepubliceerde apps, worden de apps weer gegeven in [mijn apps](../user-help/my-apps-portal-end-user-access.md) en het [Start programma voor de Office 365-app](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Voordat u begint

Houd bij het instellen van de URL van de start pagina de volgende vereisten in acht:

- Het pad dat u opgeeft, moet een subdomein zijn van de URL van het hoofd domein.

  Als de URL van het hoofd domein bijvoorbeeld is `https://apps.contoso.com/app1/` , moet de URL van de start pagina die u configureert, beginnen met `https://apps.contoso.com/app1/` .

- Als u een wijziging aanbrengt in de gepubliceerde app, kan de wijziging de waarde van de URL van de start pagina mogelijk opnieuw instellen. Wanneer u de app in de toekomst bijwerkt, moet u de URL van de start pagina opnieuw controleren en, indien nodig, bijwerken.

U kunt de URL van de start pagina instellen via de Azure Portal of met behulp van Power shell.

## <a name="change-the-home-page-in-the-azure-portal"></a>De start pagina in de Azure Portal wijzigen

Als u de URL van de start pagina van uw app wilt wijzigen via de Azure AD-Portal, voert u de volgende stappen uit:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als beheerder.
1. Selecteer **Azure Active Directory** en klik vervolgens **app-registraties**. De lijst met geregistreerde apps wordt weer gegeven.
1. Kies uw app in de lijst. Er wordt een pagina weer gegeven met de details van de geregistreerde app.
1. Selecteer onder **beheren** de optie **huis stijl**.
1. Werk de **URL van de start pagina**  bij met het nieuwe pad.

   ![Huisstijl pagina voor een geregistreerde app waarin het URL-veld van de start pagina wordt weer gegeven](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Selecteer **Opslaan**.

## <a name="change-the-home-page-with-powershell"></a>De start pagina wijzigen met Power shell

Als u de start pagina van een app wilt configureren met behulp van Power shell, moet u het volgende doen:

1. Installeer de Azure AD Power shell-module.
1. Zoek de ObjectId-waarde van de app.
1. De URL van de start pagina van de app bijwerken met behulp van Power shell-opdrachten.

### <a name="install-the-azure-ad-powershell-module"></a>De Azure AD Power shell-module installeren

Installeer de Azure AD Power shell-module voordat u een aangepaste URL voor de start pagina definieert met behulp van Power shell. U kunt het pakket downloaden via de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), dat gebruikmaakt van het Graph API-eind punt.

Voer de volgende stappen uit om het pakket te installeren:

1. Open een standaard Power shell-venster en voer de volgende opdracht uit:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Als u de opdracht als niet-beheerder uitvoert, gebruikt u de `-scope currentuser` optie.

1. Selecteer bij de installatie de optie **Y** om twee pakketten van Nuget.org te installeren. Beide pakketten zijn vereist.

### <a name="find-the-objectid-of-the-app"></a>De ObjectId van de app zoeken

U krijgt de ObjectId van de app door te zoeken naar de app op de weer gegeven naam of de start pagina.

1. Importeer in hetzelfde Power shell-venster de Azure AD-module.

   ```powershell
   Import-Module AzureAD
   ```

1. Meld u aan bij de Azure AD-module als Tenant beheerder.

   ```powershell
   Connect-AzureAD
   ```

1. Zoek de app. In dit voor beeld wordt Power shell gebruikt om de ObjectId te vinden door te zoeken naar de app met een weergave naam van `SharePoint` .

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Er wordt een resultaat weer gegeven dat er ongeveer als volgt uitziet. Kopieer de ObjectId-GUID voor gebruik in de volgende sectie.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   U kunt ook gewoon de lijst met alle apps ophalen, in de lijst zoeken naar de app met een specifieke weergave naam of start pagina en de ObjectId van de app kopiëren nadat de app is gevonden.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>De URL van de start pagina bijwerken

Maak de URL van de start pagina en werk uw app bij met die waarde. Ga verder met hetzelfde Power shell-venster, of Meld u opnieuw aan bij de Azure AD-module met als u een nieuw Power shell-venster gebruikt `Connect-AzureAD` . Volg nu deze stappen:

1. Maak een variabele voor de ObjectId-waarde die u in de vorige sectie hebt gekopieerd. (Vervang de ObjectId-waarde voor in dit share point-voor beeld met de waarde van de ObjectId van uw app.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Controleer of u de juiste app hebt door de volgende opdracht uit te voeren. De uitvoer moet identiek zijn aan de uitvoer die u in de vorige sectie hebt gezien ([Zoek de ObjectId van de app](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Maak een leeg toepassings object om de wijzigingen op te slaan die u wilt aanbrengen.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Stel de URL van de start pagina in op de gewenste waarde. De waarde moet een subpad van het subdomein van de gepubliceerde app zijn. Als u bijvoorbeeld de URL van de start pagina wijzigt van `https://sharepoint-iddemo.msappproxy.net/` in naar `https://sharepoint-iddemo.msappproxy.net/hybrid/` , gaan app-gebruikers rechtstreeks naar de aangepaste start pagina.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. De start pagina bijwerken.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Als u wilt bevestigen dat de wijziging is geslaagd, voert u de volgende opdracht uit stap 2 opnieuw uit.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   In ons voor beeld wordt de uitvoer nu als volgt weer gegeven:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Start de app opnieuw om te bevestigen dat de start pagina als eerste scherm wordt weer gegeven, zoals verwacht.

> [!NOTE]
> Wijzigingen die u in de app aanbrengt, kunnen de URL van de start pagina mogelijk opnieuw instellen. Als de URL van uw start pagina opnieuw wordt ingesteld, herhaalt u de stappen in deze sectie om deze weer in te stellen.

## <a name="next-steps"></a>Volgende stappen

- [Externe toegang tot SharePoint inschakelen met Azure AD-toepassingsproxy](application-proxy-integrate-with-sharepoint-server.md)
- [Zelfstudie: Een on-premises toepassing voor externe toegang toevoegen via Application Proxy in Azure Active Directory](application-proxy-add-on-premises-application.md)
