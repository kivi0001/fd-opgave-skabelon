### REFLEKSION

Vi har i opgaven haft til formål at omsætte et Figma design til et færdigt site i frameworket Astro, ved brug af de værktøjer vi har lært i temaet om robust CSS.
Vi har gennem semestrene haft fokus på responsivitet, men i denne opgave har vi lært om, og anvendt nye metoder til at sikre responsivitet, meningsfuld struktur, og vedligeholdelsesvenlig kode.

Udfordringerne har primært været at omsætte tidligere viden om responsivitet i en ny sammenhæng. Vi har oplevet udfordringer med fx at anvende subgrid og container-queries, og samtidig har vi gennem udfordringerne lært om fordelene ved værktøjerne. På den måde har vores udfordringer også været vores successer, da læringen og forståelsen er kommet gennem anvendelsen.

### EKSEMPLER PÅ KODE

Vi har valgt at lave et makrolayout på siden, men da vi tidligt i processen fandt det meningsfuldt at lave vores makro-layout på 'main' html-elementet, fik vi senere lidt udfordringer, da vores 'header' og 'footer' skulle anvende samme grid - dette gav en læring om det overblik vi skal have i starten af processen, for at spare arbejdsbyrden senere i processen.
Bortset fra det, har vi anvendt subgrid fra makro-layoutet mange steder i opgaven, og set fordelen ved at have et overordnet udgangspunkt for al anden senere styling. Gennem Astros struktur med komponenter, har det været meningsfuldt at have et makro-layout, når flere forskellige personer arbejder på forskellige komponenter.

# Her ses et uddrag af vores makro-layout, fra css filen 'global.css':

:root {
--content-width: 1200px;
--half: minmax(0, calc(var(--content-width) / 2));
}

body {
margin-inline: auto;
}

main {
display: grid;
grid-template-columns:
[full-start] minmax(1rem, 1fr)
[content-start] var(--half)
[middle]
var(--half) [content-end]
minmax(1rem, 1fr) [full-end];
}

section,
header,
footer {
display: grid;
grid-template-columns: subgrid;
grid-column: full-start / -1; > :not(&) {
grid-column: content;
}
}

# og her ses et uddrag af anvendelsen af subgrid ifm makro-layout, fra komponenten 'Features.astro':

    .value-container[data-surface="dark"] {
      background-color: var(--color-ui-primary);
      color: var(--color-on-ui-primary);
      display: grid;
      grid-template-columns: subgrid;
      grid-column: full / -1;
      text-wrap: pretty;
      padding: var(--section-spacing-l);

      .inner-container {
        container-type: inline-size;
        container-name: cards;
        grid-column: content;
        margin: var(--space-s) 0;
      }
    }

Derudover har vi også forsøgt at anvende nogle af variablerne fra makro-layoutet --content-width og --half, til at udregne størrelse andre steder på siden, som et forsøg på at sikre responsivitet, relativt til sidens størrelse, og i et vedligeholdelsesvenligt format.

# Her ses et eksempel på anvendelse af --content-width i en calc()-sammenhæng, fra komponenten 'Team.astro':

    .header-div {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(0, var(--content-width)));

      .team-text-div {
        max-width: calc(var(--content-width) / 2);
      }
    }

Samtidig har anvendelsen af custom properties og tokens været meget meningsfuld i grupperegi. Dog kræver det øvelse at opstarte et projekt; hvor starter vi, i hvilken rækkefælge er det meningsfuldt at opbygge global styling, og hvordan fordeler vi disse opgaver.

Samtidig ser vi fordelene ved at bygge vedligeholdelsesvenlig kode gennem denne opgave og de givne værktøjer. Når vi har skulle lave rettelser i koden og stylingen, har vi oplevet både fordelene, når vi har anvendt værktøjerne korrekt, og ulemperne, når man er sprunget over hvor gærdet er lavest tidligere i processen.

Fx fik vi hurtigt lavet 'primitives' på --step og --space, men fik først senere i processen lavet tokens til den konkrete anvendelse af disse. Her kunne vi have sparet tid, og sikret en ensrettet arbejdsgang og styling, ved at forholde os til disse tidligere i processen.

