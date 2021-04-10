---
title: ABAP-functie module voor het uitpakken van meta gegevens in SAP R3-Azure controle sfeer liggen
description: Dit artikel bevat een overzicht van de stappen voor het implementeren van de ABAP-functie module in SAP server
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 9bd3c315fcc15317a9fa483289fdc326ca6aa47f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614357"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>De ABAP-functie module voor het uitpakken van meta gegevens implementeren voor de SAP R3-familie van bruggen

In dit artikel vindt u een overzicht van de stappen voor het implementeren van de functie module ABAP in SAP server.

## <a name="overview"></a>Overzicht

SAP Business Suite 4 HANA (S/4HANA), ECC en R/3 ERP Bridge kan worden gebruikt om meta gegevens van de SAP-server op te halen. Dit wordt bereikt door de ABAP-functie module op de SAP-server te plaatsen. Deze functie module is op afstand toegankelijk voor de Bridge voor het opvragen en downloaden (als een tekst bestand) de meta gegevens die zich in de SAP-server bevindt.

Wanneer de Bridge wordt uitgevoerd, dan:

1. Hiermee worden meta gegevens uit een bestaand bestand geïmporteerd dat al lokaal is gedownload van een eerdere Bridge-uitvoering.

2. Hiermee wordt de ABAP-module-API aangeroepen, wordt gewacht op downloaden en worden vervolgens meta gegevens uit dat bestand geïmporteerd.

In dit document worden de stappen beschreven die nodig zijn voor het implementeren van deze module.

> [!Note]
> De volgende instructies zijn gecompileerd op basis van SAP GUI v. 7.2

## <a name="deployment-of-the-module"></a>Implementatie van de module

### <a name="create-a-package"></a>Een pakket maken

Deze stap is optioneel en een bestaand pakket kan worden gebruikt.

1. Meld u aan bij de SAP S/4HANA of SAP ECC-server en open **object Navigator** (SE80-trans actie).

2. Selecteer optie **pakket** in de lijst en voer een naam in voor het nieuwe pakket (bijvoorbeeld Z \_ MITI) en druk vervolgens op knop **weergave**.

3. Selecteer **Ja** in het venster **pakket maken** . Daarom wordt er een venster **pakket Builder: Create-pakket** geopend. Voer een waarde in het veld **korte beschrijving** in en selecteer het pictogram **door gaan** .

4. Selecteer **eigen aanvragen** in het venster **prompt voor lokale Workbench-aanvraag** . Selecteer een **ontwikkelings** aanvraag.

### <a name="create-a-function-group"></a>Een functie groep maken

Selecteer in object Navigator de **functie groep** in de lijst en typ de naam in het onderstaande veld voor invoer (bijvoorbeeld Z \_ MITI \_ FGROUP). Selecteer het **weergave** pictogram.

1. Selecteer in het venster **object maken** de optie **Ja** om een nieuwe functie groep te maken.

2. Geef in het **korte tekst** veld een passende beschrijving op en druk op de knop **Opslaan**.

3. Kies een pakket dat in de vorige stap is voor bereid **een pakket maken** en selecteer **Opslaan**.

4. Bevestig een aanvraag door **door** te drukken op het pictogram.

5. Activeer de functie groep.

### <a name="create-the-abap-function-module"></a>De functie module ABAP maken

1. Wanneer de functie groep is gemaakt, selecteert u deze.

2. Klik met de rechter muisknop op de naam van de functie groep in de browser van de opslag plaats en selecteer **maken** en vervolgens **functie module**.

3. Voer in het veld **functie module** in `Z_MITI_DOWNLOAD` . Vul de **korte tekst** invoer met de juiste beschrijving in.

Wanneer de module is gemaakt, geeft u de volgende informatie op:

1. Navigeer naar het tabblad **Attributes** .

2. Selecteer **verwerkings type** als **functie module met externe** functionaliteit.

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="Bron optie registreren-Remote-Enabled functie module" border="true":::

3. Navigeer naar het tabblad **bron code** . Er zijn twee manieren om code te implementeren voor de functie:

   a. Upload in het hoofd menu het tekst bestand [Z MITI-bestand \_ \_ downloaden](https://github.com/Azure/Purview-Samples/tree/master/connectors/sap) . Hiertoe selecteert u **Hulpprogram ma's**, **meer Hulpprogram ma's**, **uploaden/downloaden** en vervolgens **uploaden**.

   b. U kunt ook het bestand openen, de inhoud ervan kopiëren en plakken in het **bron code** gebied.

4. Ga naar het tabblad **importeren** en maak de volgende para meters:

   a.  P \_ -gebieds type DD02L-TABNAME (optioneel = True)

   b.  *P \_ \_Teken reeks voor het type lokale pad* (optioneel = True)

   c.  *P \_ -taal type L001TAB-data standaard \' E\'*

   d.  *ROWSKIPS TYPE dus \_ int standaard 0*

   e.  *ROWCOUNT-TYPE dus \_ int standaard 0*

   > [!Note]
   > Kies een **door Geef** voor al deze waarden

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="Bronnen registreren optie-para meters importeren" border="true":::

5. Ga naar het tabblad Tables en definieer het volgende:

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="Bronnen opties registreren-tabblad tabellen" border="true":::

6. Ga naar het tabblad **uitzonde ringen** en definieer de volgende uitzonde ring: `E_EXP_GUI_DOWNLOADFAILED`

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="Bronnen opties registreren-tabblad uitzonde ringen" border="true":::

7. Sla de functie op (druk op CTRL + S of kies **functie module** en vervolgens **Opslaan** in het hoofd menu).

8. Klik op het pictogram **activeren** op de werk balk (CTRL + F3) en selecteer de knop  **door gaan** in het dialoog venster. Als u hierom wordt gevraagd, moet u de gegenereerde includes selecteren om samen met de functie module te activeren.

### <a name="testing-the-function"></a>De functie testen

Wanneer alle vorige stappen zijn voltooid, volgt u de onderstaande stappen om de functie te testen:

1. Open de Z \_ MITI- \_ functie module voor downloaden.

2. Kies **functie module**, vervolgens **testen** en **test functie module** in het hoofd menu (of druk op F8).

3. Geef een pad op naar de map op het lokale bestands systeem in para meter P \_ lokaal \_ pad en druk op het pictogram **uitvoeren** op de werk balk (of druk op F8).

4. Plaats de naam van het gedeelte van de interesse in het veld P- \_ gebied als een bestand met meta gegevens moet worden gedownload of bijgewerkt. Wanneer de functie is voltooid, moet de map die is aangegeven in de \_ para meter voor het lokale pad, een \_ aantal bestanden met meta gegevens bevatten. De namen van bestanden worden aangeduid met gebieden die in het veld P-gebied kunnen worden opgegeven \_ .

De uitvoering van de functie wordt voltooid en meta gegevens worden veel sneller gedownload in het geval van het starten op de computer met een snelle netwerk verbinding met SAP S/4HANA of ECC server.

## <a name="next-steps"></a>Volgende stappen

- [SAP ECC-bron registreren en scannen](register-scan-sapecc-source.md)
- [SAP S/4HANA-bron registreren en scannen](register-scan-saps4hana-source.md)
