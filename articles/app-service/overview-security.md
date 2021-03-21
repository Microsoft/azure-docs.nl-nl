---
title: Beveiliging
description: Meer informatie over hoe App Service uw app kunt beveiligen en hoe u uw app kunt vergren delen tegen bedreigingen.
keywords: Azure app service, Web-app, mobiele app, API-app, functie-app, beveiliging, beveiligd, beveiligd, naleving, compatibel, certificaat, certificaten, HTTPS, ftps, TLS, vertrouwen, versleuteling, versleutelen, IP-beperking, verificatie, autorisatie, verificatie, authenticatie, MSI, beheerde service-identiteit, beheerde identiteit, geheimen, beveiliging, patches, patch, patches, versie
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: 61bffcfa8b98ed666e450c344023258b752e4880
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736102"
---
# <a name="security-in-azure-app-service"></a>Beveiliging in Azure App Service

Dit artikel laat u zien hoe [Azure app service](overview.md) uw web-app, back-end voor mobiele apps, API-app en [functie-app](../azure-functions/index.yml)kunt beveiligen. Ook wordt uitgelegd hoe u uw app verder kunt beveiligen met de ingebouwde functies van App Service.

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

In de volgende secties ziet u hoe u uw App Service-app verder kunt beveiligen tegen bedreigingen.

## <a name="https-and-certificates"></a>HTTPS en certificaten

