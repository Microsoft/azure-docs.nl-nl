---
title: Uw GCP-account verbinden met Azure Security Center
description: Uw GCP-resources bewaken vanuit Azure Security Center
author: memildin
ms.author: memildin
ms.date: 02/08/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 94c7a800fc551faf6650b8e30fe7c2188f7d2dbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100008380"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>Uw GCP-accounts verbinden met Azure Security Center

Veel workloads in de cloud beslaan meerdere cloudplatforms, dus moeten cloudbeveiligingsservices hetzelfde doen.

Azure Security Center biedt bescherming voor workloads in Azure, Amazon Web Services (AWS) en Google Cloud Platform (GCP).

De GCP-accounts in Security Center worden voor bereid, integreert GCP Security-opdracht en Azure Security Center. Security Center biedt zo inzicht in en bescherming van beide cloudomgevingen, waardoor het volgende mogelijk wordt:

- Detectie van onjuiste beveiligingsconfiguraties
- Een enkele weergave met aanbevelingen van Security Center en bevindingen van GCP Command Center
- Integratie van uw GCP-resources in de berekening van de veiligheidsscore van Security Center
- Integratie van GCP Security Command Center-aanbevelingen op basis van de CIS-standaard in het nalevingsdashboard van Security Center

In de onderstaande schermopname ziet u de GCP-projecten die worden weergegeven in het overzichtsdashboard van Security Center.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3 GCP-projecten worden weergegevens op het overzichtsdashboard van Security Center" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|[Azure Defender voor servers](defender-for-servers-introduction.md) is vereist|
|Vereiste rollen en machtigingen:|**Eigenaar** of **Inzender** voor het relevante Azure-abonnement|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Nee](./media/icons/no-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)|
|||

## <a name="connect-your-gcp-account"></a>Verbinding maken met uw GCP-account

Maak een connector voor elke organisatie die u van Security Center wilt bewaken.

