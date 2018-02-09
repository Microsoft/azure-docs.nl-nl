## <a name="microsoft-open-source-code-of-conduct"></a>Microsoft Open Source-gedragscode

Op dit project is de [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Microsoft Open Source-gedragscode) van toepassing.
Zie voor meer informatie de [Code van Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) of neem contact op met [ opencode@microsoft.com ](mailto:opencode@microsoft.com) met aanvullende vragen of opmerkingen.

## <a name="contribute-to-azure-technical-documentation"></a>Bijdragen aan technische documentatie voor Azure
Welkom bijdragen uit onze community (gebruikers, klanten, partners, MSFT werknemers buiten core Azure producteenheden, enz.) als werknemers in core Azure producteenheden. Hoe u bijdragen, is afhankelijk van wie u bent:

* **Community - minder ingrijpende updates**: als u minder ingrijpende updates buiten de nauwkeurigheid van uw hart bijdragen, kunt u het artikel niet vinden in deze repository of Ga naar het artikel op [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) en klik op de **Bewerken** koppeling in het artikel die u gaat dan naar de GitHub-bron voor het artikel. NET vervolgens de GitHub UI gebruikt om de updates. Of u bent Welkom bij de opslagplaats fork en updates van uw fork verzenden.

* **Community - nieuwe artikelen**: als u deel uit van de Azure-community en u wilt een nieuw artikel maakt, moet u werken met een werknemer ervoor te zorgen dat nieuwe inhoud in door een combinatie van werk in de openbare en persoonlijke-opslagplaats.

* **Werknemers**: als u een technische writer, programmamanager zijn of ontwikkelaar van het productteam voor een Azure-service en het is uw taak bijdragen aan of het schrijven van technische artikelen, moet u de opslagplaats voor persoonlijke (https://github.com/ gebruiken MicrosoftDocs/azure-docs-pr). Als u belangrijke wijzigingen aan een bestaand artikel doorvoert, moet toevoegen of wijzigen van afbeeldingen of een nieuw artikel u deze repository vertakken, Git Bash en markdown-editor installeren en informatie over een aantal git-opdrachten. Zie [handleiding voor de interne bijdragers](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) voor meer informatie.


## <a name="about-your-contributions-to-azure-content"></a>Over uw bijdragen aan Azure-content
### <a name="minor-corrections"></a>Kleine correcties
Kleine correcties of verduidelijkingen u indienen voor documentatie en codevoorbeelden in deze opslagplaats vallen onder de [docs.microsoft.com-gebruiksvoorwaarden](https://docs.microsoft.com/legal/termsofuse).

### <a name="larger-submissions"></a>Grotere wijzigingen
Als u een nieuwe of belangrijke wijzigingen in de documentatie of codevoorbeelden pull-aanvraag indient, sturen we een opmerking in GitHub waarin u een online bijdrage License Agreement (CLA) indienen als u niet een medewerker van Microsoft. U moet het online formulier invullen voordat we uw pull-aanvraag kunnen accepteren.

## <a name="tools-and-setup"></a>Hulpprogramma's en installatie
Medewerkers van de community kunnen gebruiken de GitHub UI of de opslagplaats om bij te dragen vertakken. Werknemers moeten bezoeken [handleiding voor de interne bijdragers](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) instellen voor meer informatie over hoe u kunt bijdragen aan de technische documentatie.

## <a name="repository-organization"></a>Organisatie van de repository
De inhoud in de opslagplaats azure docs volgt de documentatiestructuur op https://docs.microsoft.com/azure. Deze bibliotheek bevat twee hoofdmappen:

### <a name="articles"></a>\articles
De map *\articles* bevat de documentatieartikelen die zijn opgemaakt als markdownbestanden met de indeling *.md*. Artikelen zijn doorgaans gegroepeerd op Azure-service.

De *\articles* map bevat de *\media* voor root directory mediabestanden binnen deze map zich submappen met de afbeeldingen voor elk artikel.  De servicemappen bevatten een afzonderlijke mediamap voor de artikelen in elke servicemap. De mappen met afbeeldingen voor de artikelen hebben dezelfde naam als het artikelbestand, maar zonder de bestandsextensie *.md*.

### <a name="includes"></a>\includes
U kunt herbruikbare blokken met conAtent maken die moeten worden opgenomen in een of meer artikelen. 

## <a name="how-to-use-markdown-to-format-your-topic"></a>Hoe u markdown kunt gebruiken om uw onderwerp op te maken
Alle artikelen in deze bibliotheek gebruiken GitHub-markdown.  Hier volgt een lijst van resources.

* [Basisprincipes van markdown](https://help.github.com/articles/markdown-basics/)
* [Afdrukbare cheatsheet voor markdown](https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf)


## <a name="labels"></a>Labels
Geautomatiseerde labels worden in de opslagplaats voor openbare azure-documenten toegewezen aan het pull-aanvragen stellen van de werkstroom pull-aanvraag beheren en te laten u weten wat er gebeurt met uw pull-aanvraag:

* Gebruiksrechtovereenkomst bijdrage gerelateerd
  * CLA niet is vereist: de wijziging is relatief klein en vereist niet dat u zich een CLA aanmeldt.
  * CLA vereist: het bereik van de wijziging is relatief groot en vereist dat u zich een CLA aanmeldt.
  * CLA ondertekend: de inzender de CLA ondertekend, zodat de pull-aanvraag kunt nu verdergaan voor revisie.
* Wijzigen is verzonden naar de auteur: de auteur van de in behandeling zijnde pull-aanvraag is gewaarschuwd.
* gereed voor samenvoegen: gereed voor controle door ons team pull-aanvraag controleren.