Met App Service kunt u uw apps beveiligen met [https](https://wikipedia.org/wiki/HTTPS). Wanneer uw app wordt gemaakt, is de standaard domein naam ( \<app_name> . azurewebsites.net) al toegankelijk met behulp van HTTPS. Als u [een aangepast domein voor uw app configureert](app-service-web-tutorial-custom-domain.md), moet u [dit ook beveiligen met een TLS/SSL-certificaat](configure-ssl-bindings.md) , zodat client browsers beveiligde HTTPS-verbindingen met uw aangepaste domein kunnen maken. Er worden verschillende soorten certificaten ondersteund door App Service:

- Beheerd certificaat Free App Service
- App Service certificaat
- Certificaat van derden
- Het certificaat is geïmporteerd uit Azure Key Vault

Zie [een TLS/SSL-certificaat toevoegen in azure app service](configure-ssl-certificate.md)voor meer informatie.

## <a name="insecure-protocols-http-tls-10-ftp"></a>Niet-beveiligde protocollen (HTTP, TLS 1,0, FTP)

Als u uw app wilt beveiligen tegen alle niet-versleutelde (HTTP) verbindingen, biedt App Service een configuratie met één klik voor het afdwingen van HTTPS. Niet-beveiligde aanvragen worden verwijderd voordat ze uw toepassings code kunnen bereiken. Zie [https afdwingen](configure-ssl-bindings.md#enforce-https)voor meer informatie.

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1,0 wordt niet meer beschouwd als veilig door industriële normen, zoals [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Met App Service kunt u verouderde protocollen uitschakelen door [TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)af te dwingen.

App Service ondersteunt zowel FTP-als FTPS voor het implementeren van uw bestanden. FTPS moet echter wel worden gebruikt in plaats van FTP, indien dit mogelijk is. Wanneer een of beide protocollen niet in gebruik zijn, moet u [deze uitschakelen](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Beperkingen voor statische IP-adressen

Standaard accepteert uw App Service-app aanvragen van alle IP-adressen van het Internet, maar u kunt de toegang tot een kleine subset van IP-adressen beperken. Met App Service in Windows kunt u een lijst met IP-adressen definiëren die toegang hebben tot uw app. De lijst met toegestane namen kan afzonderlijke IP-adressen bevatten of een bereik van IP-adressen die zijn gedefinieerd door een subnetmasker. Zie [Azure app service static IP-beperkingen](app-service-ip-restrictions.md)voor meer informatie.

Voor App Service in Windows kunt u IP-adressen ook dynamisch beperken door de _web.config_ te configureren. Zie [dynamische IP-beveiliging \<dynamicIpSecurity>](/iis/configuration/system.webServer/security/dynamicIpSecurity/)voor meer informatie.

## <a name="client-authentication-and-authorization"></a>Client verificatie en-autorisatie

Azure App Service biedt versleutelings verificatie en autorisatie van gebruikers of client-apps. Wanneer deze functie is ingeschakeld, kunnen gebruikers en client-apps worden aangemeld met weinig of geen toepassings code. U kunt uw eigen verificatie-en autorisatie-oplossing implementeren of App Service in plaats daarvan voor u afhandelen. De module verificatie en autorisatie verwerkt webaanvragen voordat deze aan uw toepassings code worden door gegeven en weigert niet-geautoriseerde aanvragen voordat de code wordt bereikt.

App Service-verificatie en-autorisatie ondersteunen meerdere verificatie providers, waaronder Azure Active Directory, micro soft-accounts, Facebook, Google en Twitter. Raadpleeg [Verificatie en autorisatie in Azure App Service](overview-authentication-authorization.md) voor meer informatie.

## <a name="service-to-service-authentication"></a>Verificatie van service-tot-service

Bij verificatie bij een back-end-service biedt App Service twee verschillende mechanismen, afhankelijk van uw behoeften:

- **Service-identiteit** : Meld u aan bij de externe resource met behulp van de identiteit van de app zelf. Met App Service kunt u eenvoudig een [beheerde identiteit](overview-managed-identity.md)maken, die u kunt gebruiken om te verifiëren met andere services, zoals [Azure SQL database](/azure/sql-database/) of [Azure Key Vault](../key-vault/index.yml). Zie voor een end-to-end zelf studie [een beveiligde Azure SQL database verbinding van app service met behulp van een beheerde identiteit](app-service-web-tutorial-connect-msi.md).
- Namens de gebruiker **(OBO)** : Maak gedelegeerde toegang tot externe bronnen. Met Azure Active Directory als verificatie provider kan uw App Service-app gedelegeerde aanmelding uitvoeren op een externe service, zoals [Microsoft Graph API](../active-directory/develop/microsoft-graph-intro.md) of een externe API-App in app service. Zie voor een end-to-end-zelf studie de [gebruikers verifiëren en autoriseren in azure app service](tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Connectiviteit met externe bronnen

Er zijn drie typen externe resources die uw app nodig heeft om toegang te krijgen tot: 

- [Azure-resources](#azure-resources)
- [Resources in een Azure-Virtual Network](#resources-inside-an-azure-virtual-network)
- [On-premises resources](#on-premises-resources)

In elk van deze gevallen biedt App Service een manier om beveiligde verbindingen te maken, maar u moet wel rekening houden met de aanbevolen beveiligings procedures. Gebruik bijvoorbeeld altijd versleutelde verbindingen, zelfs als de back-end-bron niet-versleutelde verbindingen toestaat. Zorg er bovendien voor dat uw back-end Azure-service de minimale set IP-adressen toestaat. U kunt de uitgaande IP-adressen voor uw app vinden op [inkomende en uitgaande IP-adressen in azure app service](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Azure-resources

Wanneer uw app verbinding maakt met Azure-resources, zoals [SQL database](https://azure.microsoft.com/services/sql-database/) en [Azure Storage](../storage/index.yml), blijft de verbinding binnen Azure en heeft deze geen grenzen van het netwerk. De verbinding gaat echter via het gedeelde netwerk in azure, dus zorg er altijd voor dat uw verbinding is versleuteld. 

Als uw app wordt gehost in een [app service omgeving](environment/intro.md), moet u [verbinding maken met ondersteunde Azure-Services met behulp van Virtual Network Service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Resources in een Azure-Virtual Network

Uw app kan toegang krijgen tot bronnen in een [Azure-Virtual Network](../virtual-network/index.yml) via [Virtual Network-integratie](web-sites-integrate-with-vnet.md). De integratie wordt tot stand gebracht met een Virtual Network met behulp van een punt-naar-site-VPN. De app kan vervolgens toegang krijgen tot de resources in het Virtual Network met behulp van hun privé-IP-adressen. De punt-naar-site-verbinding gaat echter nog steeds over op de gedeelde netwerken in Azure. 

Als u de bron connectiviteit volledig wilt isoleren van de gedeelde netwerken in azure, maakt u uw app in een [app service omgeving](environment/intro.md). Omdat er altijd een App Service omgeving wordt geïmplementeerd op een toegewezen Virtual Network, is connectiviteit tussen uw app en bronnen in de Virtual Network volledig geïsoleerd. Zie [netwerk isolatie](#network-isolation)voor andere aspecten van netwerk beveiliging in een app service omgeving.

### <a name="on-premises-resources"></a>On-premises resources

U kunt op drie manieren veilig toegang krijgen tot on-premises resources, zoals data bases: 

- [Hybride verbindingen](app-service-hybrid-connections.md) : Hiermee wordt een Point-to-point-verbinding met uw externe bron tot stand gebracht via een TCP-tunnel. De TCP-tunnel wordt tot stand gebracht met behulp van TLS 1,2 met SAS-sleutels (Shared Access Signature).
- [Virtual Network integratie](web-sites-integrate-with-vnet.md) met site-naar-site-VPN-zoals beschreven in [resources binnen een Azure-Virtual Network](#resources-inside-an-azure-virtual-network), maar de Virtual Network kan worden verbonden met uw on-premises netwerk via een [site-naar-site-VPN](../vpn-gateway/tutorial-site-to-site-portal.md). In deze netwerk topologie kan uw app verbinding maken met on-premises resources zoals andere resources in de Virtual Network.
- [App service omgeving](environment/intro.md) met site-naar-site-VPN, zoals beschreven in [resources binnen een Azure-Virtual Network](#resources-inside-an-azure-virtual-network), maar de Virtual Network kan worden verbonden met uw on-premises netwerk via een [site-naar-site-VPN](../vpn-gateway/tutorial-site-to-site-portal.md). In deze netwerk topologie kan uw app verbinding maken met on-premises resources zoals andere resources in de Virtual Network.

## <a name="application-secrets"></a>Toepassings geheimen

Sla geen toepassings geheimen op, zoals database referenties, API-tokens en persoonlijke sleutels in uw code of configuratie bestanden. De algemeen geaccepteerde aanpak is om ze als [omgevings variabelen](https://wikipedia.org/wiki/Environment_variable) te benaderen met het standaard patroon in uw taal. In App Service kunt u omgevings variabelen definiëren met behulp van [app-instellingen](configure-common.md#configure-app-settings) (en, met name voor .NET-toepassingen, [verbindings reeksen](configure-common.md#configure-connection-strings)). App-instellingen en verbindings reeksen worden versleuteld opgeslagen in Azure en ze worden alleen ontsleuteld voordat ze worden ingevoegd in het proces geheugen van uw app wanneer de app wordt gestart. De versleutelings sleutels worden regel matig gedraaid.

U kunt ook uw App Service-app integreren met [Azure Key Vault](../key-vault/index.yml) voor het beheer van geavanceerde geheimen. Door [de Key Vault met een beheerde identiteit te openen](../key-vault/general/tutorial-net-create-vault-azure-web-app.md), heeft uw app service-app veilig toegang tot de geheimen die u nodig hebt.

## <a name="network-isolation"></a>Netwerkisolatie

Met uitzonde ring van de **geïsoleerde** prijs categorie worden met alle lagen uw apps uitgevoerd op de gedeelde netwerk infrastructuur in app service. De open bare IP-adressen en de front-end load balancers worden bijvoorbeeld gedeeld met andere tenants. De **geïsoleerde** laag biedt u volledige netwerk isolatie door uw apps in een speciale [app service omgeving](environment/intro.md)uit te voeren. Een App Service omgeving wordt uitgevoerd in uw eigen exemplaar van [Azure Virtual Network](../virtual-network/index.yml). U kunt het volgende doen: 

- U kunt uw apps gebruiken via een speciaal openbaar eind punt, met speciale front-ends.
- Een interne toepassing uitvoeren met behulp van een interne load balancer (ILB), waarmee alleen toegang wordt toegestaan vanuit uw Azure Virtual Network. De ILB heeft een IP-adres van uw particuliere subnet. Dit biedt een Total isolatie van uw apps via internet.
- [Gebruik een ILB achter een Web Application firewall (WAF)](environment/integrate-with-application-gateway.md). De WAF biedt beveiliging op bedrijfs niveau voor uw open bare toepassingen, zoals DDoS-beveiliging, URI-filtering en preventie van SQL-injecties.

Zie [Introduction to Azure app service environments](environment/intro.md)(Engelstalig) voor meer informatie.