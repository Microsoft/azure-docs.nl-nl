---
title: Uw AWS-account verbinden met Azure Security Center
description: Uw AWS-resources bewaken vanuit Azure Security Center
author: memildin
ms.author: memildin
ms.date: 01/24/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2ad3ab8c39d9f9a29e74bd139fb5ddd1e5faeff9
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755150"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Uw AWS-accounts verbinden met Azure Security Center

Veel workloads in de cloud beslaan meerdere cloudplatforms, dus moeten cloudbeveiligingsservices hetzelfde doen.

Azure Security Center biedt bescherming voor workloads in Azure, Amazon Web Services (AWS) en Google Cloud Platform (GCP).

Met de onboarding van uw AWS-account in Security Center worden AWS Security Hub en Azure Security Center geïntegreerd. Security Center biedt zo inzicht in en bescherming van beide cloudomgevingen, waardoor het volgende mogelijk wordt:

- Automatisch inrichten van agents (Security Center gebruikt [Azure Arc](../azure-arc/servers/overview.md) om de Log Analytics-agent te implementeren in uw AWS-instanties)
- Beleidsbeheer
- Beheer van beveiligingsproblemen
- Geïntegreerde eindpuntdetectie en -respons (EDR)
- Detectie van onjuiste beveiligingsconfiguraties
- Een enkele weergave met aanbevelingen van Security Center en bevindingen van AWS Security hub
- Integratie van uw AWS-resources in de berekening van de veiligheidsscore van Security Center
- Beoordelingen of uw AWS-resources voldoen aan regelgeving

In de onderstaande schermopname ziet u de AWS-accounts die worden weergegeven in het overzichtsdashboard van Security Center.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="Drie GCP-projecten die worden vermeld op het overzichts Dashboard van Security Center" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|[Azure Defender voor servers](defender-for-servers-introduction.md) is vereist|
|Vereiste rollen en machtigingen:|**Eigenaar**: voor het relevante Azure-abonnement<br>**Inzender**: kan ook verbinding maken met een AWS-account, indien een eigenaar de details van de service-principal levert|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Nee](./media/icons/no-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)|
|||



## <a name="connect-your-aws-account"></a>Verbinding maken met uw AWS-account

Volg de onderstaande stappen om uw AWS-cloudconnector te maken. 

### <a name="step-1-set-up-aws-security-hub"></a>Stap 1. AWS Security Hub instellen:

1. Als u beveiligingsaanbevelingen voor meerdere regio's wilt weergeven, herhaalt u de volgende stappen voor elke relevante regio.

    > [!IMPORTANT]
    > Als u een AWS-hoofdaccount gebruikt, herhaalt u de volgende drie stappen om het hoofdaccount en alle verbonden lidaccounts voor alle relevante regio's te configureren

    1. Schakel [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html) in.
    1. Schakel [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html) in.
    1. Controleer of er gegevens naar de Security Hub worden gestuurd.

        Wanneer u Security Hub voor het eerst inschakelt, kan het enkele uren duren voordat er gegevens beschikbaar zijn.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>Stap 2. Verificatie instellen voor Security Center in AWS

Er zijn twee manieren om Security Center te verifiëren bij AWS:

- **Een IAM-rol maken voor Security Center**: dit is de veiligste methode en wordt daarom ook aanbevolen
- **Een AWS-gebruiker maken voor Security Center**: dit is een minder veilige optie als u IAM niet hebt ingeschakeld

#### <a name="create-an-iam-role-for-security-center"></a>Een IAM-rol maken voor Security Center
1. Ga in uw Amazon Web Services-console naar **Beveiliging, identiteit en naleving** en selecteer **IAM**.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS-services":::

1. Selecteer **Rollen** en **Rol maken**.
1. Selecteer **Een ander AWS-account**.
1. Voer de volgende details in:

    - **Account-id**: voer de id van uw Microsoft-account (**158177204117**) in, zoals wordt weergegeven op de pagina van de AWS-connector in Security Center.
    - **Externe is vereisen** moet geselecteerd zijn
    - **Externe id**: voer de abonnements-id is zoals wordt weergegeven op de pagina van de AWS-connector in Security Center 

