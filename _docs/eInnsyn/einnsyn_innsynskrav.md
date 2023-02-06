---
title: Innsynskrav og e-postoppsett
description: Innsynskrav og e-postoppsett
summary: ""

sidebar: einnsyn_sidebar
redirect_from: /einnsyn_innsynskrav
---

Når en sluttbruker bestiller innsynskrav til en virksomhet så skal dette mottas på e-post. Denne e-posten har en forklarende tekst på hvilket dokument det er bestillt innsyn i. Vedlagt i e-posten ligger det en order.xml fil som skal importeres til sakarkivsystemet. Om dette skjer automatisk eller manuelt er opp til hver innholdsleverandør. Deretter må filen behandles av arkivar og svar på innsynskravet må sendes ut til innsynskravbestillers e-postadresse.

## Hvordan fungerer det ?

Når en sluttbruker bestiller et innsynskrav så vil Digitaliseringsdirektoratet sin einnsyn-klient generere en bestilling og sende denne til Digitaliseringsdirektoratet sitt integrasjonspunkt. Dette integrasjonspunktet vil dermed kryptere, signere og pakke meldingen for så å sende denne via Azure Servicebus til mottaker sitt integrasjonspunkt. Her vil det bli dekryptert og sendt videre til mottakers einnsyn-klienten. Denne vil kontakte en intern SMTP-server og be den sende bestillingen. Det vil så gå en e-post fra denne e-postserveren, men med avsender e-postadresse "no_reply@einnsyn.no". Denne e-posten går til den adressen som er angitt på einnsyn.no under ``` virksomhet -> "..." -> endre -> Innsynskravepost ```. Deretter må filen importeres inn i sakarkivsystemet. 

![nettverksoppsett einnsyn-klient]({{site.baseurl}}/images/einnsyn/nettverksoppsett.png)

Om en ikke mottar e-posten, så kan det være lurt å sjekke spam/søppelpost mappen.

## Ved direkteintegrasjon (uten einnsyn-klient)

Fagsystem som har støtte for direkteintegrasjon kan hente order.xml direkte fra integrasjonspunktet.

## Utvidet variant av order.xml

Vi har utviklet to varianter av order.xml. Versjon 1 er den originale og "default". Hvis det er ønskelig og støtte for å motta den utvidete versjonen (versjon 2), kan det settes/endres i eInnsyn Admin av en virksomhetsadministrator:
![order_xml versjon einnsyn admin]({{site.baseurl}}/images/einnsyn/orderversjon_admin.png)

Versjon 1 (eksempel):
```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<bestilling>
	<id>http://data.einnsyn.no/innsynskrav/3954cfaa-c5fc-4c05-b3a6-9968855a42af</id>
	<bestillingsdato>21.12.2021</bestillingsdato>
	<til>
		<enhet>LoremIpsum</enhet>
		<enhetepost>lorem@eksempel.no</enhetepost>
	</til>
	<innsynskravepost>innsyn@eksempel.no</innsynskravepost>
	<kontaktinfo>
		<forsendelsesmåte>e-post</forsendelsesmåte>
		<navn> </navn>
		<organisasjon/>
		<land/>
		<e-post>olanordmann@gmail.com</e-post>
	</kontaktinfo>
	<dokumenter>
		<dokument>
			<saksnr>2025/23456</saksnr>
			<dokumentnr>1</dokumentnr>
			<journalnr>2171234/21</journalnr>
			<saksbehandler>saksbehandler</saksbehandler>
		</dokument>
	</dokumenter>
</bestilling>
```
Versjon 2 (eksempel):
```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ns2:bestilling xmlns:ns2="http://einnsyn.no/schema/order/v2">
	<id>http://data.einnsyn.no/innsynskrav/b05b9fd4-cbda-4e17-9d09-21a889526484</id>
	<bestillingsdato>2021-12-21+01:00</bestillingsdato>
	<til>												
		<virksomhet>LoremIpsum</virksomhet>
		<orgnr>987654321</orgnr>
		<enhetepost>lorem@eksempel.no</enhetepost>
		<innsynskravepost>innsyn@eksempel.no</innsynskravepost>
	</til>
	<kontaktinfo>									
		<forsendelsesmåte>e-post</forsendelsesmåte>
		<e-post>olanordmann@gmail.com</e-post>
	</kontaktinfo>
	<dokumenter>
		<dokument>
			<fagsysteminfo>
				<id>cb746da3-41d9-4f29-9895-c62152c207b1</id>
				<delId>3ae3e707-a046-4970-9ac7-f8a270a193e5</delId>	
			</fagsysteminfo>
			<id>http://test.einnsyn.no/noark5/3056b799-a88e-435b-95fc-4b7621b57d26</id>
			<systemId>3056b799-a88e-435b-95fc-4b7621b57d26</systemId>	
			<saksnr>2025/23456</saksnr>
			<dokumentnr>1</dokumentnr>
			<journalnr>2171234/21</journalnr>
			<saksbehandler>saksbehandler</saksbehandler>
			<admEnhet>enhetskode</admEnhet>
		</dokument>
	</dokumenter>
</ns2:bestilling>
```
fagsysteminfo-id/delId er hentet fra SystemID til "arkiv"/"arkivdel". Disse må derfor være inkludert i den opprinnelige publiseringen/avleveringen for at det skal kunne inkluderes i xml'en. Det samme gjelder systemId på dokumentet. Versjon 2 er derfor kun støttet av fagsystemer som publiserer på Noark5 xml og Json-ld format.


## Meldingsflyt 

Se forklaring under bildet.

![meldingsflyt einnsyn]({{site.baseurl}}/images/einnsyn/meldingsflyt.bmp)

[Trykk her for større bilde]({{site.baseurl}}/images/einnsyn/meldingsflyt.bmp)

1. Arkivar henter trigger eksport av oep saker
2. Laster opp oep fil til filområde arkivar og eInnsynsklient har tilgang til
3. eInnsynsklient splitter opp oep meldingen til eInnsynsmeldinger,
4. Laster eInnsysnsmelding til integrasjonspunktet
5. Integrasjonspunktet gjør oppslag for å finne mottaker (capability oppslag)
6. Intgrasjonspunktet krypterer, signerer og pakker melding. Laster deretter opp til mottakers kø
7. Ingegrasjonspunktet laster ned nye meldinger fra kø, pakker opp, sjekker signatur, dekrypterer melding, tilgjengeligjør for mottaker
8. eInnsysnsapplikasjon henter meldinger fra integrasjonpunktet, tilgjengeliggjør i eInnsynssøk
9. Person søker innsyn
10. Innsynskrav lastes opp til integrasjonspunkt
11. Integrasjonspunktet gjør oppslag for å finne mottaker (capability oppslag)
12. integrasjonspunktet krypterer, signerer og pakker melding. Laster deretter opp til mottakers kø
13. Integrasjonspunktet laster ned nye meldinger fra kø, pakker opp, sjekker signatur, dekrypterer melding, tilgjengeliggjør for mottaker
14. eInnsynsklient sender innsynskrav via mottakers mailserver
15. Innsynskrav tilgjengeliggjøres i via mottakers sakarkvisystem/mailserver e.l.

