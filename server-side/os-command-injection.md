# OS Command Injection \[Command Execution\]

## 1. Introduction:

**OS command injection** \(also known as shell injection\) is a **web security vulnerability** that allows an attacker to execute arbitrary operating system \(OS\) commands **on the server that is running an application**, and typically **fully compromise the application** and all its data. 

{% hint style="warning" %}
**Command injection attacks are possible largely** due to **insufficient input validation**.
{% endhint %}

## 2. Typical vulnerable code:

```python
import os
import sys

file = sys.argv[1]

text = os.system("cat " +  file)
```

![Here&apos;s command injection in action.](../.gitbook/assets/image%20%284%29%20%281%29.png)

## 3. Mitigations:

{% hint style="warning" %}
You should also spend some time on the following pages since defending against injections is quite similar regardless of what vulnerable vector you are dealing with.
{% endhint %}

{% page-ref page="sql-injections.md" %}

{% page-ref page="xxe.md" %}

### 3.1. Avoid calling OS commands directly:

The primary defense **in the case of OS command injection** is **using built-in functions instead**, as they **cannot be manipulated to perform any malicious task**. Here is an example:

Instead of having `os.system("mkdir " + dir_name)`, we have to use its alternative, which is `os.mkdir(dir_name)`

### 3.2. Escaping the OS commands arguments:

By **escaping the user-supplied input** we can make sure **that we capsulate it into a "safer" format** for the `os.system` command to execute. As **escaping** the user-supplied input should **be used on top of an already existing defense mechanism**, it is recommended to **avoid** using OS commands directly.

An **example of escaping OS commands is showcased in the PHP function** [escapeshellarg](https://www.php.net/manual/en/function.escapeshellarg.php). Here, the function **adds single quotes around a string** and **quotes/escapes any existing single quotes** allowing you to **pass a string directly to a shell function** and having it be **treated as a single safe argument.**

```php
<?php

    system('ls '.escapeshellarg($dir));

?>

```

### 3.3. Parametrization with input validation:

Should calling **os commands on user-supplied input** be _**unavoidable**_ \(although it almost always is\), a developer can make use of the following **two defensive layers**. As discussed in the SQLi section, one can defend their endpoint with:

1. **Parameterization:** If available, use structured mechanisms that automatically enforce the separation between data and command. These mechanisms can help provide the relevant quoting and encoding.
2. **Input validation:** The values for commands and the relevant arguments should be both validated. There are different degrees of validation for the actual command and its arguments:

* When it comes to the **commands** used, these must be validated against a list of allowed commands.
* In regards to the **arguments** used for these commands, they should be validated using the following options:
  * **Positive or allow list input validation**: Where are the arguments allowed explicitly defined.
  * **Allow list Regular Expression**: Where a list of safe, allowed characters and the maximum length of the string are defined. Ensure that metacharacters, like these: 

    ```text
    & |  ; $ > < ` \ !
    ```

     and white spaces are not part of the Regular Expression. For example, the following regular expression **only allows lowercase letters** and numbers and does **not contain metacharacters**. The length is also being limited to 3-10 characters: `^[a-z0-9]{3,10}$`

{% hint style="warning" %}
Ideally, a developer should use an existing API/ libraries for their language. For example \(Python\): Rather than use `os.system()` to issue a **`mail`** command, use the available Python `mail` library.

If no such available API exists, the developer **should scrub all input for malicious characters**. Typically, it is **much easier to define** the **legal** characters than the **illegal** characters.
{% endhint %}

### 3.4. Additional Defenses: <a id="additional-defenses"></a>

On top of primary defenses, parameterizations, and input validation, it is also recommended adopting all of these extra defenses:

* Applications **should run using the lowest privileges** that are required to accomplish the necessary tasks.
* If possible, **create isolated accounts** with **limited privileges** that are only used for **a single task**.

## 4. Takeaways:

**Avoiding directly calling OS commands** is an obvious first step towards defending against command injection. As it should be a standard by now, when dealing with **user supplied input**, a developer should always make sure to **escape as well as validate it.**

{% hint style="info" %}
You can find more resources on this topic here:

* [OS Command Injection Defense\[OWASP\]](https://cheatsheetseries.owasp.org/cheatsheets/OS_Command_Injection_Defense_Cheat_Sheet.html)
* [Command Injection\[OWASP\]](https://owasp.org/www-community/attacks/Command_Injection)
{% endhint %}

