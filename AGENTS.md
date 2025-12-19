# Repository Guidelines

## Project Structure & Module Organization
- Root contains lightweight ontology assets; no application runtime. `vocabs/` holds the published vocabularies (Turtle and RDF/XML variants). `docs/` holds design notes, prompts, and reference papers. `README.md` and `LICENSE` stay minimal by design.
- Treat `vocabs/` as the source of truth; avoid renaming the files unless the namespace changes. Keep auxiliary diagrams or drafts in `docs/brainstorm.md`.

## Build, Test, and Development Commands
- There is no build pipeline; focus on validating vocab files. Examples:
  - `riot --validate vocabs/ibis.ttl` (Apache Jena) or `rapper -i turtle -c vocabs/ibis.ttl` to check syntax.
  - `python -m rdflib.tools.rdfpipe -i turtle -o turtle vocabs/ibis.ttl > /tmp/out.ttl` to normalize and spot parser warnings.
  - `rg "@" docs` for quickly finding prefixes or examples across notes.

## Coding Style & Naming Conventions
- Use Turtle where possible; keep indentation at two spaces for predicates and lists. Group prefixes at the top, ordered by specificity (lingue prefixes before broader vocabularies like `foaf:`).
- File naming stays lowercase with hyphens; RDF/XML gets `.rdf`, Turtle gets `.ttl`. Keep namespace URIs stable and documented inside the file header comments.
- Prefer explicit IRIs over blank nodes; use SHACL shapes for capability constraints as shown in `docs/dev-prompts/requirements.md`.

## Testing Guidelines
- Before publishing changes, run a validator on every touched `.ttl`/`.rdf` file. Ensure SHACL examples remain coherent (shared prefixes resolve, shapes target existing classes).
- If you add negotiation examples, include at least one `ASK`/`TELL` exchange demonstrating intersecting capabilities. Keep examples small and runnable through the chosen RDF tool.

## Commit & Pull Request Guidelines
- Keep commit subjects imperative and concise (e.g., “add mqtt capability shape”). Current history is minimal; start setting the pattern now.
- Pull requests should summarize the ontology deltas (new classes/properties, namespace changes, SHACL updates) and mention any downstream impacts on agents that consume the vocab. Link related issues or discussion threads and attach validator output when possible.

## Security & Integrity
- Do not embed secrets or proprietary endpoints in examples. When referencing external vocabularies, pin to stable IRIs and note versions in comments. Validate downloaded references before merging to avoid corrupted vocab files.
