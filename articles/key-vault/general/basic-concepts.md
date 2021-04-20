---
title: Wat is Azure Sleutelkluis? | Microsoft Docs
description: Meer informatie over Azure Key Vault beveiliging van cryptografische sleutels en geheimen die cloudtoepassingen en -services gebruiken.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 6fafacda322a974d04a04bb5e79d1ee086eaf7a5
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753394"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure Key Vault basisconcepten

Azure Key Vault is een cloudservice voor het veilig opslaan en openen van geheimen. Een geheim is alles waar u de toegang tot nauw wilt beheren, zoals API-sleutels, wachtwoorden, certificaten of cryptografische sleutels. Key Vault-service ondersteunt twee typen containers: kluizen en HSM-pools (Managed Hardware Security Module). Kluizen bieden ondersteuning voor het opslaan van sleutels, geheimen en certificaten met software- en HSM-ondersteuning. Beheerde HSM-pools ondersteunen alleen door HSM ondersteunde sleutels. Zie [Azure Key Vault REST API overzicht](about-keys-secrets-certificates.md) voor meer informatie.

Hier vindt u andere belangrijke termen:

- **Tenant**: een tenant is de organisatie die een specifiek exemplaar van Microsoft-cloudservices in eigendom heeft en beheert. Het wordt meestal gebruikt om te verwijzen naar de set Azure- en Microsoft 365-services voor een organisatie.

- **Kluiseigenaar**: een kluiseigenaar kan een sleutelkluis maken en heeft er volledige toegang toe en controle over. De eigenaar van de kluis kan ook controles instellen om vast te leggen wie toegang heeft tot geheimen en sleutels. Beheerders kunnen de levenscyclus van sleutels beheren. Ze kunnen een nieuwe versie van de sleutel instellen, een back-up maken en gerelateerde taken uitvoeren.

- **Kluisconsument**: een kluisconsument kan acties uitvoeren op de elementen in de sleutelkluis wanneer de eigenaar van de kluis toegang verleent aan de consument. De beschikbare acties zijn afhankelijk van de verleende machtigingen.

- **Beheerde HSM-beheerders:** gebruikers aan wie de rol Administrator is toegewezen, hebben volledige controle over een beheerde HSM-pool. Ze kunnen meer roltoewijzingen maken om beheerde toegang aan andere gebruikers te delegeren.

- **Crypto Officer/gebruiker van beheerde HSM:** ingebouwde rollen die meestal worden toegewezen aan gebruikers of service-principals die cryptografische bewerkingen uitvoeren met behulp van sleutels in beheerde HSM. Cryptogebruiker kan nieuwe sleutels maken, maar geen sleutels verwijderen.

- Cryptoserviceversleuteling van beheerde **HSM:** ingebouwde rol die meestal wordt toegewezen aan een serviceaccounts beheerde service-identiteit (bijvoorbeeld opslagaccount) voor versleuteling van data-at-rest met door de klant beheerde sleutel.

- **Resource**: een resource is een beheerbaar item dat beschikbaar is via Azure. Veelvoorkomende voorbeelden zijn virtuele machines, opslagaccounts, web-apps, databases en virtuele netwerken. Er zijn er nog veel meer.

- **Resourcegroep**: een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. De resourcegroep kan alle resources voor de oplossing bevatten of enkel de resources die u als groep wilt beheren. U bepaalt hoe resources worden toegewezen aan resourcegroepen op basis van wat voor uw organisatie het meest zinvol is.

- **Beveiligingsprincipaal:** een Azure-beveiligingsprincipaal is een beveiligingsidentiteit die door de gebruiker gemaakte apps, services en automatiseringshulpprogramma's gebruikt voor toegang tot specifieke Azure-resources. U kunt dit zien als een 'gebruikersidentiteit' (gebruikersnaam en wachtwoord of certificaat) met een specifieke rol en nauw beheerde machtigingen. Een beveiligingsprincipaal hoeft alleen specifieke dingen te doen, in tegenstelling tot een algemene gebruikersidentiteit. Het verbetert de beveiliging als u deze alleen het minimale machtigingsniveau verleent dat nodig is om de beheertaken uit te voeren. Een beveiligingsprincipaal die wordt gebruikt met een toepassing of service wordt specifiek een **service-principal genoemd.**

