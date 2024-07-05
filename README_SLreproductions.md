# Set up your python env locally

## Set up the env for your reproduction project

Based on the version the customer is using:

1. Generate your .python-version file:

```
pyenv local 3.9
```

If pipenv is not already installed for this version/shim, run:

```
pip install --user --upgrade pipenv
```

2. Use Pipenv to initialize your env with the needed version

```
pipenv --python 3.9
```

> Note: You can do step 2 and 3 at the same time: `pipenv --python 3.9 install --dev`

3. Install the project deps

From Pipfile:

```
pipenv install --dev
```

From requirements.txt:

```
pipenv run pip install -r requirements.txt
```

## Install SeaLights agent:

If necessary, install with: `pipenv run pip install sealights-python-agent~=2.2.1`

> Note: Assuming you used the `--dev` flag for your install earlier, and assuming that `sealights-python-agent~=2.2.1` is 
> still one of your dev deps in your Pipfile, this will already be installed.

## Version checks

Use these commands to prove to yourself that you have installed what you think you have at this point

- Check your virtual env python version: `pipenv run python -V`
- Check your sealights-python-agent version: `pipenv run sl-python --version`

## Prove pytest works

From the root of the project:

```
pipenv run pytest --junit-xml=junit_xml_test_report.xml --cov-branch --cov=. tests --cache-clear
```

This should generate the coverage files specified based on the tests in the tests dir. If this works as expected, proceed to using `sl-python` to run `pytest`

*Note: pytest.ini tells pytest which dirs to look at with `python_files = */tests/*.py`*


## Use sl-python to run pytest

### PREREQUISITES

* The following commands assume that you have a valid sltoken.txt in the project root.
* If you haven't already, install sealights-python-agent with the command: `pipenv run pip install sealights-python-agent~=2.2.1`



1. Config:

```
pipenv run sl-python config --appname svgpytestini --branchname demo1 --buildname 2 --tokenfile sltoken.txt --exclude '*tests*,*venv*,*sealights_layer*' 
```

This step generates a buildSessionId.txt, used in the next step

2. Scan

```
pipenv run sl-python scan --tokenfile sltoken.txt --buildsessionidfile buildSessionId.txt 
```

3. Open test stage; Run pytest with sl-python

```
pipenv run sl-python pytest --teststage 'Unit Tests' --cov-report 'coverage.xml' --junit-xml=junit_xml_test_report.xml --buildsessionidfile buildSessionId.txt --tokenfile sltoken.txt --cache-clear
```

You should see the results of your tests in the terminal output

4. End the test stage

```
pipenv run sl-python end --buildsessionidfile buildSessionId.txt --tokenfile sltoken.txt
```

5. Upload the reports

```
pipenv run sl-python uploadReports --tokenfile sltoken.txt --buildsessionidfile buildSessionId.txt --reportfile junit_xml_test_report.xml
```