1. Selecteer **Next**.
1. Selecteer de volgende beleidsregels in de sectie **Machtigingsbeleid toevoegen**:

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. Voeg eventueel tags toe. Het toevoegen van labels aan de gebruiker heeft geen invloed op de verbinding.
1. Selecteer **Next**.

1. Kies in de lijst met rollen de rol die u hebt gemaakt

1. Sla de naam van de Amazon-resource (ARN) op voor later. 

#### <a name="create-an-aws-user-for-security-center"></a>Een AWS-gebruiker maken voor Security Center 
1. Open het tabblad **Gebruikers** en selecteer **Gebruiker toevoegen**.
1. Voer in de stap **Details** een gebruikersnaam in voor Security Center en zorg ervoor dat u **Programmatische toegang** selecteert bij het toegangstype voor AWS. 
1. Selecteer **Next Permissions**.
1. Selecteer **Bestaande beleidsregels rechtstreeks toevoegen** en pas de volgende beleidsregels toe:
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Selecteer **Volgende: Tags**. Voeg eventueel tags toe. Het toevoegen van labels aan de gebruiker heeft geen invloed op de verbinding.
1. Selecteer **Beoordelen**.
1. Sla de automatisch gegenereerde CSV-bestanden met de **Toegangssleutel-id** en **Geheime toegangssleutel** op voor later.
1. Controleer de samenvatting en klik vervolgens op **Gebruiker maken**.


### <a name="step-3-configure-the-ssm-agent"></a>Stap 3. De SSM-agent configureren

AWS Systems Manager is vereist voor het automatiseren van taken in uw AWS-resources. Als uw EC2-instanties niet beschikken over de SSM-agent, volgt u de relevante instructies van Amazon:

- [SSM-agent installeren en configureren op Windows-instanties](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [SSM-agent installeren en configureren op Amazon EC2 Linux-instanties](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>Stap 4. Azure Arc-vereisten voltooien
1. Zorg ervoor dat de juiste [Azure-resourceproviders](../azure-arc/servers/agent-overview.md#register-azure-resource-providers) zijn geregistreerd:
    - Microsoft.HybridCompute
    - Microsoft.GuestConfiguration

1. Een service-principal voor onboarding op schaal. Als **Eigenaar** van het abonnement dat u wilt gebruiken voor de onboarding maakt u een service-principal voor Azure Arc-onboarding zoals beschreven in [Een service-principal maken voor onboarding op schaal](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale).


### <a name="step-5-connect-aws-to-security-center"></a>Stap 5. AWS verbinden met Security Center

1. Selecteer **Meerdere cloudconnectors** in het menu van Security Center.
1. Selecteer **AWS-account toevoegen**.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Knop 'AWS-account toevoegen' op de pagina voor meerdere cloudconnectors van Security Center":::
1. Configureer de opties op het tabblad **AWS-verificatie**:
    1. Voer een **Weergavenaam** in voor de connector.
    1. Bevestig dat het abonnement het juiste is. Dit abonnement gaat de connector en de aanbevelingen van AWS Security Hub bevatten.
    1. Afhankelijk van de verificatie optie, kiest u in [stap 2. Verificatie instellen voor Security Center in AWS](#step-2-set-up-authentication-for-security-center-in-aws):
        - Selecteer **Rol aannemen** en plak de ARN van [Een IAM-rol maken voor Security Center](#create-an-iam-role-for-security-center).
            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Plakken van het ARN-bestand in het relevante veld van de wizard AWS-verbinding in de Azure Portal":::

            OF

        - Selecteer **Referenties** en plak de **toegangssleutel** en **geheime sleutel** uit het CSV-bestand dat u hebt opgeslagen in [Een AWS-gebruiker maken voor Security Center](#create-an-aws-user-for-security-center).
1. Selecteer **Next**.
1. Configureer de opties op het tabblad **Azure Arc-configuratie**:

    Security Center detecteert de EC2-instanties in het verbonden AWS-account en gebruikt SSM om ze op Azure Arc te onboarden. 

    > [!TIP]
    > Voor de lijst ondersteunde besturingssystemen, raadpleegt u [Welke besturingssystemen worden ondersteund voor mijn EC2-instanties?](#what-operating-systems-for-my-ec2-instances-are-supported) in de veelgestelde vragen.

    1. Selecteer de **Resourcegroep** en **Azure-regio** voor de onboarding van de gedetecteerde AWS EC2's in het geselecteerde abonnement.
    1. Voer de **Service-principal-id** en het **Service-principal-clientgeheim** voor Azure Arc in, zoals hier wordt beschreven [Een service-principal maken voor onboarding op schaal](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. Als de machine verbinding maakt met het internet via een proxyserver, geeft u het IP-adres van de proxyserver op of de naam en het poortnummer die de machine zal gebruiken om met de proxyserver te communiceren. Voer de waarde in de indeling ```http://<proxyURL>:<proxyport>``` in.
    1. Selecteer **Controleren + maken**.

        Lees de overzichtsinformatie

        In de secties Tags worden alle Azure-tags vermeld die automatisch worden gemaakt voor elke EC2 met de eigen relevante gegevens, zodat deze eenvoudig kunnen worden herkend in Azure. 

        U vindt meer informatie over Azure-tags via [Tags gebruiken om uw Azure-resources en beheerhiërarchie te organiseren](../azure-resource-manager/management/tag-resources.md).

### <a name="step-6-confirmation"></a>Stap 6. Bevestiging

Als de connector is gemaakt, is de AWS Security hub correct geconfigureerd:

- Security Center scant de omgeving op AWS EC2-instanties en gaat over op de onboarding naar Azure Arc, zodat de Log Analytics-agent kan worden geïnstalleerd en bescherming tegen beveiligingen en beveiligingsaanbevelingen kunnen worden geboden. 
- De ASC-service scant elke 6 uur op nieuwe AWS EC2-instanties en gaat over tot onboarding op basis van de configuratie.
- De AWS CIS-standaard wordt weergegeven in het dashboard voor wettelijke naleving van Security Center.
- Als Security Hub-beleid is ingeschakeld, worden aanbevelingen 5-10 minuten nadat de onboarding is voltooid, weergegeven in de Security Center-portal en het dashboard voor wettelijke naleving.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="AWS-resources en aanbevelingen op de pagina met aanbevelingen van Security Center":::



## <a name="monitoring-your-aws-resources"></a>Uw AWS-resources bewaken

Zoals hierboven wordt weergegeven, worden uw AWS-resource op de pagina met beveiligingsaanbevelingen getoond samen met uw Azure- en GCP-resources zodat u een overzicht van meerdere clouds hebt.

Als u alle actieve aanbevelingen voor uw resources op resourcetype wilt weergeven, gebruikt u de pagina Assetvoorraad van Security Center en filtert u op het AWS-resourcetype dat u wilt bekijken:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Het filter voor resourcetypen van de pagina Assetvoorraad met de opties voor AWS"::: 


## <a name="aws-in-security-center-faq"></a>Veelgestelde vragen over AWS in Security Center

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Welke besturingssystemen voor mijn EC2-instanties worden ondersteund?

Ondersteunde besturingssystemen voor automatische onboarding naar Azure Arc voor AWS-machines

- Ubuntu 16.04 - SSM-agent is standaard vooraf geïnstalleerd
- Ubuntu 18,04-SSM-agent is standaard vooraf geïnstalleerd
- Windows Server - SSM-agent is standaard vooraf geïnstalleerd
- CentOS Linux 7 – SSM moet handmatig worden geïnstalleerd of apart worden gehouden bij het onboarden
- SUSE Linux Enterprise Server (SLES) 15 (x64) - SSM moet handmatig worden geïnstalleerd of apart worden gehouden bij het onboarden
- Red Hat Enterprise Linux (RHEL) 7 (x64) - SSM moet handmatig worden geïnstalleerd of apart worden gehouden bij het onboarden


## <a name="next-steps"></a>Volgende stappen

Het koppelen van uw AWS-account maakt deel uit van de ervaring voor meerdere clouds die beschikbaar is in Azure Security Center. Zie de volgende pagina voor gerelateerde informatie:

- [Uw GCP-accounts verbinden met Azure Security Center](quickstart-onboard-gcp.md)
