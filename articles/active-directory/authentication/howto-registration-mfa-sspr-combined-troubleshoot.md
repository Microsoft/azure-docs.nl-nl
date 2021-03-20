---
title: Problemen met gecombineerde registratie oplossen-Azure Active Directory
description: Problemen met Azure AD Multi-Factor Authentication en self-service voor wacht woord opnieuw instellen combi neren
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: db87887fc2b51c7cb8cb300eb8e711d3ae9b6ac8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98610778"
---
# <a name="troubleshooting-combined-security-information-registration"></a>Problemen met het registreren van gecombineerde beveiligings gegevens oplossen

De informatie in dit artikel is bedoeld om beheerders te helpen bij het oplossen van problemen die worden gerapporteerd door gebruikers van de gecombineerde registratie-ervaring.

## <a name="audit-logs"></a>Auditlogboeken

De gebeurtenissen die zijn vastgelegd voor gecombineerde registratie vindt u in de service verificatie methoden in de Azure AD-controle Logboeken.

![Azure AD audit logboeken interface met registratie gebeurtenissen](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

De volgende tabel geeft een lijst van alle controle gebeurtenissen die worden gegenereerd door gecombineerde registratie:

| Activiteit | Status | Reden | Beschrijving |
| --- | --- | --- | --- |
| Gebruiker heeft alle vereiste beveiligings gegevens geregistreerd | Geslaagd | Gebruiker heeft alle vereiste beveiligings gegevens geregistreerd. | Deze gebeurtenis treedt op wanneer de registratie van een gebruiker is voltooid.|
| Gebruiker heeft alle vereiste beveiligings gegevens geregistreerd | Fout | De gebruiker heeft de registratie van beveiligings gegevens geannuleerd. | Deze gebeurtenis treedt op wanneer een gebruiker de registratie van de interrupt-modus annuleert.|
| Geregistreerde beveiligings gegevens van gebruiker | Geslaagd | Geregistreerde *methode* van gebruiker. | Deze gebeurtenis treedt op wanneer een gebruiker een afzonderlijke methode registreert. De *methode* kan de verificatie-app, het telefoon nummer, het e-mail adres, de beveiligings vragen, het app-wacht woord, de alternatieve telefoon, enzovoort zijn.| 
| Door de gebruiker gecontroleerde beveiligings gegevens | Geslaagd | De gebruiker heeft beveiligings gegevens gecontroleerd. | Deze gebeurtenis treedt op wanneer een gebruiker **ziet** dat deze goed wordt geselecteerd op de pagina beveiligings gegevens controleren.|
| Door de gebruiker gecontroleerde beveiligings gegevens | Fout | De gebruiker heeft geen beveiligings gegevens kunnen controleren. | Deze gebeurtenis treedt op wanneer een gebruiker **ziet** dat deze goed wordt geselecteerd op de pagina beveiligings gegevens controleren, maar er is een fout opgetreden op de back-end.|
| Door gebruiker verwijderde beveiligings gegevens | Geslaagd | De door de gebruiker verwijderde *methode*. | Deze gebeurtenis treedt op wanneer een gebruiker een afzonderlijke methode verwijdert. De *methode* kan de verificatie-app, het telefoon nummer, het e-mail adres, de beveiligings vragen, het app-wacht woord, de alternatieve telefoon, enzovoort zijn.|
| Door gebruiker verwijderde beveiligings gegevens | Fout | De gebruiker kan de *methode* niet verwijderen. | Deze gebeurtenis treedt op wanneer een gebruiker een methode probeert te verwijderen, maar de poging om een of andere reden mislukt. De *methode* kan de verificatie-app, het telefoon nummer, het e-mail adres, de beveiligings vragen, het app-wacht woord, de alternatieve telefoon, enzovoort zijn.|
| Door de gebruiker gewijzigde standaard beveiligings gegevens | Geslaagd | De gebruiker heeft de standaard beveiligings gegevens voor de *methode* gewijzigd. | Deze gebeurtenis treedt op wanneer een gebruiker de standaard methode wijzigt. De *methode* kan een verificatie van de verificator-app zijn, een code van mijn verificator-app of-token, roep + x xxxxxxxxxx, tekst een code naar + X xxxxxxxxx, enzovoort.|
| Door de gebruiker gewijzigde standaard beveiligings gegevens | Fout | Gebruiker kan de standaard beveiligings gegevens voor de *methode* niet wijzigen. | Deze gebeurtenis treedt op wanneer een gebruiker de standaard methode probeert te wijzigen, maar de poging om een of andere reden mislukt. De *methode* kan een verificatie van de verificator-app zijn, een code van mijn verificator-app of-token, roep + x xxxxxxxxxx, tekst een code naar + X xxxxxxxxx, enzovoort.|

## <a name="troubleshooting-interrupt-mode"></a>Problemen met de interrupt-modus oplossen

| Symptoom | Stappen voor probleemoplossing |
| --- | --- |
| Ik zie niet de methoden die ik verwacht te zien. | 1. Controleer of de gebruiker een Azure AD-beheerdersrol heeft. Zo ja, Bekijk de verschillen in het SSPR-beheer beleid. <br> 2. Bepaal of de gebruiker wordt onderbroken vanwege Multi-Factor Authentication afdwinging of het afdwingen van SSPR registratie. Zie het [stroom diagram](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) onder "gecombineerde registratie modi" om te bepalen welke methoden moeten worden weer gegeven. <br> 3. Bepaal hoe recent het Multi-Factor Authentication-of SSPR-beleid is gewijzigd. Als de wijziging recent is, kan het enige tijd duren voordat het bijgewerkte beleid is door gegeven.|

## <a name="troubleshooting-manage-mode"></a>Problemen met de beheer modus oplossen

| Symptoom | Stappen voor probleemoplossing |
| --- | --- |
| Ik heb niet de optie om een bepaalde methode toe te voegen. | 1. Bepaal of de methode is ingeschakeld voor Multi-Factor Authentication of voor SSPR. <br> 2. als de methode is ingeschakeld, slaat u het beleid opnieuw op en wacht u 1-2 uur voordat u de test opnieuw uitvoert. <br> 3. als de methode is ingeschakeld, moet u ervoor zorgen dat de gebruiker niet al het maximum aantal van de methode heeft ingesteld dat ze mogen instellen.|

## <a name="disable-combined-registration"></a>Gecombineerde registratie uitschakelen

Wanneer een gebruiker een telefoon nummer en/of mobiele app registreert in de nieuwe gecombineerde ervaring, stemt onze service op een set vlaggen (StrongAuthenticationMethods) voor deze methoden voor die gebruiker. Met deze functionaliteit kan de gebruiker Multi-Factor Authentication met deze methoden uitvoeren wanneer Multi-Factor Authentication is vereist.

Als een beheerder de preview-versie inschakelt, kunnen gebruikers zich registreren via de nieuwe ervaring. vervolgens schakelt de beheerder de preview-versie uit, kunnen gebruikers ook onbekend worden geregistreerd voor Multi-Factor Authentication.

Als een gebruiker die de gecombineerde registratie heeft voltooid, naar de huidige SSPR-registratie pagina (self-service voor wachtwoord herstel) gaat [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) , wordt de gebruiker gevraagd om multi-factor Authentication uit te voeren voordat deze toegang tot de pagina heeft. Deze stap wordt verwacht van een technisch standpunt, maar is nieuw voor gebruikers die eerder zijn geregistreerd voor SSPR. Hoewel deze extra stap de beveiligings postuur van de gebruiker verbetert door een ander beveiligings niveau te bieden, kunnen beheerders mogelijk hun gebruikers terugdraaien zodat ze niet meer Multi-Factor Authentication kunnen uitvoeren.  

### <a name="how-to-roll-back-users"></a>Gebruikers terugdraaien

Als u als beheerder wilt de Multi-Factor Authentication-instellingen van een gebruiker opnieuw wilt instellen, kunt u het Power shell-script gebruiken dat in de volgende sectie wordt weer gegeven. Met het script wordt de eigenschap StrongAuthenticationMethods voor de mobiele app en/of het telefoon nummer van een gebruiker gewist. Als u dit script uitvoert voor uw gebruikers, moeten ze zich opnieuw registreren voor Multi-Factor Authentication als dat nodig is. U wordt aangeraden om terugdraaien te testen met een of twee gebruikers voordat u alle betrokken gebruikers terugkeert.

De volgende stappen helpen u bij het terugdraaien van een gebruiker of een groep gebruikers.

#### <a name="prerequisites"></a>Vereisten

1. Installeer de juiste Azure AD Power shell-modules. Voer in een Power shell-venster de volgende opdrachten uit om de modules te installeren:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Sla de lijst met betrokken gebruikers object-Id's op uw computer op als een tekst bestand met één ID per regel. Noteer de locatie van het bestand.
1. Sla het volgende script op uw computer op en noteer de locatie van het script:

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>Terugdraaiactie

Voer in een Power shell-venster de volgende opdracht uit, waarbij u het script en de gebruikers bestands locaties opgeeft. Voer de referenties van de globale beheerder in wanneer u hierom wordt gevraagd. Het script voert het resultaat van elke bijwerk bewerking van de gebruiker uit.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>De bijgewerkte ervaring uitschakelen

Voer de volgende stappen uit om de bijgewerkte ervaring voor uw gebruikers uit te scha kelen:

1. Meld u aan bij de Azure Portal als een gebruikers beheerder.
2. Ga naar **Azure Active Directory**  >  **gebruikers instellingen**  >  **instellingen voor de preview-functies van het toegangs venster beheren**.
3. Onder **gebruikers kunnen preview-functies gebruiken voor het registreren en beheren van beveiligings gegevens**, de selector instellen op **geen** en vervolgens **Opslaan** selecteren.

Gebruikers worden niet langer gevraagd om zich te registreren met behulp van de bijgewerkte ervaring.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over gecombineerde registratie voor Self-service voor wachtwoord herstel en Azure AD Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
