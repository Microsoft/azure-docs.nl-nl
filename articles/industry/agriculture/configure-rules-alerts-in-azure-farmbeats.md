---
title: Regels configureren en waarschuwingen beheren
description: Hierin wordt beschreven hoe u regels configureert en waarschuwingen beheert in FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: a04f973cbfa3a68016065f50e9e2ff4f7566da94
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182924"
---
# <a name="configure-rules-and-manage-alerts"></a>Regels configureren en waarschuwingen beheren

Met Azure FarmBeats kunt u regels maken op basis van de bedrijfs logica, naast de sensor gegevens die stromen van de Sens oren en apparaten die in uw farm zijn geïmplementeerd. Met de regels worden waarschuwingen in het systeem geactiveerd wanneer de sensor waarden een drempel waarde overschrijden. Door de waarschuwingen weer te geven en te analyseren die na de drempel waarden zijn gemaakt, kunt u snel op problemen reageren en de vereiste oplossingen ophalen.

## <a name="create-rule"></a>Regel maken

1. Ga op de start pagina naar **regels**.
2. Selecteer **nieuwe regel**. Het venster nieuwe regel wordt weer gegeven.

    ![Scherm opname van de knop nieuwe regel en de sectie nieuwe regel.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Voer de **naam** van de regel en de **regel beschrijving** in en selecteer vervolgens een farm in de vervolg keuzelijst **Farm selecteren** .
4. Typ de naam van uw farm om de sectie Farm en **voor waarden** te selecteren in hetzelfde venster.  

    ![Scherm afbeelding die de sectie voor waarden markeert.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. Voer in **voor waarden** de waarden voor **meet** waarde, **operator** en **waarde** in.
6. Typ de naam van de meting in de vervolg keuzelijst **meting** .
7. Selecteer **+ voor waarde toevoegen** om meer voor waarden toe te voegen aan de regel.
8. Selecteer het **Ernst niveau**.
9. In **actie** gaat u naar de wissel knop voor **e-mail ingeschakeld** om e-mail waarschuwingen in te scha kelen.

    ![Scherm afbeelding met de optie E-mail ingeschakeld.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Voer de **e-mail adressen** in waarnaar u de e-mail melding wilt verzenden, samen met het onderwerp van de **E-mail** en **aanvullende notities**.  
11. Ga in de **regel status** naar de **ingeschakelde** wissel knop om de regel in of uit te scha kelen.
    U kunt het aantal apparaten weer geven waarop de regel van toepassing is.
12. Selecteer **Toep assen** om de regel te maken.

## <a name="view-rule"></a>Regel weer geven

Op de **Farm** pagina wordt de lijst met beschik bare regels weer gegeven. Selecteer een **regel naam**. Er wordt een venster weer gegeven met de volgende details die van toepassing zijn op de geselecteerde regel:
 - Regelnaam
 - Koppeling naar de farm waaraan de regel is gekoppeld
 - Gemaakt op
 - Datum laatst bijgewerkt
 - Ernstniveau
 - Regel status
 - Lijst met voor waarden  
 - Aantal apparaten waarop de regel betrekking heeft

    ![Scherm opname van het venster met regel Details.](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Regel bewerken

Voer de volgende stappen uit om een regel te bewerken:

1. Selecteer op de start pagina **regels** in het navigatie menu links.
   Het venster regels wordt weer gegeven.
2. Selecteer de regel die u wilt bewerken.

    ![Scherm opname van de geselecteerde regel.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Selecteer **bewerken** in de actie balk, het venster **regel bewerken** wordt weer gegeven.

    ![Scherm afbeelding van het venster regel bewerken.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Wijzig de **naam** van de regel en **regel beschrijving** en selecteer vervolgens een farm in de vervolg keuzelijst **Farm selecteren** .
5. Typ de naam van uw farm om de farm en de **voor waarden** in hetzelfde venster te selecteren.  
6. Bewerk **meting**, **operator** en **waarde** in **voor waarden**.
7. Typ de naam van de meting in de vervolg keuzelijst **meting** .
8. Selecteer **+ voor waarde toevoegen** om voor waarden toe te voegen/te bewerken aan de regels.

    ![Scherm afbeelding met de knop voor waarde toevoegen.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Selecteer het **Ernst niveau**.  
10. In **actie** gaat u naar de wissel knop voor **e-mail ingeschakeld** om e-mail waarschuwingen in te scha kelen.
11. Bewerk de **e-mail adressen** waarnaar u de e-mail melding wilt verzenden, samen met het onderwerp van de **E-mail** en **aanvullende notities**.  
12. Ga in de **regel status** naar de **ingeschakelde** wissel knop om de regel in of uit te scha kelen.
U kunt het aantal apparaten weer geven waarop deze regel van toepassing is.
13. Selecteer **Toep assen** om de regel te bewerken.

## <a name="change-rule-status"></a>Status van regel wijzigen

Voer de volgende stappen uit om de status van een regel te wijzigen:

1. Selecteer op de start pagina **regels** in het navigatie menu links. Het venster regels wordt weer gegeven.
2. Selecteer de regel waarvoor u de status wilt wijzigen.

    ![Scherm opname van de knop wijzigings status.](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Selecteer **status wijzigen** op de actie balk. Het venster **wijzigings status** wordt weer gegeven.

    ![Scherm opname van het scherm voor de wijzigings status.](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Wijzig de regel status met behulp van de wissel knop **wijzigings status** .
   U kunt het aantal apparaten weer geven waarop de regel van toepassing is.
4. Selecteer **Toep assen** om de status van de regel te wijzigen.

## <a name="delete-rule"></a>Regel verwijderen

Voer de volgende stappen uit om een regel te verwijderen:

1. Selecteer op de start pagina **regels** in het navigatie menu links. Het venster regels wordt weer gegeven.
2. Selecteer de regel die u wilt verwijderen.

    ![Scherm afbeelding die de knop verwijderen markeert.](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Selecteer **verwijderen** in de actie balk.

    ![Een Farm Beats-project](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. Het dialoog venster **regel verwijderen** wordt weer gegeven. Selecteer **Verwijderen**.
