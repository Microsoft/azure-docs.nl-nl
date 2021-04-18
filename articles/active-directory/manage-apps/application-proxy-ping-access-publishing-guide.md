---
title: Verificatie op basis van headers met PingAccess voor Azure AD-toepassingsproxy
description: Publiceer toepassingen met PingAccess en app-proxy ter ondersteuning van verificatie op basis van headers.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 23008d785c27b901f3487d3eddff7cb8e7529f6e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600077"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Op headers gebaseerde verificatie voor eenmalige aanmelding met de toepassingsproxy en PingAccess

Azure Active Directory (Azure AD) toepassingsproxy een partnerschap met PingAccess, zodat uw Azure AD-klanten toegang hebben tot meer van uw toepassingen. PingAccess biedt een andere optie dan [geïntegreerde, op headers gebaseerde een aanmelding](application-proxy-configure-single-sign-on-with-headers.md).

## <a name="whats-pingaccess-for-azure-ad"></a>Wat is PingAccess voor Azure AD?

Met PingAccess voor Azure AD kunt u gebruikers toegang en eenmalige aanmelding (SSO) verlenen aan toepassingen die headers gebruiken voor verificatie. Application Proxy behandelt deze toepassingen hetzelfde als andere, waarbij Azure AD wordt gebruikt om de toegang te verifiëren en vervolgens het verkeer door te geven via de connectorservice. PingAccess bevindt zich voor de toepassingen en zet het toegangsteken van Azure AD om in een header. De toepassing ontvangt vervolgens de verificatie in de indeling die kan worden gelezen.

De gebruikers merken hier niets van wanneer ze zich aanmelden om uw bedrijfstoepassingen te gebruiken. Ze kunnen nog steeds vanaf elke locatie op elk apparaat werken. De toepassingsproxy-connectors sturen extern verkeer naar alle apps zonder rekening te houden met hun verificatietype, zodat de belasting automatisch wordt ge balancerd.

## <a name="how-do-i-get-access"></a>Hoe kan ik toegang krijgen?

Aangezien dit scenario afkomstig is van een samenwerking tussen Azure Active Directory en PingAccess, hebt u licenties nodig voor beide services. De Azure Active Directory Premium bevatten echter een eenvoudige PingAccess-licentie die betrekking heeft op maximaal 20 toepassingen. Als u meer dan 20 headertoepassingen wilt publiceren, kunt u een extra licentie aanschaffen bij PingAccess.

Zie [Azure Active Directory-edities](../fundamentals/active-directory-whatis.md) voor meer informatie.

## <a name="publish-your-application-in-azure"></a>Uw toepassing publiceren in Azure

Dit artikel is bedoeld voor mensen die voor de eerste keer een toepassing met dit scenario publiceren. Naast het gedetailleerd beschrijven van de publicatiestappen, helpt het u ook om aan de slag te gaan met toepassingsproxy en PingAccess. Als u beide services al hebt geconfigureerd, maar u de publicatiestappen wilt opfrissen, gaat u verder met de sectie Uw toepassing toevoegen aan [Azure AD toepassingsproxy](#add-your-application-to-azure-ad-with-application-proxy) publiceren.

> [!NOTE]
> Aangezien dit scenario een partnerschap is tussen Azure AD en PingAccess, bestaan sommige instructies op de site Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Een toepassingsproxy-connector installeren

Als u een connector toepassingsproxy ingeschakeld en geïnstalleerd, kunt u deze sectie overslaan en naar Uw toepassing toevoegen aan Azure AD gaan met [toepassingsproxy.](#add-your-application-to-azure-ad-with-application-proxy)

De toepassingsproxy-connector is een Windows Server-service die het verkeer van uw externe werknemers naar uw gepubliceerde toepassingen stuurt. Zie Zelfstudie: Een on-premises toepassing voor externe toegang toevoegen via toepassingsproxy in Azure Active Directory voor gedetailleerde [installatie-instructies.](application-proxy-add-on-premises-application.md)

1. Meld u als Azure Active Directory [aan](https://aad.portal.azure.com/) bij de portal. De **Azure Active Directory beheercentrumpagina** wordt weergegeven.
1. Selecteer **Azure Active Directory**  >  **Toepassingsproxy**  >  **Connectorservice downloaden.** De **toepassingsproxy connector downloaden** wordt weergegeven.

   ![Connector voor toepassingsproxy downloaden](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Volg de installatie-instructies.

Als u de connector downloadt, wordt de toepassingsproxy automatisch ingeschakeld voor uw directory, maar als dat niet het toepassingsproxy, **kunt u toepassingsproxy.**

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Uw toepassing toevoegen aan Azure AD met toepassingsproxy

Er zijn twee acties die u moet uitvoeren in de Azure Portal. Eerst moet u uw toepassing publiceren met toepassingsproxy. Vervolgens moet u informatie verzamelen over de toepassing die u kunt gebruiken tijdens de PingAccess-stappen.

#### <a name="publish-your-application"></a>Uw toepassing publiceren

U moet uw toepassing eerst publiceren. Deze actie omvat:

- Uw on-premises toepassing toevoegen aan Azure AD
- Een gebruiker toewijzen om de toepassing te testen en eenmalige aanmelding op basis van headers te kiezen
- De omleidings-URL van de toepassing instellen
- Machtigingen verlenen aan gebruikers en andere toepassingen voor het gebruik van uw on-premises toepassing

Uw eigen on-premises toepassing publiceren:

1. Als u zich niet in de laatste [](https://aad.portal.azure.com/) sectie hebt voored, meld u zich als Azure Active Directory toepassingsbeheerder aan bij de portal.
1. Selecteer **Bedrijfstoepassingen**  >  **Nieuwe toepassing** Een  >  **on-premises toepassing toevoegen.** De **pagina Uw eigen on-premises toepassing toevoegen** wordt weergegeven.

   ![Uw eigen on-premises toepassing toevoegen](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Vul de vereiste velden in met informatie over uw nieuwe toepassing. Gebruik de onderstaande richtlijnen voor de instellingen.

   > [!NOTE]
   > Zie Een [on-premises app](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)toevoegen aan Azure AD voor een gedetailleerd overzicht van deze stap.

   1. **Interne URL:** normaal gesproken geeft u de URL op die u naar de aanmeldingspagina van de app brengt wanneer u zich in het bedrijfsnetwerk be meldt. Voor dit scenario moet de connector de PingAccess-proxy behandelen als de voorpagina van de toepassing. Gebruik deze indeling: `https://<host name of your PingAccess server>:<port>` . De poort is standaard 3000, maar u kunt deze configureren in PingAccess.

      > [!WARNING]
      > Voor dit type een aanmelding moet de interne URL gebruikmaken van en kan `https` deze niet `http` gebruiken. Er is ook een beperking bij het configureren van een toepassing dat twee apps niet dezelfde interne URL mogen hebben, omdat app-proxy hierdoor onderscheid kan maken tussen toepassingen.

   1. **Verificatiemethode vooraf:** kies **Azure Active Directory**.
   1. **URL vertalen in headers:** kies **Nee.**

   > [!NOTE]
   > Als dit uw eerste toepassing is, gebruikt u poort 3000 om te starten en komt u terug om deze instelling bij te werken als u de PingAccess-configuratie wijzigt. Voor volgende toepassingen moet de poort overeenkomen met de listener die u hebt geconfigureerd in PingAccess. Meer informatie over [listeners in PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Selecteer **Toevoegen**. De overzichtspagina voor de nieuwe toepassing wordt weergegeven.

Wijs nu een gebruiker toe voor het testen van toepassingen en kies op headers gebaseerde een aanmelding:

1. Selecteer gebruikers en groepen Gebruikers en groepen toevoegen in de zijbalk van de toepassing  >    >  **( \<Number> Geselecteerd).** Er wordt een lijst met gebruikers en groepen weergegeven waar u uit kunt kiezen.

   ![Toont de lijst met gebruikers en groepen](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Selecteer een gebruiker voor het testen van toepassingen en selecteer **Selecteren.** Zorg ervoor dat dit testaccount toegang heeft tot de on-premises toepassing.
1. Selecteer **Toewijzen**.
1. Selecteer In de zijbalk van de toepassing **de optie Een aanmelding op basis** van  >  **header.**

   > [!TIP]
   > Als dit de eerste keer is dat u een header-gebaseerde een aanmelding gebruikt, moet u PingAccess installeren. Als u er zeker van wilt zijn dat uw Azure-abonnement automatisch wordt gekoppeld aan uw PingAccess-installatie, gebruikt u de koppeling op deze pagina voor een aanmelding om PingAccess te downloaden. U kunt de downloadsite nu openen of later teruggaan naar deze pagina.

   ![Geeft het scherm voor aanmelding op basis van headers en PingAccess weer](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Selecteer **Opslaan**.

Zorg er vervolgens voor dat uw omleidings-URL is ingesteld op uw externe URL:

1. Selecteer in **Azure Active Directory zijbalk van** het beheercentrum **Azure Active Directory**  >  **App-registraties.** Er wordt een lijst met toepassingen weergegeven.
1. Selecteer uw toepassing.
1. Selecteer de koppeling naast **Omleidings-URI's,** met het aantal omleidings-URI's dat is ingesteld voor web- en openbare clients. De **\<application name> pagina -** Verificatie wordt weergegeven.
1. Controleer of de externe URL die u eerder aan uw toepassing hebt toegewezen, in de lijst Met **omleidings-URI's** staat. Als dat niet zo is, voegt u nu de externe URL toe met behulp van het omleidings-URI-type **Web** en selecteert u **Opslaan.**

Naast de externe URL moet een geautoriseerd eindpunt van Azure Active Directory op de externe URL worden toegevoegd aan de lijst met omleidings-URI's.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

Stel ten slotte uw on-premises toepassing in zodat gebruikers leestoegang hebben en andere toepassingen lees-/schrijftoegang hebben:

1. Selecteer in **App-registraties** zijbalk voor uw toepassing API-machtigingen Een machtiging toevoegen aan   >    >  **Microsoft**  >  **API's Microsoft Graph.** De **pagina API-machtigingen** **aanvragen voor Microsoft Graph** wordt weergegeven, die de API's voor Windows-Azure Active Directory.

   ![Toont de pagina API-machtigingen aanvragen](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Selecteer **Gedelegeerde machtigingen**  >    >  **Gebruiker.Lezen.**
1. Selecteer **Toepassingsmachtigingen**  >    >  **Application Application.ReadWrite.All.**
1. Selecteer **Machtigingen toevoegen**.
1. Selecteer op **de pagina API-machtigingen** de optie **Beheerders toestemming verlenen voor \<your directory name>**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Informatie verzamelen voor de PingAccess-stappen

U moet deze drie soorten informatie (alle GUID's) verzamelen om uw toepassing in te stellen met PingAccess:

| Naam van azure AD-veld | Naam van het veld PingAccess | Gegevensindeling |
| --- | --- | --- |
| **(Client-)id van de app** | **Client ID** | GUID |
| **(Tenant-)id van de map** | **Uitgevende instelling** | GUID |
| `PingAccess key` | **Clientgeheim** | Willekeurige tekenreeks |

Deze informatie verzamelen:

1. Selecteer in **Azure Active Directory zijbalk van** het beheercentrum **Azure Active Directory**  >  **App-registraties.** Er wordt een lijst met toepassingen weergegeven.
1. Selecteer uw toepassing. De **App-registraties** voor uw toepassing wordt weergegeven.

   ![Registratieoverzicht voor een toepassing](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Selecteer naast de **waarde Toepassings-id (client)het** pictogram Kopiëren naar **klembord** en kopieer en sla deze op. U geeft deze waarde later op als client-id van PingAccess.
1. Selecteer naast **de waarde Map-id (tenant)-id** ook Kopiëren naar **klembord,** kopieer en sla deze op. U geeft deze waarde later op als vergever van PingAccess.
1. Selecteer in de zijbalk **van de App-registraties** voor uw toepassing certificaten en **geheimen**  >  **Nieuw clientgeheim.** De **pagina Een clientgeheim toevoegen** wordt weergegeven.

   ![Toont de pagina Een clientgeheim toevoegen](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. Typ **in** `PingAccess key` Beschrijving.
1. Kies **onder Verloopt** hoe u de PingAccess-sleutel in stelt: In **1** jaar , Over **2 jaar** of **Nooit.**
1. Selecteer **Toevoegen**. De Sleutel PingAccess wordt weergegeven in de tabel met clientgeheimen, met een willekeurige tekenreeks die automatisch wordt ingevuld in het **veld VALUE.**
1. Naast het veld VALUE van de  PingAccess-sleutel selecteert u het pictogram Kopiëren naar **klembord** en kopieert u het en sla u het op. U geeft deze waarde later op als het clientgeheim van PingAccess.

**Werk het veld `acceptMappedClaims` bij:**

1. Meld u als Azure Active Directory [aan](https://aad.portal.azure.com/) bij de portal.
1. Selecteer **Azure Active Directory** > **App-registraties**. Er wordt een lijst met toepassingen weergegeven.
1. Selecteer uw toepassing.
1. Selecteer manifest in de **zijbalk van App-registraties** pagina voor uw **toepassing.** De manifest-JSON-code voor de registratie van uw toepassing wordt weergegeven.
1. Zoek naar het `acceptMappedClaims` veld en wijzig de waarde in `True` .
1. Selecteer **Opslaan**.

### <a name="use-of-optional-claims-optional"></a>Gebruik van optionele claims (optioneel)

Met optionele claims kunt u standaardclaims toevoegen, maar niet standaard opgenomen claims die elke gebruiker en tenant heeft. U kunt optionele claims voor uw toepassing configureren door het toepassingsmanifest te wijzigen. Zie het artikel Het [Azure AD-toepassingsmanifest begrijpen voor meer informatie](../develop/reference-app-manifest.md)

Voorbeeld van het opnemen van een e-mailadres in access_token dat PingAccess gebruikt:

```json
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Gebruik van beleid voor claimtoewijzing (optioneel)

[Beleid voor claimtoewijzing (preview)](../develop/reference-claims-mapping-policy-type.md#claims-mapping-policy-properties) voor kenmerken die niet bestaan in AzureAD. Met claimtoewijzing kunt u oude on-prem-apps migreren naar de cloud door aanvullende aangepaste claims toe te voegen die worden gebruikt door uw ADFS- of gebruikersobjecten

Als u uw toepassing een aangepaste claim wilt laten gebruiken en aanvullende velden wilt opnemen, moet u ook een aangepast beleid voor claimtoewijzing hebben gemaakt en aan de [toepassing hebben toegewezen.](../develop/active-directory-claims-mapping.md)

> [!NOTE]
> Als u een aangepaste claim wilt gebruiken, moet u ook een aangepast beleid hebben gedefinieerd en aan de toepassing hebben toegewezen. Dit beleid moet alle vereiste aangepaste kenmerken bevatten.
>
> U kunt beleidsdefinitie en -toewijzing uitvoeren via PowerShell of Microsoft Graph. Als u ze in PowerShell doet, moet u deze mogelijk eerst gebruiken en `New-AzureADPolicy` vervolgens toewijzen aan de toepassing met `Add-AzureADServicePrincipalPolicy` . Zie Beleidstoewijzing claimstoewijzing [voor meer informatie.](../develop/active-directory-claims-mapping.md)

Voorbeeld:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>PingAccess inschakelen voor het gebruik van aangepaste claims

PingAccess inschakelen voor het gebruik van aangepaste claims is optioneel, maar vereist als u verwacht dat de toepassing extra claims verbruikt.

Wanneer u PingAccess in de volgende stap configureert, moet voor de websessie die u maakt (Settings->Access->Web Sessions) Aanvraagprofiel **zijn** uitgeschakeld en Gebruikerskenmerken vernieuwen zijn ingesteld op  **Nee**

## <a name="download-pingaccess-and-configure-your-application"></a>PingAccess downloaden en uw toepassing configureren

Nu u alle installatiestappen voor Azure Active Directory hebt voltooid, kunt u verder gaan met het configureren van PingAccess.

De gedetailleerde stappen voor het PingAccess-deel van dit scenario worden verder beschreven in de documentatie pingidentiteit. Volg de instructies in [PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) configureren voor Azure AD om toepassingen te beveiligen die zijn gepubliceerd met Microsoft Azure AD toepassingsproxy op de pingidentiteitswebsite en download de nieuwste versie [van PingAccess.](https://www.pingidentity.com/en/lp/azure-download.html?)

Met deze stappen kunt u PingAccess installeren en een PingAccess-account instellen (als u er nog geen hebt). Als u vervolgens een OIDC-verbinding (Azure AD OpenID Connect) wilt maken, stelt u een tokenprovider in met de **map-id (tenant)-id** die u hebt gekopieerd uit de Azure AD-portal. Als u vervolgens een websessie wilt maken in PingAccess, gebruikt u de toepassings-id en -waarden **(client).** `PingAccess key` Daarna kunt u identiteitstoewijzing instellen en een virtuele host, site en toepassing maken.

### <a name="test-your-application"></a>Uw toepassing testen

Wanneer u al deze stappen hebt voltooid, moet uw toepassing actief zijn. Als u deze wilt testen, opent u een browser en navigeert u naar de externe URL die u hebt gemaakt tijdens het publiceren van de toepassing in Azure. Meld u aan met het testaccount dat u aan de toepassing hebt toegewezen.

## <a name="next-steps"></a>Volgende stappen

- [PingAccess voor Azure AD configureren om toepassingen te beveiligen die zijn gepubliceerd met Microsoft Azure AD toepassingsproxy](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](what-is-single-sign-on.md)
- [Problemen en foutberichten met Application Proxy oplossen](application-proxy-troubleshoot.md)