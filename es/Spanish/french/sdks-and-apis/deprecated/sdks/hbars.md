# Hbars

| **Constructor**      | **Type** | **Description**             |
| -------------------- | -------- | --------------------------- |
| `new Hbar(<amount>)` | Hbar     | Initializes the Hbar object |

```java
new Hbar(<amount>)
```

## Hbar from:

Construct hbars from different representations.

{% tabs %}
{% tab title="V1" %}

| **Method**                   | **Type** | **Description**                      |
| ---------------------------- | -------- | ------------------------------------ |
| `Hbar.from(<amount, unit>)`  | HbarUnit | Hbars from the provided denomination |
| `Hbar.fromTinybar(<amount>)` | long     | Hbars converted from tinybars        |

{% code title="Java" %}

```java
//100 hbars converted to tinybars
Hbar.from(100, HbarUnit.Tinybar);

//Hbars from tinybars
Hbar.fromTinybar(100);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//100 hbars converted to tinybars
Hbar.from(100, HbarUnit.Tinybar);

//Hbars from tinybars
Hbar.fromTinybar(100);

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}

### Hbar to:

Convert hbars to a different unit/format.

{% tabs %}
{% tab title="V1" %}

| **Method**   | **Type** | **Description**                                                                          |
| ------------ | -------- | ---------------------------------------------------------------------------------------- |
| `as(<unit>)` | HbarUnit | Specify the unit of hbar to convert to. Use `As` for Go. |
| `toString()` | Long     | Hbar value converted to tinybars                                                         |

{% code title="Java" %}

```java
//100 hbars converted to tinybars
new Hbar(100).as(HbarUnit.Tinybar);

//100 hbars converted to tinybars
new Hbar(100).toTinybars();

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```java
//100 hbars converted to tinybars
new Hbar(100).as(HbarUnit.Tinybar);

//100 hbars converted to tinybars
new Hbar(100).toTinybars();

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}

## Hbar constants:

Provided constant values of hbars.

{% tabs %}
{% tab title="V1" %}

| **Method**  | **Type** | **Description**                                                                                                                                                 |
| ----------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Hbar.MAX`  | Hbar     | A constant value of the maximum number of hbars (50\_000\_000\_000 hbars)  |
| `Hbar.MIN`  | Hbar     | A constant value of the minimum number of hbars (-50\_000\_000\_000 hbars) |
| `Hbar.ZERO` | Hbar     | A constant value of zero hbars                                                                                                                                  |

{% code title="Java" %}

```java
//The maximum number of hbars
Hbar hbarMax = Hbar.MAX; 

//The minimum number of hbars
Hbar hbarMin = Hbar.MIN;

//Zero hbars
Hbar hbarZero = Hbar.ZERO; 

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```java
//The maximum number of hbars
const hbarMax = Hbar.MAX; 

//The minimum number of hbars
const hbarMin = Hbar.MIN;

//Zero hbars
const hbarZero = Hbar.ZERO; 

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}

## Hbar units

Modify the hbar representation to one of the hbar denominations.

{% tabs %}
{% tab title="V1" %}

| **Function**        | **Description**                                                                                       |
| ------------------- | ----------------------------------------------------------------------------------------------------- |
| `HbarUnit.Tinybar`  | The atomic (smallest) unit of hbar, used natively by the Hedera network            |
| `HbarUnit.Microbar` | Equivalent to 100 tinybar or 1⁄1,000,000 hbar.                                        |
| `HbarUnit.Millibar` | Equivalent to 100,000 tinybar or 1⁄1,000 hbar                                                         |
| `HbarUnit.Hbar`     | The base unit of hbar, equivalent to 100 million tinybar.                             |
| `HbarUnit.Kilobar`  | Equivalent to 1 thousand hbar or 100 billion tinybar.HbarUnit.Megabar |
| `HbarUnit.Megabar`  | Equivalent to 1 million hbar or 100 trillion tinybar.                                 |
| `HbarUnit.Gigabar`  | Equivalent to 1 billion hbar or 100 quadrillion tinybar.                              |

{% code title="Java" %}

```java
Hbar.from(100, HbarUnit.Tinybar);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```java
Hbar.from(100, HbarUnit.Tinybar);

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}
