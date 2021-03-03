---
title: SAP S/4HANA of BW/4HANA implementeren op een Azure VM | Microsoft Docs
description: SAP S/4HANA of BW/4HANA implementeren op een Azure VM
author: hermanndms
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 09/15/2016
ms.author: hermannd
ms.reviewer: cynthn
ms.openlocfilehash: 4b739efcf2695a5385351f531efb6201bbea1cd5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671934"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>SAP S/4HANA of BW/4HANA implementeren op Azure
In dit artikel wordt beschreven hoe u S/4HANA implementeert in azure met behulp van de SAP Cloud Appliance Library (SAP CAL) 3,0. Volg dezelfde stappen om andere oplossingen op basis van SAP HANA, zoals BW/4HANA, te implementeren.

> [!NOTE]
> Voor meer informatie over de SAP CAL gaat u naar de website van de [SAP Cloud Appliance-bibliotheek](https://cal.sap.com/) . SAP heeft ook een blog over de [SAP Cloud Appliance Library 3,0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> Vanaf 29 mei 2017 kunt u het Azure Resource Manager-implementatie model gebruiken naast het lagere klassieke implementatie model om de SAP-CAL te implementeren. We raden u aan het nieuwe implementatie model van Resource Manager te gebruiken en het klassieke implementatie model te negeren.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Stapsgewijze procedure voor het implementeren van de oplossing

In de volgende reeks scherm afbeeldingen ziet u hoe u S/4HANA implementeert in azure met behulp van de SAP CAL. Het proces werkt op dezelfde manier als bij andere oplossingen, zoals BW/4HANA.

De pagina **oplossingen** toont enkele van de SAP CAL Hana-oplossingen die beschikbaar zijn op Azure. **SAP S/4HANA 1610 FPS01, Fully-Activated apparaat** bevindt zich in de middelste rij:

![SAP CAL-oplossingen](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Een account maken in de SAP CAL
1. Als u zich voor de eerste keer wilt aanmelden bij de SAP CAL, gebruikt u uw SAP S-gebruiker of een andere gebruiker die is geregistreerd bij SAP. Definieer vervolgens een SAP CAL-account dat wordt gebruikt door de SAP CAL voor het implementeren van apparaten in Azure. In de account definitie moet u het volgende doen:

    a. Selecteer het implementatie model op Azure (Resource Manager of klassiek).

    b. Voer uw Azure-abonnement in. Een SAP CAL-account kan alleen worden toegewezen aan één abonnement. Als u meer dan één abonnement nodig hebt, moet u een andere SAP CAL-account maken.

    c. Geef de SAP CAL toestemming om te implementeren in uw Azure-abonnement.

   > [!NOTE]
   >  In de volgende stappen ziet u hoe u een SAP CAL-account maakt voor implementaties van Resource Manager. Als u al een SAP CAL-account hebt dat is gekoppeld aan het klassieke implementatie model, *moet* u deze stappen volgen om een nieuw SAP CAL-account te maken. Het nieuwe SAP CAL-account moet worden geïmplementeerd in het Resource Manager-model.

1. Maak een nieuw SAP CAL-account. Op de pagina **accounts** worden drie opties voor Azure weer gegeven: 

    a. **Microsoft Azure (klassiek)** is het klassieke implementatie model en is niet langer de voor keur.

    b. **Microsoft Azure** is het nieuwe implementatie model van Resource Manager.

    c. **Windows Azure geëxploiteerd door 21vianet** is een optie in China die gebruikmaakt van het klassieke implementatie model.

    Selecteer **Microsoft Azure** om in het Resource Manager-model te implementeren.

    ![Details van SAP CAL-account](./media/cal-s4h/s4h-pic-2a.png)

1. Voer de Azure **-abonnements-id** in die u kunt vinden op de Azure Portal.

   ![SAP CAL-accounts](./media/cal-s4h/s4h-pic3c.png)

1. Als u de SAP CAL wilt machtigen voor implementatie in het Azure-abonnement dat u hebt gedefinieerd, klikt u op **autoriseren**. De volgende pagina wordt weer gegeven op het browser tabblad:

   ![Aanmelden bij de Cloud Services van Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

1. Als er meer dan één gebruiker wordt weer gegeven, kiest u het Microsoft-account dat is gekoppeld aan de beheerder van het Azure-abonnement dat u hebt geselecteerd. De volgende pagina wordt weer gegeven op het browser tabblad:

   ![Bevestiging van Cloud Services in Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

1. Klik op **Accepteren**. Als de autorisatie is geslaagd, wordt de SAP CAL-account definitie opnieuw weer gegeven. Na korte tijd bevestigt een bericht dat het autorisatie proces is geslaagd.

1. Als u het zojuist gemaakte SAP CAL-account aan uw gebruiker wilt toewijzen, voert u uw **gebruikers-id** in het tekstvak aan de rechter kant in en klikt u op **toevoegen**.

   ![Account voor gebruikers koppeling](./media/cal-s4h/s4h-pic8a.png)

1. Als u uw account wilt koppelen aan de gebruiker die u gebruikt om u aan te melden bij de SAP CAL, klikt u op **controleren**. 
 
1. Klik op **maken** om de koppeling tussen uw gebruiker en het ZOJUIST gemaakte SAP CAL-account te maken.

   ![Gebruiker to SAP CAL-account koppeling](./media/cal-s4h/s4h-pic9b.png)

U hebt een SAP CAL-account gemaakt dat het volgende kan:

- Gebruik het Resource Manager-implementatiemodel.
- Implementeer SAP-systemen in uw Azure-abonnement.

U kunt nu beginnen met het implementeren van S/4HANA in uw gebruikers abonnement in Azure.

> [!NOTE]
> Voordat u doorgaat, moet u bepalen of u Azure vCPU-quota's hebt voor virtuele machines uit de H-serie van Azure. Op het moment maakt de SAP CAL gebruik van virtuele machines uit de H-serie van Azure om enkele van de op SAP HANA gebaseerde oplossingen te implementeren. Uw Azure-abonnement heeft mogelijk geen vCPU quota's voor de h-serie. Als dit het geval is, moet u mogelijk contact opnemen met de ondersteuning van Azure om een quotum van ten minste 16 H-serie Vcpu's te krijgen.
> 
> [!NOTE]
> Wanneer u in de SAP CAL een oplossing in azure implementeert, kunt u kiezen voor slechts één Azure-regio. Als u wilt implementeren in andere Azure-regio's dan de regio die wordt voorgesteld door de SAP CAL, moet u een CAL-abonnement aanschaffen bij SAP. Het is ook mogelijk dat u een bericht met SAP moet openen om ervoor te zorgen dat uw CAL-account is ingeschakeld voor het leveren van andere Azure-regio's dan de accounts die voor het eerst worden voorgesteld.

### <a name="deploy-a-solution"></a>Een oplossing implementeren

We gaan een oplossing implementeren op de pagina **oplossingen** van de SAP CAL. De SAP CAL heeft twee reeksen die moeten worden geïmplementeerd:

- Een Basic-reeks die gebruikmaakt van één pagina om het systeem te definiëren dat moet worden geïmplementeerd
- Een geavanceerde reeks waarmee u bepaalde opties kunt opgeven voor VM-grootten 

Hier wordt het Basic-pad voor de implementatie beschreven.

1. Op de pagina **account Details** moet u het volgende doen:

    a. Selecteer een SAP CAL-account. (Gebruik een account dat is gekoppeld aan de implementatie van het Resource Manager-implementatie model.)

    b. Voer een exemplaar **naam** in.

    c. Selecteer een Azure- **regio**. De SAP CAL stelt een regio voor. Als u een andere Azure-regio nodig hebt en u geen SAP CAL-abonnement hebt, moet u een CAL-abonnement met SAP Best Ellen.

    d. Voer een hoofd **wachtwoord** in voor de oplossing van acht of negen tekens. Het wacht woord wordt gebruikt voor de beheerders van de verschillende onderdelen.

   ![Basis modus SAP-CAL: exemplaar maken](./media/cal-s4h/s4h-pic10a.png)

1. Klik op **maken** en klik in het bericht venster dat wordt weer gegeven op **OK**.

   ![VM-grootten die door SAP CAL worden ondersteund](./media/cal-s4h/s4h-pic10b.png)

1. Klik in het dialoog venster **persoonlijke sleutel** op **Store** om de persoonlijke sleutel op te slaan in de SAP CAL. Als u wachtwoord beveiliging voor de persoonlijke sleutel wilt gebruiken, klikt u op **downloaden**. 

   ![Persoonlijke sleutel van SAP CAL](./media/cal-s4h/s4h-pic10c.png)

1. Lees het SAP CAL- **waarschuwings** bericht en klik op **OK**.

   ![Waarschuwing SAP-CAL](./media/cal-s4h/s4h-pic10d.png)

    De implementatie vindt nu plaats. Afhankelijk van de grootte en complexiteit van de oplossing (de SAP CAL biedt een schatting), wordt de status na enige tijd weer gegeven als actief en klaar voor gebruik.

1. Als u de virtuele machines wilt vinden die met de andere gekoppelde resources in één resource groep zijn verzameld, gaat u naar de Azure Portal: 

   ![SAP CAL-objecten die zijn geïmplementeerd in de nieuwe portal](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. Op de SAP CAL-portal wordt de status weer gegeven als **actief**. Klik op **verbinden** om verbinding te maken met de oplossing. Er zijn verschillende opties voor het maken van verbinding met de verschillende onderdelen geïmplementeerd in deze oplossing.

   ![SAP CAL-exemplaren](./media/cal-s4h/active_solution.png)

1. Voordat u een van de opties kunt gebruiken om verbinding te maken met de geïmplementeerde systemen, klikt u op **aan de slag-hand leiding**. 

   ![Verbinding maken met het exemplaar](./media/cal-s4h/connect_to_solution.png)

    De documentatie namen de gebruikers voor elk van de connectiviteits methoden. De wacht woorden voor deze gebruikers worden ingesteld op het hoofd wachtwoord dat u aan het begin van het implementatie proces hebt gedefinieerd. In de documentatie worden andere functionele gebruikers weer gegeven met hun wacht woord, die u kunt gebruiken om u aan te melden bij het geïmplementeerde systeem. 

    Als u bijvoorbeeld de SAP-GUI gebruikt die vooraf is geïnstalleerd op de Windows Extern bureaublad computer, ziet het systeem S/4 er als volgt uit:

   ![SM50 in de vooraf geïnstalleerde SAP-GUI](./media/cal-s4h/gui_sm50.png)

    Of als u de DBACockpit gebruikt, kan het exemplaar er als volgt uitzien:

   ![SM50 in de DBACockpit SAP-GUI](./media/cal-s4h/dbacockpit.png)

Binnen een paar uur wordt een gezonde SAP S/4-apparaat geïmplementeerd in Azure.

Als u een SAP CAL-abonnement hebt gekocht, ondersteunt SAP volledige implementaties via de SAP CAL op Azure. De ondersteunings wachtrij is BC-VCM-CAL.







