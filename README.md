# Salesforce Customers System API

API System pour l'intégration avec Salesforce Financial Services Cloud - Clients.

## Description

Cette API fournit un accès standardisé aux clients (Contacts) dans Salesforce Financial Services Cloud. Elle utilise le connecteur Salesforce de MuleSoft.

## Endpoints

### GET /api/salesforce/customers
Récupère la liste des clients Salesforce.

**Query Parameters:**
- `sfId` (optional): ID Salesforce du contact
- `limit` (optional, default: 200)
- `offset` (optional, default: 0)

### POST /api/salesforce/customers
Upsert d'un client dans Salesforce (création ou mise à jour).

### GET /api/salesforce/customers/{sfId}
Récupère un client par son ID Salesforce.

## Configuration

### Salesforce

1. Configurer les propriétés dans `src/main/resources/config.properties`:

```properties
# Salesforce Configuration
salesforce.username=your-username
salesforce.password=your-password
salesforce.securityToken=your-security-token
salesforce.environment=sandbox

# HTTP Configuration
http.port=8081
http.host=0.0.0.0
```

2. Créer une configuration Salesforce dans `global.xml` (référencée comme `Salesforce_Config`)

## Architecture Technique

### Flows Business-Logic

- `get-customers-business-logic`: Requête SOQL vers Salesforce
- `upsert-customer-business-logic`: Upsert Contact Salesforce
- `get-customer-by-sf-id-business-logic`: Get Contact par ID

### Mapping Salesforce

Les champs Salesforce mappés:
- `Id` → `externalIds[system=Salesforce].value`
- `FirstName`, `LastName` → `party.firstName`, `party.lastName`
- `PersonEmail` → `party.contactPoints[type=Email].value`
- `Phone` → `party.contactPoints[type=Phone].value`
- `AccountNumber` → `customerNumber`
- `TaxId__c` → `party.taxId`
- `Status__c` → `status`

## Exemples de Requêtes

### POST /api/salesforce/customers

```bash
curl -X POST http://localhost:8081/api/salesforce/customers \
  -H "Content-Type: application/json" \
  -d '{
    "customerNumber": "CUST001",
    "party": {
      "partyType": "Individual",
      "firstName": "John",
      "lastName": "Doe",
      "taxId": "123-45-6789"
    },
    "status": "Active",
    "externalIds": [{
      "system": "Salesforce",
      "value": "003xx000004TmiQAAS"
    }]
  }'
```

