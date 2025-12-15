# Lingue + IBIS Integration Design

## Overview

Lingue is a base ontology for communication between heterogeneous intelligent agents. This document describes how IBIS (Issue-Based Information Systems) integrates with Lingue to enable structured deliberation while maintaining human-readable communication as the primary interface.

## Core Principle: Natural Language First

The fundamental design principle is that **human-readable natural language is the default and primary communication mode**. IBIS structure and RDF representations are optional enhancements that agents can negotiate when mutually beneficial.

### Communication Layers
```
┌─────────────────────────────────────┐
│   XMPP MUC (Human-readable layer)   │
│  "I propose we use CoAP..."         │
│  "Here's why that makes sense..."   │
└─────────────────────────────────────┘
         │                    │
         │                    │ (optional structured exchange)
         ▼                    ▼
┌─────────────────────────────────────┐
│  Lingue Protocol Negotiation        │
│  "Hey, you speak IBIS-RDF?"         │
│  "Yes! Let's exchange structured"   │
└─────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────┐
│   Structured Data Exchange          │
│   (IBIS RDF, complete arguments)    │
└─────────────────────────────────────┘
```

## Lingue-Capable Agent Definition

A lingue-capable agent is a stateful software construct which implements:

1. Connection to other software via standard protocol (XMPP)
2. RDF self-description of capabilities
3. Language negotiation mechanism
4. ASK/TELL operations (minimum)

## Agent Capability Declaration
```turtle
@prefix lng: <http://purl.org/stuff/lingue/> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix ibis: <https://vocab.methodandstructure.com/ibis#> .

:Agent1 a foaf:Agent ;
    foaf:name "Sensor Monitor Bot" ;
    lng:supports lng:XMPP, lng:HTTP ;
    lng:understands ibis:, dcterms:, skos: ;
    lng:preferredFormat "text/turtle" ;
    lng:deliberationStyle :metaTransparent .
```

## XMPP Integration

### Standard XMPP Usage

The system uses standard XMPP without custom extensions:

- **XEP-0045**: Multi-User Chat (MUC) for group conversations
- **XEP-0030**: Service Discovery for capability detection
- **XEP-0054**: vcard-temp for agent profiles
- Standard `<message>`, `<iq>`, `<presence>` stanzas

### Example XMPP Flow

#### 1. Human-Readable Message to Room
```xml
<message to="room@conference.example.org" type="groupchat">
  <body>I think we should migrate to CoAP for the sensor network</body>
</message>
```

#### 2. Agent Detects Capability
```xml
<iq type="get" to="agent2@example.org">
  <query xmlns="http://jabber.org/protocol/disco#info"/>
</iq>

<iq type="result" from="agent2@example.org">
  <query xmlns="http://jabber.org/protocol/disco#info">
    <feature var="http://purl.org/stuff/lingue/ibis-rdf"/>
    <feature var="http://purl.org/stuff/lingue/ask-tell"/>
  </query>
</iq>
```

#### 3. Agent Offers Structured Exchange
```xml
<message to="agent2@example.org" type="chat">
  <body>I see you support Lingue IBIS. Would you like structured exchange?</body>
</message>
```

#### 4. Private Structured Exchange (Optional)
```xml
<message to="agent2@example.org" type="chat">
  <body>Sending structured IBIS data...</body>
  <data xmlns="jabber:x:data">
    <![CDATA[
      @prefix ibis: <https://vocab.methodandstructure.com/ibis#> .
      
      :pos-001 a ibis:Position ;
        ibis:responds-to :issue-12 ;
        rdfs:label "Migrate to CoAP" ;
        ibis:supported-by :arg-efficiency, :arg-iot-standard .
      
      :arg-efficiency a ibis:Argument ;
        ibis:supports :pos-001 ;
        rdfs:label "40% less overhead than HTTP" .
    ]]>
  </data>
</message>
```

#### 5. Meta-Transparent Explanation to Room
```xml
<message to="room@conference.example.org" type="groupchat">
  <body>Agent2 and I exchanged structured technical analysis. 
  
Summary of our deliberation:
- Issue: Migration to CoAP (issue-12)
- Position: Yes, migrate (pos-001)
- Supporting Arguments:
  * 40% less overhead than HTTP
  * RFC 7252 standard compliance
  * Better for constrained IoT devices

Full RDF available on request.</body>
</message>
```

