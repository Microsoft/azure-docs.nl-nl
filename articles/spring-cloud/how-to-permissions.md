---
title: Instructies voor het gebruik van machtigingen in azure lente Cloud
description: In dit artikel wordt beschreven hoe u aangepaste rollen maakt voor machtigingen in azure lente-Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c0b3bd81e0f73a7879382e28516378bd722bc17
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878110"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Machtigingen gebruiken in azure lente Cloud
In dit artikel wordt beschreven hoe u aangepaste rollen maakt die machtigingen voor Azure lente-cloud resources delegeren. Aangepaste rollen breiden [ingebouwde rollen van Azure uit](../role-based-access-control/built-in-roles.md) met diverse aandelen machtigingen.

De volgende aangepaste rollen worden geïmplementeerd:

* **Rol voor ontwikkel aars**: 
    * Implementeren
    * Testen
    * Apps opnieuw opstarten
    * Kan Toep assen en wijzigingen aanbrengen in de app-configuraties in de Git-opslag plaats
    * Kan de logboek stroom ophalen
* **Betrouw baarheid van OPS-site**: 
    * Apps opnieuw opstarten
    * Logboek stromen ophalen
    * Kan geen wijzigingen aanbrengen in apps of configuraties
* **Azure pipelines/Jenkins/github Action-functie**:
    * Kan bewerkingen voor maken, lezen, bijwerken, verwijderen uitvoeren
    * Kan alles in azure lente-Cloud en-apps binnen service-exemplaar maken en configureren: Azure-pijp lijnen, Jenkins-of GitHub-acties, met behulp van terraform-of ARM-sjablonen

## <a name="define-developer-role"></a>Ontwikkelaars functie definiëren

De rol ontwikkelaar bevat machtigingen voor het opnieuw opstarten van apps en het weer geven van hun logboek stromen, maar kan geen wijzigingen aanbrengen in apps, configuratie.

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>Navigeren in abonnement en Resource Group Access Control (IAM)

Volg deze stappen om te beginnen met het definiëren van een rol.

1. Open in het Azure Portal het abonnement en de resource groep waar u de aangepaste rol wilt toewijzen.
2. Open **toegangs beheer (IAM)**.
3. Klik op **+ Toevoegen**.
4. Klik op **aangepaste rol toevoegen**.
5. Klik op **Volgende**.

   ![Aangepast rol maken](media/spring-cloud-permissions/create-custom-role.png)