Wanneer u uw GCP-accounts verbindt met specifieke Azure-abonnementen, moet u rekening houden met de [Google Cloud resource Hierarchy](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#resource-hierarchy-detail) en deze richt lijnen:

- U kunt uw GCP-accounts verbinden met ASC op het niveau van de *organisatie*
- U kunt meerdere organisaties verbinden met één Azure-abonnement
- U kunt meerdere organisaties verbinden met meerdere Azure-abonnementen
- Wanneer u verbinding maakt met een organisatie, worden alle *projecten* in die organisatie toegevoegd aan Security Center

Volg de onderstaande stappen om uw GCP-cloudconnector te maken. 

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Stap 1. GCP Security Command Center instellen met Security Health Analytics

Voor alle GCP-projecten in uw organisatie moet u ook het volgende doen:

1. **GCP Security Command Center** instellen volgens [deze instructies uit de GCP-documentatie](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup).
1. **Security Health Analytics** inschakelen volgens [deze instructies uit de GCP-documentatie](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics).
1. Controleren of er gegevens naar het Security Command Center worden gestuurd.

De instructies voor het verbinden van uw GCP-omgeving voor de beveiligingsconfiguratie volgen de aanbevelingen van Google voor het gebruiken van aanbevelingen voor beveiligingsconfiguraties. De integratie maakt gebruik van het Google Security Command Center, en verbruikt extra resources die uw factuur kunnen beïnvloeden.

Wanneer u Security Health Analytics voor het eerst inschakelt, kan het enkele uren duren voordat er gegevens beschikbaar zijn.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Stap 2. GCP Security Command Center-API inschakelen

1. Selecteer in de **Cloud console-API-bibliotheek** van Google elk project in de organisatie waarmee u verbinding wilt maken Azure Security Center.
1. Zoek en selecteer **Security Command Center API** in de API-bibliotheek.
1. Selecteer **ENABLE** op de pagina van de API.

Meer informatie over de [Security Command Center API](https://cloud.google.com/security-command-center/docs/reference/rest/).


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Stap 3. Een toegewezen serviceaccount maken voor de integratie van de beveiligingsconfiguratie

1. Selecteer in de **GCP-console** een project uit de organisatie waarin u het vereiste service account maakt. 

    > [!NOTE]
    > Wanneer dit service account wordt toegevoegd op organisatie niveau, wordt het gebruikt om toegang te krijgen tot de gegevens die zijn verzameld door Security Command Center van alle andere ingeschakelde projecten in de organisatie. 

1. Selecteer **Service accounts** in het **Navigatiemenu** onder **IAM & Admin**.
1. Selecteer **CREATE SERVICE ACCOUNT** (Serviceaccount maken).
1. Voer een accountnaam in en selecteer **Create** (Maken).
1. Geef de **Role** (Rol) op als **Security Center Admin Viewer** en selecteer **Continue** (Doorgaan).
1. De sectie **Grant users access to this service account** (Gebruikers toegang geven tot dit serviceaccount) is optioneel. Selecteer **Gereed**.
1. Kopieer de **Email value** (E-mailwaarde) van het gemaakte serviceaccount en sla deze op voor later gebruik.
1. Selecteer **IAM** in het **Navigatiemenu** onder **IAM & Admin**
    1. Schakel over naar organisatieniveau.
    1. Selecteer **ADD** (Toevoegen).
    1. Plak in het veld **New members** (Nieuwe leden) de **E-mailwaarde** die u eerder hebt gekopieerd.
    1. Geef de rol op als **Security Center beheer Viewer** en selecteer vervolgens **Opslaan**.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="De relevante GCP-machtigingen instellen":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Stap 4. Een persoonlijke sleutel voor het toegewezen serviceaccount maken
1. Schakel over naar projectniveau.
1. Selecteer **Service accounts** in het **Navigatiemenu** onder **IAM & Admin**.
1. Open het toegewezen serviceaccount en selecteer Edit (Bewerken).
1. Selecteer in de sectie **Keys** (Sleutels) **ADD KEY** (Sleutel toevoegen) en vervolgens **Create new key** (Nieuwe sleutel maken).
1. Selecteer **JSON** in het scherm Create private key (Persoonlijke sleutel maken) en selecteer vervolgens **CREATE** (Maken).
1. Sla dit JSON-bestand op voor later gebruik.


### <a name="step-5-connect-gcp-to-security-center"></a>Stap 5. GCP verbinden met Security Center
1. Selecteer **Cloudconnectors** in het menu van Security Center.
1. Selecteer GCP-account toevoegen.
1. Ga op de onboardingpagina als volgt te werk en selecteer **Volgende**.
    1. Valideer het gekozen abonnement.
    1. Voer in het veld **Weergavenaam** een weergavenaam in voor de connector.
    1. Voer in het veld **Organisatie-ID** de id van uw organisatie in. Zie [Organisaties maken en beheren](https://cloud.google.com/resource-manager/docs/creating-managing-organization) als u de id niet weet.
    1. Blader in het bestandsvak **Persoonlijke sleutel** naar het JSON-bestand dat u hebt gedownload in [Stap 4. Een persoonlijke sleutel voor het toegewezen serviceaccount maken](#step-4-create-a-private-key-for-the-dedicated-service-account).


### <a name="step-6-confirmation"></a>Stap 6. Bevestiging

Wanneer de connector is gemaakt en GCP Security Command Center correct is geconfigureerd gebeurt het volgende:

- De GCP CIS-standaard wordt weergegeven in het dashboard voor wettelijke naleving van Security Center.
- 5-10 minuten nadat de onboarding is voltooid, worden beveiligingsaanbevelingen voor uw GCP-resources weergegeven in de Security Center-portal en het dashboard voor wettelijke naleving:   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="GCP-resources en aanbevelingen op de pagina met aanbevelingen van Security Center":::


## <a name="monitoring-your-gcp-resources"></a>Uw GCP-resources bewaken

Zoals hierboven wordt weergegeven, worden uw GCP-resources op de pagina met beveiligingsaanbevelingen getoond samen met uw Azure- en AWS-resources zodat u een overzicht van meerdere clouds hebt.

Als u alle actieve aanbevelingen voor uw resources op resourcetype wilt weergeven, gebruikt u de pagina Assetvoorraad van Security Center en filtert u op het GCP-resourcetype dat u wilt bekijken:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="Het filter voor resourcetypen van de pagina Assetvoorraad met de opties voor GCP"::: 


## <a name="faq-for-connecting-gcp-accounts-to-azure-security-center"></a>Veelgestelde vragen over het verbinden van GCP-accounts met Azure Security Center

### <a name="can-i-connect-multiple-gcp-organizations-to-security-center"></a>Kan ik meerdere GCP-organisaties verbinden met Security Center?
Ja. De GCP-connector van Security Center verbindt uw Google cloud-resources op *organisatie* niveau. 

Maak een connector voor elke GCP-organisatie die u van Security Center wilt bewaken. Wanneer u verbinding maakt met een organisatie, worden alle projecten in die organisatie toegevoegd aan Security Center.

Meer informatie over de Google Cloud resource Hierarchy in [de online docs van Google](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy).


### <a name="is-there-an-api-for-connecting-my-gcp-resources-to-security-center"></a>Is er een API voor het koppelen van mijn GCP-resources aan Security Center?
Ja. Zie de details van de [API connectors](/rest/api/securitycenter/connectors)om Security Center Cloud connectors te maken, bewerken of verwijderen met een rest API.

## <a name="next-steps"></a>Volgende stappen

Het koppelen van uw GCP-account maakt deel uit van de ervaring voor meerdere clouds die beschikbaar is in Azure Security Center. Zie de volgende pagina voor gerelateerde informatie:

- [Uw AWS-accounts verbinden met Azure Security Center](quickstart-onboard-aws.md)
- [Google Cloud resource Hierarchy](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy)--meer informatie over de Google Cloud resource Hierarchy in de online docs van Google