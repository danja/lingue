# Lingue
A lightweight ontology and protocol for heterogeneous agents to negotiate communication modes, exchange profiles, and share structured deliberation (IBIS) and Prolog snippets over XMPP MUC.

## Specs
- Ontology: `docs/lingue-ontology.md` — defines the Lingue namespace (`http://purl.org/stuff/lingue/`), agent/profile classes (including re-minted RPP terms), capability and channel concepts, and controlled individuals for HumanChat, IBIS text, Prolog, and Agent Profile Exchange.
- Protocol: `docs/lingue-protocol.md` — XMPP/MUC-focused negotiation flow (disco features, offers/accepts), payload conventions (plain chat, IBIS, Prolog, profiles), and ASK/TELL semantics with meta-transparency.

## Published Docs
- GitHub Pages index: https://danja.github.io/lingue/ (HTML renditions of namespace and protocol drafts)
- Local docs index: `docs/index.md`

## Vocabularies
- Working vocabularies live in `vocabs/` (Turtle/RDF); legacy RPP terms are being re-homed in the Lingue namespace per the ontology draft.

## Starting Points
- Environment: XMPP MUC as the first transport.
- Languages supported initially: agent profile exchange (RDF), human-like chat text, IBIS-augmented text, and Prolog.

## Implementations

- **TIA (TIA Intelligence Agency)**: https://github.com/danja/tia — XMPP agent framework that integrates Lingue negotiation, IBIS payloads, and MCP client/server bridges for tool discovery and chat integration.

Contributions: see `AGENTS.md` for repository guidelines.
