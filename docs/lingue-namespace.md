# Lingue Namespace Document (Version 0.1.1)

**Namespace**: `http://purl.org/stuff/lingue/`

**This version**: `vocabs/lingue.ttl`  
**Title**: Lingue Ontology  
**Status**: Draft 0.1.1 (payload-enabled exchanges, XMPP/MUC focus)  
**License**: see `LICENSE` in this repository  

## Abstract
Lingue is a lightweight ontology for agents to advertise capabilities, exchange profiles, and negotiate structured or unstructured communication. The initial environment is XMPP multi-user chat; supported language modes include human chat, IBIS-annotated text, Prolog snippets, and RDF-based agent profile exchange.

## Table of Contents
- Namespace
- Classes
- Object Properties
- Datatype Properties
- Individuals
- References
- Change History

## Namespace
All terms are in the Lingue namespace: `http://purl.org/stuff/lingue/` (prefix `lng:`). Legacy RPP profile terms have been re-minted into this namespace for continuity.

## Classes
- `lng:Agent` — participant in Lingue interactions (subclass of `foaf:Agent`).
- `lng:Profile` — RPP-style profile describing runnable or conversational capabilities.
- `lng:Component`, `lng:Availability`, `lng:Dependency`, `lng:Environment`, `lng:Library`, `lng:Algorithm`, `lng:Interface`, `lng:DataFormat`, `lng:Encoding` — re-minted structural classes for process description.
- `lng:Capability` — advertised ability; `lng:Channel` and `lng:LanguageMode` specialize it.
- `lng:Exchange` — interaction instance (negotiation/ASK/TELL).
- `lng:ProtocolShape` — SHACL node shapes for validating capability intersection.

## Object Properties
- `lng:supports` (`Agent → Capability`) — advertised abilities (channels, language modes).
- `lng:prefers` (`Agent → LanguageMode`) — preferred outgoing mode.
- `lng:profile` (`Agent → Profile`) — links an agent to its profile.
- `lng:understands` (`Agent → rdfs:Resource`) — vocabularies/serializations understood (e.g., IBIS, SHACL, MIME IRIs).
- `lng:offers` / `lng:asks` / `lng:tells` (`Agent → Exchange`) — protocol markers.
- `lng:usesLanguageMode` (`Exchange → LanguageMode`) — selected mode for an exchange.
- `lng:usesChannel` (`Exchange → Channel`) — selected channel for an exchange.
- `lng:payload` (`Exchange → rdfs:Resource`) — structured payload element (RDF or JSON) associated with an exchange.
- `lng:dependsOn` (`Profile → Dependency`) — required dependencies; alias `lng:hasDependency`.
- `lng:in` / `lng:out` (`Profile → Interface`) — input/output description.
- `lng:availability` (`Profile → Availability`) — machine-readability level.
- `lng:location` (`Profile → rdfs:Resource`) — locator for executables or endpoints.

## Datatype Properties
- `lng:alang` (`Algorithm → Literal`) — programming or specification language of the algorithm.

## Individuals (Controlled Terms)
- Language modes: `lng:HumanChat`, `lng:IBISText`, `lng:PrologProgram`, `lng:AgentProfileExchange`.
- Channel: `lng:XMPPMUC`.
- Availability values: `lng:Definition`, `lng:Source`, `lng:Executable`, `lng:Process`.

## References
- Ontology source: `vocabs/lingue.ttl`
- Related specs: `docs/lingue-ontology.md`, `docs/lingue-protocol.md`
- External vocabularies: FOAF (`http://xmlns.com/foaf/0.1/`), IBIS (`https://vocab.methodandstructure.com/ibis#`), SHACL (`http://www.w3.org/ns/shacl#`)

## Change History
- **0.1.1** — Added `lng:payload` for structured exchange payload references (RDF/JSON).
- **0.1.0** — Initial Lingue namespace, re-minting RPP profile terms; XMPP MUC and language mode individuals (human chat, IBIS text, Prolog, agent profile exchange); availability controlled terms added.
