# Leksjon 3: Sikre infrastruktur
---
Nå skal du ha en sikker applikasjon som tillater kun en innlogget bruker å lagre bilder. Et problem som fortsatt eksisterer er at Storage kontoen kan være tilgjengelig for alle over internett. Derfor kan observante personer få tilgang til bilder du laster opp uten å være innlogget. Under veiviseren, i portalen, når du lager en Storage Account så får du muligheten til å sette Connectivity method (under Networking), her kan man sette Public endpoint (all networks), Public networks (selected networks) og private endpoint. Som oftest velger man Public endpoint (all networks) fordi det er raskest å få til å fungere, men dette gjør at hvem som helst har tilgang til endpointsen.
---

Før vi låser ned Storage Accounten så er det viktig å sjekke at applikasjonen er klar. I StorageService finnes det en metode som heter GetImageUrls, sjekk at denne lager en SharedAccessSignature.

For å sikre Storage Accounten så må vi gå i portalen og sjekke/endre litt

- Pass på at container er private access. Dette finner du under Container sin Access level. - Finn Storage Accounten i portalen. - Trykk Containers, finn containeren du vil endre på og trykk på den. - Ovenfor alle filene vil det være et par valg, sjekk her at containeren har access level "Private (no anonymous access)". Trykk OK,


## \<svenska>

Gå in på Security + networking på ditt storage account, välj sedan Networking. Klicka på "Manage" under Public network access. Låt Public network access vara på Enable, men klicka i "Enable from selected networks". Skapa ett nytt virtuellt nätverk med defaultinställningar. IP-ranges i samma region eller subscription kolliderar inte med mindre att man gör så kallad "peering". Klicka på save.

## \</svenska>

- Det kan hende du må fylle ut en Address range for ditt subnet. Du fyller da ut den samme adressen som du har for Address space, men erstatter tallet etter "/" med 29.

---

I tillegg må du gi Web App'en din mulighet til å snakke med Storage Accounten din, nå når denne ligger ligger i et VNET.

- Gå til Web App'en din i portalen.
- VNET integrasjon er en Standard-tier feature i App Service Plan. Skifte App Service Plan: Trykk på "Scale Up". Velg så "Premium v3 P0v3" under "Production".
- Gå så til Networking i menyen til venstre. Trykk "Not configured" vid siden av Virtual Network Configuration under `Outbound traffic configuration`.
- Trykk "Add virtual network integration" og velg det virtuelle nettverket du opprettet i forrige. Det har bare et subnet, så velg dette. Trykk Connect. Nå har Web App'en din lov til å snakke med Storage Accounten gjennom VNET.

Test så at applikasjonen din ikke fungerer lengre, og bildene vises ikke. Hvorfor ikke? Du har jo SAS-token?
Grunnen til dette er at nå er det kun Web App'en (backend) som har lov til å snakke direkte med Storage Account i VNET, mens Web App'en kun returnerer linker (med SAS-token) direkte til Storage Account. Per nå
har du ikke tilgang til Storage Account fra din adresse.

Hvis du fulgte litt godt med så så du kanskje valget med Firewall og legg til din IP-addresse (under "Firewalls and networks settings" på Storage Account)? Huk av for den og trykk lagre på nytt. Nå skal applikasjonen din fungere igjen. Valget med IP-addressen finnes i Storage Account under samme plass som tidligere.

Gå til vnet og se litt rundt på valg. Her kan man parre med andre nettverk, legge til devices, opprette/endre subnets osv.
