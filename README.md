# abac-hr-data — HR-feiten (synthetisch), laag vertrouwen

Dit repo bestaat om één ding te scheiden van het beleid dat erop leunt: **wie
iemand in de organisatie is** (dit repo) staat los van **wat die feiten mogen
betekenen voor data-toegang** (de `organisatie_naar_toegang.json`-vertaaltabel
in de PAP, `github.com/mwisselaar/policies`).

## Wat hier staat en waarom niets anders

- `hr_kenmerken.json` — per medewerker: `login`, naam, afdeling, functie,
  `business_clearance`, `sinds`. **Nooit** een toegangsattribuut
  (`clearance`/`allowed_domains`/`allowed_tiers`/`row_scope`) — die bestaan
  uitsluitend in de PAP, buiten het bereik van dit repo en van wie hier
  schrijft.
- `kustomization.yaml` — rendert bovenstaand bestand tot de ConfigMap
  `hr-kenmerken` in namespace `opa`.
- Verder niets. Dat is met opzet: schrijftoegang tot dit repo mag nooit meer
  betekenen dan "welke afdeling/functie heeft deze persoon vandaag".

## Wie schrijft, en met welk vertrouwen

Alleen de Streamlit mini-HR-webUI (`abac-classificatie-met-opa`-project,
`hr-webui`), met een eigen fine-grained GitHub-token dat **uitsluitend**
push-recht heeft op dít repo. Dat token kan de PAP niet raken — een andere
repo, een ander credential, geen gedeeld schrijfpad. Zie
`ABAC-classificatie-met-OPA/phases/fase-5-hr-webui.md` (in `datalab`) voor de
volledige redenering en de negatieftest die dat aantoont (I28).

## Bezorging

Publiek leesbaar (de inhoud is synthetische, door Faker gegenereerde
proefproject-data — geen echte personen) zodat ArgoCD dit repo zonder
credential kan pullen. Een eigen ArgoCD-Application (`abac-hr-data`) en een
eigen AppProject (idem) — bewust **niet** hetzelfde mechanisme als de PAP:
dit repo staat niet in de `sourceRepos` van de PDP- of policy-Applications,
zodat een gecompromitteerde of buggy schrijfactie hier nooit de
beleidsleverstraat kan raken.

## Herkomst van de eerste commit

De seed-commit is de eenmalige output van
`ABAC-classificatie-met-OPA/proefproject/generator/generate_hr_kenmerken.py`
(`datalab`-repo), gecontroleerd byte-identiek tegen de PAP-kopie op het
moment van overzetten. Vanaf deze commit is dít repo de enige levende bron
van `hr_kenmerken` — de PAP-kopie blijft alleen bestaan als testfixture voor
de rego-tests, niet meer als bron voor de centrale PDP.
