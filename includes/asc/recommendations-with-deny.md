---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 03/21/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 5fc36e327a9530105182f0a23b3ef22ab324e01c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803484"
---
- Toegang tot opslagaccounts met configuraties voor firewalls en virtuele netwerken moet worden beperkt
- Automation-accountvariabelen moeten worden versleuteld
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
- Openbare toegang tot een opslagaccount moet niet worden toegestaan
- Opslagaccounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Opslagaccounts moeten netwerktoegang beperken met behulp van regels voor virtuele netwerken
- Het gebruik van hostnetwerken en -poorten moet worden beperkt
- Het gebruik van HostPath-volumekoppelingen voor pods moet worden beperkt tot een bekende lijst om de toegang tot knooppunten te beperken voor de containers die zijn gecompromitteerd
- De geldigheidsperiode van certificaten die in Azure Key Vault zijn opgeslagen, mag niet langer zijn dan twaalf maanden
- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Web Application Firewall (WAF) moet zijn ingeschakeld voor Application Gateway
- Web Application firewall (WAF) moet zijn ingeschakeld voor de service service van Azure voor de voor deur

