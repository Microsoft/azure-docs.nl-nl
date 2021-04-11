---
title: Overzicht van Azure Key Vault-beveiliging
description: Een overzicht van de beveiligings functies en aanbevolen procedures voor het Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: mbaldwin
ms.openlocfilehash: fc054d1294b55ddd3937ebc7b91643aa349cd8ea
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122183"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault-beveiliging

U gebruikt Azure Key Vault om versleutelings sleutels en geheimen te beveiligen, zoals certificaten, verbindings reeksen en wacht woorden in de Cloud. Bij het opslaan van gevoelige en bedrijfs kritieke gegevens moet u stappen ondernemen om de beveiliging van uw kluizen en de gegevens die erin zijn opgeslagen te maximaliseren.

Dit artikel bevat een overzicht van de beveiligings functies en aanbevolen procedures voor het Azure Key Vault. 

> [!NOTE]
> Voor een uitgebreide lijst met Azure Key Vault Security-aanbevelingen raadpleegt u de [beveiligings basislijn voor Azure Key Vault](security-baseline.md).

## <a name="network-security"></a>Netwerkbeveiliging

U kunt de bloot stelling van uw kluizen verminderen door op te geven welke IP-adressen er toegang tot hebben. Met de service-eind punten voor virtuele netwerken voor Azure Key Vault kunt u de toegang tot een opgegeven virtueel netwerk beperken. Met de eind punten kunt u ook de toegang beperken tot een lijst met IPv4-adresbereiken (Internet Protocol versie 4). Gebruikers die verbinding maken met uw sleutel kluis van buiten deze bronnen, krijgen geen toegang.  Zie [service-eind punten voor virtuele netwerken voor Azure Key Vault](overview-vnet-service-endpoints.md) voor meer informatie

Nadat de firewall regels van kracht zijn, kunnen gebruikers alleen gegevens van Key Vault lezen wanneer hun aanvragen afkomstig zijn van toegestane virtuele netwerken of IPv4-adresbereiken. Dit is tevens van toepassing voor toegang tot Key Vault vanuit Azure Portal. Hoewel gebruikers kunnen bladeren naar een sleutelkluis van Azure Portal, kunnen ze mogelijk geen sleutels, geheimen of certificaten weergeven als hun clientcomputer niet in de lijst met toegestane clients staat. Dit is ook van invloed op de Key Vault-kiezer door andere Azure-Services. Gebruikers zien mogelijk een lijst met sleutelkluizen, maar geen lijst met sleutels als firewallregels hun clientcomputer weigeren.  Zie [Azure Key Vault firewalls en virtuele netwerken configureren](network-security.md) voor de implementaties tappen

Met Azure Private Link hebt u via een privé-eindpunt in uw virtuele netwerk toegang tot Azure Key Vault en in Azure gehoste services van klanten of partners. Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service feitelijk in uw VNet wordt geplaatst. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulariteit krijgt in toegangsbeheer.  Voor implementaties tappen, Zie [Key Vault integreren met persoonlijke Azure-koppeling](private-link-service.md)

## <a name="tls-and-https"></a>TLS en HTTPS

- De front-end van Key Vault (Data-vlak) is een multi tenant-server. Dit betekent dat sleutel kluizen van verschillende klanten hetzelfde open bare IP-adres kunnen delen. Om isolatie te kunnen garanderen, wordt elke HTTP-aanvraag geverifieerd en onafhankelijk van andere aanvragen geautoriseerd.
- U kunt oudere versies van TLS identificeren om beveiligings problemen te melden, maar omdat het open bare IP-adres wordt gedeeld, is het niet mogelijk om oude versies van TLS uit te scha kelen voor afzonderlijke sleutel kluizen op het niveau van het Trans Port.
- Met het HTTPS-protocol kan de client deel nemen aan TLS-onderhandeling. **Clients kunnen de meest recente versie van TLS afdwingen** en wanneer een client dit doet, wordt de bijbehorende beveiliging op het niveau van de volledige verbinding gebruikt. Het feit dat Key Vault nog steeds oudere versies van TLS ondersteunt, heeft geen invloed op de beveiliging van verbindingen met nieuwere TLS-versies.
- Ondanks bekende beveiligings problemen in het TLS-protocol is er geen bekende aanval waarmee een kwaadwillende agent informatie kan ophalen uit uw sleutel kluis wanneer de aanvaller een verbinding initieert met een TLS-versie met beveiligings problemen. De aanvaller moet zich nog steeds zelf verifiëren en autoriseren, en zolang legitieme clients altijd verbinding maken met recente TLS-versies, is er geen manier waarop referenties kunnen worden gelekt van beveiligings problemen in oude TLS-versies.

## <a name="identity-management"></a>Identiteitsbeheer

Wanneer u een sleutel kluis maakt in een Azure-abonnement, wordt deze automatisch gekoppeld aan de Azure AD-Tenant van het abonnement. Iedereen die inhoud probeert te beheren of ophalen uit een kluis, moet worden geverifieerd door Azure AD. In beide gevallen kunnen toepassingen op drie manieren toegang krijgen tot Key Vault:

- **Alleen toepassing**: de toepassing vertegenwoordigt een service-principal of beheerde identiteit. Deze identiteit is het meest voorkomende scenario voor toepassingen die regel matig toegang moeten hebben tot certificaten, sleutels of geheimen van de sleutel kluis. Voor een goede werking van dit scenario `objectId` moet de toepassing worden opgegeven in het toegangs beleid en `applicationId` mag _niet_ worden opgegeven of moet zijn `null` .
- **Alleen gebruiker**: de gebruiker heeft toegang tot de sleutel kluis vanuit elke toepassing die is geregistreerd in de Tenant. Voor beelden van dit type toegang zijn Azure PowerShell en de Azure Portal. Dit scenario werkt alleen als de `objectId` gebruiker is opgegeven in het toegangs beleid en de `applicationId` mag _niet_ zijn opgegeven of moet zijn `null` .
- **Toepassings-plus-gebruiker** (soms aangeduid als _samengestelde identiteit_): de gebruiker is verplicht om toegang te krijgen tot de sleutel kluis van een specifieke toepassing _en_ de toepassing moet de stroom voor verificatie op naam (OBO) gebruiken om de gebruiker te imiteren. Als u dit scenario wilt gebruiken, `applicationId` moet u beide en `objectId` opgeven in het toegangs beleid. De `applicationId` identificeert de vereiste toepassing en de `objectId` identificeert de gebruiker. Deze optie is momenteel niet beschikbaar voor gegevens vlak Azure RBAC.

