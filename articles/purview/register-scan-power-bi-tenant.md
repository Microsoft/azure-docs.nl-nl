---
title: Een Power BI-Tenant registreren en scannen (preview)
description: Meer informatie over hoe u de Azure controle sfeer liggen-Portal kunt gebruiken om een Power BI-Tenant te registreren en te scannen.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 5f42b5f2b2a7660ee0de975068f64572c470503a
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874903"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Een Power BI-Tenant registreren en scannen (preview)

In dit artikel wordt beschreven hoe u Azure controle sfeer liggen Portal kunt gebruiken om een Power BI-Tenant te registreren en te scannen.

> [!Note]
> Als het controle sfeer liggen-exemplaar en de Power BI Tenant zich in dezelfde Azure-Tenant bevinden, kunt u alleen beheerde identiteits verificatie (MSI) gebruiken om een scan van een Power BI Tenant in te stellen. 

## <a name="create-a-security-group-for-permissions"></a>Een beveiligings groep maken voor machtigingen

Als u verificatie wilt instellen, maakt u een beveiligings groep en voegt u de beheerde identiteit van de catalogus toe.

1. Zoek in het [Azure Portal](https://portal.azure.com)naar **Azure Active Directory**.
1. Maak een nieuwe beveiligings groep in uw Azure Active Directory door te volgen [een basis groep te maken en leden toe te voegen met behulp van Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > U kunt deze stap overs Laan als u al een beveiligings groep hebt die u wilt gebruiken.

1. Selecteer **beveiliging** als het **groeps type**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Type beveiligings groep":::

1. De beheerde identiteit van uw catalogus toevoegen aan deze beveiligings groep. Selecteer **leden** en selecteer vervolgens **+ leden toevoegen**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Voeg het beheerde exemplaar van de catalogus toe aan de groep.":::

1. Zoek de catalogus en selecteer deze.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Catalogus toevoegen door ernaar te zoeken":::

    Er wordt een melding weer gegeven dat u ziet dat deze is toegevoegd.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="MSI-bestand voor catalogus toevoegen":::

## <a name="associate-the-security-group-with-the-tenant"></a>De beveiligings groep koppelen aan de Tenant

1. Meld u aan bij de [Beheer Portal van Power bi](https://app.powerbi.com/admin-portal/tenantSettings).
1. Selecteer de pagina **Tenant instellingen** .

    > [!Important]
    > U moet een Power BI beheerder zijn om de pagina met Tenant instellingen weer te geven.

1. **Beheer-API-instellingen** selecteren service-  >  **principals kunnen alleen-lezen Power bi beheer-api's (preview) worden gebruikt**.
1. Selecteer **specifieke beveiligings groepen**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Afbeelding die laat zien hoe service-principals machtigingen voor alleen-lezen Power BI-beheer-API kunnen krijgen":::

    > [!Caution]
    > Wanneer u de beveiligings groep die u hebt gemaakt (met de door de Data Catalog beheerde identiteit als lid) toestaat om alleen-lezen Power BI-beheer-Api's te gebruiken, kunt u ook toegang krijgen tot de meta gegevens (bijvoorbeeld dash board-en rapport namen, eigen aren, beschrijvingen, enzovoort) voor al uw Power BI artefacten in deze Tenant. Nadat de meta gegevens zijn opgehaald naar de Azure-controle sfeer liggen, worden de machtigingen van controle sfeer liggen, niet Power BI machtigingen, bepalen wie de meta gegevens kunnen zien.

    > [!Note]
    > U kunt de beveiligings groep verwijderen uit uw instellingen voor ontwikkel aars, maar de eerder uitgepakte meta gegevens worden niet verwijderd uit het controle sfeer liggen-account. U kunt deze afzonderlijk verwijderen, indien gewenst.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Uw Power BI registreren en een scan instellen

Nu u de catalogus machtigingen hebt gegeven om verbinding te maken met de beheer-API van uw Power BI-Tenant, kunt u uw Scan instellen vanuit de catalogus Portal.

Voeg eerst een speciale functie markering toe aan uw controle sfeer liggen-URL 

1. Selecteer het pictogram van het **beheer centrum** .

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="Pictogram beheer centrum.":::

1. Selecteer vervolgens **+ Nieuw** op **gegevens bronnen**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="Afbeelding van de knop nieuwe gegevens bron":::

    Selecteer **Power bi** als uw gegevens bron.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Afbeelding van de lijst met beschik bare gegevens bronnen":::

1. Geef uw Power BI-exemplaar een beschrijvende naam.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Afbeelding van Power BI gegevens bron-beschrijvende naam":::

    De naam moet tussen de 3-63 tekens lang zijn en mag alleen letters, cijfers, onderstrepings tekens en afbreek streepjes bevatten.  Spaties zijn niet toegestaan.

    Standaard vindt het systeem de Power BI Tenant die zich in hetzelfde Azure-abonnement bevindt.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI gegevens bron geregistreerd":::

1. Geef een naam op voor de scan. U ziet dat de enige verificatie methode die wordt ondersteund, **beheerde identiteit** is.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Afbeelding van installatie van Power BI scanner":::

    De scan naam moet tussen de 3-63 tekens lang zijn en mag alleen letters, cijfers, onderstrepings tekens en afbreek streepjes bevatten.  Spaties zijn niet toegestaan.

1. Stel een scan trigger in. Uw opties zijn **eenmaal**, **elke 7 dagen** en **om de 30 dagen**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Afbeelding van trigger voor scannen":::

1. Bij **nieuwe scan controleren** selecteert u **opslaan en uitvoeren** om de scan te starten.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Power BI scherm afbeelding opslaan en uitvoeren":::

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)