## IBIS Natural Language Patterns

Agents recognize and generate IBIS structure through natural language:

### Pattern Recognition

| Natural Language | IBIS Element |
|------------------|--------------|
| "What should we do about X?" | Issue |
| "How can we solve Y?" | Issue |
| "I propose we..." | Position |
| "Let's try..." | Position |
| "That makes sense because..." | Supporting Argument |
| "This works well due to..." | Supporting Argument |
| "However, there's a problem..." | Objecting Argument |
| "The downside is..." | Objecting Argument |

### Example Natural Language IBIS
```
Agent1: "**Issue**: How should we handle authentication?

I see three options:
1. OAuth2 (standard, but complex)
2. API keys (simple, less secure)  
3. mTLS (secure, requires PKI)

What do others think?"

[Agent internally structures this as:
  Issue → 3 Positions]
```

## ASK/TELL Operations with IBIS

### TELL: Assert IBIS Structure
```turtle
# Agent posits an Issue
TELL {
  :issue1 a ibis:Issue ;
    rdfs:label "Should we use HTTP or MQTT for sensor data?" ;
    dct:creator :Agent1 ;
    dct:created "2025-12-15T10:30:00Z"^^xsd:dateTime .
}
```

### ASK: Query IBIS Network
```sparql
# Agent requests positions on an issue
ASK {
  ?position ibis:responds-to :issue1 ;
    a ibis:Position ;
    rdfs:label ?label ;
    dct:creator ?creator .
}
```

### Complex Deliberation Exchange
```turtle
# Complete IBIS subnet exchange
:Agent1 lng:tells :Agent2 {
    :issue-42 a ibis:Issue ;
        rdfs:label "Sensor repair prioritization strategy?" ;
        ibis:concerns :systemMaintenance .
    
    :pos-critical a ibis:Position ;
        ibis:responds-to :issue-42 ;
        rdfs:label "Prioritize by criticality score" ;
        dct:creator :Agent1 .
    
    :arg-safety a ibis:Argument ;
        ibis:supports :pos-critical ;
        rdfs:label "Safety-critical sensors must be prioritized" .
    
    :pos-geographic a ibis:Position ;
        ibis:responds-to :issue-42 ;
        rdfs:label "Prioritize by geographic proximity" ;
        dct:creator :Agent1 .
    
    :arg-efficiency a ibis:Argument ;
        ibis:supports :pos-geographic ;
        rdfs:label "Reduces technician travel time by 30%" .
}
```

## LLM Agent Integration

Many agents will be LLM-powered. They use natural language processing for:

### 1. IBIS Structure Detection
```javascript
// LLM analyzes conversation to extract IBIS structure
async function detectIBISStructure(naturalLanguageText) {
  const prompt = `
    Analyze this conversation and identify IBIS elements:
    - Issues (questions, problems to solve)
    - Positions (proposed solutions)
    - Arguments (supporting or objecting points)
    
    Conversation: "${naturalLanguageText}"
    
    Return as JSON with structure:
    {
      "issues": [...],
      "positions": [...],
      "arguments": [...]
    }
  `;
  
  return await llm.complete(prompt);
}
```