Derudover har vi også eksperimenteret lidt i opgaven, med forskellige løsninger. Udover at afprøve @container queries, responsive grids og calc() til forskellige tilgange til responsivitet, har vi fx også prøvet 2 forskellige løsninger af til den gule streg under overskriften.

# Her ses uddrag af koden af gul streg til Hero:

    .underline {
      position: relative;
      display: inline-block;

      > span {
        position: relative;
        z-index: 1;
      }
    }
    .underline::before {
      content: "";
      position: absolute;
      bottom: 0.09rlh;
      right: 0;
      min-width: 100vw;
      min-height: 4px;
      background: linear-gradient(90deg, black, var(--color-surface-highlight));
      z-index: 0;
    }

Her har blev udfordringen at den gule streg ikke "bryder" under de bogstaver som rager ud under x-højden på fonten.

# Her ses uddrag af kode af gul streg til resterende overskrifter, med anvendelse af anchor positioning:

    h1 {
      font-size: xx-large;
      font-family: var(--font-heading);
      position: relative;
      text-decoration: underline 4px var(--color-surface-highlight);

      anchor-name: --title;
    }

    /* Anchor Positioning (uden magic numbers) */
    .h1-yellow h1::after {
      content: "";
      position: absolute;

      inset-block-start: anchor(--title bottom);
      inset-inline-start: anchor(--title left);

      width: 45rem;
      height: 4px;
      top: 31px;
      background: var(--color-surface-highlight);
    }

Her blev udfordringen for os, at gøre det fuldt funktionelt i en responsiv sammenhæng.

### DEFENSIVE CSS

Særligt gennem vores opbygning af layout og responsivitet, indtænker vi defensive CSS. Ved at vi designer til forskellige container-størrelser eller tilstande vha @container queries, eller ved brug af fluid typography og spacing, sikrer vi en flydende responsivitet på tværs af skærme og størrelser, som ikke er bundet til den specifikke pixel-størrelse på en given skærm. På den måde undgår vi også for mange breakpoints, og sikrer både en bedre brugeroplevelse, og en mere vedligeholdelsesvenlig kode på sigt.

### PROGRESSIVE ENHANCEMENT

Vi har i komponenten AccordionItem.astro indtænkt progressive enhancement, hvor vi forholder os til de browsere som ikke kan læse de keywords, som vi anvender i animationen af details-indholdet, når dette foldes ud.
På den måde tager vi et valg om et design af sitet, som ikke er begrænset af dette benspænd - men samtidig sikrer vi en god oplevelse for alle brugere uanset browser, ved at forholde os til oplevelsen af det valgte design i de tilfælde, hvor det ønskede design ikke kan konverteres i den pågældende browser, som brugeren anvender.

### OPBYGNING AF CSS STRUKTUR

Vi har valgt at anvende de udleverede @layers til opbygningen af vores CSS struktur.
Det vil sige at vi har en rækkefølge af proritet til de forskellige CSS-filer. "Nederst" ligger et reset af default-styling i reset.css. Derefter ligger vores globale styling af main-grid, overordnet styling på tekster osv - dvs vores egen "default" styling. Det næste lag er tokens.css, hvor vi har samlet alle vores variabler for font-størrelse, spacing, farver og border-radius. Stylesheetet components.css ligger som vores sidste/øverste lag, som derved kan overskrive andre css-lag. Derfor har vi i alle komponenter skrevet vores styling inde i dette lag. Main.css samler alle lagene, og definerer den proriterede rækkefælge, som er importeret til vores Layout.astro komponent. Denne komponent anvendes på alle pages, og er derved implementeret på hele sitet.

### AFRUNDING

Vi er gennem opgaven blevet mere fortrolige med CSS og Astro. Vi er blevet klogere på både responsivitet, layout og tilgange til vedligeholdelsesvenlig kode. Derudover har vi fået et nyt indblik i tilgængelighed, og tilgange til at sikre en god brugeroplevelse for alle. Vi har fået et nyt indblik i arbejdet med at omsætte andres design til et færdigt site, og hvordan dette kan gøres i et framework som Astro i grupperegi. Derved er vi også blevet klogere på hvordan man kan sikre ensretning i et team, ved hjælp af de givne værktøjer fra undervisningen.
