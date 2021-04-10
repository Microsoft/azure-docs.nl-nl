---
title: Hybride Azure Active Directory-deelname configureren voor federatieve domeinen | Microsoft Docs
description: Hybride Azure Active Directory-koppeling configureren voor federatieve domeinen.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221b7bdbb8ab5d0121e9c8032be8f18d8ae60d1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578053"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Zelfstudie: Hybride Azure Active Directory-deelname configureren voor federatieve domeinen

Net als een gebruiker in uw organisatie is een apparaat een kernidentiteit die u wilt beveiligen. U kunt de apparaat-id gebruiken om uw resources altijd en overal te beschermen. U kunt dit doel bereiken door de apparaat-id's op te halen en te beheren in Azure Active Directory (Azure AD) met een van de volgende methoden:

- Azure AD-koppeling
- Hybride Azure AD-koppeling
- Azure AD-registratie

Door uw apparaten in Azure AD te zetten, optimaliseert u de productiviteit van uw gebruikers via eenmalige aanmelding (SSO) bij al uw on-premises bronnen en cloudbronnen. U kunt de toegang tot uw on-premises bronnen en cloudbronnen beveiligen met [voorwaardelijke toegang](../conditional-access/howto-conditional-access-policy-compliant-device.md).

Een gefedereerde omgeving moet een id-provider hebben die de volgende vereisten ondersteunt. Als u een gefedereerde omgeving hebt met Active Directory Federation Services (AD FS), worden de onderstaande vereisten al ondersteund.

- **WIAORMULTIAUTHN-claim:** Deze claim is verwijst voor een hybride Azure AD-koppeling oplossen voor downlevel Windows-apparaten.
- **WS-Trust-protocol:** Dit protocol is vereist voor de verificatie van Windows Current Hybrid Azure AD gekoppelde apparaten met Azure AD.
  Wanneer u AD FS gebruikt, moet u de volgende WS-Trust-eind punten inschakelen: `/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Zowel **adfs/services/trust/2005/windowstransport** als **adfs/services/trust/13/windowstransport** dienen alleen te worden ingeschakeld als naar intranet gerichte eindpunten en mogen NIET beschikbaar worden gemaakt als naar extranet gerichte eindpunten via de webtoepassingsproxy. Zie voor meer informatie over het uitschakelen van WS-Trust Windows eindpunten [Disable WS-Trust Windows endpoints on the proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Onder **Service** > **Eindpunten** in de AD FS-beheerconsole kunt u zien welke eindpunten zijn ingeschakeld.

In deze zelfstudie leert u hoe u hybride Azure AD-koppeling kunt configureren voor Active Directory-computerapparaten in een gefedereerde omgeving die bij een domein horen door AD FS te gebruiken.

In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Hybride Azure AD-koppeling configureren
> * Downlevel Windows-apparaten inschakelen
> * De registratie verifiëren
> * Problemen oplossen

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u bekend bent met deze artikelen:

- [Wat is een apparaat-id?](overview.md)
- [Hoe plant u uw implementatie van hybride Azure AD-deelname](hybrid-azuread-join-plan.md)
- [Hoe voert u gecontroleerde validatie van hybride Azure AD-deelname uit](hybrid-azuread-join-control.md)

Voor het configureren van het scenario in deze zelfstudie hebt u het volgende nodig:

- Windows Server 2012 R2 met AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) versie 1.1.819.0 of hoger

Vanaf versie 1.1.819.0 bevat Azure AD Connect een wizard die u kunt gebruiken om een hybride Azure AD-koppeling te configureren. De wizard maakt het configuratieproces aanzienlijk eenvoudiger. De gerelateerde wizard:

- Configureert de serviceverbindingspunten (SCP’s) voor apparaatregistratie
- Maakt een back-up van uw bestaande vertrouwensrelatie van de Relying Party van Azure AD
- Werkt de claimregels in uw Azure AD-vertrouwensrelatie bij

De configuratiestappen in dit artikel zijn gebaseerd op gebruik van de Azure AD Connect-wizard. Als u een oudere versie van Azure AD Connect hebt geïnstalleerd, moet u deze bijwerken naar 1.1.819 of hoger om de wizard te gebruiken. Zie [hybride Azure AD-koppeling handmatig configureren](hybrid-azuread-join-manual.md) als het voor u niet mogelijk is om de nieuwste versie van Azure AD Connect te installeren.

Voor hybride Azure AD-koppeling moeten apparaten toegang tot de volgende Microsoft-bronnen hebben vanuit het netwerk van uw organisatie:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- De beveiligingstokenservice (voor STS - federatieve domeinen) van uw organisatie
- `https://autologon.microsoftazuread-sso.com` (Als u naadloze eenmalige aanmelding gebruikt of wilt gebruiken)

