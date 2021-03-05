---
title: Een aangepaste classificatie en classificatie regel maken (preview)
description: In dit artikel wordt beschreven hoe u aangepaste classificaties kunt maken om gegevens typen te definiëren in uw gegevens die uniek zijn voor uw organisatie. Ook wordt het maken van aangepaste classificatie regels beschreven waarmee u opgegeven gegevens in uw eigen gegevens kunt vinden.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/5/2021
ms.openlocfilehash: d1a0873552ac9043d8f584f38ecd41c5e8543489
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202754"
---
# <a name="custom-classifications-in-azure-purview"></a>Aangepaste classificaties in azure controle sfeer liggen 

In dit artikel wordt beschreven hoe u aangepaste classificaties kunt maken om gegevens typen te definiëren in uw gegevens die uniek zijn voor uw organisatie. Ook wordt het maken van aangepaste classificatie regels beschreven waarmee u opgegeven gegevens in uw eigen gegevens kunt vinden.

## <a name="default-classifications"></a>Standaard classificaties

De Azure controle sfeer liggen Data Catalog biedt een groot aantal standaard classificaties die typische persoons gegevens typen vertegenwoordigen die u mogelijk in uw data-erfgoed hebt.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="classificatie selecteren" border="true":::

U kunt ook aangepaste classificaties maken als een van de standaard classificaties niet aan uw behoeften voldoet.

## <a name="steps-to-create-a-custom-classification"></a>Stappen voor het maken van een aangepaste classificatie

Als u een aangepaste classificatie wilt maken, gaat u als volgt te werk:

1. Selecteer in uw catalogus **beheer centrum** in het menu links.

2. Selecteer **classificaties** onder **meta gegevens beheer**.

3. Selecteer **+ Nieuw**

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="Nieuwe classificatie" border="true":::

Het deel venster **nieuwe classificatie toevoegen** wordt geopend, waarin u een naam en beschrijving voor de classificatie kunt opgeven. Het is raadzaam een Conventie voor naam afstand te gebruiken, zoals `your company name.classification name` .
De systeem classificaties van micro soft worden gegroepeerd onder de gereserveerde `MICROSOFT.` naam ruimte. Een voor beeld hiervan is **micro soft. Government. VS. Sofi \_ - \_ nummer**.

De naam van uw classificatie moet beginnen met een letter gevolgd door een reeks letters, cijfers en punten (.) of onderstrepings tekens.
Er zijn geen spaties toegestaan. Terwijl u typt, genereert de UX automatisch een beschrijvende naam. Deze beschrijvende naam is wat gebruikers te zien krijgen wanneer u deze toepast op een asset in de catalogus.

Om de naam kort te blijven, maakt het systeem de beschrijvende naam op basis van de volgende logica:

- Alle, behalve de laatste twee segmenten van de naam ruimte, worden afgekapt.

- De behuizing wordt aangepast zodat de eerste letter van elk woord wordt gekapitaliseerd. Alle andere letters worden omgezet in kleine letters.

- Alle onderstrepings tekens ( \_ ) worden vervangen door spaties.

Als u bijvoorbeeld uw classificatie **CONTOSO.HR heet. WERK NEMER \_ -id**, de beschrijvende naam wordt opgeslagen in het systeem als **HR. werk nemer-id**.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

Selecteer **OK** om de nieuwe classificatie toe te voegen aan de lijst met classificaties.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="Aangepaste classificatie" border="true":::

Als u de classificatie in de lijst selecteert, wordt de pagina classificatie Details geopend. Hier vindt u alle informatie over de classificatie.
Deze informatie omvat het aantal exemplaren dat er is, de formele naam, de bijbehorende classificatie regels (indien van toepassing) en de naam van de eigenaar.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="Classificatie selecteren" border="true":::

## <a name="custom-classification-rules"></a>Aangepaste classificatie regels

De catalogus service biedt een aantal standaard classificatie regels die door de scanner worden gebruikt voor het automatisch detecteren van bepaalde gegevens typen. U kunt ook uw eigen aangepaste classificatie regels toevoegen om andere typen gegevens te detecteren die u mogelijk wilt zoeken in uw gegevens. Deze mogelijkheid kan zeer krachtig zijn wanneer u \' gegevens in uw gegevens onroerend goed wilt vinden.

\'Stel dat een bedrijf met de naam contoso werk nemer-id's heeft die zijn gestandaardiseerd in het hele bedrijf met het woord \" werk nemer \" gevolgd door een GUID voor het maken van werk nemer {GUID}. Een exemplaar van een werk nemer-ID ziet er bijvoorbeeld uit zoals EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55.

Contoso kan het Scan systeem zo configureren dat er instanties van deze Id's worden gevonden door een aangepaste classificatie regel te maken. Ze kunnen in dit geval een reguliere expressie opgeven die overeenkomt met het gegevens patroon `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` . Als de gegevens doorgaans zich in een kolom bevinden waarvan ze de naam van hebben, zoals werk nemer \_ -id of EmployeeID, kunnen ze een reguliere expressie voor kolom patroon toevoegen om de scan nog nauw keuriger te maken. Een voor beeld van een reguliere expressie is werk nemer \_ -id \| .

Het Scan systeem kan deze regel vervolgens gebruiken om de werkelijke gegevens in de kolom en de naam van de kolom te controleren om te proberen om elke instantie van waar het werk nemer-ID-patroon wordt gevonden.

