---
title: Beveiligings aanbevelingen voor Queue Storage
titleSuffix: Azure Storage
description: Meer informatie over beveiligings aanbevelingen voor Queue Storage. Als u deze richt lijnen implementeert, kunt u voldoen aan uw beveiligings verplichtingen, zoals beschreven in ons gedeelde verantwoordelijkheids model.
author: tamram
services: storage
ms.author: tamram
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: security-recommendations
ms.openlocfilehash: db0e033adf553c25c6b7b401f8d0df1a2cd5995f
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592157"
---
# <a name="security-recommendations-for-queue-storage"></a>Beveiligings aanbevelingen voor Queue Storage

Dit artikel bevat beveiligings aanbevelingen voor Queue Storage. Als u deze aanbevelingen implementeert, kunt u voldoen aan uw beveiligings verplichtingen, zoals beschreven in ons gedeelde verantwoordelijkheids model. Zie [gedeelde verantwoordelijkheden voor Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)voor meer informatie over de verantwoordelijkheden van micro soft voor het uitvoeren van service providers.

Enkele van de aanbevelingen in dit artikel kunnen automatisch worden bewaakt door Azure Security Center. Azure Security Center is de eerste verdedigings linie bij het beveiligen van uw resources in Azure. Zie [Wat is Azure Security Center?](../../security-center/security-center-introduction.md)voor informatie over Azure Security Center.

Azure Security Center regel matig de beveiligings status van uw Azure-resources analyseren om mogelijke beveiligings problemen te identificeren. Vervolgens krijgt u aanbevelingen over hoe u deze kunt aanpakken. Zie [beveiligings aanbevelingen in azure Security Center](../../security-center/security-center-recommendations.md)voor meer informatie over Azure Security Center aanbevelingen.

## <a name="data-protection"></a>Gegevensbeveiliging

| Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|----|--|
| Het Azure Resource Manager-implementatie model gebruiken | Maak nieuwe opslag accounts met behulp van het Azure Resource Manager-implementatie model voor belang rijke verbeteringen in de beveiliging, waaronder superieure Azure-functies voor op rollen gebaseerd toegangs beheer (Azure RBAC) en controle, implementatie en beheer op basis van een resource manager, toegang tot beheerde identiteiten, toegang tot Azure Key Vault voor geheimen en verificatie en autorisatie op basis van Azure AD voor toegang tot Azure Storage gegevens en bronnen. Als dat mogelijk is, migreert u bestaande opslag accounts die gebruikmaken van het klassieke implementatie model om Azure Resource Manager te gebruiken. Zie [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md)voor meer informatie over Azure Resource Manager. | - |
| Geavanceerde beveiliging tegen bedreigingen inschakelen voor al uw opslag accounts | Advanced Threat Protection voor Azure Storage biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van opslag accounts worden gedetecteerd. Beveiligings waarschuwingen worden in Azure Security Center geactiveerd wanneer afwijkingen in de activiteit optreden en ook via e-mail worden verzonden naar abonnements beheerders, met details over verdachte activiteiten en aanbevelingen voor het onderzoeken en oplossen van bedreigingen. Zie [Advanced Threat Protection voor Azure Storage](../common/azure-defender-storage-configure.md)voor meer informatie. | [Ja](../../security-center/security-center-remediate-recommendations.md) |
| Alleen SAS-tokens (Shared Access Signature) beperken tot HTTPS-verbindingen | HTTPS vereisen wanneer een client een SAS-token gebruikt voor toegang tot de wachtrij gegevens helpt het risico op inbreuk te minimaliseren. Zie [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../common/storage-sas-overview.md)voor meer informatie. | - |

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

| Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|----|--|
| Azure Active Directory (Azure AD) gebruiken om toegang tot wachtrij gegevens te autoriseren | Azure AD biedt een superieure beveiliging en gebruiks vriendelijk gebruik van de gedeelde sleutel autorisatie voor het machtigen van aanvragen voor het Queue Storage. Zie [toegang tot Azure-blobs en-wacht rijen toestaan met Azure Active Directory](../common/storage-auth-aad.md)voor meer informatie. | - |
| Houd bij het toewijzen van machtigingen aan een Azure AD-beveiligings-principal met behulp van Azure RBAC de belangrijkste bevoegdheid. | Wanneer u een rol aan een gebruiker, groep of toepassing toewijst, moet u die beveiligings-principal alleen de machtigingen verlenen die nodig zijn om hun taken uit te voeren. Het beperken van de toegang tot bronnen voor komt zowel onbedoelde als kwaad aardige misbruik van uw gegevens. | - |
| De toegangs sleutels van uw account beveiligen met Azure Key Vault | Micro soft raadt u aan Azure AD te gebruiken om aanvragen voor Azure Storage te autoriseren. Als u echter gedeelde sleutel autorisatie moet gebruiken, kunt u uw account sleutels beveiligen met Azure Key Vault. U kunt de sleutels uit de sleutel kluis tijdens runtime ophalen in plaats van deze op te slaan in uw toepassing. | - |
| Uw account sleutels periodiek opnieuw genereren | Als u de account sleutels draait, vermindert u het risico dat uw gegevens worden blootgesteld aan kwaad aardige actors. | - |
| Houd bij het toewijzen van machtigingen aan een SAS de hoofd van de minimale bevoegdheid | Wanneer u een SAS maakt, geeft u alleen de machtigingen op die de client nodig heeft om zijn functie uit te voeren. Het beperken van de toegang tot bronnen voor komt zowel onbedoelde als kwaad aardige misbruik van uw gegevens. | - |
| Er is een intrekkings plan aanwezig voor elke SAS die u verleent aan clients | Als een SAS is aangetast, wilt u die SA'S zo snel mogelijk intrekken. Als u een SA'S voor gebruikers overdracht wilt intrekken, trekt u de sleutel voor gebruikers overdracht in om alle hand tekeningen die zijn gekoppeld aan die sleutel, ongeldig te maken. Als u een service-SA'S wilt intrekken die aan een opgeslagen toegangs beleid is gekoppeld, kunt u het opgeslagen toegangs beleid verwijderen, de naam van het beleid wijzigen of de verloop tijd wijzigen in een tijd die in het verleden ligt. Zie [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../common/storage-sas-overview.md)voor meer informatie.  | - |
| Als een service-SAS niet is gekoppeld aan een beleid voor opgeslagen toegang, stelt u de verloop tijd in op één uur of minder | Een service-SA'S die niet aan een opgeslagen toegangs beleid is gekoppeld, kunnen niet worden ingetrokken. Daarom wordt de verloop tijd beperkt zodat de SAS één uur geldig of minder wordt aanbevolen. | - |

