---
title: Extend Transform Service
---

Use this information to extend the functionality of the Transform Service by adding and configuring custom transform engines (T-Engines).

Two things need to be configured in the transform router (T-Router) so that it knows about a custom transform engine (T-Engine) and can use it. One is information about the T-Engine, such as the access URL and JMS queue name, and the other is the actual transform routes. This configuration is described in the linked pages.

To match the transform routes with the T-Engines, the T-Router uses internal T-Engine labels (also known as transformer names). Examples of such labels are: `IMAGEMAGICK`, `LIBREOFFICE`, `PDF_RENDERER`, `TIKA`, `TRANSFORMER1`, `CUSTOM_ENGINE`.

> **Note:**
>
* The T-Engine labels are case-insensitive, and come into play when the T-Router configuration is parsed, and T-Engine HTTP URLs and JMS queue names are identified.
* The T-Engine labels in the route configuration files must match the URL and queue variable names.

## Configure routes in T-Router

Use this information to configure the transform routes in the Transform Service.

There is one default route configuration file bundled in the standard T-Router artifact / Docker image (the top-level resource `transformer-routes.yaml`).

The default routes file is specified through the `transformer-routes-path` SpringBoot property, which can be overridden by the `TRANSFORMER_ROUTES_PATH` environment variable.

> **Note:** It is not recommended to override the default routes file. Instead, you can specify additional route files, which can overwrite specific routes or append new ones.

The route file format is YAML. It contains a list of simple and multi-step routes. Here's a snippet:

```yaml
routes:
  - sourceMediaType: application/msword
    targetMediaType: application/pdf
    maxSourceSizeBytes: 204800000
    engine: LIBREOFFICE

  - sourceMediaType: application/pdf
    targetMediaType: image/png
    maxSourceSizeBytes: 102400000
    engine: PDF_RENDERER

  - sourceMediaType: image/png
    targetMediaType: image/jpeg
    maxSourceSizeBytes: 102400000
    engine: IMAGEMAGICK

  - sourceMediaType: application/msword
    targetMediaType: image/jpeg
    maxSourceSizeBytes: 102400000
    steps:
      - application/pdf
      - image/png
```

### Extend route configuration

Additional route files can be specified through environment variables with the `TRANSFORMER_ROUTES_ADDITIONAL_` prefix:

```bash
export TRANSFORMER_ROUTES_ADDITIONAL_<name>="/path/to/the/additional/route/file.yaml"
```

> **Note:** The `<name>` suffix can be a random string. It doesn't need to match any other labels - it just differentiates multiple additional route files.

The additional route files need to be YAML files with the same format as the default routes file. For instance, an additional routes file for the AWS AI Engine would be specified through the environment variable:

```bash
TRANSFORMER_ROUTES_ADDITIONAL_AI_ROUTES="/ai-routes.yaml"
```

For example, the `ai-routes.yaml` content could be:

```bash
routes:
  - sourceMediaType: image/png
    targetMediaType: application/vnd.alfresco.ai.labels.v1+json
    maxSourceSizeBytes: 102400000
    engine: AWS_AI

  - sourceMediaType: image/jpeg
    targetMediaType: application/vnd.alfresco.ai.labels.v1+json
    maxSourceSizeBytes: 102400000
    engine: AWS_AI

  - sourceMediaType: image/gif
    targetMediaType: application/vnd.alfresco.ai.labels.v1+json
    maxSourceSizeBytes: 102400000
    steps:
      - image/jpeg

  - sourceMediaType: image/tiff
    targetMediaType: application/vnd.alfresco.ai.labels.v1+json
    maxSourceSizeBytes: 102400000
    steps:
      - image/gif
      - image/jpeg

  - sourceMediaType: application/pdf
    targetMediaType: application/vnd.alfresco.ai.labels.v1+json
    maxSourceSizeBytes: 102400000
    steps:
      - image/png
```

The custom route files must be mounted on the T-Router container file-system.

Multiple additional route files can be specified. Ideally, for each new custom engine a separate custom routes file should be added.

In case of clashes between routes:

* Routes from the additional files override routes in the default file;
* If the same route is specified in multiple additional files, a warning will be printed at startup, and one of the routes will randomly be kept as the T-Router configuration.
