---
title: Een Azure AD-app maken & service-principal in de portal
titleSuffix: Microsoft identity platform
description: Maak een nieuwe Azure Active Directory app & service-principal om de toegang tot resources te beheren met op rollen gebaseerd toegangsbeheer in Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: 3ccc340727a437b3b1e953ea5e742ecdf7f21d40
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814078"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Procedure: Gebruik de portal voor het maken van een Azure AD-toepassing en service-principal die toegang hebben tot resources

In dit artikel wordt beschreven hoe u een nieuwe Azure Active Directory (Azure AD)-toepassing en service-principal maakt die kunnen worden gebruikt met op rollen gebaseerd toegangsbeheer. Wanneer u toepassingen, gehoste services of geautomatiseerde hulpprogramma's hebt die toegang moeten krijgen tot of wijzigen van resources, kunt u een identiteit voor de app maken. Deze identiteit staat bekend als een service-principal. Toegang tot resources wordt beperkt door de rollen die zijn toegewezen aan de service-principal, zodat u kunt bepalen welke resources toegankelijk zijn en op welk niveau. Uit veiligheidsoverwegingen is het altijd aanbevolen om voor geautomatiseerde tools service-principals te gebruiken, in plaats van deze zich te laten aanmelden met een gebruikers-id.

In dit artikel wordt beschreven hoe u de portal gebruikt om de service-principal te maken in de Azure Portal. Het is gericht op een toepassing met één tenant waarin de toepassing is bedoeld om binnen slechts één organisatie te worden uitgevoerd. Normaal gesproken gebruikt u toepassingen met één tenant voor Line-Of-Business-toepassingen die binnen uw organisatie worden uitgevoerd.  U kunt ook [Azure PowerShell om een service-principal te maken.](howto-authenticate-service-principal-powershell.md)

> [!IMPORTANT]
> In plaats van een service-principal te maken, kunt u overwegen om beheerde identiteiten voor Azure-resources te gebruiken voor uw toepassingsidentiteit. Als uw code wordt uitgevoerd op een service die beheerde identiteiten ondersteunt en toegang heeft tot resources die ondersteuning bieden voor Azure AD-verificatie, zijn beheerde identiteiten een betere optie voor u. Zie Wat zijn beheerde identiteiten voor Azure-resources? voor meer informatie over beheerde identiteiten [voor Azure-resources,](../managed-identities-azure-resources/overview.md)waaronder welke services deze momenteel ondersteunen.

## <a name="app-registration-app-objects-and-service-principals"></a>App-registratie, app-objecten en service-principals
Er is geen manier om rechtstreeks een service-principal te maken met behulp van Azure Portal.  Wanneer u een toepassing registreert via de Azure Portal, worden er automatisch een toepassingsobject en service-principal gemaakt in uw basismap of tenant.  Lees Toepassings- en service-principal-objecten in Azure Active Directory voor meer informatie over de relatie [tussen app-registratie,](app-objects-and-service-principals.md)toepassingsobjecten en service-principals.

## <a name="permissions-required-for-registering-an-app"></a>Vereiste machtigingen voor het registreren van een app

U moet voldoende machtigingen hebben om een toepassing te registreren bij uw Azure AD-tenant en een rol toewijzen aan de toepassing in uw Azure-abonnement.

### <a name="check-azure-ad-permissions"></a>Azure AD-machtigingen controleren

1. Selecteer **Azure Active Directory**.
1. Noteer uw rol. Als u de rol **Gebruiker** hebt, moet u ervoor zorgen dat niet-beheerders toepassingen kunnen registreren.

   ![Zoek uw rol. Als u een gebruiker bent, moet u ervoor zorgen dat niet-beheerders apps kunnen registreren](./media/howto-create-service-principal-portal/view-user-info.png)

1. Selecteer Gebruikersinstellingen in het **linkerdeelvenster.**
1. Controleer de **App-registraties** instelling. Deze waarde kan alleen worden ingesteld door een beheerder. Als deze optie is **ingesteld op Ja,** kan elke gebruiker in de Azure AD-tenant een app registreren.

