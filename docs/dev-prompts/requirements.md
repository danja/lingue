# Lingue Requirements

*expressed as fait accompli*

Lingue is a base ontology designed to facilitate communication between heterogenous intelligent agents. 

Each *lingue-equipped* carries a description of themselves, expressed in RDF. When a pair of agents have made contact with each other, through any suitable channel, they may negotiate to find the most appropriate language through which to communicate. In the abstract, at minimum they will be able to carry out ASK and TELL operations, where TELL is a complete delimited set of declared statements and ASK is a request based on an incomplete set of declarations which requires completion.   

## Definition : Lingue-Capable Agent

A lingue-capable agent is a stateful software construct which implements some means of connecting to other software via a standard protocol together with a self-description.

## Language Negotiation

We have two agents which each has an open RDF description of their capabilities , including the communication protocols they support, SHACL shapes are used to determine what protocols they have in common.

### Example SHACL

```turtle
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix lng: <http://purl.org/stuff/lingue/> .
@prefix eg: <http://example.org/> .

# Agent capability descriptions
eg:Agent1 a foaf:Agent ;
    lng:supports lng:HTTP, lng:MQTT, lng:CoAP .

eg:Agent2 a foaf:Agent ;
    lng:supports lng:HTTP, lng:CoAP, lng:DDS .

# SHACL shape to validate protocol intersection
lng:IntersectionShape a sh:NodeShape ;
    sh:targetClass foaf:Agent ;
    sh:property [
        sh:path lng:supports ;
        sh:in ( lng:HTTP lng:CoAP ) ;  # Common protocols
        sh:minCount 1 ;
    ] .
```

## IBIS

https://vocab.methodandstructure.com/ibis

## Usage Scenario

[TIA Intelligence Agency](https://github.com/danja/tia) provides both basic XMPP client examples and a complete AI-powered chatbot service. It includes examples for connecting to XMPP servers, sending and receiving messages, working with Multi-User Chat (MUC) rooms, and deploying AI agents that can participate in conversations using the Mistral AI API.