6. Klik op **Machtigingen toevoegen**.

   ![Machtigingen toevoegen starten](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>Zoeken naar Azure lente-Cloud machtigingen:
7. Zoek in het zoekvak naar *Microsoft.app*.
Selecteer *Microsoft Azure lente-Cloud*.

   ![Azure lente-Cloud selecteren](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Selecteer de machtigingen voor de rol ontwikkelaar:

Selecteer in **micro soft. AppPlatform/lente**:
* Schrijven: Azure lente-Cloud service-exemplaar maken of bijwerken
* Lezen: Azure lente-Cloud service-exemplaar ophalen
* Overige: test sleutels voor de Azure-veer Cloud service-instantie weer geven

Selecteer in **micro soft. AppPlatform/lente/apps** het volgende:
* Lezen: Microsoft Azure lente-Cloud toepassing lezen
* Overige: ophalen van Microsoft Azure lente-URL voor bron toepassing

Selecteer in **micro soft. AppPlatform/lente/apps/bindingen**:
* Lezen: Microsoft Azure lente Cloud Application binding lezen

Selecteer in **micro soft. AppPlatform/lente/apps/implementaties**:
* Schrijven: Schrijf Microsoft Azure veer implementatie van de Cloud toepassing
* Lezen: Microsoft Azure lente-implementatie van de Cloud toepassing lezen
* Overige: Start Microsoft Azure lente-implementatie van de Cloud toepassing
* Overige: Stop Microsoft Azure veer implementatie van de Cloud toepassing
* Overige: Microsoft Azure lente-implementatie van de Cloud toepassing opnieuw starten
* Overige: de URL van het toepassings logboek bestand van Microsoft Azure lente ophalen

Selecteer in **micro soft. AppPlatform/lente/Apps/domeinen**:
* Lezen: Microsoft Azure lente van een andere Cloud toepassing

Selecteer de volgende opties van **micro soft. AppPlatform/lente/certificaten**:
* Lezen: Microsoft Azure lente-Cloud certificaat lezen

Selecteer in **micro soft. AppPlatform/locaties/operationResults/lente**:
* Lezen: resultaat van Lees bewerking

Selecteer in **micro soft. AppPlatform/locations/operationStatus/operationId**:
* Lezen: status Lees bewerking

    [![Developler-machtigingen ](media/spring-cloud-permissions/developer-permissions-box.png) maken](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. Klik op **Add**.

10. Controleer de machtigingen.

11. Klik op **Controleren en maken**.

## <a name="define-devops-engineer-role"></a>DevOps-Engineer definiëren
Met deze procedure definieert u een rol met machtigingen voor het implementeren, testen en opnieuw starten van Azure lente-Cloud-apps.

1. Herhaal de procedure om te navigeren naar abonnement, resource groep en toegangs beheer (IAM).
2. Selecteer de machtigingen voor de rol van de DevOps-Engineer:

Selecteer in **micro soft. AppPlatform/lente**:
* Schrijven: Azure lente-Cloud service-exemplaar maken of bijwerken
* Verwijderen: Azure lente-Cloud service-exemplaar verwijderen
* Lezen: Azure lente-Cloud service-exemplaar ophalen
* Overige: het eind punt van het Azure-programma voor Cloud service-exemplaren inschakelen
* Overige: het eind punt van het Azure-programma voor Cloud service-exemplaar uitschakelen
* Overige: test sleutels voor de Azure-veer Cloud service-instantie weer geven
* Overige: de test sleutel voor het Cloud service-exemplaar van Azure lente opnieuw genereren

Selecteer in **micro soft. AppPlatform/lente/apps** het volgende:
* Schrijven: Schrijf Microsoft Azure lente-Cloud toepassing
* Verwijderen: Microsoft Azure lente-Cloud toepassing verwijderen
* Lezen: Microsoft Azure lente-Cloud toepassing lezen
* Overige: ophalen van Microsoft Azure lente-URL voor bron toepassing
* Overige: een aangepast domein voor de toepassing van de Cloud valideren Microsoft Azure

Selecteer in **micro soft. AppPlatform/lente/apps/bindingen**:
* Schrijven: write Microsoft Azure lente Cloud Application binding
* Verwijderen: Microsoft Azure lente Cloud toepassings binding verwijderen
* Lezen: Microsoft Azure lente Cloud Application binding lezen

Selecteer in **micro soft. AppPlatform/lente/apps/implementaties**:
* Schrijven: Schrijf Microsoft Azure veer implementatie van de Cloud toepassing
* Verwijderen: Azure lente-implementatie van Cloud toepassing verwijderen
* Lezen: Microsoft Azure lente-implementatie van de Cloud toepassing lezen
* Overige: Start Microsoft Azure lente-implementatie van de Cloud toepassing
* Overige: Stop Microsoft Azure veer implementatie van de Cloud toepassing
* Overige: Microsoft Azure lente-implementatie van de Cloud toepassing opnieuw starten
* Overige: de URL van het toepassings logboek bestand van Microsoft Azure lente ophalen

Selecteer in **micro soft. AppPlatform/lente/apps/implementaties/sku's**:
* Lezen: beschik bare sku's voor de implementatie van toepassingen weer geven

Selecteer in **micro soft. AppPlatform/locaties**:
* Overige: Beschik baarheid van de naam controleren

Van micro soft. AppPlatform/locaties/operationResults/lente selecteren: lezen: resultaat van Lees bewerking

Selecteer in **micro soft. AppPlatform/locations/operationStatus/operationId**:
* Lezen: status Lees bewerking

Selecteer in **micro soft. AppPlatform/sku's**:
* Lezen: beschik bare sku's weer geven

   [![Dev/op machtigingen ](media/spring-cloud-permissions/dev-ops-permissions.png)](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. Klik op **Add**.

4. Controleer de machtigingen.

5. Klik op **Controleren en maken**.

## <a name="define-ops---site-reliability-engineering-role"></a>Een OPS-site betrouwbaarheids techniek definiëren
Met deze procedure definieert u een rol met machtigingen voor het implementeren, testen en opnieuw starten van Azure lente-Cloud-apps.

1. Herhaal de procedure om te navigeren naar abonnement, resource groep en toegangs beheer (IAM).

2. Selecteer de machtigingen voor de functie OPS-site betrouwbaarheids techniek:

Selecteer in **micro soft. AppPlatform/lente**:
* Lezen: Azure lente-Cloud service-exemplaar ophalen
* Overige: test sleutels voor de Azure-veer Cloud service-instantie weer geven

Selecteer in **micro soft. AppPlatform/lente/apps** het volgende:
* Lezen: Microsoft Azure lente-Cloud toepassing lezen

Selecteer in **micro soft. AppPlatform/apps/implementaties**:
* Lezen: Microsoft Azure lente-implementatie van de Cloud toepassing lezen
* Overige: Start Microsoft Azure lente-implementatie van de Cloud toepassing
* Overige: Stop Microsoft Azure veer implementatie van de Cloud toepassing
* Overige: Microsoft Azure lente-implementatie van de Cloud toepassing opnieuw starten

Selecteer in **micro soft. AppPlatform/locaties/operationResults/lente**:
* Lezen: resultaat van Lees bewerking

Selecteer in **micro soft. AppPlatform/locations/operationStatus/operationId**:
* Lezen: status Lees bewerking

   [![OPS/SRE-machtigingen ](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. Klik op **Add**.

4. Controleer de machtigingen.

5. Klik op **Controleren en maken**.

## <a name="define-azure-pipelinesprovisioning-role"></a>Azure-pijp lijnen/inrichtings functie definiëren
Met deze Jenkins/github-actie functie kunnen alles in azure lente-Cloud en-apps met een service-exemplaar worden gemaakt en geconfigureerd. Deze rol is voor het vrijgeven of implementeren van code.

1. Herhaal de procedure om te navigeren naar abonnement, resource groep en toegangs beheer (IAM).

2. Open de opties voor **machtigingen** .

3. Selecteer de machtigingen voor de rol Azure pipelines/inrichting:
  
Selecteer in **micro soft. AppPlatform/lente**:
* Schrijven: Azure lente-Cloud service-exemplaar maken of bijwerken
* Verwijderen: Azure lente-Cloud service-exemplaar verwijderen
* Lezen: Azure lente-Cloud service-exemplaar ophalen
* Overige: het eind punt van het Azure-programma voor Cloud service-exemplaren inschakelen
* Overige: het eind punt van het Azure-programma voor Cloud service-exemplaar uitschakelen
* Overige: test sleutels voor de Azure-veer Cloud service-instantie weer geven
* Overige: de test sleutel voor het Cloud service-exemplaar van Azure lente opnieuw genereren

Selecteer in **micro soft. AppPlatform/lente/apps** het volgende:
* Schrijven: Schrijf Microsoft Azure lente-Cloud toepassing
* Verwijderen: Microsoft Azure lente-Cloud toepassing verwijderen
* Lezen: Microsoft Azure lente-Cloud toepassing lezen
* Overige: ophalen van Microsoft Azure lente-URL voor bron toepassing
* Overige: een aangepast domein voor de toepassing van de Cloud valideren Microsoft Azure

Selecteer in **micro soft. AppPlatform/lente/apps/bindingen**:
* Schrijven: write Microsoft Azure lente Cloud Application binding
* Verwijderen: Microsoft Azure lente Cloud toepassings binding verwijderen
* Lezen: Microsoft Azure lente Cloud Application binding lezen

Selecteer in **micro soft. AppPlatform/lente/apps/implementaties**:
* Schrijven: Schrijf Microsoft Azure veer implementatie van de Cloud toepassing
* Verwijderen: Azure lente-implementatie van Cloud toepassing verwijderen
* Lezen: Microsoft Azure lente-implementatie van de Cloud toepassing lezen
* Overige: Start Microsoft Azure lente-implementatie van de Cloud toepassing
* Overige: Stop Microsoft Azure veer implementatie van de Cloud toepassing
* Overige: Microsoft Azure lente-implementatie van de Cloud toepassing opnieuw starten
* Overige: de URL van het toepassings logboek bestand van Microsoft Azure lente ophalen

Selecteer in **micro soft. AppPlatform/sku's**:
* Lezen: beschik bare sku's weer geven

Selecteer in **micro soft. AppPlatform/locaties**:
* Overige: Beschik baarheid van de naam controleren

Selecteer in **micro soft. AppPlatform/locaties/operationResults/lente**:
* Lezen: resultaat van Lees bewerking

Selecteer in **micro soft. AppPlatform/locations/operationStatus/operationId**:
* Lezen: status Lees bewerking

Selecteer in **micro soft. AppPlatform/sku's**:
* Lezen: beschik bare sku's weer geven

   [![Pijp lijnen machtigingen ](media/spring-cloud-permissions/pipelines-permissions-box.png)](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. Klik op **Add**.

5. Controleer de machtigingen.

6. Klik op **Controleren en maken**.


## <a name="see-also"></a>Zie ook
* [Aangepaste Azure-rollen maken of bijwerken met behulp van Azure Portal](../role-based-access-control/custom-roles-portal.md)

Zie voor meer informatie over drie methoden voor het definiëren van aangepaste machtigingen:
* [Een rol klonen](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [De procedure vanaf het begin uitvoeren](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [Starten vanuit JSON](../role-based-access-control/custom-roles-portal.md#start-from-json)