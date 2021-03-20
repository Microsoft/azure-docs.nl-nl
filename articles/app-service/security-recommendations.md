---
title: Aanbevelingen voor beveiliging
description: Implementeer de beveiligings aanbevelingen om te voldoen aan uw beveiligings verplichtingen, zoals vermeld in ons model van de gedeelde groep. Verbeter de beveiliging van uw app.
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 09dbe0fbefb8b90b4c4e1ddef57abf3b13856183
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148083"
---
# <a name="security-recommendations-for-app-service"></a>Beveiligings aanbevelingen voor App Service

Dit artikel bevat beveiligings aanbevelingen voor Azure App Service. Als u deze aanbevelingen implementeert, kunt u voldoen aan uw beveiligings verplichtingen zoals beschreven in ons gedeelde verantwoordelijkheids model en wordt de algehele beveiliging van uw web-app-oplossingen verbeterd. Lees [Azure-infrastructuur beveiliging](../security/fundamentals/infrastructure.md)voor meer informatie over wat micro soft doet aan de verantwoordelijkheden van de service provider.

## <a name="general"></a>Algemeen

| Aanbeveling | Opmerkingen |
|-|-|----|
| Blijf up-to-date | Gebruik de nieuwste versies van ondersteunde platforms, programmeer talen, protocollen en frameworks. |

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

| Aanbeveling | Opmerkingen |
|-|----|
| Anonieme toegang uitschakelen | Schakel anonieme toegang uit, tenzij u anonieme aanvragen moet ondersteunen. Zie [verificatie en autorisatie in azure app service](overview-authentication-authorization.md)voor meer informatie over Azure app service verificatie opties.|
| Verificatie vereisen | Gebruik waar mogelijk de module App Service verificatie in plaats van code te schrijven om verificatie en autorisatie te verwerken. Zie [verificatie en autorisatie in azure app service](overview-authentication-authorization.md). |
| Back-end-bronnen beveiligen met geverifieerde toegang | U kunt de identiteit van de gebruiker gebruiken of een toepassings-id gebruiken om een back-end-bron te verifiëren. Wanneer u ervoor kiest om een toepassings-id te gebruiken, moet u een [beheerde identiteit](overview-managed-identity.md)gebruiken.
| Verificatie van client certificaat vereisen | Verificatie van client certificaten verbetert de beveiliging door alleen verbindingen toe te staan van clients die kunnen worden geverifieerd met behulp van door u verstrekte certificaten. |

## <a name="data-protection"></a>Gegevensbeveiliging

| Aanbeveling | Opmerkingen |
|-|-|
| HTTP omleiden naar HTTPs | Clients kunnen standaard verbinding maken met web-apps met behulp van HTTP of HTTPS. U kunt het beste HTTP omleiden naar HTTPs omdat HTTPS het SSL/TLS-protocol gebruikt om een beveiligde verbinding te bieden, die zowel is versleuteld als geverifieerd. |
| De communicatie met Azure-resources versleutelen | Wanneer uw app verbinding maakt met Azure-resources, zoals [SQL database](https://azure.microsoft.com/services/sql-database/) of [Azure Storage](../storage/index.yml), blijft de verbinding in Azure. Omdat de verbinding via het gedeelde netwerk in azure verloopt, moet u altijd alle communicatie versleutelen. |
| Vereisen dat de nieuwste TLS-versie mogelijk is | Sinds 2018 nieuwe Azure App Service-apps gebruiken TLS 1,2. Nieuwere versies van TLS bevatten beveiligings verbeteringen ten opzichte van oudere protocol versies. |
| FTPS gebruiken | App Service ondersteunt zowel FTP-als FTPS voor het implementeren van uw bestanden. Gebruik waar mogelijk FTPS in plaats van FTP. Wanneer een of beide protocollen niet in gebruik zijn, moet u [deze uitschakelen](deploy-ftp.md#enforce-ftps). |
| Toepassingsgegevens beveiligen | Sla geen toepassings geheimen op, zoals database referenties, API-tokens of persoonlijke sleutels in uw code of configuratie bestanden. De algemeen geaccepteerde aanpak is om ze als [omgevings variabelen](https://wikipedia.org/wiki/Environment_variable) te benaderen met het standaard patroon in uw taal. In Azure App Service kunt u omgevings variabelen definiëren met behulp van [app-instellingen](./configure-common.md) en [verbindings reeksen](./configure-common.md). App-instellingen en verbindings reeksen worden versleuteld opgeslagen in Azure. De app-instellingen worden alleen ontsleuteld voordat ze worden ingevoegd in het proces geheugen van uw app wanneer de app wordt gestart. De versleutelings sleutels worden regel matig gedraaid. U kunt ook uw Azure App Service-app integreren met [Azure Key Vault](../key-vault/index.yml) voor het beheer van geavanceerde geheimen. Door [de Key Vault met een beheerde identiteit te openen](../key-vault/general/tutorial-net-create-vault-azure-web-app.md), heeft uw app service-app veilig toegang tot de geheimen die u nodig hebt. |

## <a name="networking"></a>Netwerken

| Aanbeveling | Opmerkingen |
|-|-|
| Statische IP-beperkingen gebruiken | Met Azure App Service in Windows kunt u een lijst met IP-adressen definiëren die toegang hebben tot uw app. De lijst met toegestane namen kan afzonderlijke IP-adressen bevatten of een bereik van IP-adressen die zijn gedefinieerd door een subnetmasker. Zie [Azure app service static IP-beperkingen](app-service-ip-restrictions.md)voor meer informatie.  |
| De geïsoleerde prijs categorie gebruiken | Met uitzonde ring van de geïsoleerde prijs categorie worden met alle lagen uw apps uitgevoerd op de gedeelde netwerk infrastructuur in Azure App Service. De geïsoleerde laag biedt u volledige netwerk isolatie door uw apps in een speciale [app service omgeving](environment/intro.md)uit te voeren. Een App Service omgeving wordt uitgevoerd in uw eigen exemplaar van [Azure Virtual Network](../virtual-network/index.yml).|
| Beveiligde verbindingen gebruiken voor toegang tot on-premises resources | U kunt [hybride verbindingen](app-service-hybrid-connections.md), [Virtual Network integratie](web-sites-integrate-with-vnet.md)of [app service omgeving](environment/intro.md) gebruiken om verbinding te maken met on-premises resources. |
| Beperk de bloot stelling aan binnenkomend netwerk verkeer | Met netwerk beveiligings groepen kunt u netwerk toegang beperken en het aantal weer gegeven eind punten beheren. Zie voor meer informatie [inkomend verkeer naar een app service Environment beheren](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Bewaking

| Aanbeveling | Opmerkingen |
|-|-|
|Azure Security Center Standard-laag gebruiken | [Azure Security Center](../security-center/defender-for-app-service-introduction.md) is systeem eigen geïntegreerd met Azure app service. Dit kan analyses uitvoeren en aanbevelingen voor beveiliging bieden. |

## <a name="next-steps"></a>Volgende stappen

Neem contact op met uw toepassings provider om na te gaan of er aanvullende beveiligings vereisten gelden. Zie [documentatie voor beveiligde ontwikkel aars](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/)voor meer informatie over het ontwikkelen van beveiligde toepassingen.