> [!WARNING]
> Als uw organisatie proxyservers gebruikt die SSL-verkeer onderscheppen voor scenario's zoals preventie van gegevensverlies of beperkingen voor Azure AD-tenants, moet u ervoor zorgen dat verkeer naar 'https://device.login.microsoftonline.com ' wordt uitgesloten van TLS break-and-inspect. Als u 'https://device.login.microsoftonline.com ' niet uitsluit, kan dit problemen geven met de verificatie van clientcertificaten, die op hun beurt weer problemen veroorzaken met apparaatregistratie en voorwaardelijke toegang op basis van apparaten.

Vanaf Windows 10 1803 is het zo dat als de directe hybride Azure AD-koppeling mislukt voor federatieve omgeving met gebruik van AD FS, we vertrouwen op Azure AD Connect om het computerobject in Azure AD te synchroniseren dat vervolgens wordt gebruikt om de apparaatregistratie voor hybride Azure AD-koppeling te voltooien. Verifieer dat Azure AD Connect de computerobjecten heeft gesynchroniseerd van de apparaten die u hybride Azure AD-gekoppeld wilt maken. Als de computerobjecten bij specifieke organisatie-eenheden horen, moet u ook deze OE's worden configureren voor synchronisatie in Azure AD Connect. Zie [Filteren configureren door Azure AD Connect te gebruiken](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) (Op organisatie-eenheden gebaseerde filters) voor meer informatie over het synchroniseren van computerobjecten met behulp van Azure AD Connect.

> [!NOTE]
> Als u de synchronisatie koppeling voor apparaatregistratie wilt ophalen, moet u als onderdeel van de configuratie van de apparaatregistratie de standaard kenmerken van het apparaat niet uitsluiten van de configuratie van de Azure AD Connect synchronisatie. Zie [kenmerken gesynchroniseerd door Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10)voor meer informatie over standaard kenmerken van apparaten die zijn GESYNCHRONISEERD met Aad.

Als uw organisatie toegang tot internet via een uitgaande proxy vereist, raadt Microsoft u aan [WPAD (Web Proxy Auto-Discovery) te implementeren](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) om Windows 10-computers in staat te stellen apparaten te registreren met Azure AD. Zie [Troubleshooting automatic detection](/previous-versions/tn-archive/cc302643(v=technet.10)) (Problemen met automatische detectie oplossen) als u problemen ondervindt met het configureren en beheren van WPAD. 

Als u WPAD niet gebruikt enproxy-instellingen op uw computer wilt configureren, kunt u dit doen vanaf Windows 10 1709. Zie [WinHTTP-instellingen configureren met behulp van een groepsbeleidsobject (GPO)](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo) voor meer informatie.

> [!NOTE]
> Als u proxy-instellingen op uw computer configureert met behulp van WinHTTP-instellingen, kunnen computers die geen verbinding hebben met de geconfigureerde proxy, geen verbinding maken met internet.

Als uw organisatie internettoegang via een geverifieerde uitgaande proxy vereist, moet u ervoor zorgen dat uw Windows 10-computers succesvol kunnen verifiëren bij de uitgaande proxy. Omdat Windows 10-computers apparaatregistratie uitvoeren via machinecontext, moet u verificatie naar een uitgaande proxy configureren via machinecontext. Vraag uw provider van de uitgaande proxy naar de configuratievereisten.

Om te controleren of het apparaat toegang heeft tot de bovenstaande Microsoft-resources onder het systeemaccount, kunt u het script [Test Device Registration Connectivity](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) gebruiken.

## <a name="configure-hybrid-azure-ad-join"></a>Hybride Azure AD-koppeling configureren

Voor het configureren van een hybride Azure AD-koppeling via Azure AD Connect hebt u het volgende nodig:

- De referenties van een globale beheerder voor uw Azure AD-tenant  
- De referenties van een ondernemingsbeheerder voor elk van de forests
- De referenties van uw AD FS-beheerder

**Ga als volgt te werk om een hybride Azure AD-koppeling te configureren via Azure AD Connect**:

1. Start Azure AD Connect en selecteer **Configureren**.

   ![Welkom](./media/hybrid-azuread-join-federated-domains/11.png)

1. Selecteer op de pagina **Extra taken** de optie **Apparaatopties configureren** en selecteer **Volgende**.

   ![Extra taken](./media/hybrid-azuread-join-federated-domains/12.png)

1. Selecteer **Volgende** op de pagina **Overzicht**.

   ![Overzicht](./media/hybrid-azuread-join-federated-domains/13.png)

1. Voer op de pagina **Verbinding maken met Azure AD** de referenties in van een globale beheerder voor uw Azure AD-tenant en selecteer **Volgende**.

   ![Verbinding maken met Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Selecteer op de pagina **Apparaatopties** de optie **Hybride Azure AD-koppeling configureren** en selecteer **Volgende**.

   ![Apparaatopties](./media/hybrid-azuread-join-federated-domains/15.png)

1. Voer op de pagina **SCP** de volgende stappen uit en selecteer **Volgende**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Selecteer de forest.
   1. Selecteer de verificatieservice. U moet **AD FS-server** selecteren tenzij uw organisatie alleen maar Windows 10-clients heeft en u de synchronisatie van computers/apparaten hebt geconfigureerd, of als uw organisatie van SSO gebruikmaakt.
   1. Selecteer **Toevoegen** om de referenties van een ondernemingsbeheerder in te voeren.

1. Selecteer op de pagina **Apparaatbesturingssystemen** de besturingssystemen die worden gebruikt door de apparaten in uw Active Directory-omgeving en selecteer **Volgende**.

   ![Apparaatbesturingssysteem](./media/hybrid-azuread-join-federated-domains/17.png)

1. Voer op de pagina **Federatieconfiguratie** de referenties van uw AD FS-beheerder in en selecteer **Volgende**.

   ![Federatieconfiguratie](./media/hybrid-azuread-join-federated-domains/18.png)

1. Selecteer op de pagina **Gereed om te configureren** op **Configureren**.

   ![Klaar om te configureren](./media/hybrid-azuread-join-federated-domains/19.png)

1. Selecteer de pagina **Configuratie voltooid** op **Afsluiten**.

   ![Configuratie voltooid](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Downlevel Windows-apparaten inschakelen

Als sommige van uw domein-gekoppelde apparaten downlevel Windows-apparaten zijn, moet u het volgende doen:

- De lokale intranetinstellingen voor apparaatregistratie configureren
- Microsoft Workplace Join installeren voor downlevel Windows-computers

> [!NOTE]
> Windows 7-ondersteuning is beëindigd op 14 januari 2020. Bekijk [Ondersteuning voor Windows 7 is beëindigd](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020) voor meer informatie.

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>De lokale intranetinstellingen voor apparaatregistratie configureren

Om de hybride Azure AD-koppeling van uw downlevel Windows-apparaten succesvol te voltooien en om certificaatprompts te vermijden wanneer apparaten worden geverifieerd bij Azure AD, kunt u een beleid naar uw domein-gekoppelde apparaten pushen om de volgende URL’s toe te voegen aan de zone Lokaal intranet in Internet Explorer:

- `https://device.login.microsoftonline.com`
- De STS van uw organisatie (voor federatieve domeinen)
- `https://autologon.microsoftazuread-sso.com` (voor naadloze eenmalige aanmelding)

U moet ook **Statusbalkupdates via scripts toestaan** inschakelen in de zone Lokaal intranet van de gebruiker.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Microsoft Workplace Join installeren voor downlevel Windows-computers

Voor het registreren van downlevel Windows-apparaten moeten organisaties [Microsoft Workplace Join voor niet-Windows 10-computers](https://www.microsoft.com/download/details.aspx?id=53554) installeren. Microsoft Workplace Join voor niet-Windows 10-computers is beschikbaar in het Microsoft Downloadcentrum.

U kunt het pakket implementeren met behulp van een softwaredistributiesysteem, zoals  [Microsoft Endpoint Configuration Manager](/configmgr/). Het pakket ondersteunt de standaardopties voor installatie op de achtergrond met de parameter `quiet`. De huidige branch van Configuration Manager biedt voordelen ten opzichte van eerdere versies, zoals de mogelijkheid om voltooide registraties bij te houden.

Het installatieprogramma maakt een geplande taak op het systeem dat wordt uitgevoerd in de gebruikerscontext. De taak wordt geactiveerd wanneer de gebruiker zich aanmeldt bij Windows. De taak wordt op de achtergrond met Azure AD aan het apparaat gekoppeld met behulp van de referenties van de gebruiker na verificatie met Azure AD.

## <a name="verify-the-registration"></a>De registratie verifiëren

Hier volgen drie manieren om de status van het apparaat te zoeken en te controleren:

### <a name="locally-on-the-device"></a>Lokaal op het apparaat

1. Open Windows PowerShell.
2. Voer `dsregcmd /status` in.
3. Controleer of zowel **AzureAdJoined** als **DomainJoined** zijn ingesteld op **JA**.
4. U kunt de **DeviceId** gebruiken en de status van de service vergelijken met behulp van de Azure-portal of PowerShell.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

1. Ga naar de apparatenpagina met behulp van een [directe link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Informatie over het vinden van een apparaat vindt u in [How to manage device identities using the Azure portal](./device-management-azure-portal.md) (Apparaat-id's beheren met de Azure-portal).
3. Als er **In behandeling** in de kolom **Geregistreerd** staat, is de hybride Azure AD-koppeling niet voltooid. In federatieve omgevingen kan dit alleen gebeuren als het registreren niet is gelukt en AAD Connect is geconfigureerd voor het synchroniseren van de apparaten.
4. Als er een **datum/tijd** in de kolom **Geregistreerd** staat, is de hybride Azure AD-koppeling wel voltooid.

### <a name="using-powershell"></a>PowerShell gebruiken

Controleer de registratiestatus van apparaten in uw Azure-tenant met behulp van **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** . Deze cmdlet bevindt zich in de [Azure Active Directory PowerShell-module](/powershell/azure/active-directory/install-msonlinev1).

Wanneer u de cmdlet **Get-MSolDevice** gebruikt om de servicedetails te controleren:

- Moet er een object bestaan met de **apparaat-id** die overeenkomt met de id op de Windows-client.
- Is de waarde voor **DeviceTrustType** **Toegevoegd aan domein**. Deze instelling is equivalent aan de status **Toegevoegd aan hybride Azure AD** op de pagina **Apparaten** in de Azure AD-portal.
- Voor apparaten die met Voorwaardelijke toegang worden gebruikt, is de waarde voor **Ingeschakeld** **Waar** en voor **DeviceTrustLevel** **Beheerd**.

1. Open Windows PowerShell als beheerder.
2. Voer `Connect-MsolService` in om verbinding te maken met uw Azure-tenant.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Tel alle hybride Azure AD-gekoppelde apparaten (met uitzondering van die met de status **In behandeling** )

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Tel alle hybride Azure AD-gekoppelde apparaten met de status **In behandeling**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Maak een lijst van alle hybride Azure AD-gekoppelde apparaten

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Maak een lijst van alle hybride Azure AD-gekoppelde apparaten met de status **In behandeling**

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>De details van één apparaat weergeven:

1. Voer `get-msoldevice -deviceId <deviceId>` in (dit is de **DeviceId** die lokaal van het apparaat wordt opgehaald).
2. Verifieer dat **Ingeschakeld** is ingesteld op **Waar**.

## <a name="troubleshoot-your-implementation"></a>Problemen met uw implementatie oplossen

Als er problemen zijn met het voltooien van de hybride Azure AD-koppeling voor domein-gekoppelde Windows-apparaten, raadpleegt u:

- [Problemen met apparaten oplossen met behulp van de dsregcmd-opdracht](./troubleshoot-device-dsregcmd.md)
- [Problemen met hybride Azure AD-koppeling oplossen voor actuele Windows-apparaten](troubleshoot-hybrid-join-windows-current.md)
- [Problemen met hybride Azure AD-koppeling oplossen voor downlevel Windows-apparaten](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [beheren van apparaatidentiteiten met behulp van de Azure-portal](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
