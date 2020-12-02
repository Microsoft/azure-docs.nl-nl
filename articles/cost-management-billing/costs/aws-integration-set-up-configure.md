---
title: AWS-integratie met Azure Cost Management instellen
description: Dit artikel begeleidt u bij het instellen en configureren van AWS voor de integratie van kosten- en gebruiksrapporten met Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.openlocfilehash: 2b8a008decc41a5686fb2c8d9fee271f95f0fef3
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122410"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>AWS-integratie van kosten- en gebruiksrapporten instellen en configureren

Met Amazon Web Services (AWS)-integratie voor kosten- en gebruiksrapporten (CUR) kunt u uw AWS-uitgaven in Azure Cost Management bewaken en beheren. De integratie staat één locatie in de Azure-portal toe waar u de bestedingen kunt bewaken en beheren voor Azure en AWS. In dit artikel wordt uitgelegd hoe u de integratie instelt en configureert zodat u Azure Cost Management functies kunt gebruiken om kosten te analyseren en budgetten te controleren.

Cost Management verwerkt het AWS-kosten- en gebruiksrapport dat is opgeslagen in een S3-bucket door gebruik te maken van uw AWS-toegangsreferenties om rapportdefinities op te halen en rapport-GZIP CSV-bestanden te downloaden.

Bekijk de video [Connectors voor AWS instellen in Cost Management](https://www.youtube.com/watch?v=Jg5KC1cx5cA) voor meer informatie over het instellen van AWS-rapportintegratie. Als u andere video’s wilt bekijken, gaat u naar het [YouTube-kanaal voor Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/Jg5KC1cx5cA]

## <a name="create-a-cost-and-usage-report-in-aws"></a>Een kosten- en gebruiksrapport maken in AWS

Het gebruik van een kosten- en gebruiksrapport is de voor AWS aanbevolen manier om AWS-kosten te verzamelen en te verwerken. Zie voor meer informatie de documentatie [Rapport over kosten en gebruik van AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html).

Gebruik de pagina **Kosten- & gebruiksrapporten** van de console Facturering en Cost Management in AWS om een kosten- en gebruiksrapport te maken met de volgende stappen:

1. Meld u aan bij de AWS-beheer console en open de [Facturering en Cost Management-console](https://console.aws.amazon.com/billing).
2. Selecteer in het navigatiedeelvenster **Kosten- & gebruiksrapporten**.
3. Selecteer **Rapport maken**.
4. Voer onder **Rapportnaam** een naam in voor uw rapport.
5. Selecteer **Resource-Id's toevoegen** onder **Aanvullende rapportdetails**.
6. Voor **Instellingen voor het vernieuwen van gegevens** selecteert u of u wilt dat het AWS-rapport voor kosten en gebruik wordt vernieuwd als AWS restituties, tegoeden of ondersteuningskosten op uw rekening toepast nadat uw factuur is voltooid. Wanneer een rapport wordt vernieuwd, wordt een nieuw rapport geüpload naar Amazon S3. U wordt aangeraden de instelling geselecteerd te laten.
7. Selecteer **Next**.
8. Kies **Configureren** voor **S3-bucket**.
9. In het dialoogvenster Configure S3 Bucket voert u de naam van een bucket in en de regio waar u een nieuwe bucket wilt maken en kiest u **Next**.
10. Selecteer **Ik heb bevestigd dat dit beleid juist is** en klik vervolgens op **Opslaan**.
11. (Optioneel) Voer voor het voorvoegsel van het rapportpad, het padvoorvoegsel naar het rapport in dat u wilt toevoegen aan de naam van het rapport.
Als u geen voorvoegsel opgeeft, is het standaard voorvoegsel de naam die u voor het rapport hebt opgegeven. Het datumbereik heeft de `/report-name/date-range/`-indeling.
12. Voor **Tijdseenheid** kiest u **Elk uur**.
13. Voor **Rapportversie** kiest u of u wilt dat elke versie van het rapport de vorige versie overschrijft of dat u aanvullende nieuwe rapporten wilt.
14. Voor **Gegevensintegratie voor** is geen selectie vereist.
15. Selecteer **GZIP** voor **compressie**.
16. Selecteer **Next**.
17. Nadat u de instellingen voor uw rapport hebt gecontroleerd, selecteert u **Controleren en voltooien**.

    Noteer de naam van het rapport. U gebruikt deze in latere stappen.

Het kan tot 24 uur duren voordat AWS rapporten aan uw Amazon S3-bucket begint te leveren. Nadat de levering is gestart, werkt AWS de AWS-kosten- en gebruiksrapportbestanden ten minste één keer per dag bij. U kunt doorgaan met het configureren van uw AWS-omgeving zonder te wachten tot de levering wordt gestart.

## <a name="create-a-role-and-policy-in-aws"></a>Een rol en beleid maken in AWS

Azure Cost Management heeft meerdere keren per dag toegang tot de S3-bucket waar het kosten- en gebruiksrapport is opgeslagen. De service moet toegang hebben tot referenties om te controleren op nieuwe gegevens. U maakt een rol en beleid in AWS om Cost Management hiertoe toegang te geven.

Om op rollen gebaseerde toegang tot een AWS-account in Cost Management in te schakelen, wordt de rol gemaakt in de AWS-console. U moet de _ARN-rol_ en _externe id_ van de AWS-console. Later kunt u ze gebruiken op de pagina **Een AWS-connector maken** in Cost Management.

Gebruik de wizard Een nieuwe rol maken:

1. Meld u aan bij de AWS-console op en selecteer **Services**.
2. Selecteer **IAM** in de lijst met services.
3. Selecteer **Rollen** en selecteer vervolgens **Rol maken**.
4. Selecteer op de volgende pagina **Een ander AWS-account**.
5. Voer **432263259397** in **account-ID** in.
6. In **Opties** selecteert u **externe ID vereisen (aanbevolen procedure wanneer een derde partij deze rol gaat gebruiken)** .
7. Voer in **Externe id** de externe id in die een gedeelde wachtwoordcodecode is tussen de rol AWS en Azure Cost Management. Dezelfde externe ID wordt ook gebruikt op de pagina **Nieuwe connector** in Cost Management. Microsoft raadt u aan om een beleid voor sterke wachtwoordcodes te gebruiken bij het invoeren van de externe id.
    > [!NOTE]
    > Wijzig de selectie van **MFA vereisen** niet. Deze moet leeg blijven.
8. Selecteer **Volgende: Permissions**.
9. Selecteer **Beleid maken**. Er wordt een nieuw browsertabblad geopend. Hier maakt u een beleid.
10. Selecteer **Een service kiezen**.

Machtiging voor het Kosten- en gebruiksrapport configureren:

1. Voer **Kosten- en gebruiksrapport** in.
2. Selecteer **Toegangs niveau** > **Lezen** > **DescribeReportDefinitions**. Met deze stap kan Cost Management lezen welke CUR-rapporten zijn gedefinieerd en bepalen of ze overeenkomen met de vereiste voor de rapportdefinitie.
3. Selecteer **Aanvullende machtigingen toevoegen**.

Machtiging voor uw S3-bucket en -objecten configureren:

1. Selecteer **Een service kiezen**.
2. Voer **S3** in.
3. Selecteer **Toegangsniveau** > **Lijst** > **ListBucket**. Met deze actie wordt de lijst met objecten in de S3-bucket opgehaald.
4. Selecteer **Toegangsniveau** > **Lezen** > **GetObject**. Met deze actie kunnen factureringsbestanden worden gedownload.
5. Selecteer **Resources**.
6. Selecteer **Bucket – ARN toevoegen**.
7. Voer in **Bucketnaam** de bucket in die wordt gebruikt voor het opslaan van de CUR-bestanden.
8. Selecteer **Object – ARN toevoegen**.
9. Voer in **Bucketnaam** de bucket in die wordt gebruikt voor het opslaan van de CUR-bestanden.
10. Selecteer in **Objectnaam** **Alle**.
11. Selecteer **Aanvullende machtigingen toevoegen**.

Machtiging voor Cost Explorer configureren:

1. Selecteer **Een service kiezen**.
2. Voer **Cost Explorer Service** in.
3. Selecteer **Alle serviceacties van Cost Explorer (ce:\*)** . Met deze actie wordt gecontroleerd of de verzameling juist is.
4. Selecteer **Aanvullende machtigingen toevoegen**.

Machtiging toevoegen voor AWS-organisaties:

1. Voer **Organisaties** in.
2. Selecteer **Toegangsniveau** > **Lijst** > **ListAccounts**. Met deze actie worden de namen van de accounts opgehaald.
3. Geef bij **Beleid controleren** een naam voor het nieuwe beleid op. Controleer of u de juiste gegevens hebt ingevoerd en selecteer vervolgens **Beleid maken**.
4. Ga terug naar het vorige tabblad en vernieuw de webpagina van uw browser. Zoek in de zoekbalk naar uw nieuwe beleid.
5. Selecteer **Volgende: Review**.
6. Geef een naam op voor de nieuwe rol. Controleer of u de juiste gegevens hebt ingevoerd en selecteer vervolgens **Rol maken**.

    Let op de functie ARN en de externe ID die in de voorgaande stappen worden gebruikt tijdens het maken van de rol. U gebruikt deze later bij het instellen van de Azure Cost Management-connector.

De JSON van het beleid moet eruitzien als in het volgende voorbeeld. Vervang _bucketname_ door de naam van uw S3-bucket.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
             "ce:*",
             "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-connector-for-aws-in-azure"></a>Een nieuwe connector voor AWS instellen in Azure

Gebruik de volgende informatie om een AWS-connector te maken en te beginnen met het bewaken van uw AWS-kosten:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Ga naar de startpagina van Azure door te klikken op **Home** in het menu aan de linkerkant ('hamburger'-menupictogram met 3 lijnen).
3. Ga aan de onderkant van de pagina naar **Hulpprogramma's** > **Cost Management**.
3. Onder **Instellingen** selecteert u **Connectors voor AWS**.  
4. Selecteer **+toevoegen** bovenaan de pagina om een connector te maken.  
    :::image type="content" source="./media/aws-integration-setup-configure/aws-connector.png" alt-text="Voorbeeld met de instelling van Connectors voor AWS" :::
1. Voer op de pagina **Connector maken** in **Weergavenaam** een naam in voor uw connector.  
    :::image type="content" source="./media/aws-integration-setup-configure/create-aws-connector01.png" alt-text="Voorbeeld van de pagina voor het maken van een AWS-connector" :::
1. Selecteer desgewenst de standaardbeheergroep. Alle gedetecteerde gekoppelde accounts worden opgeslagen. U kunt dit later instellen.
1. Stel in de sectie **Facturering** de optie **Automatisch verlengen** in op **Aan** als u deze connector wilt blijven gebruiken. Als u de automatische optie selecteert, moet u een factureringsabonnement selecteren.
1. Voor **Role ARN** voert u de waarde in die u hebt gebruikt bij het instellen van de functie in AWS.
1. Voor **Externe id** voert u de waarde in die u hebt gebruikt bij het instellen van de rol in AWS.
1. Voer voor **Rapportnaam** de naam in die u hebt gemaakt in AWS.
1. Selecteer **Volgende**, en selecteer vervolgens **Maken**.

Het kan enkele uren duren voordat de nieuwe AWS-scopes, het geconsolideerde AWS-account, de gekoppelde AWS-accounts en de bijbehorende kostengegevens worden weergegeven.

Wanneer u de connector hebt gemaakt, wordt u aangeraden er toegangsbeheer aan toe te wijzen. Aan gebruikers zijn machtigingen toegewezen voor de nieuwe gedetecteerde bereiken: Het geconsolideerde AWS-account en het gekoppelde AWS-account. De gebruiker die de connector maakt, is de eigenaar van de connector, het geconsolideerde account en alle gekoppelde accounts.

Het toewijzen van connectormachtigingen aan gebruikers nadat detectie plaatsvindt, wijst geen machtigingen toe voor de bestaande AWS-bereiken. In plaats daarvan worden alleen aan nieuwe gekoppelde accounts machtigingen toegewezen.

## <a name="take-additional-steps"></a>Aanvullende stappen nemen

- [Stel beheergroepen](../../governance/management-groups/overview.md#initial-setup-of-management-groups) in als u dat nog niet hebt gedaan.
- Controleer of er nieuwe scopes zijn toegevoegd aan de bereik-kiezer. Selecteer **Vernieuwen** om de meest recente gegevens weer te geven.
- Selecteer op de pagina **Cloudconnectors** de connector en selecteer **Ga naar de factureringsrekening** om het gekoppelde account aan beheergroepen toe te wijzen.

> [!NOTE]
> Beheergroepen worden momenteel niet ondersteund voor klanten met een Microsoft-klantovereenkomst (MCA). MCA-klanten kunnen de connector maken en hun AWS-gegevens weergeven. MCA-klanten kunnen hun Azure-kosten en AWS-kosten echter niet samen weergeven onder een beheergroep.

## <a name="manage-aws-connectors"></a>AWS-connectors beheren

Wanneer u een connector selecteert op de pagina **Connectors voor AWS**, kunt u het volgende doen:

- Selecteer **Ga naar de factureringsrekening** om informatie weer te geven voor het geconsolideerde AWS-account.
- Selecteer **Toegangsbeheer** om de roltoewijzing voor de connector te beheren.
- Selecteer **Bewerken** om de connector bij te werken. U kunt het AWS-accountnummer niet wijzigen omdat het wordt weergegeven in de rol-ARN. U kunt echter wel een nieuwe connector maken.
- Selecteer **Controleren** om de verificatietest opnieuw uit te voeren om ervoor te zorgen dat Cost Management gegevens kan verzamelen met behulp van de connectorinstellingen.

:::image type="content" source="./media/aws-integration-setup-configure/aws-connector-details.png" alt-text="Voorbeeldgegevens van AWS-connector" :::

## <a name="set-up-azure-management-groups"></a>Azure-beheergroepen instellen

Plaats uw Azure-abonnementen en gekoppelde AWS-accounts in dezelfde beheergroep om één locatie te maken waar u gegevens over de cross-cloud provider kunt zien. Zie [Eerste installatie van beheergroepen](../../governance/management-groups/overview.md#initial-setup-of-management-groups) als u uw Azure-omgeving nog niet hebt geconfigureerd met beheergroepen.

Als u kosten wilt scheiden, kunt u een beheergroep maken die alleen gekoppelde AWS-accounts bevat.

## <a name="set-up-an-aws-consolidated-account"></a>Een geconsolideerd AWS-account instellen

Het geconsolideerde AWS-account combineert facturering en betaling voor meerdere AWS-accounts. Het fungeert ook als een gekoppeld AWS-account. U kunt de detailgegevens voor uw geconsolideerd AWS-account weergeven met behulp van de link op de pagina AWS-connector. 

:::image type="content" source="./media/aws-integration-setup-configure/aws-consolidated-account01.png" alt-text="Voorbeeld van details voor een geconsolideerd AWS-account" :::

Op de pagina kunt u het volgende doen:

- **Update** selecteren om de koppeling van gekoppelde AWS-accounts met een beheergroep bulksgewijs bij te werken.
- **Toegangsbeheer** selecteren om de roltoewijzing voor het bereik in te stellen.

### <a name="permissions-for-an-aws-consolidated-account"></a>Machtigingen voor een geconsolideerd AWS-account

Machtigingen voor een geconsolideerd AWS-account worden standaard ingesteld op basis van de AWS-connectormachtigingen. De maker van de connector is de eigenaar.

U beheert het toegangsniveau met behulp van de pagina **Toegangsniveau** van het geconsolideerde AWS-account. Gekoppelde AWS-accounts nemen echter geen machtigingen over van het geconsolideerde AWS-account.

## <a name="set-up-an-aws-linked-account"></a>Een gekoppeld AWS-account instellen

Het gekoppelde AWS-account is waar AWS-resources worden gemaakt en beheerd. Een gekoppeld account fungeert ook als een beveiligingsgrens.

Vanaf deze pagina kunt u het volgende doen:

- **Update** selecteren om de koppeling van een gekoppeld AWS-account met een beheergroep bij te werken.
- **Toegangsbeheer** selecteren om een roltoewijzing voor het bereik in te stellen.

:::image type="content" source="./media/aws-integration-setup-configure/aws-linked-account01.png" alt-text="Voorbeeld van de pagina Gekoppeld AWS-account" :::

### <a name="permissions-for-an-aws-linked-account"></a>Machtigingen voor een gekoppeld AWS-account

Machtigingen voor een gekoppeld AWS-account worden standaard ingesteld bij het maken, op basis van de AWS-connectormachtigingen. De maker van de connector is de eigenaar. U beheert het toegangsniveau met behulp van de pagina **Toegangsniveau** van het gekoppelde AWS-account. Gekoppelde AWS-accounts nemen echter geen machtigingen over van een geconsolideerde AWS-account.

Gekoppelde AWS-accounts nemen altijd machtigingen over van de beheergroep waarvan ze deel uitmaken.

## <a name="next-steps"></a>Volgende stappen

- Nu u de integratie van AWS-kosten- en gebruiksrapporten hebt ingesteld en geconfigureerd, gaat u door met het [Beheren van AWS-kosten en gebruik](aws-integration-manage.md).
- Als u niet bekend bent met kostenanalyse, raadpleegt u de snelstartgids [Kosten verkennen en analyseren met kostenanalyse](quick-acm-cost-analysis.md).
- En als u niet bekend bent met budgetten in Azure, raadpleegt u [Azure-budgetten maken en beheren](tutorial-acm-create-budgets.md).
