---
title: Synchronisatie van wachtwoordhashes inschakelen voor Azure AD Domain Services | Microsoft Docs
description: In deze zelfstudie leert u hoe u met behulp van Azure AD Connect synchronisatie van wachtwoordhashes inschakelt met een door Azure Active Directory Domain Services beheerd domein.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: e83d8941d1be7fd36f53a881a21716252ad01954
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618345"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Zelfstudie: Wachtwoordsynchronisatie met Azure Active Directory Domain Services inschakelen voor hybride omgevingen

Voor hybride omgevingen kan een Azure Active Directory-tenant (Azure AD) worden geconfigureerd om een on-premises Active Directory Domain Services-omgeving (AD DS) te synchroniseren met Azure AD Connect. Standaard worden verouderde NTLM- (NT LAN Manager) en Kerberos-wachtwoordhashes in Azure AD Connect, die nodig zijn voor Azure Active Directory Domain Services (Azure AD DS), niet gesynchroniseerd.

Als u Azure AD DS wilt gebruiken met accounts die zijn gesynchroniseerd vanuit een on-premises AD DS-omgeving, moet u Azure AD Connect configureren om de vereiste wachtwoordhashes voor NTLM- en Kerberos-verificatie te synchroniseren. Nadat Azure AD Connect is geconfigureerd, worden ook verouderde wachtwoordhashes met Azure AD gesynchroniseerd wanneer een on-premises account wordt gemaakt of een wachtwoord wordt gewijzigd.

U hoeft deze stappen niet uit te voeren als u cloudaccounts zonder een on-premises AD DS-omgeving gebruikt of als u een *resourceforest* gebruikt. Voor beheerde domeinen die gebruikmaken van een resourceforest, worden hashes van on-premises wachtwoorden nooit gesynchroniseerd. Verificatie voor on-premises accounts gebruik de forest-vertrouwensrelatie(s) op uw eigen AD DS-domeincontrollers.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * Waarom verouderde NTLM- en Kerberos-wachtwoordhashes nodig zijn
> * De synchronisatie van verouderde wachtwoordhashes configureren voor Azure AD Connect

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u de volgende resources nodig:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement dat met behulp van Azure AD Connect met een on-premises directory is gesynchroniseerd.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
    * [Schakel indien nodig Azure AD Connect in voor de synchronisatie van wachtwoordhashes][enable-azure-ad-connect].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * [Maak en configureer zo nodig een door Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Synchronisatie van wachtwoordhashes met Azure AD Connect

Azure AD Connect wordt gebruikt om objecten, zoals gebruikersaccounts en groepen uit een on-premises AD DS-omgeving, te synchroniseren in een Azure AD-tenant. Als onderdeel van het proces wordt met synchronisatie van wachtwoordhashes ingeschakeld dat accounts hetzelfde wachtwoord in de on-premises AD DS-omgeving en Azure AD gebruiken.

Voor de verificatie van gebruikers in het beheerde domein heeft Azure AD DS wachtwoordhashes nodig in een indeling die geschikt is voor NTLM- en Kerberos-verificatie. Totdat u Azure AD DS voor uw tenant inschakelt, bewaart Azure AD geen wachtwoordhashes in de vereiste indeling voor NTLM- of Kerberos-verificatie. Om veiligheidsredenen slaat Azure AD ook geen wachtwoorden op in niet-gecodeerde vorm. Azure AD kan deze wachtwoordhashes voor NTLM of Kerberos niet automatisch genereren op basis van bestaande referenties van gebruikers.

Azure AD Connect kan worden geconfigureerd om de vereiste NTLM- of Kerberos-wachtwoordhashes te synchroniseren voor Azure AD DS. Zorg ervoor dat u de stappen hebt voltooid om [Azure AD Connect in te schakelen voor synchronisatie van wachtwoordhashes][enable-azure-ad-connect]. Als u een bestaande instantie van Azure AD Connect had, moet u [de nieuwste versie downloaden en bijwerken][azure-ad-connect-download] om ervoor te zorgen dat u de verouderde wachtwoordhashes voor NTLM en Kerberos kunt synchroniseren. Deze functionaliteit is niet beschikbaar in eerdere versies van Azure AD Connect of in het oudere DirSync-hulpprogramma. Azure AD Connect-versie *1.1.614.0* of later is vereist.

> [!IMPORTANT]
> Azure AD Connect moet alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS-omgevingen. Het installeren van Azure AD Connect in een beheerd Azure AD DS-domein om objecten weer naar Azure AD te synchroniseren, wordt niet ondersteund.

## <a name="enable-synchronization-of-password-hashes"></a>Synchronisatie van wachtwoordhashes inschakelen

Zodra Azure AD Connect is geïnstalleerd en geconfigureerd om te synchroniseren met Azure AD, kunt u de synchronisatie van verouderde wachtwoordhashes voor NTLM en Kerberos configureren. Er wordt een PowerShell-script gebruikt om de vereiste instellingen te configureren en vervolgens wordt een volledige wachtwoordsynchronisatie met Azure AD gestart. Wanneer dat synchronisatieproces van wachtwoordhashes in Azure AD Connect is voltooid, kunnen gebruikers zich via Azure AD DS aanmelden bij toepassingen waarvoor verouderde NTLM- of Kerberos-wachtwoordhashes worden gebruikt.

1. Ga op de computer waarop Azure AD Connect is geïnstalleerd naar het startmenu en open **Azure AD Connect > Synchronisatieservice**.
1. Selecteer het tabblad **Connectors**. De verbindingsgegevens die worden gebruikt om de synchronisatie tussen de on-premises AD DS-omgeving en Azure AD tot stand te brengen, worden vermeld.

    Het **Type** is ofwel *Windows Azure Active Directory (Microsoft)* voor de Azure AD-connector of *Active Directory Domain Services* voor de on-premises AD DS-connector. Noteer de connectornamen die in het PowerShell-script moeten worden gebruikt in de volgende stap.

    ![De connectornamen in Sync Service Manager noteren](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    In deze voorbeeldschermopname worden de volgende connectors gebruikt:

    * De Azure AD-connector heeft de naam *contoso.onmicrosoft.com: AAD*
    * De on-premises AD DS-connector heeft de naam *onprem.contoso.com*

1. Kopieer en plak het volgende PowerShell-script op de computer waarop Azure AD Connect is geïnstalleerd. Door het script wordt een volledige wachtwoordsynchronisatie geactiveerd, inclusief verouderde wachtwoordhashes. Werk de variabelen `$azureadConnector` en `$adConnector` bij met de connectornamen uit de vorige stap.

    Voer dit script uit in elke AD-forest om NTLM- en Kerberos-wachtwoordhashes van on-premises accounts te synchroniseren met Azure AD.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    Afhankelijk van de grootte van uw directory, wat het aantal accounts en groepen betreft, kan de synchronisatie van de verouderde wachtwoordhashes met Azure AD enige tijd duren. De wachtwoorden worden vervolgens gesynchroniseerd met het beheerde domein nadat ze zijn gesynchroniseerd met Azure AD.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Waarom verouderde NTLM- en Kerberos-wachtwoordhashes nodig zijn
> * De synchronisatie van verouderde wachtwoordhashes configureren voor Azure AD Connect

> [!div class="nextstepaction"]
> [Informatie over synchronisatie in een door Azure AD Domain Services beheerd domein](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
