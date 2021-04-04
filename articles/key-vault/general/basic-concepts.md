---
title: Wat is Azure Sleutelkluis? | Microsoft Docs
description: Meer informatie over hoe Azure Key Vault cryptografische sleutels en geheimen beveiligt die worden gebruikt door Cloud toepassingen en-services.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: cc00a4f1c1551932b4a30a8ef9b27cb1d4082667
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99071593"
---
# <a name="azure-key-vault-basic-concepts"></a>Basis concepten Azure Key Vault

Azure Key Vault is een Cloud service voor het veilig opslaan en openen van geheimen. Een geheim is alles wat u de toegang tot, zoals API-sleutels, wacht woorden, certificaten of cryptografische sleutels, nauw keurig wilt beheren. Key Vault-service ondersteunt twee typen containers: kluizen en beheerde HSM-groepen. Kluizen ondersteunen de opslag van software-en HSM-sleutels, geheimen en certificaten. Beheerde HSM-Pools bieden alleen ondersteuning voor HSM-ondersteunde sleutels. Zie [Azure Key Vault rest API overzicht](about-keys-secrets-certificates.md) voor volledige informatie.

Hier volgen enkele belang rijke voor waarden:

- **Tenant**: een tenant is de organisatie die een specifiek exemplaar van Microsoft-cloudservices in eigendom heeft en beheert. Dit wordt vaak gebruikt om te verwijzen naar de set Azure-en Microsoft 365-Services voor een organisatie.

- **Kluiseigenaar**: een kluiseigenaar kan een sleutelkluis maken en heeft er volledige toegang toe en controle over. De eigenaar van de kluis kan ook controles instellen om vast te leggen wie toegang heeft tot geheimen en sleutels. Beheerders kunnen de levenscyclus van sleutels beheren. Ze kunnen een nieuwe versie van de sleutel instellen, een back-up maken en gerelateerde taken uitvoeren.

- **Kluisconsument**: een kluisconsument kan acties uitvoeren op de elementen in de sleutelkluis wanneer de eigenaar van de kluis toegang verleent aan de consument. De beschikbare acties zijn afhankelijk van de verleende machtigingen.

- **Beheerde HSM-beheerders**: gebruikers aan wie de rol beheerder is toegewezen, hebben volledige controle over een beheerde HSM-groep. Ze kunnen meer roltoewijzingen maken voor het delegeren van beheerde toegang tot andere gebruikers.

- **Beheerde HSM crypto Officer/gebruiker**: ingebouwde rollen die meestal worden toegewezen aan gebruikers of service-principals die cryptografische bewerkingen uitvoeren met behulp van sleutels in beheerde HSM. Crypto grafie-gebruiker kan nieuwe sleutels maken, maar kan geen sleutels verwijderen.

- **Beheerde HSM crypto Encryption service**: ingebouwde rol die meestal wordt toegewezen aan een service-account beheerde service-identiteit (bijvoorbeeld opslag account) voor versleuteling van gegevens in rust met door de klant beheerde sleutel.

- **Resource**: een resource is een beheerbaar item dat beschikbaar is via Azure. Algemene voor beelden hiervan zijn virtuele machines, opslag accounts, Web-apps, data bases en virtuele netwerken. Er zijn nog veel meer.

- **Resourcegroep**: een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. De resourcegroep kan alle resources voor de oplossing bevatten of enkel de resources die u als groep wilt beheren. U bepaalt hoe resources worden toegewezen aan resourcegroepen op basis van wat voor uw organisatie het meest zinvol is.

