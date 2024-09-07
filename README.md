# Replace Structurally - issue

This project demonstrates an issue with IntelliJ's "Replace Structurally".

The aim of the pattern is to replace a method annotation referring to a parameter by name,
with an annotation on that parameter.

This issue has been reported as [IDEA-358911](https://youtrack.jetbrains.com/issue/IDEA-358911/Structural-replace-drops-a-variable)

## Code example
Code before:
```java
@MyMethodAnnotation("paramName")
public void method1(String paramName) {
}
```

Expected code after:
```java
@MyMethodAnnotation
public void method1(@MyParamAnnotation String paramName) {
}
```

## Pattern
Search pattern (target `method`):
```
@org.example.MyMethodAnnotation("$text$")
$ReturnType$ $method$($ArgType$ $text$);
```
Replacement pattern:
```
@org.example.MyMethodAnnotation
$ReturnType$ $method$(@MyParamAnnotation $ArgType$ $text$);
```

The exact pattern is included in the project settings (`.idea/structuralSearch.xml`).

## The issue
The pattern matches just fine.
Even more complicated variations of the pattern / code match without issue 
(e.g. additional annotations, additional annotation parameters, additional method parameters).

The replacement, however, drops the argument type.
Running the structural replacement on this project, results in the following code:
```java
@MyMethodAnnotation
public void method1(@MyParamAnnotation paramName) {
}
```
i.e. the `$ArgType$` is dropped.
Swapping `$ArgType$` with `$text$` in the replacement pattern results in `@MyParamAnnotation String`, dropping `$text$` instead.


Side note: just inserting the annotation on the method parameter works fine if not referencing the method annotation.