## <a name="steps-to-create-a-custom-classification-rule"></a>Stappen voor het maken van een aangepaste classificatie regel

Een aangepaste classificatie regel maken:

1. Maak een aangepaste classificatie door de instructies in de bovenstaande sectie te volgen. U voegt deze aangepaste classificatie toe aan de classificatie regel configuratie, zodat deze door het systeem wordt toegepast wanneer een overeenkomst in de kolom wordt gevonden.

2. Selecteer het pictogram van het **beheer centrum** .

3. Selecteer de sectie **classificatie regels** .

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classificationrules.png" alt-text="Tegel van classificatie regels" border="true":::

4. Selecteer **Nieuw**.

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/newclassificationrule.png" alt-text="Nieuwe classificatie regel toevoegen" border="true":::

5. Het dialoog venster **nieuwe classificatie regel** wordt geopend. Vul de velden in en beslis of u een **reguliere expressie regel** of een **woordenlijst regel** wilt maken.

    |Veld     |Beschrijving  |
    |---------|---------|
    |Name   |    Vereist. De maximum waarde is 100 tekens.    |
    |Beschrijving      |Optioneel. De maximum waarde is 256 tekens.    |
    |Classificatie naam    | Vereist. Selecteer de naam van de classificatie in de vervolg keuzelijst om de scanner te laten Toep assen als er een overeenkomst wordt gevonden.        |
    |Staat   |  Vereist. De opties worden in-of uitgeschakeld. Ingeschakeld is de standaard instelling.    |

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-classification-rule.png" alt-text="Nieuwe classificatie regel maken" border="true":::

### <a name="creating-a-regular-expression-rule"></a>Een regel voor een reguliere expressie maken

1. Als u een regel voor een reguliere expressie maakt, wordt het volgende scherm weer gegeven. U kunt eventueel een bestand uploaden dat wordt gebruikt om **voorgestelde regex-patronen** voor uw regel te genereren.

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-regex-rule.png" alt-text="Nieuwe regex-regel maken" border="true":::

1. Als u besluit een voorgesteld patroon voor regex te genereren, selecteert u na het uploaden van een bestand een van de voorgestelde patronen en klikt u op **toevoegen aan patronen** om de voorgestelde gegevens en kolom patronen te gebruiken. U kunt de voorgestelde patronen aanpassen of ook uw eigen patronen typen zonder een bestand te uploaden.

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/suggested-regex.png" alt-text="Voorgestelde regex genereren" border="true":::

    |Veld     |Beschrijving  |
    |---------|---------|
    |Gegevens patroon    |Optioneel. Een reguliere expressie die de gegevens vertegenwoordigt die zijn opgeslagen in het gegevens veld. De limiet is zeer groot. In het vorige voor beeld testen de gegevens patronen voor een werk nemer-ID die letterlijk het woord is `Employee{GUID}` .  |
    |Kolom patroon    |Optioneel. Een reguliere expressie die de kolom namen vertegenwoordigt die u wilt zoeken. De limiet is zeer groot.          |

1. Onder **gegevens patroon** zijn er twee drempel waarden die u kunt instellen:

    - **Drempel waarde voor DISTINCT**: het totale aantal afzonderlijke gegevens waarden dat in een kolom moet worden gevonden voordat de scanner het gegevens patroon erop uitvoert. De voorgestelde waarde is 8. Deze waarde kan hand matig worden aangepast in een bereik van 2 tot en met 32. Het systeem vereist deze waarde om ervoor te zorgen dat de kolom voldoende gegevens bevat voor de scanner om deze nauw keurig te classificeren. Een kolom die bijvoorbeeld meerdere rijen bevat die alle de waarde 1 bevatten, wordt niet geclassificeerd. Kolommen die één rij met een waarde bevatten en de rest van de rijen hebben null-waarden, worden ook niet geclassificeerd. Als u meerdere patronen opgeeft, geldt deze waarde voor elk patroon.

    - **Drempel waarde voor minimum overeenkomst**: u kunt deze instelling gebruiken om het minimum percentage van de overeenkomende gegevens waarden in een kolom op te stellen die moeten worden gevonden door de scanner zodat de classificatie kan worden toegepast. De voorgestelde waarde is 60%. U moet voorzichtig zijn met deze instelling. Als u het niveau onder 60% verlaagt, kunt u onwaare positieve classificaties in uw catalogus introduceren. Als u meerdere gegevens patronen opgeeft, wordt deze instelling uitgeschakeld en wordt de waarde vastgesteld op 60%.

1. U kunt nu uw regel controleren en deze **maken** .
    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/verify-rule.png" alt-text="Controleer de regel voordat u deze maakt" border="true":::

### <a name="creating-a-dictionary-rule"></a>Een woordenlijst regel maken

1.  Als u een woordenlijst regel maakt, wordt het volgende scherm weer gegeven. Upload een bestand dat alle mogelijke waarden bevat voor de classificatie die u in één kolom maakt.

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-rule.png" alt-text="Woordenlijst regel maken" border="true":::

1.  Nadat de woorden lijst is gegenereerd, kunt u de drempel waarden voor DISTINCT match en minimum match aanpassen en de regel verzenden.

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Woordenlijst regel maken" border="true":::

## <a name="next-steps"></a>Volgende stappen

Nu u de classificatie regel hebt gemaakt, kunt u deze toevoegen aan een scan regel set, zodat de scan de regel gebruikt bij het scannen. Zie [een set met scan regels maken](create-a-scan-rule-set.md)voor meer informatie.
