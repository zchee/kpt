## create-setter

Create or modify a field setter

<link rel="stylesheet" type="text/css" href="/kpt/gifs/asciinema-player.css" />
<asciinema-player src="/kpt/gifs/cfg-create-setter.cast" speed="1" theme="solarized-dark" cols="100" rows="26" font-size="medium" idle-time-limit="1"></asciinema-player>
<script src="/kpt/gifs/asciinema-player.js"></script>

    kpt tutorial cfg create-setter

[tutorial-script]

### Synopsis

    kpt cfg create-setter DIR NAME VALUE

  DIR

    A directory containing Resource configuration.

  NAME

    The name of the setter to create.

  VALUE

    The current value of the field, or a substring within the field.

### Creating a Custom Setter

**Given the YAML:**

    # resource.yaml
    apiVersion: v1
    kind: Service
    metadata:
      ...
    spec:
      ...
      ports:
        ...
      - name: http
        port: 8080
        ...

**Create a new setter:**

    # create a setter for ports
    $ kpt cfg set create DIR/ http-port 8080 --type "integer" --field "port"

  Resources fields with a field name matching `--field` and field value matching `VALUE` will
  have a line comment added marking this field as settable.

**Newly modified YAML:**

    # resource.yaml
    apiVersion: v1
    kind: Service
    metadata:
      ...
    spec:
      ...
      ports:
        ...
      - name: http
        port: 8080  # {"type":"integer","x-kustomize":{"partialFieldSetters":[{"name":"http-port","value":"8080"}]}}
        ...

  Setters may also be defined directly by editing the yaml and adding the comment.

Users may not set the field value using the `set` command:

    # change the http-port value to 8081
    $ kpt cfg set DIR/ http-port 8081

### Using default values

The default values for a setter may be:

- valid field values (e.g. `8080` or `008080` for a port)
- invalid values that adhere to the schema (e.g. `0000` for a port)
- values that do not adhere to the schema (e.g. `[PORT]` for port)

A setter may be for a substring of a full field:

    $ kpt cfg set create DIR/ image-tag v1.0.01 --type "string" --field "image"

    image: gcr.io/example/app:v1.0.1 # # {"type":"string","x-kustomize":{"partialFieldSetters":[{"name":"image-tag","value":"v1.0.1"}]}}

A single field value may have multiple setters applied to it for different parts of the field.

### Examples

    # create a setter for port fields matching "8080"
    kpt cfg create-setter DIR/ port 8080 --type "integer" --field port \
         --description "default port used by the app"

    # create a setter for a substring of a field rather than the full field -- e.g. only the
    # image tag, not the full image
    kpt cfg create-setter DIR/ image-tag v1.0.1 --type "string" \
        --field image --description "current stable release"

### 
