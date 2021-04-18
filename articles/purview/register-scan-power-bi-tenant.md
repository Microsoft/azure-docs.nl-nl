---
title: Een tenant voor een Power BI registreren en scannen (preview)
description: Meer informatie over het gebruik van de Azure Purview-portal voor het registreren en scannen van een Power BI tenant.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 8fb4c797df7961726ca785a56a6ab25807999842
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600859"
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

1. Meld u aan [bij Power BI-beheerportal.](https://app.powerbi.com/admin-portal/tenantSettings)
1. Selecteer de **pagina Tenantinstellingen.**

    > [!Important]
    > U moet een beheerder zijn Power BI de pagina tenantinstellingen te zien.

1. Selecteer **Beheer-API-instellingen**  >  **Service-principals toestaan alleen-lezen te gebruiken Power BI beheerders-API's (preview)**.
1. Selecteer **Specifieke beveiligingsgroepen.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Afbeelding waarin wordt getoond hoe service-principals alleen-lezen kunnen krijgen Power BI api-machtigingen voor beheerders":::

    > [!Caution]
    > Wanneer u toestaat dat de beveiligingsgroep die u hebt gemaakt (die uw door Purview beheerde identiteit als lid heeft) alleen-lezen Power BI-beheer-API's gebruikt, geeft u deze ook toegang tot de metagegevens (zoals dashboard- en rapportnamen, eigenaren, beschrijvingen enzovoort) voor al uw Power BI-artefacten in deze tenant. Zodra de metagegevens zijn binnengehaald in Azure Purview, bepalen de machtigingen van Purview, niet Power BI machtigingen, wie die metagegevens kan zien.

    > [!Note]
    > U kunt de beveiligingsgroep verwijderen uit uw instellingen voor ontwikkelaars, maar de eerder geëxtraheerde metagegevens worden niet verwijderd uit het Purview-account. U kunt deze afzonderlijk verwijderen, als u wilt.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Uw Power BI registreren en een scan instellen

Nu u de machtigingen van Purview Managed Identity hebt gegeven om verbinding te maken met de beheer-API van uw Power BI-tenant, kunt u de scan instellen vanuit Azure Purview Studio.

1. Selecteer bronnen **in** het linkernavigatievenster.

1. Selecteer vervolgens **Registreren**.

    Selecteer **Power BI** als uw gegevensbron.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Afbeelding met de lijst met gegevensbronnen die u kunt kiezen":::

3. Geef uw Power BI-exemplaar een gebruiksvriendelijke naam.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Afbeelding van Power BI naam die gebruiksvriendelijk is voor de gegevensbron":::

    De naam moet tussen de 3 en 63 tekens lang zijn en mag alleen letters, cijfers, onderstrepingstekens en afbreekstreepingstekens bevatten.  Spaties zijn niet toegestaan.

    Standaard vindt het systeem de Power BI tenant die in hetzelfde Azure-abonnement bestaat.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI gegevensbron geregistreerd":::

    > [!Note]
    > Voor Power BI is registratie en scan van gegevensbron toegestaan voor slechts één exemplaar.


4. Geef uw scan een naam. Selecteer vervolgens de optie om de persoonlijke werkruimten op te nemen of uit te sluiten. U ziet dat beheerde identiteit de enige verificatiemethode is **die wordt ondersteund.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Afbeelding van de Power BI scaninstallatie":::

    > [!Note]
    > * Als u de configuratie van een scan inschakelt om een persoonlijke werkruimte op te nemen of uit te sluiten, wordt een volledige scan van de PowerBI-bron uitgevoerd
    > * De scannaam moet tussen de 3 en 63 tekens lang zijn en mag alleen letters, cijfers, onderstrepingstekens en afbreekstreepingstekens bevatten. Spaties zijn niet toegestaan.

5. Stel een scantrigger in. Uw opties zijn **Eenmaal,** **Om de 7 dagen** en Elke **30 dagen.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Afbeelding van scantrigger":::

6. Selecteer **opslaan en uitvoeren bij Nieuwe scan controleren** **om** de scan te starten.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Afbeelding van Power BI scherm opslaan en uitvoeren":::

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)
