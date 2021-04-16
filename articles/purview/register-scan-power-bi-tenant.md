---
title: Een tenant voor een Power BI registreren en scannen (preview)
description: Meer informatie over het gebruik van de Azure Purview-portal voor het registreren en scannen van een Power BI tenant.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 6646f131488a5ae4aa9b20fe614d7ebb46133444
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538859"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Een tenant voor een Power BI registreren en scannen (preview)

In dit artikel wordt beschreven hoe u de Azure Purview-portal gebruikt om een tenant Power BI scannen.

> [!Note]
> Als het Purview-exemplaar en de Power BI-tenant zich in dezelfde Azure-tenant, kunt u alleen MSI-verificatie (Beheerde identiteit) gebruiken om een scan van een Power BI tenant in te stellen. 

## <a name="create-a-security-group-for-permissions"></a>Een beveiligingsgroep maken voor machtigingen

Als u verificatie wilt instellen, maakt u een beveiligingsgroep en voegt u de beheerde identiteit Purview hieraan toe.

1. Zoek in [Azure Portal](https://portal.azure.com)naar **Azure Active Directory**.
1. Maak een nieuwe beveiligingsgroep in uw Azure Active Directory door Een basisgroep maken te volgen en leden toe te voegen [met behulp van Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > U kunt deze stap overslaan als u al een beveiligingsgroep hebt die u wilt gebruiken.

1. Selecteer **Beveiliging** als **groepstype.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Type beveiligingsgroep":::

1. Voeg uw door Purview beheerde identiteit toe aan deze beveiligingsgroep. Selecteer **Leden** en selecteer vervolgens **+ Leden toevoegen.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Voeg het beheerde exemplaar van de catalogus toe aan groep.":::

1. Zoek uw beheerde identiteit purview en selecteer deze.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Catalogus toevoegen door er naar te zoeken":::

    Als het goed is, ziet u een melding dat deze is toegevoegd.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Catalogus-MSI toevoegen geslaagd":::

## <a name="associate-the-security-group-with-the-tenant"></a>De beveiligingsgroep koppelen aan de tenant

1. Meld u aan [bij Power BI beheerportal.](https://app.powerbi.com/admin-portal/tenantSettings)
1. Selecteer de **pagina Tenantinstellingen.**

    > [!Important]
    > U moet een beheerder Power BI om de pagina tenantinstellingen te kunnen zien.

1. Selecteer **Beheer-API-instellingen**  >  **Service-principals toestaan om alleen-lezen Power BI beheerders-API's (preview) te gebruiken.**
1. Selecteer **Specifieke beveiligingsgroepen.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Afbeelding waarin wordt getoond hoe service-principals alleen-lezen kunnen krijgen Power BI api-machtigingen voor beheerders":::

    > [!Caution]
    > Wanneer u toestaat dat de beveiligingsgroep die u hebt gemaakt (die uw door Purview beheerde identiteit als lid heeft) alleen-lezen Power BI-beheer-API's gebruikt, geeft u deze ook toegang tot de metagegevens (zoals dashboard- en rapportnamen, eigenaren, beschrijvingen enzovoort) voor al uw Power BI-artefacten in deze tenant. Nadat de metagegevens zijn binnengehaald in Azure Purview, bepalen de machtigingen van Purview, niet Power BI machtigingen, wie die metagegevens kan zien.

    > [!Note]
    > U kunt de beveiligingsgroep verwijderen uit uw instellingen voor ontwikkelaars, maar de eerder geëxtraheerde metagegevens worden niet verwijderd uit het Purview-account. U kunt deze indien nodig afzonderlijk verwijderen.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Uw Power BI registreren en een scan instellen

Nu u de machtigingen voor Beheerde identiteit voor Purview hebt opgegeven om verbinding te maken met de beheer-API van uw Power BI-tenant, kunt u de scan instellen vanuit Azure Purview Studio.

1. Selecteer het **pictogram Beheercentrum.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="Pictogram beheercentrum.":::

1. Selecteer vervolgens **+ Nieuw** in **Gegevensbronnen.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="Afbeelding van de knop Nieuwe gegevensbron":::

    Selecteer **Power BI** als uw gegevensbron.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Afbeelding met de lijst met gegevensbronnen die u kunt kiezen":::

3. Geef uw Power BI een gebruiksvriendelijke naam.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Afbeelding van Power BI naam die gebruiksvriendelijk is voor de gegevensbron":::

    De naam moet tussen de 3 en 63 tekens lang zijn en mag alleen letters, cijfers, onderstrepingstekens en afbreekstreepingstekens bevatten.  Spaties zijn niet toegestaan.

    Standaard vindt het systeem de Power BI tenant die in hetzelfde Azure-abonnement bestaat.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI geregistreerde gegevensbron":::

    > [!Note]
    > Voor Power BI is registratie en scan van gegevensbron toegestaan voor slechts één exemplaar.


4. Geef een naam op voor de scan. Selecteer vervolgens de optie om de persoonlijke werkruimten op te nemen of uit te sluiten. U ziet dat beheerde identiteit de enige **ondersteunde verificatiemethode is.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Afbeelding van Power BI scaninstallatie":::

    > [!Note]
    > * Als u de configuratie van een scan om een persoonlijke werkruimte op te nemen of uit te sluiten overschakelt, wordt een volledige scan van de PowerBI-bron uitgevoerd
    > * De scannaam moet tussen de 3 en 63 tekens lang zijn en mag alleen letters, cijfers, onderstrepingstekens en afbreekstreepingstekens bevatten. Spaties zijn niet toegestaan.

5. Stel een scantrigger in. Uw opties zijn **Eenmaal,** **Om de 7 dagen** en Elke **30 dagen.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Afbeelding van scantrigger":::

6. Selecteer **in Nieuwe scan controleren** de optie Opslaan en uitvoeren **om** de scan te starten.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Een schermafbeelding opslaan Power BI uitvoeren":::

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)