Als de instelling voor app-registraties is ingesteld op **Nee,** kunnen alleen gebruikers met een beheerdersrol deze typen toepassingen registreren. Zie [Ingebouwde Azure AD-rollen](../roles/permissions-reference.md#all-roles) voor meer informatie over beschikbare beheerdersrollen en de specifieke machtigingen in Azure AD die aan elke rol worden verleend. Als aan uw account de rol Gebruiker is toegewezen, maar de instelling voor app-registratie is beperkt tot gebruikers met beheerdersrechten, vraagt u de beheerder om u een van de beheerdersrollen toe te wijzen waarmee alle aspecten van app-registraties kunnen worden gemaakt en beheert, of om gebruikers in staat te stellen apps te registreren.

### <a name="check-azure-subscription-permissions"></a>Machtigingen voor Azure-abonnementen controleren

In uw Azure-abonnement moet uw account toegang `Microsoft.Authorization/*/Write` hebben om een rol toe te wijzen aan een AD-app. Deze toegang wordt verleend via de rol [Eigenaar](../../role-based-access-control/built-in-roles.md#owner) of [Administrator voor gebruikerstoegang](../../role-based-access-control/built-in-roles.md#user-access-administrator). Als aan uw account de rol **Inzender is** toegewezen, hebt u niet voldoende machtigingen. U ontvangt een foutmelding wanneer u probeert de service-principal een rol toe te wijzen.

Ga als volgende te werk om uw abonnementsmachtigingen te controleren:

1. Zoek en selecteer **Abonnementen** of selecteer **Abonnementen** op de **startpagina.**

   ![Zoeken](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selecteer het abonnement waarin u de service-principal wilt maken.

   ![Abonnement selecteren voor toewijzing](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Als u het abonnement dat u zoekt niet ziet, selecteert u het **filter Globale abonnementen.** Zorg ervoor dat het want-abonnement is geselecteerd voor de portal.

1. Selecteer **Mijn machtigingen.** Selecteer vervolgens Klik **hier om de volledige toegangsgegevens voor dit abonnement weer te geven.**

   ![Selecteer het abonnement waarin u de service-principal wilt maken](./media/howto-create-service-principal-portal/view-details.png)

1. Selecteer **Weergeven** in **Roltoewijzingen** om uw toegewezen rollen weer te geven en te bepalen of u voldoende machtigingen hebt om een rol toe te wijzen aan een AD-app. Als dat niet het zo is, vraagt u de abonnementsbeheerder om u toe te voegen aan de rol Gebruikerstoegangbeheerder. In de volgende afbeelding krijgt de gebruiker de rol Eigenaar toegewezen, wat betekent dat de gebruiker voldoende machtigingen heeft.

   ![In dit voorbeeld ziet u dat aan de gebruiker de rol Eigenaar is toegewezen](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="register-an-application-with-azure-ad-and-create-a-service-principal"></a>Een toepassing registreren bij Azure AD en een service-principal maken

Laten we direct aan de zelf-id gaan gaan doen. Als er een probleem is, controleert u de [vereiste machtigingen](#permissions-required-for-registering-an-app) om ervoor te zorgen dat uw account de identiteit kan maken.

1. Meld u aan bij uw Azure-account via <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Selecteer **Azure Active Directory**.
1. Selecteer **App-registraties**.
1. Selecteer **Nieuwe registratie**.
1. Noem de toepassing. Selecteer een ondersteund accounttype, waarmee wordt bepaald wie de toepassing mag gebruiken. Selecteer **onder Omleidings-URI** de optie **Web** voor het type toepassing dat u wilt maken. Voer de URI in waar het toegangsken naar wordt verzonden. U kunt geen referenties maken voor een native [toepassing](../manage-apps/application-proxy-configure-native-client-application.md). U kunt dat type niet gebruiken voor een geautomatiseerde toepassing. Nadat u de waarden heeft instelling, selecteert u **Registreren.**

   ![Typ een naam voor uw toepassing](./media/howto-create-service-principal-portal/create-app.png)

U hebt uw Azure AD-toepassing en service-principal gemaakt.

> [!NOTE]
> U kunt meerdere toepassingen met dezelfde naam registreren in Azure AD, maar de toepassingen moeten verschillende toepassings-ID's (client) hebben.

## <a name="assign-a-role-to-the-application"></a>Een rol toewijzen aan de toepassing

Als u toegang wilt krijgen tot resources in uw abonnement, moet u een rol toewijzen aan de toepassing. Bepaal welke rol de juiste machtigingen voor de toepassing biedt. Zie Ingebouwde Azure-rollen voor meer informatie [over de beschikbare rollen.](../../role-based-access-control/built-in-roles.md)

U kunt het bereik instellen op het niveau van het abonnement, de resourcegroep of de resource. Machtigingen worden overgenomen naar lagere bereikniveaus. Als u bijvoorbeeld een toepassing toevoegt aan *de rol Lezer* voor een resourcegroep, kan deze de resourcegroep en alle resources die deze bevat lezen.

1. Selecteer in Azure Portal het bereikniveau waar u de toepassing aan wilt toewijzen. Als u bijvoorbeeld een rol wilt toewijzen in het **abonnementsbereik,** zoekt en selecteert u Abonnementen of **selecteert** u Abonnementen op de **startpagina.**

   ![Wijs bijvoorbeeld een rol toe op abonnementsbereik](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selecteer het specifieke abonnement waar u de toepassing aan wilt toewijzen.

   ![Abonnement selecteren voor toewijzing](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Als u het abonnement dat u zoekt niet ziet, selecteert u het **filter Globale abonnementen.** Zorg ervoor dat het want-abonnement is geselecteerd voor de portal.

1. Klik op **Toegangsbeheer (IAM)** .
1. Selecteer **Roltoewijzing toevoegen**.
1. Selecteer de rol die u aan de toepassing wilt toewijzen. Als u bijvoorbeeld wilt toestaan dat de toepassing  acties uitvoert zoals opnieuw **opstarten,** **exemplaren** starten en stoppen, selecteert u de **rol Inzender.**  Meer informatie over de [beschikbare rollen](../../role-based-access-control/built-in-roles.md) Standaard worden Azure AD-toepassingen niet weergegeven in de beschikbare opties. Als u uw toepassing wilt zoeken, zoekt u de naam en selecteert u deze.

   ![De rol selecteren die u wilt toewijzen aan de toepassing](./media/howto-create-service-principal-portal/select-role.png)

1. Selecteer **Opslaan om** de toewijzing van de rol te voltooien. U ziet uw toepassing in de lijst met gebruikers met een rol voor dat bereik.

Uw service-principal is ingesteld. U kunt deze gebruiken om uw scripts of apps uit te voeren. Ga naar Bedrijfstoepassingen om uw service-principal te beheren (machtigingen, door de gebruiker toestemming gegeven machtigingen, te zien welke gebruikers toestemming hebben gegeven, machtigingen te controleren, aanmeldingsgegevens te bekijken en **meer).**

In de volgende sectie ziet u hoe u waarden op kunt halen die nodig zijn bij het programmatisch aanmelden.

## <a name="get-tenant-and-app-id-values-for-signing-in"></a>Waarden voor tenant- en app-id's voor aanmelden

Wanneer u zich programmatisch aanmeldt, moet u de tenant-id doorgeven met uw verificatieaanvraag en de toepassings-id.  U hebt ook een certificaat of een verificatiesleutel nodig (beschreven in de volgende sectie). U kunt deze waarden als volgt ophalen:

1. Selecteer **Azure Active Directory**.
1. Selecteer **App-registraties** in Azure AD.
1. Kopieer de map-id (tenant-id) en sla deze op in uw toepassingscode.

    ![De map-id (tenant-id) kopiëren en in uw app-code opslaan](./media/howto-create-service-principal-portal/copy-tenant-id.png)

    De map-id (tenant)-id is ook te vinden op de overzichtspagina van de standaardmap.

1. Kopieer de **Toepassings-id** en sla deze op in uw toepassingscode.

   ![De toepassings-id (client-id) kopiëren](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="authentication-two-options"></a>Verificatie: twee opties

Er zijn twee soorten verificatie beschikbaar voor service-principals: verificatie op basis van wachtwoorden (toepassingsgeheim) en verificatie op basis van certificaten. *We raden u aan een certificaat te* gebruiken, maar u kunt ook een toepassingsgeheim maken.

### <a name="option-1-upload-a-certificate"></a>Optie 1: Een certificaat uploaden

U kunt een bestaand certificaat gebruiken als u er een hebt.  U kunt eventueel alleen een zelfonder ondertekend certificaat maken *voor testdoeleinden.* Als u een zelfondertekend certificaat wilt maken, opent u PowerShell en voer [u New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) uit met de volgende parameters om het certificaat te maken in het certificaatopslag van de gebruiker op uw computer:

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exporteert dit certificaat naar een bestand met [de](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC-module Gebruikerscertificaat beheren die toegankelijk is vanuit de Windows-Configuratiescherm.

1. Selecteer **Uitvoeren** in het **menu Start** en voer **certmgr.msc in.**

   Het hulpprogramma Certificate Manager voor de huidige gebruiker wordt weergegeven.

1. Als u uw certificaten wilt weergeven, vouwt u onder **Certificaten - Huidige** gebruiker in het linkerdeelvenster de map **Persoonlijk** uit.
1. Klik met de rechtermuisknop op het certificaat dat u hebt gemaakt en selecteer **Alle >Exporteren.**
1. Volg de wizard Certificaat exporteren.  Exporteert de persoonlijke sleutel niet en exporteert niet naar een . CER-bestand.

Het certificaat uploaden:

1. Selecteer **Azure Active Directory**.
1. Selecteer **App-registraties** toepassing in Azure AD.
1. Selecteer **Certificaten en geheimen**.
1. Selecteer **Certificaat uploaden** en selecteer het certificaat (een bestaand certificaat of het zelf-ondertekende certificaat dat u hebt geëxporteerd).

    ![Selecteer Certificaat uploaden en selecteer het certificaat dat u wilt toevoegen](./media/howto-create-service-principal-portal/upload-cert.png)

1. Selecteer **Toevoegen**.

Nadat u het certificaat hebt geregistreerd bij uw toepassing in de portal voor toepassingsregistratie, moet u de code van de clienttoepassing inschakelen om het certificaat te gebruiken.

### <a name="option-2-create-a-new-application-secret"></a>Optie 2: Een nieuw toepassingsgeheim maken

Als u ervoor kiest geen certificaat te gebruiken, kunt u een nieuw toepassingsgeheim maken.

1. Selecteer **Azure Active Directory**.
1. Selecteer **App-registraties** in Azure AD.
1. Selecteer **Certificaten en geheimen**.
1. Selecteer **Clientgeheimen -> Nieuw clientgeheim**.
1. Geef een beschrijving van het geheim en een duur op. Selecteer **Toevoegen** wanneer u klaar bent.

   Nadat u het clientgeheim hebt opgeslagen, wordt de waarde van het clientgeheim weergegeven. Kopieer deze waarde omdat u de sleutel later niet meer kunt ophalen. U geeft de sleutelwaarde op met de toepassings-id om u aan te melden als de toepassing. Bewaar de sleutelwaarde op een locatie waar de toepassing deze kan ophalen.

   ![De geheimwaarde kopiëren omdat u deze later niet meer kunt ophalen](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Toegangsbeleid voor resources configureren
Houd er rekening mee dat u mogelijk aanvullende machtigingen moet configureren voor resources die uw toepassing nodig heeft om toegang te krijgen. U moet bijvoorbeeld ook het [toegangsbeleid](../../key-vault/general/security-features.md#privileged-access) van een sleutelkluis bijwerken om uw toepassing toegang te geven tot sleutels, geheimen of certificaten.

1. Ga in <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>naar uw sleutelkluis en selecteer **Toegangsbeleid.**
1. Selecteer **Toegangsbeleid toevoegen** en selecteer vervolgens de sleutel-, geheim- en certificaatmachtigingen die u aan uw toepassing wilt verlenen.  Selecteer de service-principal die u eerder hebt gemaakt.
1. Selecteer **Toevoegen om** het toegangsbeleid toe te voegen en vervolgens Opslaan **om** uw wijzigingen door te voeren.
    ![Toegangsbeleid toevoegen](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik Azure PowerShell om een service-principal te maken.](howto-authenticate-service-principal-powershell.md)
* Zie Op rollen gebaseerd toegangsbeheer van [Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het opgeven van beveiligingsbeleid.
* Zie resourceproviderbewerkingen voor een lijst met beschikbare acties die aan gebruikers kunnen worden Azure Resource Manager [geweigerd.](../../role-based-access-control/resource-provider-operations.md)
* Zie de naslaginformatie over de Api voor toepassingen voor meer informatie over het werken **met** app Microsoft Graph registraties met behulp [van](/graph/api/resources/application) Microsoft Graph.
