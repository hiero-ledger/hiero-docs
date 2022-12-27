# HBARs

| **Constructor**      | **Type** | **Description**             |
| -------------------- | -------- | --------------------------- |
| `new Hbar(<amount>)` | Hbar     | Initializes the Hbar object |

```java
new Hbar(<amount>)
```

## Hbar from:

Construct hbars from different representations.

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
//100 hbars
new Hbar(100);

//100 hbars from hbar value
Hbar.from(100);

//100 tinybars from hbars convert to unit
Hbar.from(100, HbarUnit.TINYBAR);

// 100 hbars converted from string value
Hbar.fromString("100");

//100 tinybars from string value
Hbar.fromString("100", HbarUnit.TINYBAR);

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// 100 hbars
new Hbar(100);

//100 hbars
Hbar.from(100);

//100 tinybars
Hbar.from(100, HbarUnit.TINYBAR);

// 100 hbars converted from string value
Hbar.fromString("100");

//100 tinybars from string value
Hbar.fromString("100", HbarUnit.TINYBAR);
```
{% endtab %}

{% tab title="Go" %}
```go
//100 hbars
hedera.NewHbar(100)

//100 tinybars
hedera.HbarFrom(100, hedera.HbarUnits.Tinybar)

//v2.0.0
```
{% endtab %}
{% endtabs %}

### Hbar to:

Convert hbars to a different unit/format.

| **Method**         | **Type** | **Description**                                                     |
| ------------------ | -------- | ------------------------------------------------------------------- |
| `to(<unit>)`       | HbarUnit | Specify the unit of hbar to convert to. Use `As` for Go.            |
| `toString(<unit>)` | HbarUnit | String value of the hbar unit to convert to. Use `String()` for Go. |
| `toTinybars()`     | Long     | Hbar value converted to tinybars                                    |

{% tabs %}
{% tab title="Java" %}
```java
//100 hbars converted to tinybars
new Hbar(100).to(HbarUnit.TINYBAR);

//100 hbars converted to tinybars
new Hbar(100).toString(HbarUnit.TINYBAR);

//100 hbars converted to tinybars
new Hbar(100).toTinybars();

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//100 hbars converted to tinybars
new Hbar(100).to(HbarUnit.TINYBAR);

//100 hbars converted to tinybars
new Hbar(100).toString(HbarUnit.TINYBAR);

//100 hbars converted to tinybars
new Hbar(100).toTinybars();
```
{% endtab %}

{% tab title="Go" %}
```go
//100 hbars converted to tinybars
hedera.NewHbar(100).As(hedera.HbarUnits.Tinybar)

//100 hbars to string format
hedera.NewHbar(100).String()

//100 hbars converted to tinybars
hedera.NewHbar(100).AsTinybar()
//v2.0.0
```
{% endtab %}
{% endtabs %}

## Hbar constants:

Provided constant values of hbars.

| **Method**  | **Type** | **Description**                                                            |
| ----------- | -------- | -------------------------------------------------------------------------- |
| `Hbar.MAX`  | Hbar     | A constant value of the maximum number of hbars (50\_000\_000\_000 hbars)  |
| `Hbar.MIN`  | Hbar     | A constant value of the minimum number of hbars (-50\_000\_000\_000 hbars) |
| `Hbar.ZERO` | Hbar     | A constant value of zero hbars                                             |

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

## Hbar units

Modify the hbar representation to one of the hbar denominations.

| **Function**        | **Description**                                                         |
| ------------------- | ----------------------------------------------------------------------- |
| `HbarUnit.TINYBAR`  | The atomic (smallest) unit of hbar, used natively by the Hedera network |
| `HbarUnit.MICROBAR` | Equivalent to 100 tinybar or 1⁄1,000,000 hbar.                          |
| `HbarUnit.MILLIBAR` | Equivalent to 100,000 tinybar or 1⁄1,000 hbar                           |
| `HbarUnit.HBAR`     | The base unit of hbar, equivalent to 100 million tinybar.               |
| `HbarUnit.KILOBAR`  | Equivalent to 1 thousand hbar or 100 billion tinybar.HbarUnit.Megabar   |
| `HbarUnit.MEGABAR`  | Equivalent to 1 million hbar or 100 trillion tinybar.                   |
| `HbarUnit.GIGABAR`  | Equivalent to 1 billion hbar or 100 quadrillion tinybar.                |

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
