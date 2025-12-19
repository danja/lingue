# Lingue Ontology (Draft)

## Namespaces and Scope
- `lng:` — `http://purl.org/stuff/lingue/` (all new terms, including the RPP-derived ones).
- RPP classes/properties from `http://www.citnames.com/2001/04/rpp#` are re-minted under `lng:` with the same local names to keep continuity while living in the Lingue namespace.
- `foaf:` — `http://xmlns.com/foaf/0.1/`; `ibis:` — `https://vocab.methodandstructure.com/ibis#`.

## Core Classes
- `lng:Agent` — subclass of `foaf:Agent`; any Lingue participant (software or human).
- `lng:Profile` — re-minted RPP Profile for describing runnable or conversational capabilities.
- `lng:Component`, `lng:Availability`, `lng:Dependency`, `lng:Environment`, `lng:Library`, `lng:Algorithm`, `lng:Interface`, `lng:DataFormat`, `lng:Encoding` — RPP-derived structural classes for process description.
- `lng:Capability` — abstract ability (language, channel, behavior).
- `lng:Channel` — communication medium; initial individual `lng:XMPPMUC`.
- `lng:LanguageMode` — communicative idiom; initial individuals `lng:HumanChat`, `lng:IBISText`, `lng:PrologProgram`, `lng:AgentProfileExchange`.
- `lng:Exchange` — an interaction scoped by channel and language mode, with optional negotiation state.
- `lng:ProtocolShape` — SHACL node shapes for capability intersection.

## Core Properties
- `lng:supports` (`Agent → Capability | LanguageMode | Channel`).
- `lng:prefers` (`Agent → LanguageMode`).
- `lng:profile` (`Agent → Profile`).
- `lng:understands` (`Agent → IRI`), e.g., `ibis:`, SHACL, MIME types.
- `lng:offers`, `lng:asks`, `lng:tells` (`Agent → Exchange`) — protocol markers.
- `lng:usesLanguageMode` (`Exchange → LanguageMode`) — language selected for a given exchange.
- `lng:usesChannel` (`Exchange → Channel`) — channel selected for a given exchange.
- `lng:dependsOn` (`Profile → Dependency`), `lng:hasDependency` (alias if needed).
- `lng:in` / `lng:out` (`Profile → Interface`) — I/O description.
- `lng:availability` (`Profile → Availability`) — definition/source/executable/process.
- `lng:alang` (`Algorithm → Literal`) — programming or specification language.
- `lng:location` (`Profile → Resource`) — locator for an executable or service endpoint.

## Controlled Individuals (initial)
- `lng:HumanChat` — human-readable free text; MIME `text/plain`.
- `lng:IBISText` — text with IBIS semantics; MIME `text/plain` plus IBIS RDF option.
- `lng:PrologProgram` — Prolog clauses; MIME `text/x-prolog`.
- `lng:AgentProfileExchange` — RDF profile payload; MIME `text/turtle`, `application/rdf+xml`.
- `lng:XMPPMUC` — XMPP multi-user chat.
- `lng:Definition`, `lng:Source`, `lng:Executable`, `lng:Process` — values for `lng:availability`.

## Example (Agent profile and capability advertisement)
```turtle
@prefix lng: <http://purl.org/stuff/lingue/> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix ibis: <https://vocab.methodandstructure.com/ibis#> .

:room-bot a lng:Agent ;
  foaf:name "Room Bridge Bot" ;
  lng:supports lng:XMPPMUC, lng:HumanChat, lng:IBISText, lng:PrologProgram, lng:AgentProfileExchange ;
  lng:prefers lng:HumanChat ;
  lng:profile :room-bot-profile ;
  lng:understands ibis:, <http://www.w3.org/ns/shacl#> .

:room-bot-profile a lng:Profile ;
  lng:availability lng:Executable ;
  lng:in [ a lng:Interface ; rdfs:label "XMPP <message/> text" ] ;
  lng:out [ a lng:Interface ; rdfs:label "XMPP <message/> text" ] ;
  lng:dependsOn [ a lng:Environment ; rdfs:label "Prosody/XMPP" ] ;
  lng:alang "JavaScript" .
```

## SHACL Intersection (sketch)
- A `lng:ProtocolShape` targets `lng:Agent` and asserts at least one shared `lng:LanguageMode` and `lng:Channel` via `lng:supports`, mirroring the negotiation idea in `docs/dev-prompts/requirements.md`. Use SHACL `sh:in` or `sh:hasValue` constraints to validate overlap.
