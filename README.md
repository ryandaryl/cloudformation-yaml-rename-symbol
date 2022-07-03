
# Rename symbols in YAML

When manually editing CloudFormation templates, it's sometimes necessary to rename a resource or variable. However, VS Code YAML plugins don't provide the ability to rename symbols.

One merhod to rename symbols in YAML is to temporarily convert the YAML to valid Python code.

The following regex replace values convert CloudFormation YAML to and from Python code.

To convert to Python:
```
    ([^ ])
    def $1
```

```
:([\n, ])
():$1
```

```
- 
_
```

```
"
f"
```


To change back:

```
def _
- 
```

```
def 

```

```
()

```

```
f"
f
```

Notes:
- The above regex assumes the YAML is indented with 4 spaces.
- CloudFormation function names beginning with `!` (such as `!Sub` and `!GetAttr`) are not converted to valid Python. This doesn't affect renaming symbols.
- A similar method could be used with any language, as long as an IDE plugin exists which can rename symbols.



Example YAML:

```yaml
    LambdaFunction1:
        Type: AWS::Lambda::Function
        Properties:
            - Name: !Sub my_function${LambdaFunction2}"

    LambdaFunction2:
        Type: AWS::Lambda::Function
        Properties:
            - Name: my_other_function

    LambdaPermission:
        Type: AWS::Lambda::Permission
        Properties:
            - Action: lambda:InvokeFunction
            - FunctionName: LambdaFunction1.Arn
```


After converting to Python:

```python
def Resources():
    def LambdaFunction1():
        def Type(): f"AWS::Lambda::Functionf"
        def Properties():
            def _Name(): !Sub f"my_functionf${LambdaFunction2}f"

    def LambdaFunction2():
        def Type(): f"AWS::Lambda::Functionf"
        def Properties():
            def _Name(): f"my_other_functionf"

    def LambdaPermission():
        def Type(): f"AWS::Lambda::Permissionf"
        def Properties():
            def _Action(): f"lambda:InvokeFunctionf"
            def _FunctionName(): LambdaFunction1.Arn
```
