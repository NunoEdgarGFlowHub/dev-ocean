# Squid API

Table of Contents
=================

* [Table of Contents](#table-of-contents)
   * [Common](#common)
      * [Getting an instance](#getting-an-instance)
         * [Functions](#functions)
   * [Ocean](#ocean)
      * [Accounts](#accounts)
         * [Balance](#balance)
             * [Functions](#functions-1)
         * [Tokens](#tokens)
             * [Functions](#functions-2)
      * [Decentralized Identifiers (DID)](#decentralized-identifiers-did)
         * [Functions](#functions-3)
      * [Assets](#assets)
         * [Metadata](#metadata)
             * [Functions](#functions-4)
         * [Secret Store](#secret-store)
             * [Functions](#functions-5)
      * [Service Agreements](#service-agreements)
         * [Functions](#functions-6)
      * [Orders](#orders)
          * [Functions](#functions-7)
   * [Squid API Implementation state](#squid-api-implementation-state)
      * [Deleted](#deleted)
         * [Provider Functions (Nice to Have)](#provider-functions-nice-to-have)
   * [Examples](#examples)
* [Old Squid API](#old-squid-api)

---

**🐲🦑 THERE BE DRAGONS AND SQUIDS. This is in alpha state and you can expect running into problems. If you run into them, please open up [a new issue](https://github.com/oceanprotocol/dev-ocean/issues). 🦑🐲**

---

## Common

Common functions exposed by an abstract class extended by all the other core classes

```java
/**
 * Abstract class providing the common methods like initilizers and similar
*/
abstract class OceanBase {}

/**
 * Access to Ocean functionalities
*/
class Ocean {
    public Accounts
    public Assets
    public Orders
    public ServiceAgreements
}

/**
 * Extends OceanBase providing Account functionalities
*/
class Accounts extends OceanBase {}

/**
 * Extends OceanBase providing Asset functionalities
*/
class Assets extends OceanBase {}

/**
 * Extends OceanBase providing ServiceAgreements functionalities
*/
class Orders extends OceanBase {}

/**
 * Extends OceanBase providing ServiceAgreements functionalities
*/
class ServiceAgreements extends OceanBase {}

```

### Getting an instance

#### Functions

```
getInstance(web3Dto, providerDto)
```

Parameters:

* web3Dto - Web3 client wrapped in a DTO object to avoid coupling with specific web3 specific libraries
* providerDto - Ocean Provider client wrapped in a DTO object

This method returns an instance of the **Ocean** class. It allows to get access to the other Ocean core methods

## Ocean

Interface with core Ocean functions

### Accounts

#### Balance

##### Functions

* **getOceanBalance** - SYNC. The only parameter required is an account address (ie. 0x6309b5dd9245278a7fdfb2186dfb80583caeadc7). Returns the Ocean Tokens balance for that account.
```
oceanBalance= getOceanBalance(address)
```

* **getEtherBalance** - SYNC. The only parameter required is an account address. Returns the Ether balance for that account.
```
ethBalance= getEtherBalance(address)
```

* **getBalance** - SYNC. The only parameter required is an account address. Returns the Ocean tokens and Ether balance for that account.
```
balance= getBalance(address)
```

#### Tokens

##### Functions

* **requestTokens** - SYNC. Request a number of Ocean Tokens. Returns a boolean to know if everything was right.

```
result= requestTokens(amountTokens)
```

### Decentralized Identifiers (DID)

#### Functions

* **generateDID** - SYNC. Generates a specific DID with a random id based on the given.

```
did= generateDID(seed)
```

* **resolveDID** - SYNC. Given a DID the method returns the DDO associated to it.
```
ddo= resolveDID(did)
```

* **createDDO** - SYNC. Given the public keys, services and metadata information, creates a DDO and a DID.
```
ddo= createDDO(pubKeys, services, metadata)
```

### Assets

#### Metadata

##### Functions

* **register** - ASYNC. High-level method publishing the metadata off-chain and registering the Service Agreement on-chain. It orchestrate the `publishMetadata` and `publishServiceAgreement` methods.
```
status= register(assetDDO, price)
```

* **publishMetadata** - SYNC. Given an asset DDO, register this DDO off-chain. It returns a boolean with the result of the operation.
```
status= publishMetadata(assetDDO)
```

* **updateMetadata** - SYNC. Given an asset DDO, update the DDO off-chain. This method replace the complete existing DDO by the DDO provided. It returns a boolean with the result of the operation.
```
status= updateMetadata(assetDDO)
```

* **getAssetMetadata** - SYNC. Given a DID, returns the associated DDO. Internally calls Ocean.resolveDID(did).
```
ddo= getAssetMetadata(assetDID)
```

* **getAssetPrice** - SYNC. Given a service agreement id, get's the asset price information existing on-chain.
```
getAssetPrice(serviceAgreementId)
```

* **search** - SYNC. Given a search query, returns a list of the DDO's matching with that query
```
array[ddo]= search(searchQuery)
```

* **retireMetadata** - SYNC. Given an Asset DID, this method delete logically the Asset Metadata. **Nice to Have**
```
retireMetadata(assetDID)
```

#### Secret Store

##### Functions

* **encryptDocument** - SYNC. **Private function** encapsulated as part of the **register** function. It integrates the Parity Ethereum & Secret Store API allowing to encrypt a document.
Given by a **Publisher** an unique resource id (did), the document to encrypt and the Secret Store cluster threshold (could be pre-defined to a fixed number), integrate the Secret Store API's to encrypt the document.
```
encryptedDocument= encryptDocument(did, document, threshold)
```

* **decryptDocument** - SYNC.  **Private function** encapsulated as part of the **getAssetAccess** function. It integrates the Parity Ethereum & Secret Store API allowing to decrypt a document if the user executing this function is authorized by a Smart Contract.
Given by a **Consumer** an unique resource id (did) and the document encrypted, this functions integrates the Secret Store API's to decrypt the document. The on-chain authorization phase is transparent for the user.
```
document= decryptDocument(did, encryptedDocument)
```

### Service Agreements

##### Functions

* **publishServiceAgreement** -  ASYNC. The Publisher register a Service Agreement related with a specific Asset

```
serviceAgreementId= publishServiceAgreement(assetDID, providerId, price, ..)
```

* **getServiceAgreementStatus** -  SYNC. Return's the status of a Service Agreement. Possible values are (0=> Pending, 1=> Enabled, 2=> Retired)

```
status= getServiceAgreementStatus(serviceAgreementId)
```

* **retireServiceAgreement** - ASYNC. Given a Service Agreement id, the Publisher retire a Service Agreement. It changes the status to the value 2=>Retired.
```
result= retireServiceAgreement(serviceAgreementId)
```

* **getAssetAccess** -  SYNC. Get all the information required to get access to an asset after the purchase process
```
asset= getAssetAccess(serviceAgreementId)
```

* **getAssetsMetadata** - SYNC. Given an array of DID's, returns an array of the associated DDO's. Internally calls Ocean.resolveDID(did). **Nice to Have**.
```
array[ddo]= getAssetsMetadata(array[assetDID])
```

* **getAssetServiceAgreement** - SYNC. Get a list of the service agreements published for a specific asset. providerId could be optional. **Nice to Have**
```
getAssetServiceAgreement(assetDID, providerId)
```

* **getServiceAgreements** - SYNC. Return a list of the Service agreements published by the user (Publisher). **Nice to Have**.
```
array[serviceAgreement]= getServiceAgreements()
```

### Orders

##### Functions

* **purchaseAsset** - ASYNC. Given a Service Agreement and Publisher Id, the Consumer purchase an asset
```
orderId= purchaseAsset(serviceAgreementId, publisherId , timeout)
```

* **getOrderStatus** - SYNC. Given an order id, returns an integer representing the order status as defined in the keeper. Possible values are (0=>Pending, 1=>Paid, 2=>Canceled)
```
status= getOrderStatus(orderId)
```

* **verifyOrderPayment** -  SYNC. Given an order id, verifies if the order was paid.
```
status= verifyOrderPayment(orderId)
```

* **getPurchasedOrders** - SYNC. Return a list of orders purchased by the user (Consumer). **Nice to Have**.
```
array[order]= getPurchasedOrders()
```

## Squid API Implementation state

Table not completed yet

| Category                  | Method                      | Python Implementation   | Javascript Implementation   | Java Implementation   |
|:--------------------------|:----------------------------|-------------------------|-----------------------------|-----------------------|
| Commons                   | getInstance                 | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | getOceanBalance             | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | getEtherBalance             | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | getBalance                  | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | requestTokens               | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | generateDID                 | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | resolveDID                  | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | createDDO                   | Not Implemented         | Not Implemented             | Not Implemented       |
| Assets                    | register                    | Not Implemented         | Not Implemented             | Not Implemented       |
| Assets                    | publishMetadata             | Not Implemented         | Not Implemented             | Not Implemented       |
| Assets                    | updateMetadata              | Not Implemented         | Not Implemented             | Not Implemented       |
| Assets                    | getAssetMetadata            | Not Implemented         | Not Implemented             | Not Implemented       |
| Assets                    | getAssetPrice               | Not Implemented         | Not Implemented             | Not Implemented       |
| Assets                    | search                      | Not Implemented         | Not Implemented             | Not Implemented       |
| Assets.SecretStore        | encryptDocument             | Not Implemented         | Not Implemented             | Not Implemented       |
| Assets.SecretStore        | decryptDocument             | Not Implemented         | Not Implemented             | Not Implemented       |
| Service Agreements        | publishServiceAgreement     | Not Implemented         | Not Implemented             | Not Implemented       |
| Service Agreements        | getServiceAgreementStatus   | Not Implemented         | Not Implemented             | Not Implemented       |
| Service Agreements        | retireServiceAgreement      | Not Implemented         | Not Implemented             | Not Implemented       |
| Service Agreements        | purchaseAsset               | Not Implemented         | Not Implemented             | Not Implemented       |
| Service Agreements        | getAssetAccess              | Not Implemented         | Not Implemented             | Not Implemented       |
| Service Agreements        | getOrderStatus              | Not Implemented         | Not Implemented             | Not Implemented       |
| Service Agreements        | verifyOrderPayment          | Not Implemented         | Not Implemented             | Not Implemented       |

### Deleted

- Ocean(config(web3Provider, nodeURI, gas, network, providerURI))

Constructor is private so only way to get an instance is using the `getInstance()` method.

- getAccounts() => list of accounts along with token and eth balances

Not having an Actor registry make difficult to implement this. Maybe this business logic is not necessary.

#### Provider Functions (Nice to Have)

* **registerProvider** -
```
result= registerProvider(providerAddress, url)
```

* **getProviders** -
```
getProviders()
```

* **getAssetProviders** - Get the Providers giving access to an Asset
```
getAssetProviders(assetDID)
```

## Examples

List orders in JavaScript

```javascript
import { Ocean } from '@oceanprotocol/squid'

const ocean = await Ocean.getInstance({
    nodeUri: 'http://localhost:8545'
})

const orders = await ocean.order.getOrdersByConsumer('0x970e8f18ebfEa0B08810f33a5A40438b9530FBCF')

console.log('Orders', JSON.stringify(orders, null, 2))
```

List accounts in JavaScript
```javascript
import { Ocean } from '@oceanprotocol/squid'

const ocean = await Ocean.getInstance({
    nodeUri: 'http://localhost:8545'
})

const accounts = await ocean.account.list()

console.log(JSON.stringify(accounts, null, 2))
```

Access market functionality in JavaScript

```javascript
import { Ocean } from '@oceanprotocol/squid'

const ocean = await new Ocean({network: 'kovan'})
const { market } = ocean

await market.verifyOrderPayment('0x970e8f18ebfEa0B08810f33a5A40438b9530FBCF')
```

----------

# Old squid API

## Ocean
- `js` getInstance(config(web3Provider, nodeUri, gas, network, providerUri))
- `js` getAccounts()
- `js` getOrdersByConsumer(consumerAddress)
- `js` purchaseAsset(assetId, publisherId, price, privateKey, publicKey, timeout, senderAddress, initialRequestEventHandler, accessCommittedEventHandler, tokenPublishedEventHandler)

### Token
- `py` approve(address, resource_price)
- `py` getBalance(account)
- `js` getTokenBalance(accountAddress)
- `js` getEthBalance(account)

### Market
- `py` generateID(content)
- `py` verifyOrderPayment(orderId)
- `js` verifyOrderPayment(orderId) 
- `py` register(name, description, price, publisherAddress)
- `js` registerAsset(name, description, price, publisherAddress)
- `py` requestTokens(amount, address)
- `js` requestTokens(amount, address)
- `py` sendPayment(assetId, order, publisherAddress, senderAddress)
- `js` payAsset(assetId, order, publisherAddress, senderAddress)
- `py` checkAsset(assetId)
- `js` checkAsset(assetId)
- `py` getAssetPrice(assetId)
- `js` getAssetPrice(assetId)

### `py` Acl `js` Auth
- `py` getOrderStatus(orderId)
- `js` getOrderStatus(orderId) 
- `py` initiateAccessRequest(resourceID, providerAddress, pubKey, timeout)
- `py` commitAccessRequest(id, isAvailable,  expirationDate,  discovery,  permissions,  accessAgreementRef,   accessAgreementType)
- `py` deliverAccess Token(id, encryptedAccessToken)
- `py` getEncryptedAccessToken(orderId, senderAddress)
- `js` getEncryptedAccessToken(orderId, senderAddress)
- `py` cancelAccessRequest(orderId, senderAddress)
- `js` cancelAccessRequest(orderId, senderAddress)

### `py` Web3 `js` helper
- `py` sign(accountAddress, message)
- `js` sign(accountAddress, message)
- `py` getMessageHash(message)
- `py` toChecksumAddress(address)
- `py` toHex()
- `py` toBytes()
- `js` getAccounts()

### Metadata
- `py `getAssetMetadata(assetId)
- `py` getAssets()
- `py` getAsstesMetadata()
- `js` getAssetsMetadata()
- `py` publishAsset(providerId, assetId, publisherId, name, size, author, license, contentType, contentUrls, price, *)
- `js` publishDataAsset(asset)
- `py` updateAsset(providerId, assetId, publisherId, name, size, author, license, contentType, contentUrls, price, *)
- `py `retireAsset(providerId, assetId)

### Providers
- `py` registerProvider(url, provider_address)
- `py` getProviders()
- `py` getAssetProvider(assetId)