- **Beveiligingsprincipal: een** beveiligings-principal van Azure is een beveiligings identiteit die door gebruikers gemaakte apps, services en hulpprogram ma's voor automatisering gebruikt om toegang te krijgen tot specifieke Azure-resources. U kunt het beschouwen als een ' gebruikers identiteit ' (gebruikers naam en wacht woord of certificaat) met een specifieke rol en nauw keurig beheerde machtigingen. Een beveiligingsprincipal hoeft alleen specifieke dingen te doen, in tegens telling tot een algemene gebruikers-id. Het verbetert de beveiliging als u alleen het minimale machtigings niveau hebt dat nodig is om de beheer taken uit te voeren. Een beveiligings-principal die wordt gebruikt voor een toepassing of service, wordt aangeduid met een **Service-Principal**.

- [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md): Azure AD is de Active Directory-service voor een tenant. Elke adreslijst heeft een of meer domeinen. Aan een directory kunnen vele abonnementen zijn gekoppeld, maar slechts één tenant.

- **Tenant-id voor Azure AD**: een tenant-id is een unieke manier om een Azure AD-exemplaar in een Azure-abonnement te identificeren.

- **Beheerde identiteiten**: Azure Key Vault biedt een manier om referenties en andere sleutels en geheimen veilig op te slaan, maar uw code moet worden geverifieerd bij Key Vault om deze op te halen. Het gebruik van een beheerde identiteit maakt het oplossen van dit probleem eenvoudiger door Azure-Services een automatisch beheerde identiteit in azure AD te geven. U kunt deze identiteit gebruiken voor verificatie bij Key Vault bij alle services die ondersteuning bieden voor Microsoft Azure Active Directory-verificatie, zonder dat u referenties in uw code hoeft te hebben. Zie de volgende afbeelding en het [overzicht van beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie.

## <a name="authentication"></a>Verificatie
Als u bewerkingen met Key Vault wilt uitvoeren, moet u deze eerst verifiëren. Er zijn drie manieren om te verifiëren bij Key Vault:

- [Beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md): wanneer u een app implementeert op een virtuele machine in azure, kunt u een identiteit toewijzen aan uw virtuele machine die toegang heeft tot Key Vault. U kunt ook identiteiten toewijzen aan [andere Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md). Het voor deel van deze benadering is dat de draai hoek van het eerste geheim niet door de app of service wordt beheerd. Azure roteert de identiteit automatisch. U wordt aangeraden deze benadering als best practice te doen. 
- **Service-Principal en certificaat**: u kunt een Service-Principal en een bijbehorend certificaat gebruiken dat toegang heeft tot Key Vault. Deze aanpak wordt niet aanbevolen omdat de eigenaar van de toepassing of de ontwikkelaar het certificaat moet draaien.
- **Service-Principal en geheim**: Hoewel u een Service-Principal en een geheim kunt gebruiken om te verifiëren bij Key Vault, wordt dit niet aanbevolen. Het is moeilijk om automatisch het Boots trap-geheim te draaien dat wordt gebruikt voor verificatie bij Key Vault.


## <a name="key-vault-roles"></a>Rollen van Key Vault

Gebruik de volgende tabel om beter te begrijpen hoe Key Vault u kan helpen om aan de behoeften van ontwikkelaars en beveiligingsadministrators te voldoen.

| Rol | Probleemformulering | Opgelost door Azure Key Vault |
| --- | --- | --- |
| Ontwikkelaar voor een Azure-toepassing |"Ik wil een toepassing voor Azure schrijven die sleutels gebruikt voor ondertekening en versleuteling. Maar ik wil dat deze sleutels extern zijn van mijn toepassing, zodat de oplossing geschikt is voor een toepassing die geografisch wordt gedistribueerd. <br/><br/>Ik wil dat deze sleutels en geheimen worden beveiligd, zonder dat ik de code zelf hoef te schrijven. Ik wil ook dat deze sleutels en geheimen eenvoudig kunnen worden gebruikt in mijn toepassingen, met optimale prestaties. " |√ De sleutels worden opgeslagen in een kluis en wanneer dit nodig is, aangeroepen via een URI.<br/><br/> √ De sleutels worden beveiligd door Azure. Hiervoor wordt gebruikgemaakt van algoritmen, sleutellengten en HSM's die voldoen aan de industriestandaard.<br/><br/> √ Sleutels worden verwerkt in HSM's die zich in dezelfde Azure-datacenters bevinden als de toepassingen. Deze methode biedt betere betrouwbaarheid en minder latentie dan wanneer de sleutels zich op een afzonderlijke locatie bevinden, zoals on-premises. |
| SaaS-ontwikkelaar (Software as a Service) |"Ik wil geen verantwoordelijkheid of mogelijke aansprakelijkheid voor de Tenant sleutels en geheimen van mijn klanten. <br/><br/>Ik wil dat klanten hun sleutels eigenaar kunnen maken en beheren, zodat ik kan concentreren op wat ik het beste kan doen, wat de kern software functies levert. " |√ Klanten kunnen hun eigen sleutels in Azure importeren en beheren. Wanneer een SaaS-toepassing cryptografische bewerkingen moet uitvoeren met behulp van de sleutels van de klant, Key Vault deze bewerkingen namens de toepassing. De toepassing ziet de sleutels van de klant niet. |
| Chief Security Officer (CSO) |"Ik wil weten dat onze toepassingen voldoen aan het FIPS 140-2 level 2-of FIPS 140-2 level 3 Hsm's voor beveiligd sleutel beheer. <br/><br/>Ik wil ervoor zorgen dat mijn organisatie de controle heeft over de levenscyclus van de sleutel en het sleutelgebruik kan bewaken. <br/><br/>En hoewel we meerdere Azure-Services en-resources gebruiken, wil ik de sleutels vanaf één locatie in azure beheren. " |√ Kies **kluizen** voor het FIPS 140-2 level 2-gevalideerde hsm's.<br/>√ Kies **Managed HSM Pools** voor FIPS 140-2 level 3 valided hsm's.<br/><br/>√ Key Vault is zodanig ontworpen dat Microsoft uw sleutels niet kan zien of extraheren.<br/>√ Sleutelgebruik wordt vrijwel in realtime vastgelegd.<br/><br/>√ De kluis biedt één interface, ongeacht het aantal kluizen dat u in Azure hebt, welke regio's ze ondersteunen en door welke toepassingen ze worden gebruikt. |

Iedereen met een Azure-abonnement kan sleutelkluizen maken en gebruiken. Hoewel Key Vault voor delen van ontwikkel aars en beveiligings beheerders, kan het worden geïmplementeerd en beheerd door de beheerder van een organisatie die andere Azure-Services beheert. Deze beheerder kan zich bijvoorbeeld aanmelden met een Azure-abonnement, een kluis maken voor de organisatie waarin sleutels moeten worden opgeslagen en vervolgens verantwoordelijk zijn voor operationele taken als deze:

- Een sleutel of geheim maken of importeren.
- Een sleutel of geheim intrekken of verwijderen.
- Gebruikers of toepassingen machtigingen verlenen voor toegang tot de sleutelkluis, zodat ze de sleutels en geheimen in de kluis kunnen beheren of gebruiken.
- Sleutelgebruik configureren (bijvoorbeeld ondertekenen of versleutelen).
- Sleutelgebruik bewaken.

Deze beheerder geeft ontwikkel aars vervolgens de functie voor het aanroepen van hun toepassingen. Deze beheerder geeft ook informatie over de logboek registratie van sleutel gebruik aan de beveiligings beheerder. 

![Overzicht van hoe Azure Key Vault werkt][1]

Ontwikkelaars kunnen de sleutels ook rechtstreeks beheren door gebruik te maken van API's. Zie [Ontwikkelaarshandleiding voor Key Vault](developers-guide.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [beveiligen van uw kluis](secure-your-key-vault.md).
- Meer informatie over [het beveiligen van uw beheerde HSM-Pools](../managed-hsm/access-control.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
Azure Sleutelkluis is beschikbaar in de meeste regio's. Zie de pagina [Prijzen van Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie.