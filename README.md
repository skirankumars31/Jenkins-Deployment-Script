# Jenkins Pipeline Deployment Script for Uipath

## Hva er det ?

Denne skripten i Jenkins Pipeline kan pakke, publisere og oppgradere process til latest pakke. 

## Hvordan fungerer det ?

Skript er invokert fra Jenkins med en Process Name Build Parameter. Skript henter information det trenger fra properties fil og via kobling mot Orchestrator. Det bygger opp en nuget pakke, publisere pakke til Orchestrator, Oppgradere process til ny version av pakke og koble fra Orchestrator. **Jenkins job vil hente en lisens fra Orchestrator og frier lisense etter job er utført**

## Forutsetning

Pipeline deployment trenger en Windows maskin med følgende verktøy

* Jenkins
* UiPath Powershell
* Uipath Robot med kobling mulighet til Orchestrator
* Service Bruker i Orchestrator med tilgang til publisering og oppdatering

## Build Parameters i Jenkins

Process Navn er build parameter for skripten. Process Navn format skal bli samme navn fra Orchestrator

## Hvordan kan man bruke deployment for en ny process

Clone repo, legg inn et property med process navn i repo.properties , sende inn pull request og merge inn til master 

## Stegene i Pipeline Script

* Get Process Details - Henter Process ID fra Orchestrator
* Code Checkout  - Sjekker ut kode fra GIT repository
* Koble til Orchestrator - Uipath trenger en robot lisens for å pakke nuget fra kilde koden. Dette steg henter en lisens fra Orchestrator
* Bygge Pakke  - Denne steg bygger opp nuget pakke ved bruk av Uipath Command line mode
* Publisere Pakke - Publiseres nuget package til Orchestrator
* Oppdatere Process - Oppdateres process med latest publisert package
* Koble fra Orchestrator - Kjøres en command for å slippe robot lisens
