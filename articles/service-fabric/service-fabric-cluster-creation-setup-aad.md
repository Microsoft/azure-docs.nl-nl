---
title: Azure Active Directory instellen voor client verificatie
description: Meer informatie over het instellen van Azure Active Directory (Azure AD) voor het verifiëren van clients voor Service Fabric clusters.
ms.topic: conceptual
ms.date: 6/28/2019
ms.openlocfilehash: a1f89e144f9cef12c5bff87befb00a88bad8d7d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215966"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Azure Active Directory instellen voor client verificatie

Voor clusters die worden uitgevoerd op Azure, wordt Azure Active Directory (Azure AD) aanbevolen voor het beveiligen van de toegang tot beheer eindpunten. In dit artikel wordt beschreven hoe u Azure AD zo kunt instellen dat clients voor een Service Fabric cluster worden geverifieerd.

In dit artikel wordt de term ' Application ' gebruikt om te verwijzen naar [Azure Active Directory toepassingen](../active-directory/develop/developer-glossary.md#client-application), niet service Fabric toepassingen. zo nodig wordt het onderscheid gemaakt. Azure Active Directory maakt het beheren van toegang tot toepassingen door organisaties (bekend als tenants) mogelijk.

Een Service Fabric-cluster biedt verschillende toegangspunten bij de management-functionaliteit, met inbegrip van de webconsoles [Service Fabric Explorer][service-fabric-visualizing-your-cluster] en [Visual Studio][service-fabric-manage-application-in-visual-studio]. Als gevolg hiervan maakt u twee Azure AD-toepassingen om de toegang tot het cluster te beheren: één webtoepassing en één systeem eigen toepassing. Nadat de toepassingen zijn gemaakt, kunt u gebruikers toewijzen aan alleen-lezen en beheerders rollen.

> [!NOTE]
> Op Linux moet u de volgende stappen uitvoeren voordat u het cluster maakt. In Windows hebt u ook de mogelijkheid om [Azure AD-verificatie te configureren voor een bestaand cluster](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

> [!NOTE]
> Het is een [bekend probleem](https://github.com/microsoft/service-fabric/issues/399) dat toepassingen en knoop punten op Linux Aad-clusters niet kunnen worden weer gegeven in azure Portal.



## <a name="prerequisites"></a>Vereisten
In dit artikel nemen we aan dat u al een tenant hebt gemaakt. Als u dit nog niet hebt gedaan, begint u door [Een Azure Active Directory-tenant verkrijgen][active-directory-howto-tenant] te lezen.

Ter vereenvoudiging van enkele van de stappen voor het configureren van Azure Active Directory met een Service Fabric-cluster, hebben we een set Windows PowerShell-scripts gemaakt.

1. [Kloon de opslag plaats](https://github.com/Azure-Samples/service-fabric-aad-helpers) naar uw computer.
2. [Zorg ervoor dat u beschikt over alle vereisten](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) voor de geïnstalleerde scripts.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure Active Directory-toepassingen maken en gebruikers toewijzen aan rollen

We gebruiken de scripts om twee Azure AD-toepassingen te maken om de toegang tot het cluster te beheren: één webtoepassing en één systeem eigen toepassing. Nadat u toepassingen hebt gemaakt om uw cluster aan te duiden, maakt u gebruikers voor de rollen die worden [ondersteund door service Fabric](service-fabric-cluster-security-roles.md): alleen-lezen en beheerder.

Voer `SetupApplications.ps1` uit, en geef de tenant-ID, de naam van het cluster en de antwoord-URL van de webtoepassing op als parameters.  Geef ook gebruikersnamen en wachtwoorden voor de gebruikers op. Bijvoorbeeld:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Voor nationale clouds (bijvoorbeeld Azure Government, Azure China, Azure Duitsland), moet u ook de parameter `-Location` opgeven.

U kunt uw *TenantId* vinden door de Power shell-opdracht uit te voeren `Get-AzureSubscription` . Als u deze opdracht uitvoert, wordt de TenantId voor elk abonnement weer gegeven.

De *Clusternaam* wordt gebruikt voor als prefix aan de Azure Active Directory-toepassingen die zijn gemaakt door het script. Deze hoeft niet precies overeen te komen met de naam van het daadwerkelijke cluster. De naam is uitsluitend bedoeld om het eenvoudiger te maken dat Azure Active Directory-artefacten worden toegewezen aan het Service Fabric-cluster waarmee ze worden gebruikt.

*WebApplicationReplyUrl* wordt het standaardeindpunt dat Azure Active Directory retourneert naar uw gebruikers nadat ze klaar zijn aanmelden. Stel dit eind punt in als het Service Fabric Explorer-eind punt voor uw cluster. Als u Azure AD-toepassingen maakt om een bestaand cluster weer te geven, moet u ervoor zorgen dat deze URL overeenkomt met het eind punt van uw bestaande cluster. Als u toepassingen voor een nieuw cluster maakt, moet u het eind punt plannen dat uw cluster zal hebben en ervoor zorgen dat het eind punt van een bestaand cluster niet wordt gebruikt. Het Service Fabric Explorer-eind punt is standaard:

https://&lt;cluster_domain&gt;:19080/Explorer

U wordt gevraagd of u zich aanmeldt bij een account dat beheerdersrechten voor de Azure Active Directory-tenant heeft. Nadat u zich hebt aangemeld, maakt het script de web- en systeemeigen toepassingen voor uw Service Fabric-cluster. Als u kijkt naar de toepassingen van de tenant in de [Azure-portal][azure-portal], ziet u twee nieuwe vermeldingen:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

Het script drukt de JSON af die vereist is voor de Azure Resource Manager sjabloon bij [het maken van uw Aad-cluster](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access). Daarom is het een goed idee om het Power shell-venster geopend te laten.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Help bij het oplossen van problemen met het instellen van Azure Active Directory
Het instellen van Azure AD en het gebruik ervan kan lastig zijn, dus hier volgen enkele aanwijzers op wat u kunt doen om het probleem op te lossen.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer wordt u gevraagd een certificaat te selecteren
#### <a name="problem"></a>Probleem
Nadat u zich hebt aangemeld bij Azure AD in Service Fabric Explorer, keert de browser terug naar de start pagina. er wordt een bericht weer gegeven waarin u wordt gevraagd een certificaat te selecteren.

![Dialoog venster SFX-certificaat][sfx-select-certificate-dialog]

#### <a name="reason"></a>Reden
De gebruiker heeft geen rol toegewezen in de Azure AD-cluster toepassing. Azure AD-verificatie mislukt dus op Service Fabric cluster. Service Fabric Explorer terugvallen op de verificatie van het certificaat.

#### <a name="solution"></a>Oplossing
Volg de instructies voor het instellen van Azure AD en het toewijzen van gebruikers rollen. We raden u ook aan om ' gebruikers toewijzing vereist voor toegang tot de app ' in te scha kelen `SetupApplications.ps1` .

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>De verbinding met Power shell is mislukt met de volgende fout: de opgegeven referenties zijn ongeldig
#### <a name="problem"></a>Probleem
Wanneer u Power shell gebruikt om verbinding te maken met het cluster met behulp van de beveiligings modus AzureActiveDirectory, mislukt de verbinding met een fout: de opgegeven referenties zijn ongeldig.

#### <a name="solution"></a>Oplossing
Deze oplossing is hetzelfde als die van de voor gaande.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer retourneert een fout wanneer u zich aanmeldt: ' AADSTS50011 '
#### <a name="problem"></a>Probleem
Wanneer u zich probeert aan te melden bij Azure AD in Service Fabric Explorer, retourneert de pagina een fout: "AADSTS50011: de antwoord adres- &lt; URL &gt; komt niet overeen met de antwoord adressen die zijn geconfigureerd voor de toepassing: &lt; GUID &gt; ."

![Het SFX-antwoord adres komt niet overeen][sfx-reply-address-not-match]

#### <a name="reason"></a>Reden
De cluster toepassing (web) die staat voor Service Fabric Explorer pogingen om te verifiëren bij Azure AD en als onderdeel van de aanvraag, levert de omleidings-URL. De URL wordt echter niet weer gegeven in de lijst met **antwoord-url's** voor de Azure AD-toepassing.

#### <a name="solution"></a>Oplossing
Selecteer op de pagina Azure AD-App-registratie voor uw cluster **verificatie** en voeg in het gedeelte **omleidings-URI'S** de service Fabric Explorer URL toe aan de lijst. Sla uw wijziging op.

![Antwoord-URL van webtoepassing][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>Als u verbinding maakt met het cluster via Azure AD-verificatie via Power shell, wordt er een fout bericht weer geven als u zich aanmeldt: "AADSTS50011"
#### <a name="problem"></a>Probleem
Wanneer u probeert verbinding te maken met een Service Fabric cluster met behulp van Azure AD via Power shell, retourneert de aanmeldings pagina een fout: "AADSTS50011: de antwoord-URL die in de aanvraag is opgegeven, komt niet overeen met de antwoord-url's die zijn geconfigureerd voor de toepassing: &lt; GUID &gt; ."

#### <a name="reason"></a>Reden
Net als bij het voor gaande probleem probeert Power shell verificatie uit te voeren bij Azure AD, dat een omleidings-URL biedt die niet wordt vermeld in de lijst met **antwoord-url's** van de Azure AD-toepassing.  

#### <a name="solution"></a>Oplossing
Gebruik hetzelfde proces als in het voor gaande probleem, maar de URL moet worden ingesteld op `urn:ietf:wg:oauth:2.0:oob` , een speciale omleiding voor verificatie via de opdracht regel.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Verbind het cluster met behulp van Azure AD-verificatie via Power shell
Gebruik de volgende Power shell-opdracht voor beeld om verbinding te maken met het Service Fabric cluster:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Zie [Connect-ServiceFabricCluster cmdlet](/powershell/module/servicefabric/connect-servicefabriccluster)(Engelstalig) voor meer informatie.

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Kan ik dezelfde Azure AD-Tenant in meerdere clusters gebruiken?
Ja. Maar vergeet niet om de URL van Service Fabric Explorer toe te voegen aan uw cluster toepassing (web). Anders werkt Service Fabric Explorer niet.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Waarom heb ik nog steeds een server certificaat nodig terwijl Azure AD is ingeschakeld?
FabricClient en FabricGateway voeren een wederzijdse verificatie uit. Tijdens Azure AD-verificatie biedt Azure AD-integratie een client identiteit voor de server en wordt het server certificaat gebruikt door de client om de identiteit van de server te verifiëren. Zie [X. 509-certificaten en service Fabric][x509-certificates-and-service-fabric]voor meer informatie over service Fabric certificaten.

## <a name="next-steps"></a>Volgende stappen
[Configureer en implementeer een cluster](service-fabric-cluster-creation-via-arm.md)nadat u Azure Active Directory toepassingen hebt ingesteld en rollen hebt ingesteld voor gebruikers.


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
