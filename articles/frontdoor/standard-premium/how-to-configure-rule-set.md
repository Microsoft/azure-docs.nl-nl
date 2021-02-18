---
title: 'Azure front-deur: regel voor front-deur configureren'
description: Dit artikel bevat richt lijnen voor het configureren van een regelset.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: e2fe475b171a99ec27ed162511db289891066e00
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098880"
---
# <a name="configure-a-rule-set"></a>Een regelset configureren

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

In deze zelf studie ziet u hoe u een regelset en uw eerste set regels maakt in de Azure Portal. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Configureer de regelset met behulp van de portal.
> - Regel instellingen uit uw AFD-profiel verwijderen via de portal

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Voordat u de stappen in deze zelf studie kunt volt ooien, moet u eerst een Azure front-deur standaard/Premium maken. Zie [Quick Start: een Azure front deur Standard/Premium-profiel maken](create-front-door-portal.md)voor meer informatie.

## <a name="configure-rule-set-in-azure-portal"></a>Regel instellingen in Azure Portal configureren

1. Selecteer in het voorste deur profiel de optie **regel sets** onder **instellingen**. Selecteer **toevoegen** en geef deze de naam van de regelset.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="Scherm opname van de pagina voor de land instelling van de regel.":::
    
1. Selecteer **regel toevoegen** om uw eerste regel te maken. Geef een regel naam op. Selecteer vervolgens **voor waarde toevoegen** of **actie toevoegen** om uw regel te definiëren. U kunt Maxi maal 10 voor waarden en vijf acties voor één regel toevoegen. In dit voor beeld gebruiken we server variabele om een antwoord header toe te voegen 8Geo-land * voor aanvragen die *Contoso* bevatten in de URL.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="Scherm afbeelding van de configuratie pagina voor de regel instellingen.":::
    
    > [!NOTE]
    > * Als u een voor waarde of actie van een regel wilt verwijderen, kunt u aan de rechter kant van de specifieke voor waarde of actie de prullen mand gebruiken.
    > * Als u een regel wilt maken die van toepassing is op alle binnenkomend verkeer, geeft u geen voorwaarden op.
    > * Als u wilt stoppen met het evalueren van de resterende regels als aan een specifieke regel wordt voldaan, raadpleegt u **stoppen met evalueren van resterende regel**. Als deze optie is ingeschakeld en alle resterende regels in de regelset niet worden uitgevoerd, ongeacht of aan de voor waarden van de overeenkomst is voldaan.  

1. U kunt de prioriteit van de regels in de regel set bepalen met behulp van de pijl knoppen om de regels hoger of lager te plaatsen. De lijst is in oplopende volg orde, waardoor de belangrijkste regel het eerst wordt vermeld.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="Scherm opname van de prioriteit van de regel instellingen." lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. Nadat u een of meer regels hebt gemaakt, selecteert u **Opslaan** om het maken van de regelset te volt ooien.

1. Koppel nu de regel die is ingesteld op een route zodat deze kan worden toegepast. U kunt de regels koppelen via de pagina regel instellingen of u kunt naar de eindpunt beheerder gaan om de koppeling te maken.
 
    **Pagina regel instellingen**: 
    
    1. Selecteer de regel die moet worden gekoppeld.
    
    1. Selecteer de koppeling *die niet is gekoppeld* .
     

    1. Selecteer vervolgens op de Blade **een route koppelen** het eind punt en de route die u wilt koppelen aan de regelset. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="Scherm afbeelding van een route pagina maken.":::    
        
    1. Klik op *volgende* om regel reeks orders te wijzigen als er meerdere regel sets onder de geselecteerde route zijn. De regel instellingen worden van boven naar beneden uitgevoerd. U kunt de volg orde van de orders wijzigen door de regelset te selecteren en omhoog of omlaag te verplaatsen. Selecteer vervolgens *koppelen*.
    
        > [!Note]
        > U kunt slechts één regelset koppelen aan één route op deze pagina. Als u een regelset met meerdere routes wilt koppelen, gebruikt u eindpunt beheerder.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="Scherm opname van orders van regel sets.":::
    
    1. De regelset is nu gekoppeld aan een route. U kunt de reactie header bekijken en het geo-land toevoegen.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="Scherm afbeelding van de regel die aan een route is gekoppeld.":::

   **Eindpunt beheerder**: 
    
    1. Ga naar eindpunt beheer en selecteer het eind punt dat u wilt koppelen aan de regelset.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="Scherm opname van het selecteren van een eind punt in Endpoint Manager." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. Klik op *eind punt bewerken*  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="Scherm opname van het selecteren van het eind punt bewerken in Endpoint Manager." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. Klik op de route. 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="Scherm opname van het selecteren van een route.":::
    
    1. Selecteer in de Blade *route bijwerken* in *regels* de regel sets die u wilt koppelen aan de route uit de vervolg keuzelijst. Vervolgens kunt u de orders wijzigen door de regel instellingen omhoog en omlaag te verplaatsen. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="Scherm afbeelding van de pagina een route bijwerken.":::
    
    1. Selecteer vervolgens *bijwerken* of *toevoegen* om de koppeling te volt ooien.

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>Een regelset verwijderen uit uw Azure front deur-profiel

In de voor gaande stappen hebt u een regel ingesteld en gekoppeld aan de route. Als u de regelset die aan uw voor deur is gekoppeld niet meer wilt, kunt u de regelset verwijderen door de volgende stappen uit te voeren:

1. Ga naar de **pagina regelset** onder **instellingen** om de regelset van alle gekoppelde routes te ontkoppelen.

1. Vouw de route uit en klik op de drie puntjes om *de route bewerken* te selecteren.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="Scherm opname van uitgevouwen route in regel reeks.":::

1. Ga naar de sectie regels op de pagina route, selecteer de regelset en selecteer op de knop *verwijderen* . 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="Scherm afbeelding van de pagina route bijwerken om een regelset te verwijderen." lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. Selecteer *bijwerken* en de regel instellingen worden ontkoppeld van de route.

1. Herhaal stap 2-5 voor het loskoppelen van andere routes die zijn gekoppeld aan deze regelset totdat u ziet dat de status *van* de routes niet-gekoppeld is.

1. Voor een regel die niet is *gekoppeld*, kunt u de regelset verwijderen door op de drie puntjes aan de rechter kant te klikken en *verwijderen* te selecteren. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="Scherm opname van het verwijderen van een regelset.":::

1. De regel instellingen worden nu verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

* Een regelset maken
* Een regel die is ingesteld aan uw AFD-route koppelen.
* Een regelset uit uw AFD-profiel verwijderen

Ga door naar de volgende zelf studie voor meer informatie over het toevoegen van beveiligings headers met een regel set.

> [!div class="nextstepaction"]
> [Beveiligings headers met de ingestelde regels]()
