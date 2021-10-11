# Sogn.dk leverandør API
1. [ Indledning](#1-indledning)

    A. [Sikkerhed](#a-sikkerhed)

    B. [Adgang](#b-adgang)

    C. [Opslagstabeller](#c-opslagstabeller)

    D. [Gudstjenester og arrangementer på sogn.dk](#d-gudstjenester-og-arrangementer-på-sogndk)
2. [ Gudstjenester](#2-gudstjenester)

    A. [Opret gudstjeneste](#a-opret-gudstjeneste)

    B. [Hent gudstjeneste](#b-hent-gudstjeneste)

    C. [Opdater gudstjeneste](#c-opdater-gudstjeneste)

    D. [Slet gudstjeneste](#d-slet-gudstjeneste)
3. [ Arrangementer](#3-arrangementer)

    A. [Opret arrangement](#a-opret-arrangement)

    B. [Hent arrangement](#b-hent-arrangement)

    C. [Opdater arrangement](#c-opdater-arrangement)

    D. [Slet arrangement](#d-slet-arrangement)

    E. [Opdater arrangement eller gudstjeneste med deltagerantal](#e-opdater-arrangement-eller-gudstjeneste-med-deltagerantal)
4. [Præster](#4-præster)
    
    A. [Hent præster](#a-hent-præster)
5. [Kirker](#5-kirker)
    
    A. [Hent kirker](#a-hent-kirker)
6. [Kategorier](#6-kategorier)
    
    A. [Hent kategorier](#a-hent-kategorier)
7. [Handlings fejl](#7-handlings-fejl)
8. [Ændringslog](#8-ændringslog)

## 1. Indledning
<p>Folkekirken har på sogn.dk et website hvor alle folkekirkens sogne har en oplysningsside. Alle sider er oprettet efter en standardiseret skabelon.</p>
<p>Oplysningerne på sogn.dk vedligeholdes automatisk via abonnementer i CPR-registret og Kirkenettets Informationssystem (KIS). De automatisk vedligeholdte oplysninger kan suppleres med oplysninger, som det enkelte sogn selv vedligeholder.</p>
<p>Oplysninger om begivenheder (gudstjenester og arrangementer med forskellige kategorier) vedligeholdes af sognene.</p>
<p>Sognene kan også selv vedligeholde information via adgangskode på sogn.dk/admin.</p>
<p>En række sogne anvender et tredjeparts administrationssystem, som kan anvendes til administration af begivenheder og herunder advisering af ansatte og frivillige m.m.</p>
<p>Dette dokument beskriver hvordan oplysninger om begivenheder fra disse andre systemer kan overføres til sogn.dk, som eksponerer oplysningerne på folkekirken.dk, stifts- og provstihjemmesider foruden at sogn.dk leverer dataudtræk til pressen via tjenesten sogn.dk/presse og til eksterne kalendersystemer som kirku.dk og kultunaut.dk.</p>
<p>Dokumentet beskriver den brugergrænseflade (API), som leverandører af administrationssystemer skal anvende.</p>
<p>Grænsefladen er baseret på OIOREST:(OIO = Offentlig Information Online / REST = Representational State Transfer), se også www.oiorest.dk.</p>

### A. Sikkerhed
Adgang til Sogn.dk API vil være lukket med HTTP Digest Authentication.
Enhver uautoriseret forespørgsel, som ikke er godkendt, vil blive præsenteret for svar med HTTP header kode 401.
For mere info om HTTP Digest Authentication:
https://en.wikipedia.org/wiki/Digest_access_authentication

### B. Adgang
Virksomheder, som leverer administrationssystemer til folkekirken, kan med angivelse af en fortegnelse over hvilke sogne der anvender deres system, få adgang til sogn.dk leverandør api’et.
Ansøgning herom fremsendes til Folkekirkens It pr. e-mail:

**it-kontoret@km.dk**

Når ansøgning er modtaget, fremsendes brugernavn og adgangskode til et testsystem, hvori brugerens løsning skal afprøves på et passende antal sogne og dertil hørende gudstjenester.
Ved afprøvningen skal leverandøren demonstrere, at løsningen kan oprette, ændre og slette gudstjenester og arrangementer.
Når en testleverance er afprøvet og godkendt af Folkekirkens It, fremsendes brugernavn og kodeord til produktionssystemet.

### C. Opslagstabeller
Til brug for leverandørens implementering af sin overførselsløsning, er der mulighed for via en service at rekvirere følgende tabeller:
-	Sogne, tabel med samtlige sogne og tilhørende myndighedskoder
-	Kirker, tabel med samtlige kirker og tilknytning til det sogn kirken er beliggende i
-	Præster, tabel med samtlige præster med tilknytning til et sogneembede
-	Kategorier, tabel med kategorier for arrangementer og gudstjenester

Alle tabeller synkroniseres med KIS hver nat.
Der leveres ikke ændringsudtræk, men hver gang et fuldt udtræk.

### D. Gudstjenester og arrangementer på sogn.dk
Gudstjenester og arrangementer vises på sogn.dk på hvert sogns egen forside. som feks: https://sogn.dk/testsogn/ og specifikt på den underside herfra, der hedder kalender:
https://sogn.dk/testsogn/kalender/
Sogne fremsøges fra søgeboksen på forsiden af sogn.dk.

## 2. Gudstjenester
### A. Opret gudstjeneste
Gudstjenester kan oprettes ved at sende en POST forespørgsel til Sogn.dk's API.
Eksempel:

**URL**

`POST https://sogn.dk/api/gudstjeneste.xml`

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE gudstjeneste SYSTEM "https://sogn.dk/api/dtd/api.dtd">
    <gudstjeneste>
        <sogn id="[sogn_id]" />
        <type>[type_navn]</type>
        <tidspunkt-start>[tidspunkt-start]</tidspunkt-start>
        <tidspunkt-slut>[tidspunkt-slut]</tidspunkt-slut>
        <praest id="[praest_id]" />
        <praedikant>[praedikant_navn]</praedikant>
        <kirke id="[kirke_id]" />
    ... se listen med alle de mulige elementer nedenfor .... 
        <sted>[sted_navn]</sted>
        <beskrivelse><![CDATA[beskrivelse uden alt for meget HTML men uden at tags eller tegn er html entity encoded]]></beskrivelse>
        <abstract><![CDATA[Hvis I afleverer plain text her behøver I ikke at putte indholdet i CDATA blok, ellers skal I,- og så konverterer vi det til plain text med newlines (char(10))]]></link>
        <link><![CDATA[https://km.dk]]></link>
    </gudstjeneste>

Det er vigtigt at man sender data i den rækkefølge der fremgår af DTD'et (https://sogn.dk/api/dtd/api.dtd):

    <?xml version="1.0" encoding="UTF-8"?>
    <!ELEMENT gudstjeneste (sogn,type,tidspunkt-start,tidspunkt-slut?,ingen-sluttid?,praest?,praedikant?,overskrift?,kirke?,sted?,beskrivelse?,abstract?,link?,slettet?,oprettet?,redigeret?,deltagere?,konfirmationer?,daab?,kommentar?,modtager?,online?,dawaid?)>
    <!ATTLIST gudstjeneste xmlns CDATA #IMPLIED>
    <!ELEMENT arrangement (sogn,kategori,tidspunkt-start,tidspunkt-slut?,ingen-sluttid?,overskrift,kirke?,sted?,beskrivelse?,abstract?,link?,slettet?,oprettet?,redigeret?, deltagere?,konfirmationer?,daab?,kommentar?,modtager?,online?,dawaid?)>
    <!ATTLIST arrangement xmlns CDATA #IMPLIED>
    <!ELEMENT sogn (#PCDATA)>
    <!ATTLIST sogn id CDATA #REQUIRED>
    <!ELEMENT type (#PCDATA)>
    <!ELEMENT kategori (#PCDATA)>
    <!ELEMENT tidspunkt-start (#PCDATA)>
    <!ELEMENT tidspunkt-slut (#PCDATA)>
    <!ELEMENT ingen-sluttid (#PCDATA)>
    <!ELEMENT praest (#PCDATA)>
    <!ATTLIST praest id CDATA #IMPLIED>
    <!ELEMENT praedikant (#PCDATA)>
    <!ELEMENT overskrift (#PCDATA)>
    <!ELEMENT kirke (#PCDATA)>
    <!ATTLIST kirke id CDATA #IMPLIED>
    <!ELEMENT sted (#PCDATA)>
    <!ATTLIST sted id CDATA #IMPLIED>
    <!ELEMENT beskrivelse (#PCDATA)>
    <!ELEMENT abstract (#PCDATA)>
    <!ELEMENT link (#PCDATA)>
    <!ELEMENT slettet (#PCDATA)>
    <!ELEMENT oprettet (#PCDATA)>
    <!ELEMENT redigeret (#PCDATA)>
    <!ELEMENT deltagere (#PCDATA)>
    <!ELEMENT konfirmationer (#PCDATA)>
    <!ELEMENT daab (#PCDATA)>
    <!ELEMENT kommentar (#PCDATA)>
    <!ELEMENT modtager (#PCDATA)>
    <!ELEMENT online (#PCDATA)>
    <!ELEMENT dawaid (#PCDATA)>

De elementer der har et spørgsmålstegn efter sig, er ikke obligatoriske. Dvs. at de kan udelades hvis der alligevel ikke er indhold. Dem der ikke har spørgsmålstegn efter sig kan ikke udelades. Man vil få en fejl hvis de ikke er med.
Se beskrivelse af de forskellige parametre nedenfor.
Generelt skal data som indeholder HTML tags (<>&) wrappes i en CDATA-blok. Dette er specielt relevant for beskrivelsesfeltet, men kan også bruges på andre felter. Vi fjerner alle HTML tags før vi gemmer indhold fra alle felter. Block tags konverteres til linjeskift i beskrivelsesfeltet. Der er altså ikke muligt at få HTML markup på nogle felter (kun linjeskift).

| Parametre | Type | Beskrivelse |
| --- | --- | -------------------------------------------- |
| sogn | integer | 4-cifret myndighedskode. (angives i id attributten). |
|type |string |Typebetegnelse for gudstjeneste (tilsvarer kategori for arrangement). |
|tidspunkt-start | timestamp | Tidsstempel følgende RFC822 format: <br>`Wed, 02 Oct 2011 15:00:00 +0100` |
|tidspunkt-slut | timestamp | Tidsstempel følgende RFC822 format: <br>`Wed, 02 Oct 2011 15:00:00 +0100` <br>Hvis det ikke er udfyldt / medsendt tillægges en time til `tidspunkt_start` og bruges som `tidspunkt_slut`.  (med mindre `ingen_sluttid` er sat til 1)
|ingen-sluttid | boolean (default `false`) | Hvis feltet er sat /true (ikke tomt og ikke 0) vil vi markere at dette event ikke har angivet en sluttid. <br>Vi vil dog stadig medsende den sluttid der evt er medsendt (eller beregnet ud fra starttid), så det er op til modtageren af informationerne at bruge denne information. |
|praest | integer | ID for præsten (ID findes fra præsteservice). Bemærk at dette id skal sættes i attributten id. Selve feltet er altså tomt. Alternativt modtages: praedikant jfr. nedenfor. |
|praedikant | string | Prædikantens navn. Modtages i stedet for praest id, hvor dette ikke findes. (typisk ”gæsteprædikanter”)
|overskrift | string | Overskriften. Hvis denne ikke er medsendt bruges navnet på kategori/type.
|kirke | integer | Kirkens ID. ID findes fra kirkeservice. Bemærk at dette id skal sættes i attributten id. Selve feltet er altså tomt. Alternativt modtages: sted_navn jfr. nedenfor. |
|sted | string | Lokalitet (stedets navn), hvor gudstjenesten/arrangement afholdes. Kan udelades hvis der er angivet et kirkeid. |
|beskrivelse | string | Fritekst på gudstjeneste. Kan indeholde HTML, som dog vil blive fjernet og konverteret til linieskift. Vi anbefaler at man fjerner HTML og gør sine visninger af gudstjenester uafhængig af HTML gemt med beskrivelses data. Vi bruger indholdet fra dette felt til at lave en ren tekst beskrivelse som man bedre kan bruge i udtræk. (se feks. afsnit 2.b) |
|abstract | string (max 255 karakterer) | En kort beskrivelse som evt. kan bruges som en slags teaser til en længere beskrivelse. Det vil typisk være indholdet fra dette felt som man vil bruge i listeudtræk til eksempelvis kirku.dk eller kultunaut (hvis det ikke er tomt). |
|link | string | En URL til en længere beskrivelse af denne gudstjeneste. `https://…` |
|deltagere | integer | Antal deltagere til et givent arrangement/gudstjeneste.  Hvis man laver en opdatering er det vigtigt ikke at medsende denne parameter (eller lade den være tom), da den ellers vil nulstille en evt allerede indberettet tælling. Et 0 vil blive tolket som 0 deltagere. |
|konfirmationer | integer | Antal konfirmationer til en gudstjeneste. Hvis man laver en opdatering er det vigtigt ikke at medsende denne parameter (eller lade den være tom), da den ellers vil nulstille en evt allerede indberettet tælling. Et 0 vil blive tolket som 0 konfirmationer. |
|daab | integer | Antal dåb til en gudstjeneste. Hvis man laver en opdatering er det vigtigt ikke at medsende denne parameter (eller lade den være tom), da den ellers vil nulstille en evt allerede indberettet tælling. Et 0 vil blive tolket som 0 deltagere. |
|kommentar | string (max 255 karakterer) | En kommentar til tællingen. Er kun tiltænkt meget specielle omstændigheder og forventes generelt ikke. |
|modtager | Integer | Et gyldigt danske mobilnummer som vil modtage en SMS som kan bruges til at foretage indberetningen af disse statistiktal med. Information om denne feature kan findes andet sted eller rekvireres ved henvendelse. |
|online | boolean (default `false`) | Hvis feltet er sat /true (ikke tomt og ikke 0) vil vi markere at dette event (også) afvikles online / virtuelt. Beskrivelsen kan så eventuelt uddybe dette (såfremt der også er en mulighed for fysisk tilstedeværelse). Dette bør således blot indikere om der er mulighed for at følge begivenheden via en form for stream / broadcast / udsendelse. Link til udsendelsen kan tilføjes i beskrivelsen. |
|dawaid | Integer | Et gyldigt dansk adgangsadresse id som beskrevet her: https://dawadocs.dataforsyningen.dk/dok/api/adgangsadresse

**Bemærk** at vi benytter adgangsadresser og ikke adresser.
Dette id kan angives som alternativ til sted eller kirke og vil overstyre de andre lokationsangivelser. Den endelige implementering er endnu ikke afsluttet, så dette er en test indtil videre man bør ikke basere lokationsangivelse alene på dette felt endnu. 

**Bemærk** felterne slettet, oprettet og redigeret kan ikke påvirkes og er kun i DTD'en fordi den samme DTD bruges i andre XML services. De er ikke obligatioriske og forventes udeladt. Skulle de være medsendt ignoreres de.

**Svar**

Hvis gudstjenesten er blevet oprettet korrekt, vil Sogn.dk API levere følgende svar:

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE svar PUBLIC "-//SOGN.DK//DTD XML 1.0//DA" "https://sogn.dk/api/dtd/defaultreply.dtd">
    <svar xmlns="https://sogn.dk/api/dtd/defaultreply.dtd">
        <kode>1</kode>
        <besked>Succes!</besked>
        <gudstjeneste id="185185"/>
    </svar>

Parametre | Type |
| --- | --- |
gudstjeneste id | integer |

Såfremt hændelsen ikke blev oprettet, returnerer sogn.dk API en fejlmeddelelse [(se afsnit 7)](#7-handlings-fejl).

### B. Hent gudstjeneste
Gudstjeneste hentes ved at sende en GET forespørgsel til Sogn.dk API:

`GET https://sogn.dk/api/gudstjeneste.xml?id={gudstjeneste_id}`

**URL**

*(ingen body)*

| Parametre | Type | Beskrivelse |
| --- | --- | ---- |
| gudstjeneste_id |integer | ID på gudstjeneste, tidligere leveret af Sogn.dk API. |

**Svar**

Svaret vil bestå af samme felter som blev brugt til at POSTe data. Det er også samme DTD der bruges. Se derfor beskrivelsen ovenfor.

    <?xml version="1.0" encoding="UTF-8"?>
    <gudstjeneste xmlns="https://sogn.dk/api/dtd/api.dtd">
        <sogn id="[sogn_id]" />
        <type><![CDATA[[type_navn]]]></type>
        <tidspunkt-start>[tidspunkt-start]</tidspunkt-start>
        <praest id="[praest_id]" />
    .... se hele listen med felter ovenfor ...
        <link>[link]</link>
        <slettet>[slettet]</slettet>
        <oprettet>[oprettet]</oprettet>
        <redigeret>[redigeret]</redigeret>
    </gudstjeneste>

Nedenfor er beskrevet de felter der er tilføjet når man henter en gudstjeneste/arrangement.

| Felt | Type | Beskrivelse |
| --- | --- | ------- |
| slettet | integer | Er sat til 1 hvis gudstjenesten er slettet. Ellers 0. |
| Oprettet |timestamp | Tidsstempel for hvornår denne gudstjeneste blev oprettet. Det følger RFC822 format: <br>`Wed, 02 Oct 2011 15:00:00 +0100` |
| redigeret | timestamp | Tidsstempel for hvornår denne gudstjeneste blev redigeret sidst (opdateret, oprettet eller slette). Det følger RFC822 format: <br>`Wed, 02 Oct 2011 15:00:00 +0100` |
| online | boolean (default `false`) | Hvis feltet er sat og true/1 betyder det at dette event (også) afvikles online / virtuelt. Beskrivelsen kan så eventuelt uddybe dette (såfremt der også er en mulighed for fysisk tilstedeværelse). <br>Dette bør således blot indikere om der er mulighed for at følge begivenheden via en form for stream/broadcast/udsendelse. <br>Link til udsendelsen kan tilføjes i beskrivelsen. |
| dawaid | Integer | Et gyldigt dansk adgangsadresse id som beskrevet her: https://dawa.aws.dk/dok/api#adgangsadresser **Bemærk** at vi benytter adgangsadresser og ikke adresser. <br>Dette id kan angives som alternativ til sted eller kirke og vil overstyre de andre lokationsangivelser. Den endelige implementering er endnu ikke afsluttet, så dette er en test indtil videre man bør ikke basere lokationsangivelse alene på dette felt endnu. |

Såfremt en ikke eksisterende hændelse forsøges hentet, returnerer sogn.dk API en fejlmeddelelse [(se afsnit 7)](#7-handlings-fejl).

Bemærk at svaret også vil indeholde de tællingsdata:
- deltagere
- konfirmationer
- daab
- kommentar
- modtager

### C. Opdater gudstjeneste
En gudstjeneste kan opdateres ved at sende en POST forespørgsel til Sogn.dk API.

**URL**

`POST https://sogn.dk/api/gudstjeneste.xml?id={gudstjeneste_id}`

| Parametre | Type | Beskrivelse |
| --- | --- | ------- |
| gudstjeneste_id | Integer | ID på gudstjeneste, tidligere leveret af Sogn.dk API. |

Forespørgslen sendes i samme format, med samme DTD og med samme POST parametre som angivet for indsættelse af en gudstjeneste [(Se afsnit 2.A)](#a-opret-gudstjeneste).

**Svar**

Hvis hændelsen er blevet korrekt ajourført, vil Sogn.dk API levere følgende svar:

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE svar PUBLIC "-//SOGN.DK//DTD XML 1.0//DA" "https://sogn.dk/api/dtd/defaultreply.dtd">
    <svar xmlns="https://sogn.dk/api/dtd/defaultreply.dtd">
        <kode>1</kode>
        <besked>Succes!</besked>
        <gudstjeneste id="[gudstjeneste_id]" />
    </svar>

Såfremt en ikke eksisterende hændelse forsøges ajourført, returnerer sogn.dk API en fejlmeddelelse [(se afsnit 7)](#7-handlings-fejl).
### D. Slet gudstjeneste
En gudstjeneste kan slettes ved at sende et DELETE forespørgsel til Sogn.dk API.

**URL**

`DELETE https://sogn.dk/api/gudstjeneste.xml?id={gudstjeneste_id}`

*(ingen body)*

| Parametre | Type | Beskrivelse |
| --- | --- | ------- |
| gudstjeneste_id | integer | ID på gudstjeneste, tidligere leveret af Sogn.dk API. |

**Svar**

Såfremt gudstjenesten slettes korrekt vil Sogn.dk API levere følgende svar:

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE svar PUBLIC "-//SOGN.DK//DTD XML 1.0//DA" "https://sogn.dk/api/dtd/defaultreply.dtd">
    <svar xmlns="https://sogn.dk/api/dtd/defaultreply.dtd">
        <kode>1</kode>
        <besked>Succes!</besked>
    </svar>

Såfremt en ikke eksisterende hændelse forsøges slettet, returnerer sogn.dk API en fejlmeddelelse [(se afsnit 7)](#7-handlings-fejl).


## 3. Arrangementer
### A. Opret arrangement
Arrangementer kan oprettes ved at sende en POST forespørgsel til Sogn.dk API.
Da det stort set er samme felter og funktionalitet som for Gudstjenester beskrives kun forskelle fra gudstjenester i dette afsnit.
For arrangementer benyttes feltet kategori I modsætning til gudstjenester hvor det hedder type. Den URL der skal benyttes til oprettelse af nye arrangementer er:

URL

`POST https://sogn.dk/api/arrangement.xml`

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE arrangement SYSTEM "https://sogn.dk/api/dtd/api.dtd">
    <arrangement>
        <sogn id="[sogn_id]" />
        <kategori>[kategori_navn]</kategori>
    .... se beskrivelsen for gudstjenester ....

    </arrangement>

**Parametre**

Samme parametre som til gudstjeneste, med følgende undtagelser:

| Parametre | Type | Beskrivelse |
| --- | --- | ------- |
| kategori | string | Kategori for arrangement. <br> Kategorier leveres jfr. pkt.1.3 <br>Såfremt afsender systemet ikke har de samme kategorier som sogn.dk, skal afsender systemet omsætte sine kategorier til sogn.dk kategorier.

Felterne `praest` og `praedikant` kan ikke sendes til et arrangement.

**Svar**

Hvis et arrangement er blevet oprettet, vil Sogn.dk API give følgende svar:

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE svar PUBLIC "-//SOGN.DK//DTD XML 1.0//DA" "https://sogn.dk/api/dtd/defaultreply.dtd">
    <svar xmlns="https://sogn.dk/api/dtd/defaultreply.dtd">
        <kode>1</kode>
        <besked>Succes!</besked>
        <arrangement id="185186"/>
    </svar>

| Parametre | Type | Beskrivelse |
| --- | --- | ------- |
| arrangement id | integer | ID genereret af Sogn.dk API. Referencen bruges til senere opdatering eller sletning af arrangement. |

Såfremt hændelsen ikke blev oprettet, returnerer sogn.dk API en fejlmeddelelse [(se afsnit 7)](#7-handlings-fejl).

### B. Hent arrangement

Arrangement kan hentes ved at sende en GET forespørgsel til Sogn.dk API.

**URL**

`GET https://sogn.dk/api/arrangement.xml?id={arrangement_id}`

*(ingen body)*

| Parametre | Type | Beskrivelse |
| --- | --- | ------- |
| arrangement_id | integer | ID på arrangement, tidligere leveret af Sogn.dk API. |

**Svar**

Svaret vil bestå af samme felter som blev brugt til at POST'e data. Det er også samme DTD der bruges. Se derfor beskrivelsen ovenfor.

    <?xml version="1.0" encoding="UTF-8"?>
    <arrangement xmlns="https://sogn.dk/api/dtd/api.dtd">
        <sogn id="[sogn_id]" />
        <kategori><![CDATA[[kategori_navn]]]></kategori>
        <tidspunkt-start>[tidspunkt-start]</tidspunkt-start>
        <tidspunkt-slut>[tidspunkt-slut]</tidspunkt-slut>
        <overskrift>[overskrift]</overskrift>
        <kirke id="[kirke_id]" />
        <sted><![CDATA[[sted_navn]]]></sted>
        <beskrivelse><![CDATA[[beskrivelse]]]></beskrivelse>
        <abstract><![CDATA[]]></abstract>
        <beskrivelse_plain><![CDATA[]]></beskrivelse_plain>
        <slettet>0</slettet>
        <oprettet>Thu, 29 Nov 12 15:14:54 +0100</oprettet>
        <redigeret>Thu, 29 Nov 12 15:14:54 +0100</redigeret>
    </arrangement>

Arrangementer vil også indeholde tællingsdata såfremt disse er indberettet. Og dawaid og online indikation, såfremt disse er blevet medsendt under oprettelsen (eller ved en opdatering).

### C. Opdater arrangement
Et arrangement kan opdateres ved at sende en POST forespørgsel til Sogn.dk API.

**URL**

`POST https://sogn.dk/api/arrangement.xml?id={arrangement_id}`

| Parametre | Type | Beskrivelse |
| --- | --- | ------- |
| arrangement_id | integer | ID på arrangement, tidligere leveret af Sogn.dk API.|

Forespørgslen sendes i samme format, med samme DTD og med samme POST parametre som angivet for indsættelse af et arrangement. Se afsnit 3.a.

**Svar**

Hvis et arrangement er blevet opdateret korrekt, vil Sogn.dk API give følgende svar:

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE svar PUBLIC "-//SOGN.DK//DTD XML 1.0//DA" "https://sogn.dk/api/dtd/defaultreply.dtd">
    <svar xmlns="https://sogn.dk/api/dtd/defaultreply.dtd">
        <kode>1</kode>
        <besked>Succes!</besked>
    </svar>

Såfremt en ikke eksisterende hændelse forsøges ajourført, returnerer sogn.dk API en fejlmeddelelse [(se afsnit 7)](#7-handlings-fejl).
### D. Slet arrangement
Sletning af et arrangement sker ved at sende en DELETE forespørgsel til Sogn.dk API.

**URL**

`DELETE https://sogn.dk/api/arrangement.xml?id={arrangement_id}`

*(ingen body)*

| Parametre | Type | Beskrivelse |
| --- | --- | ------- |
| arrangement_id | integer | ID på arrangement, tidligere leveret af Sogn.dk API. |

**Svar**

Hvis et arrangement er slettet korrekt vil Sogn.dk API levere følgende svar:

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE svar PUBLIC "-//SOGN.DK//DTD XML 1.0//DA" "https://sogn.dk/api/dtd/api.dtd">
    <svar xmlns="https://sogn.dk/api/dtd/api.dtd">
        <kode>1</kode>
        <besked>Succes!</besked>
        <result>1</result>
    </svar>

Der returneres et result, som angiver, hvor mange records der er berørt af den sendte forespørgsel (her 1).
Bemærk: Den brugte DTD validerer ikke med det givne svar (for Slet forespørgsler). Det beklager vi og retter på et senere tidspunkt. Dette svar har aldrig valideret, så det er nok noget I har fundet ud af at omgå.

### E. Opdater arrangement eller gudstjeneste med deltagerantal
Det er samme felter man kan angive som under oprettelse. Hvis man ikke angiver noget og der i forvejen er angivet noget vil de eksisterende angivelser ikke blive overskrevet.
Man skal altså være opmærksom på at man ikke overskriver angivelser som et sogn måske har foretaget via sogn.dk/admin ved at medsende et 0. Hent evt. først den begivenhed der skal opdateres for at vide hvad der i forvejen er registreret på en begivenhed.

Husk også at opdatere den DTD der medsendes når du bruger de opdaterede services (https://sogn.dk/api/dtd/api.dtd).

## 4. Præster
### A. Hent præster
Præster hentes ved at sende en GET forespørgsel til Sogn.dk API:
Hvis man forespørger uden GET parametre returneres de præster der er ansat i de sogne som man som leverandør har fået adgang til at opdatere kalender informationer for.
Man kan hente hele listen med alle præster i Danmark ved at medsende GET parameteren all med en værdi på 1 (https://sogn.dk/api/praester.xml?all=1).

**URL**

`GET https://sogn.dk/api/praester.xml`

**Svar**

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE praester PUBLIC "-//SOGN.DK//DTD XML 1.0//DA" "https://sogn.dk/api/dtd/praester.dtd">
    <praester xmlns="https://sogn.dk/api/dtd/praester.dtd">
        <praest id="[praest_id]">
            <sogn id="[sogn_id]"/>
            <navn>[navn]</navn>
        </praest>
    </praester>

| Parametre | Type | Beskrivelse |
| --- | --- | ------- |
praest_id | integer | Systemets unikke ID for prædikanten. |
| sogn_id | integer | 4-cifret myndighedskode. |
| navn | string | Prædikantens navn. |

Svar eksempel

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE praester PUBLIC "-//SOGN.DK//DTD XML 1.0//DA" "https://sogn.dk/api/dtd/praester.dtd">
    <praester xmlns="https://sogn.dk/api/dtd/praester.dtd">
        <praest id="30177419255">
            <sogn id="7741"/>
            <navn>Anne Pia Tønsberg-Jensen</navn>
        </praest>
        <praest id="30177429255">
            <sogn id="7742"/>
            <navn>Anne Pia Tønsberg-Jensen</navn>
        </praest>
        .....
    </praester>

## 5. Kirker
### A. Hent kirker
Kirker hentes ved at sende en GET forespørgsel til Sogn.dk API:

**URL**

`GET https://sogn.dk/api/kirker.xml`

*(ingen body)*

**Svar**

    <?xml version="1.0" encoding="UTF-8"?>
    <kirker xmlns="https://sogn.dk/dtd/api.dtd">
        <kirke id="[kirke_id]">
            <sogn id=”[sogn_id]"/>
            <navn>[navn]</navn>
        </kirke>
    </kirker>

| Parametre | Type | Beskrivelse |
| --- | --- | ------- |
| kirke_id |  integer | Systemets unikke ID for kirken. |
| sogn_id | integer | 4-cifret myndighedskode. |
| Navn | string | Prædikantens navn. |

## 6. Kategorier
### A. Hent kategorier
Kategorier hentes ved at sende en GET forespørgsel til Sogn.dk API:

**URL**

`GET https://sogn.dk/api/kategorier.xml`

**Svar**

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE kategorier PUBLIC "-//SOGN.DK//DTD XML 1.0//DA" "https://sogn.dk/api/dtd/kategorier.dtd">
    <kategorier xmlns="https://sogn.dk/api/dtd/kategorier.dtd">
        <kategori id="[kategori_id]">
            <navn>[navn]</navn>
            <type>[type]</type>
        </kategori>
    </kategorier>

| Parametre | Type | Beskrivelse |
| --- | --- | ------- |
| kategori_id | integer | Systemets unikke ID for kategorien. |
| Navn | string | Kategoriens navn. |
|Type | string | Kategoriens type, værende ”gudstjeneste” eller ”arrangement”. |

Svar eksempel

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE kategorier PUBLIC "-//SOGN.DK//DTD XML 1.0//DA" "https://sogn.dk/api/dtd/kategorier.dtd">
    <kategorier xmlns="https://sogn.dk/api/dtd/kategorier.dtd">
        <kategori id="31">
            <navn>Højmesse</navn>
            <type>gudstjeneste</type>
        </kategori>
        <kategori id="1">
            <navn>Gudstjeneste</navn>
            <type>gudstjeneste</type>
        </kategori>
        .....
    </kategorier>

## 7. Handlings fejl
Hvis en given handling fejler, vil Sogn.dk API levere følgende svar:

    <?xml version="1.0" encoding="UTF-8"?>
    <svar xmlns="https://sogn.dk/api/dtd/api.dtd">
        <kode>[kode]</kode>
        <besked>Fejl!</besked>
        <forklaring>[forklaring]<forklaring>
    </svar>

| Parametre | Type | Beskrivelse |
| --- | --- | ------- |
| kode | integer | Kodenr. som kan tilbageføres til en fejlmelding. |
| forklaring | string |	Forklaring af årsag til fejlmelding. |

Såfremt I modtager en anden fejl (der ikke følger ovenstående XML format) kan det måske være jeres XML som ikke opfylder kravene i DTD'en. I det tilfælde får man en fejl sendt fra libxml, som er XML parseren. Tjek evt. det XML I sender ved at bruge en IE browser og kopiere XML'en ind i den validator der kan findes her:

https://www.w3schools.com/xml/xml_validator.asp

**Bemærk:** Den brugte DTD validerer ikke med det givne svar. Det beklager vi og retter på et senere tidspunkt. Den har aldrig valideret, så det er nok noget I har fundet ud af at omgå.

## 8. Ændringslog
Version 9, udgivet januar 2021.
1.	Har tilføjet beskrivelse til online og dawaid felterne til gudstjenester og arrangementer
2.	Har rettet brugen af http til https
3.	Har lavet nogle tekst- og typografirettelser.
4.	Gjort tilgængelig på Github.

Version 8, udgivet december 2014.
1.	Har fjernet beskrivelserne til alle ...2013-adresser da disse nu er erstattet af de beskrevne. De gamle 2013 adresser kan stadig benyttes men vil blive udfaset i løbet af et par år.
2.	Har opdateret med beskrivelse af nye felter for indberetning af tællinger for gudstjenester og arrangementer og en evt smsmodtager via feltet modtager.
3.	Generel oprydning og opdatering

Version 7, blev aldrig udgivet.

Version 6, udgivet 31-5-2013:
1. Har opdateret dokumentationen med beskrivelse af nogle nye services ("...2013.xml"), som kan bruges til at indberette antal deltagere, dåbshandlinger, konfirmationer og en kommentar til hhv gudstjenester og arrangementer. Disse "...2013.xml" services skulle være bagudkompatibel så leverandører umiddelbart kan skifte til disse adresser samtidigt med at man skifter den DTD man bruger til den medsendte xml.
2. Har rettet og opdateret den DTD der refereres til i svar fra de nye services (" .. 2013.xml"). Så nu skulle man have mulighed for at parse svaret fra forespørgsler uden at få XML fejl.
3. Opdateret den DTD der medsendes ved forespørgsler til kategorier.xml både i dokumentationen og i selve svaret. Nu skulle den returnerede XML validere.
4. Opdateret den DTD der medsendes ved forespørgsler til praester.xml både i dokumentationen og i selve svaret. Nu skulle den returnerede XML validere.
5. Opdateret praester.xml så man nu kan hente alle præster i Danmark ved at medsende en all get parameter.
6. Tilføjede en bemærkning om manglende validitet af svar på sletninger.

KM Itknr.: 122129-11_v4, udgivet 10-12-2012:

1. Ændrede fejl i DOCTYPE for arrangement fra:
```
    <!DOCTYPE gudstjeneste SYSTEM "https://sogn.dk/api/dtd/arrangement.dtd">
    <arrangement> 
```

Til:
```
    <!DOCTYPE arrangement SYSTEM "https://sogn.dk/api/dtd/arrangement.dtd">
    <arrangement>
```
2.	Tilføjede kapitlet "Ændringslog" til at notere ændringer foretaget i hver ny version af denne dokumentation.
