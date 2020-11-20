---
title: Ondersteunings ticket of case maken voor de StorSimple 8000-serie
description: Meer informatie over het registreren van de ondersteunings aanvraag en het starten van een ondersteunings sessie op het StorSimple 8000-serie apparaat.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: c288f59b1cb9a85ed4bc978d1c3fba18ba30b572
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968754"
---
# <a name="contact-microsoft-support"></a>Neem contact op met Microsoft Ondersteuning

De StorSimple-Apparaatbeheer biedt de mogelijkheid om **een nieuwe ondersteunings aanvraag in te loggen** op de Blade service overzicht. Als u problemen ondervindt met uw StorSimple-oplossing, kunt u een service aanvraag voor technische ondersteuning maken. In een online-sessie met uw ondersteunings technicus moet u mogelijk ook een ondersteunings sessie starten op uw StorSimple-apparaat. Dit artikel begeleidt u door:

* Een ondersteunings aanvraag maken.
* Een ondersteunings aanvraag levenscyclus beheren vanuit de portal.
* Een ondersteunings sessie starten in de Windows Power shell-interface van uw StorSimple-apparaat.

Bekijk de [StorSimple 8000-serie ondersteuning voor sla's en informatie](./storsimple-8000-support-options.md) voordat u een ondersteuningsaanvraag maakt.

## <a name="create-a-support-request"></a>Een ondersteuningsaanvraag maken

Afhankelijk van uw [ondersteunings plan](https://azure.microsoft.com/support/plans/)kunt u rechtstreeks op de Blade StorSimple Apparaatbeheer service samen vatting ondersteunings tickets maken voor een probleem op uw StorSimple-apparaat. Voer de volgende stappen uit om een ondersteunings aanvraag te maken:

#### <a name="to-create-a-support-request"></a>Een ondersteunings aanvraag maken

1. Ga naar uw StorSimple-apparaatbeheerservice. Ga in de Blade instellingen service overzicht naar **ondersteuning en probleem oplossing** en klik vervolgens op **nieuwe ondersteunings aanvraag**.
     
    ![Contact opnemen met MS-ondersteuning via de nieuwe portal](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. Selecteer op de Blade **nieuwe ondersteunings aanvraag** de optie **basis**. Voer de volgende stappen uit op de Blade **basis beginselen** :
   1. Selecteer in de vervolg keuzelijst **probleem type** de optie **technisch**.
   2. Het huidige **abonnement**, **service** type en de **resource** (StorSimple Apparaatbeheer service) worden automatisch gekozen. 
   3. Selecteer een **ondersteunings plan** in de vervolg keuzelijst als er meerdere plannen aan uw abonnement zijn gekoppeld. U hebt een betaald ondersteunings abonnement nodig om technische ondersteuning mogelijk te maken.
   4. Klik op **Volgende**.

       ![Contact opnemen met MS-ondersteuning via nieuwe Portal 2](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. Selecteer op de Blade **nieuwe ondersteunings aanvraag** het **probleem met stap 2**. Voer op de Blade **probleem** de volgende stappen uit:
    
    1. Kies de **Ernst**.
    2. Geef op of het probleem betrekking heeft op het apparaat of de StorSimple-Apparaatbeheer service.
    3. Kies een **categorie** voor dit probleem en geef meer **informatie** over het probleem.
    4. Geef de begin datum en-tijd op voor het probleem.
    5. Klik in het **bestand uploaden** op het mappictogram om naar uw ondersteunings pakket te bladeren.
    6. Controleer de **Diagnostische gegevens voor delen**.
    7. Klik op **Volgende**.

       ![Contact opnemen met MS-ondersteuning via nieuwe portal 3](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Klik op de Blade **nieuwe ondersteunings aanvraag** op **stap 3 contact gegevens**. Voer de volgende stappen uit op de Blade **contact gegevens** :

   1. Geef in de **contact opties** uw favoriete contact wijze op (telefoon of e-mail) en de taal. De reactie tijd wordt automatisch geselecteerd op basis van uw abonnement.
   2. Geef in de contact gegevens uw naam, e-mail adres, optionele contact persoon, land/regio. Schakel het selectie vakje **wijzigingen in contact persoon opslaan voor toekomstige ondersteunings aanvragen** in.
   3. Klik op **Create**.
   
       ![Contact opnemen met MS-ondersteuning via nieuwe Portal 4](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      Microsoft Ondersteuning gebruikt deze informatie om u te bereiken voor meer informatie, diagnose en oplossing.
      Nadat u uw aanvraag hebt ingediend, neemt een ondersteunings technicus zo snel mogelijk contact met u op om te kunnen door gaan met uw aanvraag.

## <a name="manage-a-support-request"></a>Een ondersteunings aanvraag beheren

Nadat u een ondersteuningsticket hebt gemaakt, kunt u de voortgang van het ticket vanuit de portal beheren.

#### <a name="to-manage-your-support-requests"></a>Uw ondersteunings aanvragen beheren

1. Als u naar de pagina Help en ondersteuning wilt gaan, bladert u naar **> Help en ondersteuning**.

    ![Ondersteunings aanvragen beheren](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Een lijst in tabel vorm van alle ondersteunings aanvragen wordt weer gegeven op de Blade **Help en ondersteuning** .

    ![Ondersteunings aanvragen beheren 2](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Selecteer en klik op een ondersteunings aanvraag. U kunt de status en de Details voor deze aanvraag bekijken. Klik op **+ Nieuw bericht** als u deze aanvraag wilt opvolgen.

    ![Ondersteunings aanvragen beheren 3](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Een ondersteunings sessie starten in Windows PowerShell voor StorSimple

Als u problemen wilt oplossen met het StorSimple-apparaat, moet u zich aanmelden met het Microsoft Ondersteuning-team. Microsoft Ondersteuning moet mogelijk een ondersteunings sessie gebruiken om u aan te melden bij uw apparaat.

Voer de volgende stappen uit om een ondersteunings sessie te starten:

#### <a name="to-start-a-support-session"></a>Een ondersteunings sessie starten

1. Direct toegang tot het apparaat met behulp van de seriële console of via een Telnet-sessie vanaf een externe computer. Volg hiervoor de stappen in [putty gebruiken om verbinding te maken met de seriële console van het apparaat](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Druk in de sessie die wordt geopend op de **Enter** -toets om een opdracht prompt op te halen.
3. Selecteer optie 1 in het menu seriële console en **Meld u aan met volledige toegang**.
4. Typ bij de prompt het volgende wacht woord:
   
    `Password1`
5. Typ achter de prompt de volgende opdracht:
   
    `Enable-HcsSupportAccess`
6. Een versleutelde teken reeks wordt aan u gepresenteerd. Kopieer deze teken reeks in een tekst editor, zoals Klad blok.
7. Sla deze teken reeks op en verzend deze in een e-mail bericht naar Microsoft Ondersteuning.

> [!IMPORTANT]
> U kunt de ondersteunings toegang uitschakelen door uit te voeren `Disable-HcsSupportAccess` . Het StorSimple-apparaat probeert ook de toegang tot 8 uur na het starten van de sessie uit te scha kelen. Het is een best practice om uw StorSimple te wijzigen na het initiëren van een ondersteunings sessie.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [vaststellen en oplossen van problemen met uw StorSimple 8000 Series-apparaat](storsimple-8000-troubleshoot-deployment.md)