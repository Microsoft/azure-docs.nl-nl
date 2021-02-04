---
title: Onjuiste configuraties voor komen met Azure Security Center
description: Meer informatie over het gebruik van de opties voor afdwingen en weigeren van Security Center op de pagina met details van aanbevelingen
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: a3da9cdea543894aa7aec66112e28658beac84b5
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558195"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Onjuiste configuraties voorkomen met afdwingen/weigeren

Onjuiste beveiligingsconfiguraties zijn een belangrijke oorzaak van beveiligingsincidenten. Security Center biedt nu de mogelijkheid om onjuiste configuratie van nieuwe resources met betrekking tot specifieke aanbevelingen te helpen *voorkomen*. 

Deze functie kan u helpen uw workloads veilig te houden en uw beveiligingsscore stabiel te houden.

Het afdwingen van een veilige configuratie op basis van een specifieke aanbeveling wordt aangeboden in twee modi:

- Met behulp van de optie **Weigeren** van Azure Policy, kunt u het maken van resources die niet in orde zijn, stoppen
- Met de optie **Afdwingen** kunt u profiteren van het effect **DeployIfNotExist** van Azure Policy en niet-compatibele resources automatisch herstellen wanneer deze worden gemaakt

Deze vindt u boven aan de pagina Resource Details voor geselecteerde beveiligings aanbevelingen (Zie [aanbevelingen met opties voor weigeren/afdwingen](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Het maken van resources voor komen

1. Open de aanbeveling waaraan uw nieuwe resources moeten voldoen en selecteer de knop **weigeren** boven aan de pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="De pagina aanbeveling met de knop weigeren gemarkeerd":::

    In het deel venster configuratie worden de scope opties weer gegeven. 

1. Stel het bereik in door het relevante abonnement of beheer groep te selecteren.

    > [!TIP]
    > U kunt de drie punten aan het einde van de rij gebruiken om één abonnement te wijzigen of de selectie vakjes gebruiken om meerdere abonnementen of groepen te selecteren. Selecteer vervolgens **wijzigen in weigeren**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Het bereik instellen voor Azure Policy weigeren":::


## <a name="enforce-a-secure-configuration"></a>Een veilige configuratie afdwingen

1. Open de aanbeveling voor het implementeren van een sjabloon implementatie voor als nieuwe resources niet voldoen en selecteer de knop **afdwingen** boven aan de pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="De pagina aanbeveling met de knop afdwingen gemarkeerd":::

    Het configuratie venster wordt geopend met alle configuratie opties voor het beleid. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Configuratie opties afdwingen":::

1. Stel het bereik, de naam van de toewijzing en andere relevante opties in.

1. Selecteer **Controleren + maken**.

## <a name="recommendations-with-denyenforce-options"></a>Aanbevelingen met opties voor weigeren/afdwingen

Deze aanbevelingen kunnen worden gebruikt in combi natie met de optie **weigeren** :

- Toegang tot opslagaccounts met configuraties voor firewalls en virtuele netwerken moet worden beperkt
- Azure Cache voor Redis moet zich in een virtueel netwerk bevinden
- Voor Azure Cosmos DB-accounts moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van data-at-rest
- Azure Machine Learning-werkruimten moeten worden versleuteld met een door de klant beheerde sleutel (CMK)
- Azure Spring Cloud moet netwerkinjectie gebruiken
- Voor Cognitive Services accounts moet gegevens versleuteling worden ingeschakeld met een door de klant beheerde sleutel (CMK)
- De CPU- en geheugenlimieten van containers moeten worden afgedwongen
- Containerinstallatiekopieën mogen alleen worden geïmplementeerd vanuit vertrouwde registers
- Containerregisters moeten worden versleuteld met een door de klant beheerde sleutel (CMK)
- Container met escalatie van bevoegdheden moet worden vermeden
- Containers die gevoelige hostnaamruimten delen, moeten worden vermeden
- Containers mogen alleen op toegestane poorten luisteren
- Onveranderbaar (alleen-lezen) hoofdbestandssysteem moet worden afgedwongen voor containers
- Key Vault-sleutels moeten een vervaldatum hebben
- Key Vault-geheimen moeten een vervaldatum hebben
- Beveiliging tegen leegmaken moet zijn ingeschakeld voor sleutelkluizen
- Voorlopig verwijderen moet zijn ingeschakeld voor sleutelkluizen
- Minimaal bevoegde Linux-functies moeten worden afgedwongen voor containers
- Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld
- Het overschrijven of uitschakelen van het AppArmor-profiel voor containers moet worden beperkt
- Bevoegde containers moeten worden vermeden
- Het uitvoeren van containers als hoofdgebruiker moet worden vermeden
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Voor Service Fabric-clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign
- Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor clientverificatie
- Services mogen alleen op toegestane poorten luisteren
- Opslagaccounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Opslagaccounts moeten netwerktoegang beperken met behulp van regels voor virtuele netwerken
- Het gebruik van hostnetwerken en -poorten moet worden beperkt
- Het gebruik van HostPath-volumekoppelingen voor pods moet worden beperkt tot een bekende lijst om de toegang tot knooppunten te beperken voor de containers die zijn gecompromitteerd
- De geldigheidsperiode van certificaten die in Azure Key Vault zijn opgeslagen, mag niet langer zijn dan twaalf maanden
- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Web Application Firewall (WAF) moet zijn ingeschakeld voor Application Gateway
- Web Application firewall (WAF) moet zijn ingeschakeld voor de service service van Azure voor de voor deur

Deze aanbevelingen kunnen worden gebruikt in combi natie met de optie **afdwingen** :

- Controle op SQL-servers moet zijn ingeschakeld
- Azure Backup moet zijn ingeschakeld voor virtuele machines
- Azure Defender voor SQL moet zijn ingeschakeld voor uw SQL-servers
- Diagnostische logboeken in Azure Stream Analytics moeten zijn ingeschakeld
- Diagnostische logboeken in Batch-accounts moeten worden ingeschakeld
- Diagnostische logboeken in Data Lake Analytics moeten zijn ingeschakeld
- Diagnostische logboeken in Event Hub moeten zijn ingeschakeld
- Diagnostische logboeken in Key Vault moeten zijn ingeschakeld
- Diagnostische logboeken in Logic Apps moeten zijn ingeschakeld
- Diagnostische logboeken in zoekservices moeten zijn ingeschakeld
- Diagnostische logboeken in Service Bus moeten zijn ingeschakeld