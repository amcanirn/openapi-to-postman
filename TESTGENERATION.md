## Postman test suite generation options

This CLI option will provide the option to add basic Postman test and/or add manual defined Postman tests.
The goal is to allow easy usage of OpenApi based generated Postman collections with integrated tests, ready to be used
by the Newman test runner.

To generate the tests, define a JSON file like the example (postman-testsuite.json) below and run the CLI with the --generate option.

```terminal
$ openapi2postmanv2 -s spec.yaml -o collection.json -p -g postman-testsuite.json
```

Current postman-testsuite JSON properties

```JSON
{
  "version": 1.0,
  "generateTests": {
    "responseChecks": {
      "StatusSuccess": {
        "enabled": true
      },
      "responseTime": {
        "enabled": true,
        "maxMs": 300
      },
      "contentType": {
        "enabled": true
      },
      "jsonBody": {
        "enabled": true
      },
      "schemaValidation": {
        "enabled": true
      }
    }
  },
  "extendTests": [
    {
      "openApiOperationId": "get-lists",
      "tests": [
        "pm.test('200 ok', function(){pm.response.to.have.status(200);});",
        "pm.test('check userId after create', function(){Number.isInteger(responseBody);});"
      ]
    }
  ]
}
```

The JSON test suite format consists out of 3 parts:
- **version** : which refers the JSON test suite version
(not relevant but might handy for future backward compatibility options).
- **generateTests** : which refers the default available generated postman tests.
The default tests are grouped per type (response, request)
  - **responseChecks** : All response basic checks. (For now we have only included response checks).
  - **limitOperations**: refers to a list of operation IDs for which tests will be generated. (Default not set, so test will be generated for **all** operations).
- **extendTests**:  which refers the custom additions of manual created postman tests.
The manual tests are added during generation. The tests are mapped based on the OpenApi operationId.
Anything added in `tests` array, will be added to the postman test scripts.
  - **openApiOperationId (String)** : Reference to the OpenApi operationId for which the tests will be extended
  - **tests (Array)** : Array of additional postman test scripts.
  - **responseChecks (array)** : Extends the generateTests `responseChecks` (see [Postman test suite properties](#postman-test-suite-properties)) with specifics for the openApiOperationId.
  - **overwrite (Boolean true/false)** : Resets all generateTests and overwrites them with the defined tests from the `tests` array.
  Default: false

See "postman-testsuite-advanced.json" file for an advanced example of the setting options.

## Postman test suite properties

Version 1.0

| name                                | id                  | type    | default/0 | availableOptions/0 | availableOptions/1 | description                                                                                                                                                  | external | usage/0         |
|-------------------------------------|---------------------|---------|-----------|--------------------|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-----------------|
| Response status success (2xx) check | StatusSuccess       | boolean | false     | enabled            |                    | Adds the check if the response of the postman request return a 2xx                                                                                           | true     | TEST GENERATION |
| Response time check                 | responseTime        | boolean | false     | enabled            | maxMs 300          | Adds the check if the response of the postman request is within a number of ms.                                                                              | true     | TEST GENERATION |
| Response content-type check         | contentType         | boolean | false     | enabled            |                    | Adds the check if the postman response header is matching the expected content-type defined in the OpenApi spec.                                             | true     | TEST GENERATION |
| Response JSON body format check     | jsonBody            | boolean | false     | enabled            |                    | Adds the check if the postman response body is matching the expected content-type defined in the OpenApi spec.                                               | true     | TEST GENERATION |
| Response Schema validation check    | schemaValidation    | boolean | false     | enabled            |                    | Adds the check if the postman response body is matching the JSON schema defined in the OpenApi spec. The JSON schema is inserted inline in the postman test. | true     | TEST GENERATION |
| Response Header presence check      | headersPresent      | boolean | false     | enabled            |                    | Adds the check if the postman response header has the header names present, like defined in the OpenApi spec.                                                | true     | TEST GENERATION |
