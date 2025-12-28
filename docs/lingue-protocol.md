# Lingue Protocol (Draft)

## Scope and Transport
- Default environment: XMPP with **MUC** for human-visible messages and private **chat** stanzas for structured exchange.
- No custom XMPP extension required initially; rely on XEP-0030 (disco#info) for capability discovery and XEP-0045 for rooms. Payloads may carry RDF/Turtle, Prolog, or plain text in `<body>` or Lingue `payload` elements.

## Roles
- **Lingue Agent**: advertises capabilities, negotiates, executes ASK/TELL.
- **Human**: participates via `lng:HumanChat`; may trigger negotiations.

## Capability Advertisement
- Use disco#info features with Lingue IRIs, e.g.:
  - `http://purl.org/stuff/lingue/feature/lang/human-chat`
  - `http://purl.org/stuff/lingue/feature/lang/ibis-text`
  - `http://purl.org/stuff/lingue/feature/lang/prolog`
  - `http://purl.org/stuff/lingue/feature/lang/agent-profile-exchange`
  - `http://purl.org/stuff/lingue/feature/channel/xmpp-muc`

## Minimal Negotiation Flow
1) **Discover** — request disco#info; inspect shared features.
2) **Offer** — post to room: “Lingue-capable; supports IBIS and Prolog; reply ‘yes ibis’ to enable structured exchange.”
3) **Accept** — peer responds privately (NL or form) selecting a `feature/lang/*`.
4) **Exchange** — switch to agreed mode; structured payload + short human summary posted to room (meta-transparency).
5) **Close** — send summary to room; optionally provide link/IRI for persisted artifacts.

## Payload Conventions
- **Agent profile exchange**: Turtle/XML carrying `lng:Profile` and dependencies; include a one-line purpose.
- **Human-like chat**: free text in `<body>`.
- **IBIS-augmented text**: plain text plus optional RDF snippet (Turtle) in a `payload`; always include one-line IBIS summary in the room.
- **Prolog**: `text/x-prolog` clauses in a `payload`; include intent (“query for issue-12 positions”) and expected bindings.
- **Status/meta-transparency**: `payload` in `lng:HumanChat` mode with JSON `{ type: "status", ... }` and empty `<body>` when suppressing chat noise.

## ASK/TELL Semantics
- **ASK**: request graph pattern or capability; in RDF, use `lng:asks` linking agent to an `lng:Exchange` that contains the graph pattern. Include a paraphrase in `<body>`.
- **TELL**: assert graph; use `lng:tells`; include provenance (who/when) and mode (`lng:IBISText` or `lng:PrologProgram`). Always pair with a plain-language summary to the room when exchanged privately.

## Example Stanzas
**Discovery result**
```xml
<iq type="result" from="agent@example.org">
  <query xmlns="http://jabber.org/protocol/disco#info">
    <feature var="http://purl.org/stuff/lingue/feature/lang/human-chat"/>
    <feature var="http://purl.org/stuff/lingue/feature/lang/ibis-text"/>
    <feature var="http://purl.org/stuff/lingue/feature/lang/prolog"/>
    <feature var="http://purl.org/stuff/lingue/feature/lang/agent-profile-exchange"/>
    <feature var="http://purl.org/stuff/lingue/feature/channel/xmpp-muc"/>
  </query>
</iq>
```

**Offer structured exchange (private)**
```xml
<message to="peer@example.org" type="chat">
  <body>I can share IBIS RDF or Prolog queries. Choose a mode?</body>
  <x xmlns="jabber:x:data" type="form">
    <field var="mode" type="list-single">
      <option label="IBIS Text"><value>http://purl.org/stuff/lingue/feature/lang/ibis-text</value></option>
      <option label="Prolog"><value>http://purl.org/stuff/lingue/feature/lang/prolog</value></option>
    </field>
  </x>
</message>
```

**TELL (IBIS snippet + NL summary)**
```xml
<message to="peer@example.org" type="chat">
  <body>Summary: Position = migrate to CoAP; supports: lower overhead, IoT fit.</body>
  <payload xmlns="http://purl.org/stuff/lingue/" mime="text/turtle" mode="http://purl.org/stuff/lingue/IBISText"><![CDATA[
@prefix ibis: <https://vocab.methodandstructure.com/ibis#> .
@prefix lng:  <http://purl.org/stuff/lingue/> .

:pos-001 a ibis:Position ;
  ibis:responds-to :issue-12 ;
  rdfs:label "Migrate to CoAP" .
  ]]></payload>
</message>
```

**Status payload (room transparency)**
```xml
<message to="room@conference.example.org" type="groupchat">
  <body></body>
  <payload xmlns="http://purl.org/stuff/lingue/" mime="application/json" mode="http://purl.org/stuff/lingue/HumanChat">
    {"type":"status","sessionId":"abc-123","message":"MFR solution request for abc-123","timestamp":"2025-12-28T09:20:21.448Z"}
  </payload>
</message>
```

## Error and Fallback
- If no shared features are discovered, remain in `lng:HumanChat` and state that structured exchange is unavailable.
- On timeouts or rejections, announce to the room: e.g., “Structured exchange declined; continuing in plain chat.”

## Security and Integrity (initial)
- Do not transmit secrets; Prolog payloads MUST be sandboxed by receivers.
- Agents SHOULD sign or provide retrievable IRIs for profile documents; consumers validate before execution.

## Open Items for Review
- Confirm IRIs for protocol states (e.g., negotiation open/closed).
- Add SHACL for MUC initiation policy and feature intersection validation.
- Decide where IBIS/Prolog artifacts are persisted (room log vs. external store).

## Change Notes
- 0.1.1: clarified `payload` elements (mode/mime metadata) and status JSON for meta-transparency.