## <a name="networking"></a>Netwerken

| Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|----|--|
| Configureer de mini maal vereiste versie van Transport Layer Security (TLS) voor een opslag account.  | Vereisen dat clients een veiligere versie van TLS gebruiken om aanvragen voor een Azure Storage account te maken door de minimale versie van TLS voor dat account te configureren. Zie voor meer informatie [Mini maal vereiste versie van Transport Layer Security (TLS) configureren voor een opslag account](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)| - |
| Schakel de optie **veilige overdracht vereist** in al uw opslag accounts in | Wanneer u de optie **beveiligde overdracht vereist** inschakelt, moeten alle aanvragen voor het opslag account via beveiligde verbindingen plaatsvinden. Aanvragen die via HTTP worden verzonden, mislukken. Zie [veilige overdracht vereisen in azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)voor meer informatie. | [Ja](../../security-center/security-center-remediate-recommendations.md) |
| Firewallregels inschakelen | Configureer firewall regels om de toegang tot uw opslag account te beperken tot aanvragen die afkomstig zijn van opgegeven IP-adressen of bereiken of van een lijst met subnetten in een virtueel Azure-netwerk (VNet). Zie [Azure Storage firewalls en virtuele netwerken configureren](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)voor meer informatie over het configureren van firewall regels. | - |
| Vertrouwde micro soft-Services toegang geven tot het opslag account | Door de firewall regels voor uw opslag account in te scha kelen, worden binnenkomende aanvragen voor gegevens standaard geblokkeerd, tenzij de aanvragen afkomstig zijn van een service die binnen een Azure VNet wordt uitgevoerd of van toegestane open bare IP-adressen. Aanvragen die zijn geblokkeerd, zijn onder andere die van andere Azure-Services, van de Azure Portal, van de services logboek registratie en metrische gegevens, enzovoort. U kunt aanvragen van andere Azure-Services toestaan door een uitzonde ring toe te voegen om vertrouwde micro soft-Services toegang te geven tot het opslag account. Zie [Azure Storage firewalls en virtuele netwerken configureren](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)voor meer informatie over het toevoegen van een uitzonde ring voor vertrouwde micro soft-Services.| - |
| Privé-eindpunten gebruiken | Een persoonlijk eind punt wijst een privé-IP-adres van uw Azure VNet toe aan het opslag account. Hiermee wordt al het verkeer beveiligd tussen uw VNet en het opslag account via een persoonlijke koppeling. Zie voor meer informatie over privé-eind punten [persoonlijke verbinding maken met een opslag account met behulp van een persoonlijk Azure-eind punt](../../private-link/tutorial-private-endpoint-storage-portal.md). | - |
| VNet-service tags gebruiken | Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen. Zie [overzicht van Azure-service Tags](../../virtual-network/service-tags-overview.md)voor meer informatie over service tags die door Azure Storage worden ondersteund. Zie [toegang tot PaaS-resources beperken](../../virtual-network/tutorial-restrict-network-access-to-resources.md)voor een zelf studie waarin wordt getoond hoe u service tags kunt gebruiken om uitgaande netwerk regels te maken. | - |
| Netwerk toegang tot specifieke netwerken beperken | Het beperken van netwerk toegang tot netwerken die als host fungeren voor clients die toegang vereisen, worden de bloot stelling van uw resources aan netwerk aanvallen beperkt. | [Ja](../../security-center/security-center-remediate-recommendations.md) |

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

| Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|----|--|
| Bijhouden hoe aanvragen worden geautoriseerd | Schakel Azure Storage logboek registratie in om bij te houden hoe elke aanvraag voor Azure Storage is geautoriseerd. De logboeken geven aan of een aanvraag anoniem is gemaakt door gebruik te maken van een OAuth 2,0-token, met behulp van een gedeelde sleutel of door gebruik te maken van een Shared Access Signature (SAS). Zie [Azure Storage Analytics-logboek registratie](../common/storage-analytics-logging.md)voor meer informatie. | - |

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor Azure-beveiliging](../../security/index.yml)
- [Documentatie voor beveiligde ontwikkel aars](../../security/develop/index.yml).
