---
title: KS SvarUt og SvarInn
description: ""
summary: ""
product: eFormidling
sidebar: eformidling_sidebar
---

eFormidling støtter følgende prosesser og dokumenttyper med meldingstjenesten KS SvarUt og SvarInn (DPF).

| **Prosess**                                           | **Dokumenttype**                              | **Eksempel**                           |
|-------------------------------------------------------|-----------------------------------------------|----------------------------------------|
| [Saksbehandling](../../Funksjonalitet/saksbehandling) | [Arkivmelding](../Dokumenttyper/arkivmelding) | [Eksempel](../Eksempel/saksbehandling) |
| [Taushetsbelagt saksbehandling](../../Funksjonalitet/taushetsbelagt_saksbehandling) | [Arkivmelding](../Dokumenttyper/arkivmelding) | [Eksempel](../Eksempel/taushetsbelagt_saksbehandling) |

Om KS SvarUt og SvarInn er en del av en mottakers kapabiliteter eller ikke avhenger av flere faktorer slik som beskrevet
for den enkelte prosess.

For å bruke KS SvarUt og SvarInn må integrasjonspunktet konfigureres med nødvendige tilganger til APIet KS SvarUt og
SvarInn tilbyr.

KS SvarInn sin selvbetjeningsløsning gir mulighet til å konfigurere en integrasjon (ett mottakersystem) til å motta meldinger for flere organisasjonsnummer. Et slikt oppsett kan brukes for å støtte mottak av meldinger adressert til underenheter. Dette vil fungere for meldinger sendt fra KS SvarUt direkte og også de sendt via eFormidling (så lenge avsender har konfigurert integrasjonspunktet med støtte for KS SvarUt).

Integrasjonspunktet omformer dokumenttypen arkivmelding til kall mot APIet KS SvarUt tilbyr. Integrasjonspunktet
omformer i tillegg responser fra APIet KS SvarInn tilbyr til dokumenttypen arkivmelding. Se dokumenttypene for detaljer.
KS SvarUt har ikke støtte for prosesser. Meldinger blir derfor videreformidlet til KS SvarUt uten informasjon om
tilknyttet prosess. KS SvarInn har ikke støtte for prosesser. Alle meldinger mottatt fra KS SvarInn får derfor tildelt
prosess som konfigurert i integrasjonspunktet.

Ved bruk av grensesnittet BEST/EDU omformer integrasjonspunktet først fra BEST/EDU til arkivmelding, før meldingen
sendes som beskrevet over.

Integrasjonspunktet omformer meldingsstatusene fra KS SvarUt og SvarInn til eFormidlings meldingsstatuser og
arkivmelding-kvittering.

Flytskisse for `arkivmelding` fra stat til kommune:

<div class="mermaid">
graph LR
A("fa:fa-user Saksbehandler<br>stat") --> |Fylle inn| B("fa:fa-server Sak/arkiv")
B --> |Arkivmelding| C("fa:fa-server Integrasjonspunktet")
C --> D("fa:fa-server KS SvarUt")
D --> E("fa:fa-server KS SvarInn")
E --> F("fa:fa-server Sak/arkiv")
F --> G("fa:fa-user Saksbehandler<br>kommune")
C --> |Status| B
C --> |Arkivmelding-kvittering| B
D --> C
</div>

Flytskisse for `arkivmelding` fra kommune til stat:

<div class="mermaid">
graph LR
A("fa:fa-user Saksbehandler<br>kommune") --> |Fylle inn| B("fa:fa-server Sak/arkiv")
B --> C("fa:fa-server KS SvarUt")
C --> D("fa:fa-server KS SvarInn")
D --> E("fa:fa-server Integrasjonspunktet")
E --> |Arkivmelding| F("fa:fa-server Sak/arkiv")
F --> G("fa:fa-user Saksbehandler<br>stat")
F --> |Arkivmelding-kvittering| E
E --> D
</div>

## Meldingsstatuser

Meldingsstatuser for meldinger sendt med KS SvarUt:

| Status    | Kommentar                                                                                  |
|-----------|--------------------------------------------------------------------------------------------|
| OPPRETTET | Integrasjonspunktet mottar bestEdu-melding fra sak-arkivsystem og oppretter en DPF-melding |
| SENDT     | Integrasjonspunkt har sendt forsendelsen til meldingsformidler                             |
| LEVERT    | Forsendelsen er klar for mottak og kan hentes ned fra SvarInn                              |
| LEST      | En forsendelse er lest når hele forsendelsesfilen er lastet ned av mottaker                | 

Meldingsstatuser for meldinger mottatt med KS SvarInn:

| Status              | Kommentar                                                          |
|---------------------|--------------------------------------------------------------------|
| OPPRETTET           | Integrasjonspunkt laster frå KS ned og oppretter meldingen hos seg |
| INNKOMMENDE_MOTTATT | Meldingen blir konvertert til internt format                       | 
| INNKOMMENDE_LEVERT  | Meldingen er levert til sak-arkivsystem                            | 

## Neste steg

- [Produktdokumentasjon for KS SvarUt og SvarInn](https://www.ks.no/fagomrader/digitalisering/felleslosninger/svar-inn-og-svar-ut/) (ekstern lenke)
- [Teknisk dokumentasjon for KS SvarUt og SvarInn](https://ks-no.github.io/svarut/) (ekstern lenke)
- Flere [meldingstjenester](./)