### 2. RDF Generation from Natural Language
```javascript
// LLM converts detected structure to RDF
async function generateIBISRDF(ibisStructure) {
  const prompt = `
    Convert this IBIS structure to RDF Turtle format
    using the IBIS vocabulary (https://vocab.methodandstructure.com/ibis#):
    
    ${JSON.stringify(ibisStructure)}
  `;
  
  return await llm.complete(prompt);
}
```

### 3. Natural Language from RDF
```javascript
// LLM explains structured data naturally
async function explainIBISRDF(rdfGraph) {
  const prompt = `
    Explain this IBIS RDF graph in natural language
    suitable for a chatroom audience:
    
    ${rdfGraph}
    
    Make it conversational and clear.
  `;
  
  return await llm.complete(prompt);
}
```

## Meta-Transparency

**Principle**: Agents explain their structured exchanges to humans in the room.

### Transparency Levels
```turtle
:Agent a foaf:Agent ;
    lng:transparencyLevel :metaTransparent .

# Options:
# :fullyPublic - All exchanges visible to everyone
# :metaTransparent - Agents explain what they're doing (DEFAULT)
# :private - Structured exchanges hidden (NOT RECOMMENDED)
```

### Meta-Transparent Communication Pattern
```javascript
class MetaTransparentAgent {
  async handleStructuredExchange(fromAgent, ibisData) {
    // 1. Process the structured data
    const analysis = await this.analyzeIBIS(ibisData);
    
    // 2. Generate human-readable summary
    const summary = await this.llm.complete(`
      Explain this IBIS exchange in simple terms:
      ${ibisData}
      
      Make it conversational and mention:
      - What we discussed
      - What conclusions we reached
      - Why those conclusions matter
    `);
    
    // 3. Post to room with transparency
    await this.sendToRoom(`
🤖 Structured Exchange Complete

${fromAgent.name} and I just exchanged detailed IBIS data about: "${analysis.issue}"

${summary}

Technical details available on request. Just ask!
    `);
  }
}
```

## Graceful Degradation

The system works at multiple capability levels:

| Capability | Communication Mode | Use Case |
|------------|-------------------|----------|
| Human only | Natural language text | Person in chatroom |
| Basic chatbot | NL + simple commands | Basic AI assistant |
| Lingue-aware | NL + protocol negotiation | Smart agent |
| Full Lingue+IBIS | NL + structured RDF | Sophisticated deliberation |

## TIA Implementation Example
```javascript
// TIA XMPP bot with Lingue + IBIS capability
import { Client } from '@xmpp/client';
import { parseRDF, serializeRDF } from './rdf-utils.js';
import { LLM } from './llm-client.js';

class LingueIBISBot {
  constructor(jid, password) {
    this.xmpp = new Client({ service: 'xmpp://server', username: jid, password });
    this.llm = new LLM();
    this.capabilities = new Set(['ibis-rdf', 'ask-tell', 'meta-transparent']);
  }
  
  async handleMessage(msg) {
    // Always process natural language
    const nlResponse = await this.generateNLResponse(msg.body);
    
    // Check if sender is Lingue-capable
    if (await this.isLingueCapable(msg.from)) {
      // Detect IBIS structure in conversation
      const ibisStructure = await this.detectIBISPattern(msg.body);
      
      if (ibisStructure && ibisStructure.confidence > 0.7) {
        // Offer structured exchange
        await this.offerStructuredExchange(msg.from, ibisStructure);
      }
    }
    
    // Always respond in human-readable form to room
    return this.sendToRoom(nlResponse);
  }
  
  async offerStructuredExchange(agentJID, structure) {
    // Private negotiation
    await this.sendDirect(agentJID, 
      "I detected IBIS structure. Want to exchange RDF?");
    
    // Wait for acceptance
    const accepted = await this.waitForAcceptance(agentJID);
    
    if (accepted) {
      // Exchange complete RDF graph privately
      const rdfGraph = await this.structureToRDF(structure);
      await this.sendStructured(agentJID, rdfGraph);
      
      // Meta-transparent explanation to room
      const explanation = await this.explainExchange(structure);
      await this.sendToRoom(`
🤖 Just exchanged structured IBIS data with ${agentJID}

${explanation}

Ask me for details if you're curious!
      `);
    }
  }
  
  async detectIBISPattern(text) {
    const prompt = `
      Analyze this text for IBIS patterns (Issues, Positions, Arguments).
      Return confidence score (0-1) and detected structure.
      
      Text: "${text}"
    `;
    
    return await this.llm.complete(prompt, { format: 'json' });
  }
  
  async isLingueCapable(jid) {
    // XEP-0030 Service Discovery
    const disco = await this.xmpp.iqCaller.request(
      xml('iq', { type: 'get', to: jid },
        xml('query', { xmlns: 'http://jabber.org/protocol/disco#info' })
      )
    );
    
    const features = disco.getChild('query').getChildren('feature');
    return features.some(f => 
      f.attrs.var === 'http://purl.org/stuff/lingue/ibis-rdf'
    );
  }
}

// Usage
const bot = new LingueIBISBot('bot@example.org', 'password');
bot.joinRoom('deliberation@conference.example.org');
```

## Persistent IBIS Networks

### Storage Options

#### Option A: Shared Knowledge Graph
```turtle
# Stored in triple store accessible to all agents
:network-2025-12-15 a ibis:Network ;
    dct:title "Sensor System Design Deliberation" ;
    dct:created "2025-12-15T09:00:00Z"^^xsd:dateTime ;
    ibis:hasIssue :issue-1, :issue-2, :issue-3 .

:issue-1 a ibis:Issue ;
    rdfs:label "How should we handle authentication?" ;
    ibis:response :pos-oauth, :pos-apikeys, :pos-mtls .
```

#### Option B: Distributed (Agent-Local) Cache
```javascript
// Each agent maintains local IBIS cache
class IBISCache {
  constructor() {
    this.store = new N3.Store();
  }
  
  async syncWithPeer(peerJID) {
    // Request peer's IBIS data
    const peerData = await this.requestIBIS(peerJID);
    
    // Merge into local store
    this.store.addQuads(parseRDF(peerData));
  }
}
```

#### Option C: Hybrid (Recommended)

- Real-time exchanges cached locally
- Important deliberations persisted to shared graph
- Agents sync on demand
```turtle
:Agent1 a foaf:Agent ;
    lng:cacheStrategy :hybrid ;
    lng:persistsTo <https://kb.example.org/ibis/> ;
    lng:cacheTTL "P7D"^^xsd:duration .  # 7 days
```

## Privacy Mechanisms (TBD)

### Placeholder for Future Privacy Features
```turtle
# Privacy controls to be defined
:StructuredExchange a lng:ExchangeEvent ;
    lng:privacyLevel :private ;  # or :public, :group, :pairwise
    lng:visibleTo :Agent1, :Agent2 ;
    lng:summarizeFor :room-participants .
```

**Note**: Privacy mechanisms are intentionally left open for future design decisions based on use case requirements.

## Key Design Decisions

### ✅ Decided

1. **Natural language is primary interface** - All communication has human-readable form
2. **Standard XMPP** - No custom extensions, use existing XEPs
3. **Meta-transparent by default** - Agents explain their structured exchanges
4. **LLM-powered pattern recognition** - Agents detect/generate IBIS from natural language
5. **Opt-in structured exchange** - Agents negotiate when to "upgrade" communication

### 🔄 To Be Determined

1. **Privacy granularity** - What level of privacy control is needed?
2. **Persistence strategy** - Where/how long to store IBIS networks?
3. **Conflict resolution** - How to handle disagreements about IBIS structure?
4. **Discovery mechanism** - How to find existing deliberations to join?
5. **Access control** - Who can create/modify Issues?

## Protocol Summary

### Minimal Lingue+IBIS Protocol

A minimal implementation must support:

1. **Capability Advertisement**
```turtle
   :Agent lng:understands ibis: .
```

2. **TELL Operation**
```turtle
   :Agent1 lng:tells :Agent2 { :issue1 a ibis:Issue ; ... }
```

3. **ASK Operation**
```sparql
   :Agent1 lng:asks :Agent2 { ?pos ibis:responds-to :issue1 }
```

4. **Natural Language Fallback**
   - Every structured exchange can be expressed in natural language
   - Agents can always communicate even if one doesn't support IBIS

## Next Steps

### Phase 1: Basic Implementation

1. Extend TIA XMPP bot with capability detection
2. Implement simple IBIS pattern recognition (Issue/Position/Argument)
3. Create RDF serialization/deserialization utilities
4. Add meta-transparent summary generation

### Phase 2: LLM Integration

1. Fine-tune prompts for IBIS structure extraction
2. Implement confidence scoring for pattern detection
3. Add natural language generation from RDF
4. Test with multiple LLM backends (Mistral, GPT, Claude, etc.)

### Phase 3: Multi-Agent Deliberation

1. Implement structured exchange negotiation
2. Add IBIS network persistence
3. Create visualization tools for IBIS graphs
4. Deploy multiple agents in test environment

### Phase 4: Production Hardening

1. Define privacy mechanisms
2. Add authentication/authorization
3. Implement conflict resolution
4. Performance optimization for large IBIS networks

## References

- **IBIS Vocabulary**: https://vocab.methodandstructure.com/ibis
- **gIBIS Paper**: Conklin & Begeman (1988), "gIBIS: A Hypertext Tool for Exploratory Policy Discussion"
- **TIA Project**: https://github.com/danja/tia
- **XMPP Standards**: https://xmpp.org/rfcs/
- **RDF 1.1 Turtle**: https://www.w3.org/TR/turtle/
- **SHACL**: https://www.w3.org/TR/shacl/

---

*This is a living document. Updates and refinements will be made as implementation proceeds.*