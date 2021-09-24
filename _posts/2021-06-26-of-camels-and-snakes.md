---
layout: post
title: Of camels and snakes
subtitle: and Blaise Pascal eating a kebab in Hungary
tags:
- code quality
- coding standards
date: 2021-06-26 23:06 -0700
---
This article was originally meant to be posted before [pre-commit.ci and pydocstyle join the family]({{ site.url }}/2021/06/21/pre-commit-ci-and-pydocstyle-join-the-family/), but it has now become part 4 of the series of articles about [coding standards]({{ site.url }}/tags/#coding standards). And while the last three articles have revolved around Python, this article is meant to be a bit broader. So, without further ado, welcome to part 4.

As the title suggests, I will discuss the following programming naming conventions:

- [camelCase](#camelcase)
- [snake_case](#snake_case)
- [PascalCase](#PascalCase)
- [kebab-case](#kebab-case)
- [Hungarian notation](#hungarian-notation)

The last three articles have focused on style conventions, but let's not forget that establishing naming conventions is another important part of code quality and coding standards.

I will not stop repeating that naming conventions are necessary, as establishing one will assist in producing code that is readable and, possibly, easy to maintain. Some programming languages do prefer one naming convention over others, which makes the choice a bit easier. So is the case of Python, where [**snake_case**](#snake_case) is usually preferred over other conventions.

But just as you define which cases are allowed, you also must be clear which cases are not.

### camelCase

> Camel case is named after the "hump" of its protruding capital letter, similar to the hump of common camels.[^1]

Most commonly used when declaring variables or parameters in languages like C#[^2] or variables and method names for Java[^3].

```java
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Calendar;

public class DateToStringExample {
    public static void main(String args[]) {
        Date date = Calendar.getInstance().getTime();
        DateFormat dateFormat = new SimpleDateFormat("yyyy-mm-dd hh:mm:ss");
        String strDate = dateFormat.format(date);
        System.out.println("Converted String: " + strDate);
    }
}
```

### snake_case

> **Snake case** (stylized as **snake_case**) refers to the style of writing in which each space is replaced by an underscore (_) character and the first letter of each word written in lowercase.[^4]

I usually apply this to function names and variables in Python, as well as most Database objects (tables, functions, views, stored procedures), and the use of **UPPER_CASE_WITH_UNDERSCORES** for naming constants at the module level.

```python
# Constants.
CURRENT_DIRECTORY = "."
PARENT_DIRECTORY = ".."
PATH_SEPARATOR = "/"

def swap_windows(from_path, to_path, params=None):
    pass
```

Python documents some additional cases where it is recommended to use a single leading underscore, a double leading underscore, a trailing underscore, as well as double leading and trailing underscores.

### PascalCase

Similar to [camelCase](#camelcase), but the initial letter is also uppercase.

Most commonly used when naming classes across multiple programming languages like C#, Java, Python, etc.

```python
class ApplicationError(Exception):
    """Application Error class."""

    pass
```

### kebab-case

> Kebab case is the way to write compound words separated by hyphens (-) instead of using space.[^5]

One of its most common uses is for separating words in a URL; just take a look at the URL for this post.

But I must confess that even though I had seen and used this convention in the past it was not until recently that I learned about its name.

### Hungarian notation

> Hungarian notation is an identifier naming convention in computer programming, in which the name of a variable or function indicates its intention or kind, and in some dialects its type.[^6]

While some may argue that this naming convention is rarely properly applied or it may be outdated, I still use some form of it for naming UI elements.

One simple example is that nowadays it is very simple to drag and drop components while designing a UI, but in cases, like the Ignition Designer, the components are named by default just like the component type, i.e. "Text Field" for a text box. But when you drop multiple components of the same type you might end up with names like "Text Field 1", "Text Field 2", "Text Field 3", and so on. So I opt for using the `txt` prefix for Text Fields to avoid confusion.

Here's a list of the common prefixes I use for naming components:

- `btn` for buttons: `btnSave`
- `chk` for check boxes: `chkEnabled`
- `drp` for dropdown lists: `drpDevices`
- `lbl` for labels: `lblName`
- `num` for numerical fields: `numQuantity`
- `tbl` for tables: `tblWorkOrders`
- `txt` for text fields: `txtDescription`

And if you were to ask me where you should avoid Hungarian notation, I would have to agree that applying this convention on Database objects is a deadly sin; so I will be 100% clear.

Please avoid `tb_` for tables, `sp_` for stored procedures, `vw_` for views, but do use it for primary key fields (`PK_`), indices (`IX_`), and check constraints like unique (`UQ_`), as well as defaults (`DF_`).

### Useful tools and packages

As I've stated before, PyCharm is my preferred IDE when it comes to writing Python code, and among its many features, it includes a static code analyzer that warns you about style errors like "Function name should be lowercase" or "Argument name should be lowercase" when they do not conform to [snake_case](#snake_case) naming style.

Another tool used for Python is `pylint`, *a tool that checks for errors in Python code that tries to enforce a coding standard and looks for code smells*[^7]. Among its many error codes, code [C0103](https://vald-phoenix.github.io/pylint-errors/plerr/errors/basic/C0103) will point to a function or argument that doesn't conform to [snake_case](#snake_case) naming style, for example.

Lately, I've written code in Python and SQL more than anything else, so pardon my lack of knowledge about code linters for other languages, but feel free to leave your preferred tools in a comment.

Thanks for reading!

### Other resources

Bertrand, Aaron (2016-01-18). [Subjectivity: Naming Standards](https://www.sentryone.com/blog/aaronbertrand/subjectivity-naming-standards). SentryOne.
Sarkuni, Sehrope (2014-02-16). [How I Write SQL, Part 1: Naming Conventions](https://launchbylunch.com/posts/2014/Feb/16/sql-naming-conventions/). Launch by Lunch.

### References

[^1]: [Camel case](https://en.wikipedia.org/wiki/Camel_case). Wikipedia.
[^2]: [C# Coding Conventions](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions). Microsoft Docs.
[^3]: [Code Conventions for the Java Programming Language - Naming Conventions](https://www.oracle.com/java/technologies/javase/codeconventions-namingconventions.html). Oracle.
[^4]: [Snake case](https://en.wikipedia.org/wiki/Snake_case). Wikipedia.
[^5]: [Kebab case](https://textcaseconvert.com/blog/kebab-case/). Text Case Convert.
[^6]: [Hungarian notation](https://en.wikipedia.org/wiki/Hungarian_notation). Wikipedia.
[^7]: [Pylint User Manual](https://pylint.pycqa.org/en/latest/). PyCQA.
