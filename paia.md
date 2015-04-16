# Introduction

The **PAIA Ontology** defines an encoding of library patron account information
in RDF. The Ontology aligned with [Patrons Account Information API
(PAIA)](http://gbv.github.io/paia) by giving a mapping of PAIA core request
parameters and JSON response to RDF.

Updates and sources of this document can be found in a public git repository at
<http://github.com/gbv/paia-rdf>. The master file
[paia.md](https://github.com/gbv/paia/blob/master/paia.md) is written
in [Pandoc’s Markdown](http://johnmacfarlane.net/pandoc/demo/example9/pandocs-markdown.html).
PAIA Ontology in [in RDF/Turtle](paia.ttl), [in RDF/XML](paia.owl) and
this HTML document are generated from the master file with
[makespec](https://github.com/jakobib/makespec). The specification can be
distributed freely under the terms of CC-BY-SA.

***THIS DOCUMENT IS AN INCOMPLETE DRAFT***

# Overview

PAIA Ontology reuses classes and properties from other ontologies and defines a
small set of additional classes, properties, and individuals to express
information about [patrons in RDF], [documents in RDF], and [fees in
RDF]. 

RDF Serializations of PAIA Ontology are available in RDF/Turtle
([**`paia.ttl`**](./paia.ttl)) and in RDF/XML ([**`paia.owl`**](./paia.owl)).

## Namespaces and Ontology

The URI namespace of PAIA ontology is [http://purl.org/ontology/paia#](http://purl.org/ontology/paia#).
The namespace prefix `paia` is recommended. The URI of PAIA ontology as a whole is
<http://purl.org/ontology/paia>.

~~~ {.ttl}
@prefix paia: <http://purl.org/ontology/paia#> .
@base         <http://purl.org/ontology/paia> .
~~~

The following namspace prefixes are used to refer to related ontologies:

~~~ {.ttl}
@prefix bibo:    <http://purl.org/ontology/bibo/> .
@prefix cc:      <http://creativecommons.org/ns#> .
@prefix daia:    <http://purl.org/ontology/daia/> .
@prefix dct:     <http://purl.org/dc/terms/> .
@prefix dso:     <http://purl.org/ontology/dso#> .
@prefix frbr:    <http://purl.org/vocab/frbr/core#> .
@prefix holding: <http://purl.org/ontology/holding#> .
@prefix owl:     <http://www.w3.org/2002/07/owl#> .
@prefix rdfs:    <http://www.w3.org/2000/01/rdf-schema#> .
@prefix ssso:    <http://purl.org/ontology/ssso#> .
@prefix vann:    <http://purl.org/vocab/vann/> .
@prefix voaf:    <http://purl.org/vocommons/voaf#> .
@prefix xsd:     <http://www.w3.org/2001/XMLSchema#> .
~~~

PAIA Ontology is defined in RDF/Turtle as following:

~~~ {.ttl}
<> a owl:Ontology, voaf:Vocabulary ;
    dct:title "PAIA Ontology"@en ;
    rdfs:label "PAIA" ;
    vann:preferredNamespacePrefix "paia" ;
    vann:preferredNamespaceUri "http://purl.org/ontology/paia#" ;
    dct:description "An ontology to express library patron information, such as loans, reservations, and fees."@en ;
    dct:modified "{GIT_REVISION_DATE}"^^xsd:date ;
    owl:versionInfo "{VERSION}" ;
    cc:license <http://creativecommons.org/licenses/by/3.0/> ;
    dct:creator "Jakob Voß" . 
~~~

## Patrons in RDF

[patrons in RDF]: #patrons-in-rdf

A patron account, as returned by the PAIA core method [patron] is represented
by an instance of the class **paia:PatronAccount**. Every patron account is
also an instance of [sioc:User] (and therefore also of [foaf:OnlineAccount])
and an instance of [particip:Role]. The date of expiration can be expressed
with [particip:endDate]. The patron identifier is given with
[foaf:AccountName]. 

A patron account belongs to a person or another [foaf:Agent], connected to with
[sioc:account_of] and [foaf:account]. The full name of a patron is given with
[foaf:name] and its email address can be given with [foaf:mbox]. The address
field SHOULD NOT be mapped to RDF properties such as [schema:address] and
[vcard:hasAddress] which expect a structured object instead of a plain literal
value. The generic property [dbp:address] can be used instead.

~~~ {.ttl}
@prefix sioc:     <http://rdfs.org/sioc/ns#> .
@prefix foaf:     <http://xmlns.com/foaf/0.1/> .
@prefix particip: <http://purl.org/vocab/participation/schema#> .

paia:PatronAccount a owl:Class ;
    rdfs:label "PatronAccount"@en ;
    rdfs:subClassOf sioc:User, foaf:OnlineAccount, particip:Role ;
    rdfs:isDefinedBy <> ;
    rdfs:seeAlso
        sioc:account_of, foaf:account, particip:endDate, 
        foaf:name, foaf:mbox . 
~~~

[dbp:address]: http://live.dbpedia.org/property/address
[foaf:AccountName]: http://xmlns.com/foaf/0.1/AccountName
[foaf:Agent]: http://xmlns.com/foaf/0.1/Agent
[foaf:OnlineAccount]: http://xmlns.com/foaf/0.1/OnlineAccount
[foaf:account]: http://xmlns.com/foaf/0.1/account
[foaf:mbox]: http://xmlns.com/foaf/0.1/mbox
[foaf:name]: http://xmlns.com/foaf/0.1/name
[particip:Role]: http://purl.org/vocab/participation/schema#Role
[particip:endDate]: http://purl.org/vocab/participation/schema#endDate
[schema:address]: http://schema.org/address
[sioc:User]: http://rdfs.org/sioc/ns#User
[sioc:account_of]: http://rdfs.org/sioc/ns#account_of
[vcard:hasAddress]: www.w3.org/TR/vcard-rdf/

An instance of paia:patronAccount is assumed to be active, unless it is also
an instance of **paia:InactivePatronAccount**.

~~~ {.ttl}
paia:InactivePatronAccount a owl:Class ;
    rdfs:label "InactivePatronAccount"@en ;
    rdfs:isDefinedBy <> ;
    rdfs:subClassOf paia:PatronAccount .
~~~~

Reasons for inactivation can be given with property **paia:inactivationReason**.
The inactivation reasons **paia:AccountExpired** and **paia:OutstandingFees**
SHOULD be linked to.

~~~ {.ttl}
paia:inactivationReason a rdfs:Property ;
    rdfs:label "inactivationReason"@en ; 
    rdfs:isDefinedBy <> ;
    rdfs:domain paia:InactivePatronAccount . 
paia:AccountExpired a rdfs:Resource ;
    rdfs:isDefinedBy <> ;
    rdfs:label "AccountExpired"@en .
paia:OutstandingFees a rdfs:Resource ;
    rdfs:isDefinedBy <> ;
    rdfs:label "OutstandingFees"@en .
~~~

## Items in RDF

[documents in RDF]: #documents-in-rdf

Lists of [documents](#document-data-type), as returned by the PAIA core methods
[items], [request], [renew], and [cancel], are represented as sets of events.
Each event is an instance of **[ssso:ServiceEvent]** from the [Simple Service
Status Ontology] (SSSO) and an instance of of a specific document service class
defined in the [Document Service Ontology] (DSO).

The current [service status](#data-types) of a document service event is given
by an instance-relationship (rdf:type) with one of the following classes:

* [ssso:ReservedService](http://purl.org/ontology/ssso#ReservedService) 
  for service status 1 (reserved)
* [ssso:PreparedService](http://purl.org/ontology/ssso#PreparedService)
  for service status 2 (ordered)
* [ssso:ExecutedService](http://purl.org/ontology/ssso#ExecutedService) 
  for service status 3 (held)
* [ssso:ProvidedService](http://purl.org/ontology/ssso#ProvidedService) 
  for service status 4 (provided)
* [ssso:RejectedService](http://purl.org/ontology/ssso#RejectedService)
  for service status 5 (rejected)

The specific type of service is further given by in instance-relationship with on
of the following classes (*this needs some clarification!*):

* [dso:Loan] (borrow to use at home for a limited time)
* [dso:Presentation] (view/use within the boundaries of a library)
* [dso:Interloan] (get a document/copy mediated from another library)
* [dso:OpenAccess] (get directed to the location of a publicly available document)

~~~ {.ttl}
ssso:ServiceEvent a owl:Class ;
    rdfs:label "ServiceEvent"@en ;
    rdfs:isDefinedBy <http://purl.org/ontology/ssso> .
dso:DocumentService a owl:Class ;
    rdfs:label "DocumentService"@en ;
    rdfs:isDefinedBy <http://purl.org/ontology/dso> .
dso:ServiceConsumer a owl:Class ;
    rdfs:label "ServiceConsumer"@en ;
    rdfs:isDefinedBy <http://purl.org/ontology/service> .
~~~

The service event is connected to a patron as [service:ServiceConsumer] 
(with property [service:consumedBy]) and to a document 
(*with a property yet to be defined*).

The `starttime` end `endtime` can be mapped to any of the following properties,
among others:

starttime
  : schema:startDate, prov:prov:startedAtTime, prov:qualifiedStart
endtime
  : schema:endDate, prov:endedAtTime, prov:qualifiedEnd

[ssso:ServiceEvent]: http://purl.org/ontology/ssso#ServiceEvent
[dso:Loan]: http://purl.org/ontology/dso#Loan
[dso:Presentation]: http://purl.org/ontology/dso#Presentation
[dso:Interloan]: http://purl.org/ontology/dso#Interloan
[dso:OpenAccess]: http://purl.org/ontology/dso#OpenAccess
[service:ServiceConsumer]: http://purl.org/ontology/service#ServiceConsumer
[service:consumedBy]: http://purl.org/ontology/service#consumedBy

## Fees in RDF

[fees in RDF]: #fees-in-rdf

A fee, as returned by the method [fees], is an amount of money that has to be
paid by a patron for some reason. Each fee is represented by the following
properties of a `ssso:ServiceEvent` instance:
    
* `dc:date` (or a more specific sub property) for `fee.date`
* `schema:price` and `schema:priceCurrency` for `fee.amount`
* `dc:description` for `fee.about`
* Maybe `schema:itemOffered` to connect to a document or document service
  (item and/or edition).

The type of fee is represented by a class from the [Document Service Ontology] or
by another subclass of class [ServiceEvent] from the [Simple Service Status Ontology].
All URIs returned in `fee.feeid` SHOULD be resolvable as Linked Open Data.

## PAIA data types in RDF

string
  : A Unicode string. Strings MAY be empty.
nonnegative integer
  : An integer number larger than or equal to zero.
boolean
  : Either true or false. Note that omitted boolean values are *not* false by 
    default but unknown!
date
  : A date value in `YYYY-MM-DD` format. A datetime value with time and timezone
    SHOULD be used instead, if possible.
datetime
  : A date value in `YYY-MM-DD` format, optionally followed by a time value. A
    time value consists of the letter `T` followed by `hh:mm:ss` format, and a 
    timezone indicator (`Z` for UTC or `+hh:mm` or `-hh:mm`) where:

    * `YYYY` indicates a year (`0001` through `9999`)
    * `MM` indicates a month (`01` through `12`)
    * `DD` indicates a day (`01` through `31`)
    * `hh` indicates an hour (`00` through `23`)
    * `mm` indicates a minute (`00` through `59`)
    * `ss` indicates a second (`00` through `59`)

    Examples of valid datetime values include `2015-03-20` (a date),
    `2016-03-09T11:58:19+10:00`, and `2017-08-21T12:24:28-06:00`.

money
  : A monetary value with currency (format `[0-9]+\.[0-9][0-9] [A-Z][A-Z][A-Z]`),
    for instance `0.80 USD`.
email
  : A syntactically correct email address.
URI
  : A syntactically correct URI.

account state
  : A nonnegative integer representing the current state of a patron account. 
    Possible values are:

    0. active
    1. inactive
    2. inactive because account expired
    3. inactive because of outstanding fees
    4. inactive because account expired and outstanding fees

    A PAIA server MAY define additional states which can be mapped to `1` by PAIA 
    clients. In JSON account states MUST be encoded as numbers instead of strings.

service status
  : A nonnegative integer representing the current status in fulfillment of a
    service. In most cases the service is related to a document, so the service
	status is a relation between a particular document and a particular patron. 
	Possible values are:

    0. no relation (this applies to most combinations of document and patron, and
       it can be expected if no other state is given)
    1. reserved (the document is not accessible for the patron yet, but it will be)
    2. ordered (the document is being made accessible for the patron)
    3. held (the document is on loan by the patron)
    4. provided (the document is ready to be used by the patron)
    5. rejected

    A PAIA server MUST NOT define any other service status. In JSON service status
    MUST be encoded as numbers instead of strings.

### Document data type

A **document** is a key-value structure with the following fields

 name        occ    data type             description
----------- ------ --------------------- ----------------------------------------------------------
 status      1..1   service status        status (0, 1, 2, 3, 4, or 5)
 item        0..1   URI                   URI of a particular copy
 edition     0..1   URI                   URI of a the document (no particular copy)
 requested   0..1   URI                   URI that was originally requested
 about       0..1   string                textual description of the document
 label       0..1   string                call number, shelf mark or similar item label
 queue       0..1   nonnegative integer   number of waiting requests for the document or item
 renewals    0..1   nonnegative integer   number of times the document has been renewed
 reminder    0..1   nonnegative integer   number of times the patron has been reminded
 starttime   0..1   datetime              date and time when the status began  
 endtime     0..1   datetime              date and time when the status will expire
 duedate     0..1   date                  date when the current status will expire (*deprecated*)
 cancancel   0..1   boolean               whether an ordered or provided document can be canceled
 canrenew    0..1   boolean               whether a document can be renewed
 error       0..1   string                error message, for instance if a request was rejected
 storage     0..1   string                location of the document
 storageid   0..1   URI                   location URI
----------- ------ --------------------- ----------------------------------------------------------


For each document at least an item URI or an edition URI MUST be given.
Together, item and edition URI MUST uniquely identify a document within
the set of documents related to a patron.

The fields `starttime` and `endtime` MUST be interpreted as following:

 status   starttime                        endtime
-------- -------------------------------- --------------------------------------------------------
 0        -                                -
 1        when the document was reserved   when the reserved document is expected to be available
 2        when the document was ordered    when the ordered document is expected to be available
 3        when the document was lend       when the loan period ends or ended (due)
 4        when the document is provided    when the provision will expire
 5        when the request was rejected    -

Note that timezone information is mandatory in these fields.  The field
`duedate` is deprecated. Clients SHOULD only use it as `endtime` if no
`endtime` was given.

The response fields `label`, `storage`, `storageid`, and `queue`
correspond to properties in DAIA.

**Examples**

An example of a documentserialized in JSON is given below. In this case a
general document (`http://example.org/documents/9876543`) was requested an
mapped to a particular copy (`http://example.org/items/barcode1234567`) by the
PAIA server. The copy turned out to be lost, so the request was rejected
(status 5) at 2014-07-12, 14:07 UTC.

~~~~ {.json}
{
   "status":    5,
   "item":      "http://example.org/items/barcode1234567",
   "edition":   "http://example.org/documents/9876543",
   "requested": "http://example.org/documents/9876543",
   "starttime": "2014-07-12T14:07Z",
   "error":     "sorry, we found out that our copy is lost!"
}
~~~~

See [documents in RDF] for a mapping in RDF.

### patron

response fields
  :  name      occ    data type       description
    --------- ------ --------------- ----------------------------------------------
     name      1..1   string          full name of the patron
     email     0..1   email           email address of the patron
     address   0..1   string          freeform address of the patron
     expires   0..1   datetime        patron account expiry
     status    0..1   account state   current state (0, 1, 2, or 3)
     type      0..n   URI             list of custom URIs to identify patron types
    --------- ------ --------------- ----------------------------------------------

**Example**

~~~{.json}
{
  "name": "Jane Q. Public", 
  "email": "jane@example.org",
  "address": "Park Street 2, Springfield",
  "expires": "2015-05-18",
  "status": 0,
  "type": ["http://example.org/usertypes/default"]
}
~~~

### items

response fields
  :  name   occ    data type   description
    ------ ------ ----------- -----------------------------------------
     doc    0..n   document    list of documents (order is irrelevant)
    ------ ------ ----------- -----------------------------------------

In most cases, each document will have an item URI for a particular copy, but
users may also have requested an edition.

**Example**

~~~{.json}
{
  "doc": [{
    "status": 3,
    "item": "http://bib.example.org/105359165",
    "edition": "http://bib.example.org/9782356",
    "about": "Maurice Sendak (1963): Where the wild things are",
    "label": "Y B SEN 101",
    "queue": 0,
    "renewals": 0,
    "reminder": 0,
    "starttime": "2014-05-08T12:37Z",
    "endtime": "2014-06-09",
    "cancancel": false,
  },{
    "status": 1,
    "item": "http://bib.example.org/8861930",
    "about": "Janet B. Pascal (2013): Who was Maurice Sendak?",
    "label": "BIO SED 03",
    "queue": 1,
    "starttime": "2014-05-12T18:07Z",
    "endtime": "2014-05-24",
    "cancancel": true,
    "storage": "pickup service desk",
    "storageid": "http://bib.example.org/library/desk/7",
  }]
}
~~~

### request

request parameters
  :  name            occ    data type   description
    --------------- ------ ----------- ------------------------------
     doc             1..n               list of documents requested
     doc.item        0..1   URI         URI of a particular item
     doc.edition     0..1   URI         URI of a particular edition
     doc.storage     0..1   string      Requested pickup location
     doc.storageid   0..1   URI         Requested pickup location
    --------------- ------ ----------- ------------------------------
response fields
  :  name   occ    data type   description
    ------ ------ ----------- -----------------------------------------
     doc    1..n   document    list of documents (order is irrelevant)
    ------ ------ ----------- -----------------------------------------

### renew

request parameters
  : ------------- ------ -------- ------------------------------
     doc           1..n             list of documents to renew
     doc.item      0..1   URI       URI of a particular item
     doc.edition   0..1   URI       URI of a particular edition
    ------------- ------ --------  -----------------------------
response fields
  :  name   occ    data type   description
    ------ ------ ----------- -----------------------------------------
     doc   1..n   document     list of documents (order is irrelevant)
    ----- ------ ------------ -----------------------------------------

### cancel

request parameters
  :  name          occ    data type
    ------------- ------ ----------- -----------------------------
     doc           1..n               list of documents to cancel
     doc.item      0..1   URI         URI of a particular item
     doc.edition   0..1   URI         URI of a particular edition
    ------------- ------ ----------- -----------------------------
response fields
  :  name   occ    data type   description
    ------ ------ ----------- -----------------------------------------
     doc    1..n   document    list of documents (order is irrelevant)
    ------ ------ ----------- -----------------------------------------

### fees

response fields
  :  name          occ    data type   description
    ------------- ------ ----------- ----------------------------------------
     amount        0..1   money       Sum of all fees. May also be negative!
     fee           0..n               list of fees
     fee.amount    1..1   money       amount of a single fee
     fee.date      0..1   date        date when the fee was claimed
     fee.about     0..1   string      textual information about the fee
     fee.item      0..1   URI         item that caused the fee
     fee.edition   0..1   URI         edition that caused the fee
     fee.feetype   0..1   string      textual description of the type of fee
     fee.feeid     0..1   URI         URI of the type of fee
    ------------- ------ ----------- ----------------------------------------

If given, `fee.feetype` MUST NOT refer to the individual fee but to the type of
fee.  A PAIA server MUST return identical values of `fee.feetype` for identical
`fee.feeid`.  The default value of `fee.feeid` is:

* <http://purl.org/ontology/dso#DocumentService> if `fee.item` or `fee.edition` is set,
* <http://purl.org/ontology/service#Service> otherwise (*experimental!*).

If a fee was caused by a document (`fee.item` or `fee.edition`), the value of
`fee.feeid` SHOULD be a class URI from the [Document Service Ontology].

# References

## Normative References

* Voß, J. 2015. “Patrons Account Information API (PAIA)”.
  <http://gbv.github.io/paia>

* Bradner, S. 1997. “RFC 2119: Key words for use in RFCs to Indicate Requirement Levels”.
  <http://tools.ietf.org/html/rfc2119>.

## Informative References

* Crockford, D. 2006. “RFC 6427: The application/json Media Type for JavaScript Object Notation (JSON)”.
  <http://tools.ietf.org/html/rfc4627>.

* Klee, C. and Voss, J. 2014. “Holding Ontology“. 
  <http://purl.org/ontology/holding>. 

* Styles, Rob, Wallace, Chris and Moeller, Knud. 2008. “Participation schema“. 
  <http://vocab.org/participation/schema>.

* Voss, J. 2012. “DAIA ontology“. 
  <http://purl.org/ontology/daia>. 

* Voss, J. 2013. “Simple Service Status Ontology“.
  <http://purl.org/ontology/ssso>. 

* Voss, J. 2013. “Document Service Ontology“.
  <http://gbv.github.io/dso/>.

## Revision history

The current version of this document was last modified at {GIT_REVISION_DATE}
with revision {GIT_REVISION_HASH}.

{GIT_CHANGES}

[Document Availability Information Ontology]: http://purl.org/ontology/daia
[Simple Service Status Ontology]: http://purl.org/ontology/ssso
[Document Service Ontology]: http://gbv.github.io/dso/
[ServiceEvent]: http://purl.org/ontology/ssso#ServiceEvent

