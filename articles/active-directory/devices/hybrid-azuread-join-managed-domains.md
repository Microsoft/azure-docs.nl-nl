---
title: Hybride Azure Active Directory-deelname configureren voor beheerde domeinen | Microsoft Docs
description: Informatie over het configureren van hybride Azure Active Directory-deelname voor beheerde domeinen.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 303f02e0c6b72b7061a996b3ce8e70799954b435
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861048"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Zelfstudie: Hybride Azure Active Directory-deelname configureren voor beheerde domeinen

In deze zelfstudie leert u hoe u hybride Azure Active Directory-koppeling kunt configureren voor Active Directory-apparaten die bij een domein horen. Deze methode ondersteunt een beheerde omgeving met zowel on-premises Active Directory als Azure AD.

Net als een gebruiker in uw organisatie is een apparaat een kernidentiteit die u wilt beveiligen. U kunt de apparaat-id gebruiken om uw resources altijd en overal te beschermen. U kunt dit doel bereiken door apparaat-id's te beheren in Azure AD. Hanteer één van de volgende methoden:

- Azure AD-koppeling
- Hybride Azure AD-koppeling
- Azure AD-registratie

Dit artikel is gericht op hybride Azure AD-koppeling.

Door uw apparaten in Azure AD te zetten, optimaliseert u de productiviteit van uw gebruikers via eenmalige aanmelding (SSO) bij al uw on-premises bronnen en cloudbronnen. U kunt de toegang tot uw on-premises bronnen en cloudbronnen beveiligen met [voorwaardelijke toegang](../conditional-access/howto-conditional-access-policy-compliant-device.md).

U kunt een beheerde omgeving implementeren met behulp van [Password Hash Sync (PHS)](../hybrid/whatis-phs.md) of [Pass-Through Authentication (PTA)](../hybrid/how-to-connect-pta.md) met [naadloze eenmalige aanmelding](../hybrid/how-to-connect-sso.md). Voor deze scenario's is het niet nodig om een federatieve server te configureren voor authenticatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Hybride Azure AD-koppeling configureren
> * Downlevel Windows-apparaten inschakelen
> * Gekoppelde apparaten verifiëren
> * Problemen oplossen

## <a name="prerequisites"></a>Vereisten

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 of hoger)
- De referenties van een globale beheerder voor uw Azure AD-tenant
- De referenties van een ondernemingsbeheerder voor elk van de forests

Raak ermee vertrouwd met behulp van deze artikelen:

- [Wat is een apparaat-id?](overview.md)
- [Procedure: de implementatie van uw hybride Azure Active Directory-koppeling plannen](hybrid-azuread-join-plan.md)
- [Gecontroleerde validatie van hybride Azure AD-koppeling](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD biedt geen ondersteuning voor smartcards of certificaten in beheerde domeinen.

Verifieer dat Azure AD Connect de computerobjecten heeft gesynchroniseerd van de apparaten die u hybride Azure AD-gekoppeld wilt maken. Als de computerobjecten bij specifieke organisatie-eenheden horen, moeten deze OE's worden geconfigureerd voor synchronisatie in Azure AD Connect. Zie [Organizational unit-based filtering](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) (Op organisatie-eenheden gebaseerde filters) voor meer informatie over het synchroniseren van computerobjecten met behulp van Azure AD Connect.

Vanaf versie 1.1.819.0 bevat Azure AD Connect een wizard om een hybride Azure AD-koppeling te configureren. De wizard maakt het configuratieproces aanzienlijk eenvoudiger. De wizard configureert de serviceverbindingspunten (SCP's) voor apparaatregistratie

De configuratiestappen in dit artikel zijn gebaseerd op gebruik van deze wizard in Azure AD Connect.

Voor hybride Azure AD-koppeling moeten apparaten toegang tot de volgende Microsoft-bronnen hebben vanuit het netwerk van uw organisatie:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Als u naadloze eenmalige aanmelding gebruikt of wilt gebruiken)

> [!WARNING]
> Als uw organisatie proxyservers gebruikt die SSL-verkeer onderscheppen voor scenario's zoals preventie van gegevensverlies of beperkingen voor Azure AD-tenants, moet u ervoor zorgen dat verkeer naar 'https://device.login.microsoftonline.com ' wordt uitgesloten van TLS break-and-inspect. Als u 'https://device.login.microsoftonline.com ' niet uitsluit, kan dit problemen geven met de verificatie van clientcertificaten, die op hun beurt weer problemen veroorzaken met apparaatregistratie en voorwaardelijke toegang op basis van apparaten.

Als uw organisatie toegang tot internet via een uitgaande proxy vereist, kunt u [WPAD (Web Proxy Auto-Discovery) implementeren](/previous-versions/tn-archive/cc995261(v=technet.10)) gebruiken om Windows 10-computers in staat te stellen apparaten te registreren met Azure AD. Zie [Troubleshooting Automatic Detection](/previous-versions/tn-archive/cc302643(v=technet.10)) (Problemen met automatische detectie oplossen) als u problemen ondervindt met het configureren en beheren van WPAD. In Windows 10-apparaten vóór de 1709-update is WPAD de enige beschikbare optie voor het configureren van een proxy voor gebruik met hybride Azure AD-deelname. 

Als u WPAD niet gebruikt, kunt u WinHTTP-proxy-instellingen op uw computer configureren vanaf Windows 10 1709. Zie [WinHTTP Proxy Settings deployed by GPO](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo) (WinHTTP-proxyinstellingen geïmplementeerd door GPO) voor meer informatie.

> [!NOTE]
> Als u proxy-instellingen op uw computer configureert met behulp van WinHTTP-instellingen, kunnen computers die geen verbinding hebben met de geconfigureerde proxy, geen verbinding maken met internet.

Als uw organisatie internettoegang via een geverifieerde uitgaande proxy vereist, moet u ervoor zorgen dat uw Windows 10-computers kunnen verifiëren naar de uitgaande proxy. Omdat Windows 10-computers apparaatregistratie uitvoeren via machinecontext, moet u verificatie naar een uitgaande proxy configureren via machinecontext. Vraag uw provider van de uitgaande proxy naar de configuratievereisten.

Controleer of het apparaat toegang heeft tot de bovenstaande Microsoft-resources onder het systeemaccount door het script [Test Device Registration Connectivity](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) te gebruiken.

## <a name="configure-hybrid-azure-ad-join"></a>Hybride Azure AD-koppeling configureren

Ga als volgt te werk om een hybride Azure AD-koppeling te configureren via Azure AD Connect:

1. Start Azure AD Connect en selecteer **Configureren**.

   ![Welkom](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. Selecteer bij **Extra taken** de optie **Apparaatopties configureren** en selecteer daarna **Volgende**.

   ![Extra taken](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. Selecteer **Volgende** in **Overzicht**.

   ![Overzicht](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. Voer bij **Verbinding maken met Azure AD** de referenties in van een globale beheerder voor uw Azure AD-tenant.  

   ![Verbinding maken met Azure AD](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. Selecteer bij **Apparaatopties** de optie **Hybride Azure AD-koppeling configureren** en selecteer daarna **Volgende**.

   ![Apparaatopties](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. Voer bij **SCP-configuratie** de volgende stappen uit voor elke forest waarvoor u wilt dat Azure AD Connect de SCP configureert, en klik vervolgens op **Volgende**.

   1. Selecteer de **forest**.
   1. Selecteer een **verificatieservice**.
   1. Selecteer **Toevoegen** om de referenties van een ondernemingsbeheerder in te voeren.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. Selecteer bij **Apparaatbesturingssystemen** de besturingssystemen die worden gebruikt door apparaten in uw Active Directory-omgeving en selecteer **Volgende**.

   ![Apparaatbesturingssysteem](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. Selecteer bij **Klaar om te configureren** de optie **Configureren**.

   ![Klaar om te configureren](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. Selecteer bij **Configuratie voltooid** de optie **Afsluiten**.

   ![Configuratie voltooid](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>Downlevel Windows-apparaten inschakelen

Als sommige van uw domein-gekoppelde apparaten downlevel Windows-apparaten zijn, moet u het volgende doen:

- De lokale intranetinstellingen voor apparaatregistratie configureren
- Naadloze SSO configureren
- Microsoft Workplace Join installeren voor downlevel Windows-computers

> [!NOTE]
> Windows 7-ondersteuning is beëindigd op 14 januari 2020. Zie [Windows 7 support ended](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020) ( Windows 7-ondersteuning is beëindigd) voor meer informatie.

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>De lokale intranetinstellingen voor apparaatregistratie configureren

Om de hybride Azure AD-koppeling van uw downlevel Windows-apparaten te voltooien en om certificaatprompts te vermijden wanneer apparaten worden geverifieerd bij Azure AD, kunt u een beleid naar uw domein-gekoppelde apparaten pushen om de volgende URL's toe te voegen aan de zone Lokaal intranet in Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

U moet ook **Statusbalkupdates via scripts toestaan** inschakelen in de zone Lokaal intranet van de gebruiker.

### <a name="configure-seamless-sso"></a>Naadloze SSO configureren

Voor het uitvoeren van hybride Azure AD-koppeling van uw downlevel Windows-apparaten in een beheerd domein dat [wachtwoordhashsynchronisatie](../hybrid/whatis-phs.md) of [Pass through-verificatie](../hybrid/how-to-connect-pta.md) als uw Azure AD-cloudverificatiemethode gebruikt, moet u ook [naadloze eenmalige aanmelding configureren](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Microsoft Workplace Join installeren voor downlevel Windows-computers

Voor het registreren van downlevel Windows-apparaten moeten organisaties [Microsoft Workplace Join voor niet-Windows 10-computers](https://www.microsoft.com/download/details.aspx?id=53554) installeren. Microsoft Workplace Join voor niet-Windows 10-computers is beschikbaar in het Microsoft Downloadcentrum.

U kunt het pakket implementeren met behulp van een softwaredistributiesysteem, zoals  [Microsoft Endpoint Configuration Manager](/configmgr/). Het pakket ondersteunt de standaardopties voor installatie op de achtergrond met de parameter `quiet`. De huidige versie van Configuration Manager biedt voordelen ten opzichte van eerdere versies, zoals de mogelijkheid om voltooide registraties bij te houden.

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
3. Als er **In behandeling** in de kolom **Geregistreerd** staat, is de hybride Azure AD-koppeling niet voltooid.
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
- [Problemen met hybride Azure Active Directory-gekoppelde apparaten oplossen](troubleshoot-hybrid-join-windows-current.md)
- [Problemen oplossen met hybride Azure Active Directory-gekoppelde, downlevel apparaten](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het beheren van apparaatidentiteiten met behulp van de Azure-portal.
> [!div class="nextstepaction"]
> [Apparaatidentiteiten beheren](device-management-azure-portal.md)