Bij alle soorten toegang verifieert de toepassing met Azure AD. De toepassing gebruikt een [ondersteunde verificatie methode](../../active-directory/develop/authentication-vs-authorization.md) op basis van het toepassings type. De toepassing verkrijgt een token voor een resource in het vlak om toegang te verlenen. De resource is een eind punt in het beheer-of gegevens vlak, gebaseerd op de Azure-omgeving. De toepassing gebruikt het token en verzendt een REST API aanvraag naar Key Vault. Bekijk voor meer informatie de [volledige verificatie stroom](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Zie [Key Vault-basis beginselen voor verificatie](authentication-fundamentals.md) voor volledige informatie

## <a name="privileged-access"></a>Bevoegde toegang

Autorisatie bepaalt welke bewerkingen de aanroeper kan uitvoeren. Autorisatie in Key Vault gebruikt een combi natie van [op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure](../../role-based-access-control/overview.md) en Azure Key Vault toegangs beleid.

De toegang tot kluizen vindt plaats via twee interfaces of-abonnementen. Deze vlakken zijn het beheer vlak en het gegevens vlak.

- Het *beheer vlak* is waar u Key Vault zelf beheert en het is de interface die wordt gebruikt om kluizen te maken en verwijderen. U kunt ook sleutel kluis eigenschappen lezen en toegangs beleid beheren.
- Met het *gegevens vlak* kunt u werken met de gegevens die zijn opgeslagen in een sleutel kluis. U kunt sleutels, geheimen en certificaten toevoegen, verwijderen en wijzigen.

Toepassingen hebben toegang tot de abonnementen via eind punten. De toegangs controle voor de twee abonnementen werkt afzonderlijk. Als u een toepassing toegang wilt verlenen voor het gebruik van sleutels in een sleutel kluis, geeft u toegang tot het gegevens vlak met behulp van een Key Vault toegangs beleid of Azure RBAC. Als u een gebruiker lees toegang wilt verlenen voor Key Vault eigenschappen en tags, maar geen toegang tot gegevens (sleutels, geheimen of certificaten), verleent u beheer vlak toegang met Azure RBAC.

De volgende tabel bevat de eind punten voor de beheer-en gegevens abonnementen.

| Toegangs &nbsp; vlak | Eindpunten voor toegang | Operations | Mechanisme voor toegangs &nbsp; beheer |
| --- | --- | --- | --- |
| Beheerlaag | **Wereldwijd:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure van de Amerikaanse overheid:**<br> management.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> management.microsoftazure.de:443 | Sleutel kluizen maken, lezen, bijwerken en verwijderen<br><br>Key Vault toegangs beleid instellen<br><br>Key Vault Tags instellen | Azure RBAC |
| Gegevenslaag | **Wereldwijd:**<br> &lt;kluisnaam&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;kluisnaam&gt;.vault.azure.cn:443<br><br> **Azure van de Amerikaanse overheid:**<br> &lt;kluisnaam&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> &lt;kluisnaam&gt;.vault.microsoftazure.de:443 | Sleutels: versleutelen, ontsleutelen, wrapKey, sleutel uitpakken, ondertekenen, verifiëren, ophalen, lijst maken, bijwerken, importeren, verwijderen, herstellen, back-up maken, herstellen, opschonen<br><br> Certificaten: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, Get, List, maken, importeren, bijwerken, verwijderen, herstellen, back-up maken, herstellen, opschonen<br><br>  Geheimen: ophalen, lijst, instellen, verwijderen, herstellen, back-ups maken, herstellen, opschonen | Toegangs beleid of Azure RBAC Key Vault |

### <a name="managing-administrative-access-to-key-vault"></a>Beheer toegang tot Key Vault beheren

Wanneer u een sleutel kluis maakt in een resource groep, beheert u de toegang met behulp van Azure AD. U verleent gebruikers of groepen de mogelijkheid om de sleutel kluizen in een resource groep te beheren. U kunt toegang verlenen op een specifiek Scope niveau door de juiste Azure-rollen toe te wijzen. Als u toegang wilt verlenen aan een gebruiker om sleutel kluizen te beheren, wijst u een vooraf gedefinieerde `key vault Contributor` rol toe aan de gebruiker op een specifiek bereik. De volgende Scope niveaus kunnen worden toegewezen aan een Azure-rol:

- **Abonnement**: een Azure-rol die is toegewezen op abonnements niveau, is van toepassing op alle resource groepen en resources in dat abonnement.
- **Resource groep**: een Azure-rol die is toegewezen op het niveau van de resource groep, is van toepassing op alle resources in die resource groep.
- **Specifieke resource**: een Azure-rol die is toegewezen voor een specifieke resource, is van toepassing op die resource. In dit geval is de resource een specifieke sleutel kluis.

Er zijn verschillende vooraf gedefinieerde rollen. Als een vooraf gedefinieerde rol niet aan uw behoeften voldoet, kunt u uw eigen rol definiëren. Zie [Azure RBAC: ingebouwde rollen](../../role-based-access-control/built-in-roles.md)voor meer informatie.

> [!IMPORTANT]
> Als een gebruiker `Contributor` machtigingen heeft voor een sleutel kluis beheer vlak, kan de gebruiker zichzelf toegang verlenen tot het gegevens vlak door een Key Vault toegangs beleid in te stellen. U moet nauw keurig bepalen wie `Contributor` rollen toegang heeft tot uw sleutel kluizen. Zorg ervoor dat alleen geautoriseerde personen uw sleutel kluizen, sleutels, geheimen en certificaten kunnen gebruiken en beheren.

### <a name="controlling-access-to-key-vault-data"></a>Toegang tot Key Vault gegevens beheren

Key Vault toegangs beleid worden machtigingen afzonderlijk verleend aan sleutels, geheimen of certificaat. U kunt een gebruiker alleen toegang geven tot sleutels en niet op geheimen. Toegangs machtigingen voor sleutels, geheimen en certificaten worden beheerd op het niveau van de kluis.

> [!IMPORTANT]
> Key Vault toegangs beleid biedt geen ondersteuning voor granulaire machtigingen op object niveau, zoals een specifieke sleutel, geheim of certificaat. Wanneer een gebruiker gemachtigd is om sleutels te maken en te verwijderen, kunnen ze deze bewerkingen uitvoeren op alle sleutels in die sleutel kluis.

U kunt toegangs beleid voor een sleutel kluis instellen met behulp van de [Azure Portal](assign-access-policy-portal.md), de [Azure cli](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md)of de [rest api's van Key Vault beheer](/rest/api/keyvault/).

U kunt de toegang tot het gegevens vlak beperken met behulp van de [service-eind punten voor virtuele netwerken voor Azure Key Vault](overview-vnet-service-endpoints.md)). U kunt [firewalls en regels voor virtuele netwerken](network-security.md) configureren voor een extra beveiligingslaag.

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

Met Key Vault logboek registratie wordt informatie opgeslagen over de activiteiten die zijn uitgevoerd op uw kluis. Zie [Key Vault logboek registratie](logging.md)voor meer informatie.

U kunt Key Vault integreren met Event Grid om te worden gewaarschuwd wanneer de status van een sleutel, een certificaat of een geheim dat is opgeslagen in de sleutel kluis, is gewijzigd. Zie [Key Vault bewaken met Azure Event grid](event-grid-overview.md) voor meer informatie.

Het is ook belang rijk om de status van uw sleutel kluis te controleren om er zeker van te zijn dat uw service goed werkt. Zie [bewaking en waarschuwingen voor Azure Key Vault voor](alert.md)meer informatie over hoe u dit doet.

## <a name="backup-and-recovery"></a>Back-ups maken en herstellen

Met Azure Key Vault Soft-verwijderings-en opschoon beveiliging kunt u verwijderde kluizen en kluis objecten herstellen. Zie [Azure Key Vault overzicht van voorlopig verwijderen](soft-delete-overview.md)voor meer informatie.

U moet ook regel matig back-ups van uw kluis nemen op het bijwerken/verwijderen/maken van objecten in een kluis.  

## <a name="next-steps"></a>Volgende stappen

- [Beveiligings basislijn Azure Key Vault](security-baseline.md)
- [Best practices voor Azure Key Vault](security-baseline.md)
- [Virtuele netwerk service-eind punten voor Azure Key Vault](overview-vnet-service-endpoints.md)
- [Azure RBAC: Ingebouwde rollen](../../role-based-access-control/built-in-roles.md)