- [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md): Azure AD is de Active Directory-service voor een tenant. Elke adreslijst heeft een of meer domeinen. Aan een directory kunnen vele abonnementen zijn gekoppeld, maar slechts één tenant.

- **Tenant-id voor Azure AD**: een tenant-id is een unieke manier om een Azure AD-exemplaar in een Azure-abonnement te identificeren.

- **Beheerde identiteiten:** Azure Key Vault biedt een manier om referenties en andere sleutels en geheimen veilig op te slaan, maar uw code moet worden geverifieerd om Key Vault op te halen. Het gebruik van een beheerde identiteit maakt het oplossen van dit probleem eenvoudiger door Azure-services een automatisch beheerde identiteit te geven in Azure AD. U kunt deze identiteit gebruiken voor verificatie bij Key Vault bij alle services die ondersteuning bieden voor Microsoft Azure Active Directory-verificatie, zonder dat u referenties in uw code hoeft te hebben. Zie de volgende afbeelding en het overzicht van beheerde identiteiten voor [Azure-resources voor meer informatie.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authentication"></a>Verificatie
Als u bewerkingen wilt uitvoeren met Key Vault, moet u zich eerst verifiëren. Er zijn drie manieren om te verifiëren Key Vault:

- [Beheerde identiteiten voor Azure-resources:](../../active-directory/managed-identities-azure-resources/overview.md)wanneer u een app implementeert op een virtuele machine in Azure, kunt u een identiteit toewijzen aan uw virtuele machine die toegang heeft tot Key Vault. U kunt ook identiteiten toewijzen aan [andere Azure-resources.](../../active-directory/managed-identities-azure-resources/overview.md) Het voordeel van deze benadering is dat de app of service de rotatie van het eerste geheim niet beheert. Azure roteert de identiteit automatisch. We raden deze benadering aan als best practice. 
- **Service-principal en certificaat:** u kunt een service-principal en een bijbehorend certificaat gebruiken dat toegang heeft tot Key Vault. Deze methode wordt niet aanbevolen omdat de eigenaar of ontwikkelaar van de toepassing het certificaat moet roteren.
- **Service-principal en geheim:** hoewel u een service-principal en een geheim kunt gebruiken om te verifiëren bij Key Vault, raden we dit niet aan. Het is moeilijk om het bootstrapgeheim dat wordt gebruikt om te verifiëren, automatisch te roteren voor Key Vault.


## <a name="key-vault-roles"></a>Rollen van Key Vault

Gebruik de volgende tabel om beter te begrijpen hoe Key Vault u kan helpen om aan de behoeften van ontwikkelaars en beveiligingsadministrators te voldoen.

| Rol | Probleemformulering | Opgelost door Azure Key Vault |
| --- | --- | --- |
| Ontwikkelaar voor een Azure-toepassing |"Ik wil een toepassing voor Azure schrijven die gebruikmaakt van sleutels voor ondertekening en versleuteling. Maar ik wil dat deze sleutels extern zijn van mijn toepassing, zodat de oplossing geschikt is voor een toepassing die geografisch is gedistribueerd. <br/><br/>Ik wil dat deze sleutels en geheimen worden beveiligd, zonder dat ik de code zelf hoef te schrijven. Ik wil ook dat deze sleutels en geheimen eenvoudig kunnen worden gebruikt vanuit mijn toepassingen, met optimale prestaties." |√ De sleutels worden opgeslagen in een kluis en wanneer dit nodig is, aangeroepen via een URI.<br/><br/> √ De sleutels worden beveiligd door Azure. Hiervoor wordt gebruikgemaakt van algoritmen, sleutellengten en HSM's die voldoen aan de industriestandaard.<br/><br/> √ Sleutels worden verwerkt in HSM's die zich in dezelfde Azure-datacenters bevinden als de toepassingen. Deze methode biedt betere betrouwbaarheid en minder latentie dan wanneer de sleutels zich op een afzonderlijke locatie bevinden, zoals on-premises. |
| SaaS-ontwikkelaar (Software as a Service) |"Ik wil niet de verantwoordelijkheid of mogelijke aansprakelijkheid voor de tenantsleutels en geheimen van mijn klanten. <br/><br/>Ik wil dat klanten hun sleutels in bezit hebben en beheren, zodat ik me kan concentreren op het doen waar ik het beste in ben, wat de belangrijkste softwarefuncties is." |√ Klanten kunnen hun eigen sleutels in Azure importeren en beheren. Wanneer een SaaS-toepassing cryptografische bewerkingen moet uitvoeren met behulp van de sleutels van klanten, Key Vault deze bewerkingen namens de toepassing. De toepassing ziet de sleutels van de klant niet. |
| Chief Security Officer (CSO) |"Ik wil weten dat onze toepassingen voldoen aan FIPS 140-2 Level 2 of FIPS 140-2 Level 3 HMS's voor beveiligd sleutelbeheer. <br/><br/>Ik wil ervoor zorgen dat mijn organisatie de controle heeft over de levenscyclus van de sleutel en het sleutelgebruik kan bewaken. <br/><br/>En hoewel we meerdere Azure-services en -resources gebruiken, wil ik de sleutels vanaf één locatie in Azure beheren." |√ **kluizen kiezen voor** met FIPS 140-2 Level 2 gevalideerde HMS's.<br/>√ **Beheerde HSM-pools kiezen** voor MET FIPS 140-2 Niveau 3 gevalideerde HSM's.<br/><br/>√ Key Vault is zodanig ontworpen dat Microsoft uw sleutels niet kan zien of extraheren.<br/>√ Sleutelgebruik wordt vrijwel in realtime vastgelegd.<br/><br/>√ De kluis biedt één interface, ongeacht het aantal kluizen dat u in Azure hebt, welke regio's ze ondersteunen en door welke toepassingen ze worden gebruikt. |

Iedereen met een Azure-abonnement kan sleutelkluizen maken en gebruiken. Hoewel Key Vault voordelen biedt voor ontwikkelaars en beveiligingsbeheerders, kan het worden geïmplementeerd en beheerd door de beheerder van een organisatie die andere Azure-services beheert. Deze beheerder kan zich bijvoorbeeld aanmelden met een Azure-abonnement, een kluis maken voor de organisatie waarin sleutels worden opgeslagen en vervolgens verantwoordelijk zijn voor operationele taken als deze:

- Een sleutel of geheim maken of importeren.
- Een sleutel of geheim intrekken of verwijderen.
- Gebruikers of toepassingen machtigingen verlenen voor toegang tot de sleutelkluis, zodat ze de sleutels en geheimen in de kluis kunnen beheren of gebruiken.
- Sleutelgebruik configureren (bijvoorbeeld ondertekenen of versleutelen).
- Sleutelgebruik bewaken.

Deze beheerder geeft ontwikkelaars vervolgens URI's om aan te roepen vanuit hun toepassingen. Deze beheerder geeft ook logboekinformatie over sleutelgebruik door aan de beveiligingsbeheerder. 

![Overzicht van hoe Azure Key Vault werkt][1]

Ontwikkelaars kunnen de sleutels ook rechtstreeks beheren door gebruik te maken van API's. Zie [Ontwikkelaarshandleiding voor Key Vault](developers-guide.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [beveiligen van uw kluis](security-overview.md).
- Meer informatie over het [beveiligen van uw beheerde HSM-pools](../managed-hsm/access-control.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
Azure Sleutelkluis is beschikbaar in de meeste regio's. Zie de pagina [Prijzen van Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie.
