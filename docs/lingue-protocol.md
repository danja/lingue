# Lingue Protocol (Draft)

## Scope and Transport
- Default environment: XMPP with **MUC** for human-visible messages and private **chat** stanzas for structured exchange.
- No custom XMPP extension required initially; rely on XEP-0030 (disco#info) for capability discovery and XEP-0045 for rooms. Payloads may carry RDF/Turtle, Prolog, or plain text in `<body>` or `jabber:x:data`.

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
- **IBIS-augmented text**: plain text plus optional RDF snippet (Turtle) in `CDATA`; always include one-line IBIS summary.
- **Prolog**: `text/x-prolog` clauses in `CDATA`; include intent (“query for issue-12 positions”) and expected bindings.

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
  <data xmlns="jabber:x:data"><![CDATA[
@prefix ibis: <https://vocab.methodandstructure.com/ibis#> .
@prefix lng:  <http://purl.org/stuff/lingue/> .

:pos-001 a ibis:Position ;
  ibis:responds-to :issue-12 ;
  rdfs:label "Migrate to CoAP" .
  ]]></data>
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
