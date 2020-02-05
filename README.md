### Jenkins Pipeline Deployment Script for Uipath ###

## What is it ? ##
Denne skripten i Jenkins Pipeline kan pakke, publisere og oppgradere process til latest pakke.

## Stegene i Pipeline Script ##

* Code Checkout  - Sjekker ut kode fra GIT repository
* Koble til Orchestrator - Uipath trenger en robot lisens for å pakke nuget fra kilde koden. Dette steg henter en lisens fra Orchestrator
* Bygge Pakke  - Denne steg bygger opp nuget pakke ved bruk av Uipath Command line mode
* Publisere Pakke - Publiseres nuget package til Orchestrator
* Oppdatere Process - Oppdateres process med latest publisert package
* Koble fra Orchestrator - Kjøres en command for å slippe robot lisens