# HBAR

| **Constructor**      | **Type** | **Description**             |
| -------------------- | -------- | --------------------------- |
| `new Hbar(<amount>)` | Hbar     | Initializes the Hbar object |

```java
new Hbar(<amount>)
```

## HBAR from:

Construct _**HBAR**_ from different representations.

| **Method**                      | **Type**                     | **Description**                                                 |
| ------------------------------- | ---------------------------- | --------------------------------------------------------------- |
| `Hbar.from(<hbars>)`            | long / BigDecimal            | Returns an Hbar whose value is equal to the specified value     |
| `Hbar.from(<hbars, unit>)`      | long / BigDecimal , HbarUnit | Returns an Hbar representing the value in the given units       |
| `Hbar.fromString(<text>)`       | CharSequence                 | Converts the provided string into an amount of hbars            |
| `Hbar.fromString(<text, unit>)` | CharSequence, HbarUnit       | Converts the provided string into an amount of hbars            |
| `Hbar.fromTinybars(<tinybars>)` | long                         | Returns an Hbar converted from the specified number of tinybars |

{% tabs %}
{% tab title="Java" %}

```java
//10 HBAR
new Hbar(10);

//10 HBAR from hbar value
Hbar.from(10);

//100 tinybars from HBAR convert to unit
Hbar.from(100, HbarUnit.TINYBAR);

// 10 HBAR converted from string value
Hbar.fromString("10");

//100 tinybars from string value
Hbar.fromString("10", HbarUnit.TINYBAR);

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
// 10 HBAR
new Hbar(10);

//10 HBAR
Hbar.from(10);

//100 tinybars
Hbar.from(100, HbarUnit.TINYBAR);

// 10 HBAR converted from string value
Hbar.fromString("10");

//100 tinybars from string value
Hbar.fromString("100", HbarUnit.TINYBAR);
```

{% endtab %}

{% tab title="Go" %}

```go
//100 HBAR
hedera.NewHbar(10)

//100 tinybars
hedera.HbarFrom(10, hedera.HbarUnits.Tinybar)

//v2.0.0
```

{% endtab %}
{% endtabs %}

### HBAR to:

Convert _**HBAR**_ to a different unit/format.

| **Method**         | **Type** | **Description**                                                                                     |
| ------------------ | -------- | --------------------------------------------------------------------------------------------------- |
| `to(<unit>)`       | HbarUnit | Specify the unit of hbar to convert to. Use `As` for Go.            |
| `toString(<unit>)` | HbarUnit | String value of the hbar unit to convert to. Use `String()` for Go. |
| `toTinybars()`     | Long     | Hbar value converted to tinybars                                                                    |

{% tabs %}
{% tab title="Java" %}

```java
//10 HBAR converted to tinybars
new Hbar(10).to(HbarUnit.TINYBAR);

//10 HBAR converted to tinybars
new Hbar(10).toString(HbarUnit.TINYBAR);

//10 HBAR converted to tinybars
new Hbar(10).toTinybars();

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//10 HBAR converted to tinybars
new Hbar(10).to(HbarUnit.TINYBAR);

//10 HBAR converted to tinybars
new Hbar(10).toString(HbarUnit.TINYBAR);

//10 HBAR converted to tinybars
new Hbar(10).toTinybars();
```

{% endtab %}

{% tab title="Go" %}

```go
//10 HBAR converted to tinybars
hedera.NewHbar(10).As(hedera.HbarUnits.Tinybar)

//10 HBAR to string format
hedera.NewHbar(10).String()

//10 HBAR converted to tinybars
hedera.NewHbar(10).AsTinybar()
//v2.0.0
```

{% endtab %}
{% endtabs %}

## **HBAR** constants:

Provided constant values of _**HBAR**_.

| **Method**  | **Type** | **Description**                                                                                                                                                 |
| ----------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Hbar.MAX`  | Hbar     | A constant value of the maximum number of hbars (50\_000\_000\_000 hbars)  |
| `Hbar.MIN`  | Hbar     | A constant value of the minimum number of hbars (-50\_000\_000\_000 hbars) |
| `Hbar.ZERO` | Hbar     | A constant value of zero hbars                                                                                                                                  |

{% tabs %}
{% tab title="Java" %}

```java
//The maximum number of hbars
Hbar hbarMax = Hbar.MAX; 

//The minimum number of hbars
Hbar hbarMin = Hbar.MIN;

//A constant value of zero hbars
Hbar hbarZero = Hbar.ZERO; 

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//The maximum number of hbars
const hbarMax = Hbar.MAX; 

//The minimum number of hbars
const hbarMin = Hbar.MIN;

//A constant value of zero hbars
const hbarZero = Hbar.ZERO;
```

{% endtab %}

{% tab title="Go" %}

```go
//The maximum number of hbars
hbarMax := hedera.MaxHbar

//The minimum number of hbars
hbarMin := hedera.MinHbar

//A constant value of zero hbars
hbarZero := hedera.ZeroHbar

//v2.0.0
```

{% endtab %}
{% endtabs %}

## **HBAR** units

Modify the _**HBAR**_ representation to one of the _**HBAR**_ denominations.

| **Function**        | **Description**                                                                                       |
| ------------------- | ----------------------------------------------------------------------------------------------------- |
| `HbarUnit.TINYBAR`  | The atomic (smallest) unit of hbar, used natively by the Hedera network            |
| `HbarUnit.MICROBAR` | Equivalent to 100 tinybar or 1⁄1,000,000 hbar.                                        |
| `HbarUnit.MILLIBAR` | Equivalent to 100,000 tinybar or 1⁄1,000 hbar                                                         |
| `HbarUnit.HBAR`     | The base unit of hbar, equivalent to 100 million tinybar.                             |
| `HbarUnit.KILOBAR`  | Equivalent to 1 thousand hbar or 100 billion tinybar.HbarUnit.Megabar |
| `HbarUnit.MEGABAR`  | Equivalent to 1 million hbar or 100 trillion tinybar.                                 |
| `HbarUnit.GIGABAR`  | Equivalent to 1 billion hbar or 100 quadrillion tinybar.                              |

{% tabs %}
{% tab title="Java" %}

```java
//100 tinybars
Hbar.from(100, HbarUnit.TINYBAR);

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//100 tinybars
Hbar.from(100, HbarUnit.TINYBAR);

//v2.0.0
```

{% endtab %}

{% tab title="Go" %}

```go
//100 tinybars
hedera.HbarFrom(100, hedera.HbarUnits.Tinybar)

//v2.0.0
```

{% endtab %}
{% endtabs %}

## HBAR decimal places

The decimal precision of _**HBAR**_ varies across the different Hedera APIs. While HAPI, JSON-RPC Relay, and Hedera Smart Contract Service (EVM) provide 8 decimal places, the **`msg.value`** in JSON-RPC Relay provides 18 decimal places.

<table><thead><tr><th width="495">API</th><th>Decimal</th></tr></thead><tbody><tr><td>Hedera API (HAPI) (Crypto + SCS Service (<code>msg.value</code>))</td><td>8</td></tr><tr><td>Hedera Smart Contract Service (EVM)</td><td>8</td></tr><tr><td>JSON RPC Relay (passed as arguments)</td><td>8</td></tr><tr><td>JSON RPC Relay (<code>msg.value</code>)</td><td>18</td></tr></tbody></table>

{% hint style="warning" %}
_**Note:** The JSON-RPC Relay **`msg.value`** uses 18 decimals when it returns HBAR. As a result, the **`gasPrice`** also uses 18 decimal places since it is only utilized from the JSON-RPC Relay._
{% endhint %}
