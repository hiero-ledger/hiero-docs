# Generate a mnemonic phrase

Generate a 12 or 24-word mnemonic phrase that can be used to recover the private keys that are associated with it.

{% tabs %}
{% tab title="V1" %}
| **Method**            | **Description**                     |
| --------------------- | ----------------------------------- |
| `Mnemonic.generate()` | Generates a 24 word mnemonic phrase |

{% code title="Java" %}
```java
 Mnemonic mnemonic = Mnemonic.generate();

 //1.3.2
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
const mnemonic = Mnemonic.generate();
//v1.4.4
```
{% endcode %}
{% endtab %}
{% endtabs %}
