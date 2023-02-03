OpenFaaS Python Playwright Templates
=============================================

This repository contains an OpenFaaS compatible template ready for use
with [Playwright].

[python-playwright-focal](/python-playwright-focal) is limited to python 3.8

## Downloading the templates

Using template pull with the repository's URL:

```bash
faas-cli template pull https://github.com/openrecce/python-playwright
```

Using your `stack.yml` file:

```yaml
configuration:
    templates:
        - name: python-playwright-focal
```

# Using the template

Create a new function

```
export OPENFAAS_PREFIX=https://github.com/openrecce/python-playwright
export FN="tester"
faas-cli new --lang python-playwright-focal $FN
```

Build, push, and deploy

```
faas-cli up -f $FN.yml
```

Test the new function

```
echo -n content | faas-cli invoke $FN
```

## Playwright specifics

This template bundles `python-playwright` from Microsoft. The only browser available in
this template is `webkit`. `firefox` and `chromium` may be added at a later date. The browsers are
independently removed to reduce the already large image size.

With only `webkit` this image is approx 1.5GB instead of 2.1GB if all three browsers are included.

Simply call the playwright code you've written inside the `handle` function and return the response (if needed).

The template's `stack.yml` file may need its timeouts increased above the default `10s`, usually `40s` is more than enough.

## Example of returning a string

This pertains to the standard python-flask-debian image which should be used in all cases
unless you specially need to use `python-playwright`.


```python
def handle(req):
    """handle a request to the function
    Args:
        req (str): request body
    """

    return "Hi" + str(req)
```

## Example of returning a custom HTTP code

```python
def handle(req):
    return "request accepted", 201
```

## Example of returning a custom HTTP code and content-type

```python
def handle(req):
    return "request accepted", 201, {"Content-Type":"binary/octet-stream"}
```

## Example of accepting raw bytes in the request

Update stack.yml:

```yaml
    environment:
      RAW_BODY: True
```

> Note: the value for `RAW_BODY` is case-sensitive.

```python
def handle(req):
    """handle a request to the function
    Args:
        req (str): request body
    """

    # req is bytes, so an input of "hello" returns i.e. b'hello'
    return str(req)
```


## Testing
The `python3` templates will run `pytest` using `tox` during the `faas-cli build`. There are several options for controlling this.

### Disabling testing
The template exposes the build arg `TEST_ENABLED`. You can completely disable testing during build by passing the following flag to the CLI

```sh
--build-arg 'TEST_ENABLED=false'
```

You can also set it permanently in your stack.yaml, see the [YAML reference in the docs](https://docs.openfaas.com/reference/yaml/#function-build-args-build-args).

### Changing the test configuration
The template creates a default `tox.ini` file, modifying this file can completely control what happens during the test. You can change the test command, for example switching to `nose`. See the [tox docs](https://tox.readthedocs.io/en/latest/index.html) for more details and examples.

### Changing the test command
If you don't want to use `tox` at all, you can also change the test command that is used. The template exposes the build arg `TEST_COMMAND`. You can override the test command during build by passing the following flag to the CLI

```sh
--build-arg 'TEST_COMMAND=bash test.sh'
```
Setting the command to any other executable in the image or any scripts you have in your function.

You can also set it permanently in your stack.yaml, see the [YAML reference in the docs](https://docs.openfaas.com/reference/yaml/#function-build-args-build-args).

[playwright]: https://playwright.dev
