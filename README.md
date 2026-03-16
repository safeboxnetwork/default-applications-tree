# default-applications-tree

This repository defines the available applications and their configuration templates. Each application lives in its own subdirectory and is registered in `applications-tree.json`.

---

## applications-tree.json

The root `applications-tree.json` file lists all available applications. Each entry in the `apps` array registers one application:

```json
{
    "apps": [
        {
            "name": "Nextcloud",
            "subtitle": "File Synchronization",
            "version": "31.0.8-fpm-alpine",
            "icon": "data:image/svg+xml;base64,..."
        }
    ]
}
```

### App entry fields

| Field      | Required | Description |
|------------|----------|-------------|
| `name`     | yes      | Display name of the application. Must match the directory name (case-insensitive). |
| `version`  | yes      | Default version tag used when deploying. Use `"latest"` for the most recent image. |
| `subtitle` | no       | Short tagline shown in the app listing UI. |
| `icon`     | no       | Base64-encoded SVG or PNG image used as the app icon in the UI (`data:image/svg+xml;base64,...` or `data:image/png;base64,...`). |

---

## Application directory structure

Each application has its own directory (e.g. `nextcloud/`). The directory name must match the `name` in `applications-tree.json`. It must contain a `template.json` file and can contain additional JSON files for services, domains, secrets, firewall rules, etc.

```
nextcloud/
    template.json           ← mandatory configuration template
    service-nextcloud.json  ← service definition
    domain-nextcloud.json   ← domain/ingress configuration
    nextcloud-secret.json   ← secrets / environment variables
    firewall-nextcloud.json ← firewall rules
    ...
```

---

## template.json

The `template.json` file defines the application's metadata and the list of configuration fields that are presented in the deployment form.

### Top-level structure

```json
{
    "name": "Nextcloud",
    "title": "Nextcloud",
    "subtitle": "File Synchronization",
    "description": "Nextcloud is a suite of client-server software...",
    "icon": "data:image/svg+xml;base64,...",
    "fields": [ ... ]
}
```

### Top-level fields

| Field         | Required | Description |
|---------------|----------|-------------|
| `name`        | yes      | Must match the directory name. Used as the application identifier. |
| `title`       | no       | Human-readable display title shown in the form header (falls back to `name` if omitted). |
| `subtitle`    | no       | Short tagline displayed below the title. |
| `description` | no       | Longer description of the application shown in the form or app listing. |
| `icon`        | no       | Base64-encoded SVG or PNG icon (`data:image/svg+xml;base64,...`). |
| `fields`      | yes      | Array of configuration field definitions (see below). |

---

## Field definitions

Each element of the `fields` array defines one configuration variable. Fields are rendered as form inputs during deployment. Fields with `generated` set are auto-populated and not shown in the form.

### Field properties

| Property      | Required | Description |
|---------------|----------|-------------|
| `description` | yes      | Label text displayed before the input. |
| `key`         | yes      | The environment variable name that will hold the value. |
| `value`       | no       | Default value pre-filled in the form. For `select` fields this defines the available options (see below). |
| `required`    | no       | Set to `"true"` to make the field mandatory. The form will not submit until it is filled. |
| `type`        | no       | Input type. Defaults to `"text"`. See [Field types](#field-types). |
| `info`        | no       | Additional hint or explanatory text shown alongside or below the field. |
| `generated`   | no       | Auto-generation pattern. When set the field is not shown in the form; its value is generated automatically. See [Generated values](#generated-values). |
| `advanced`    | no       | Set to `"true"` to hide the field from the UI by default. It can be revealed by pressing the `>` button. |

### Field types

The `type` property controls how the field is rendered:

| Type        | Description |
|-------------|-------------|
| `text`      | *(default)* Single-line plain text input. |
| `password`  | The value is hidden from human-readable display wherever it is shown in the UI. |
| `textarea`  | Multi-line text input. |
| `select`    | Dropdown. Options are defined in `value` as a comma-separated list. Each option can be `optionValue` or `displayText:optionValue`. |

#### Select field examples

Simple yes/no toggle:
```json
{
    "description": "Enable feature",
    "key": "FEATURE_ENABLED",
    "value": "false,true",
    "type": "select"
}
```

Options with display labels:
```json
{
    "description": "Email provider",
    "key": "MAIL_PROVIDER",
    "value": "1:Gmail,2:Microsoft Outlook/Hotmail,3:Other",
    "type": "select"
}
```

TOTP authentication toggle (advanced):
```json
{
    "description": "TOTP authentication (true/false)",
    "key": "GUACAMOLE_TOTP",
    "value": "false,true",
    "required": "true",
    "type": "select",
    "advanced": "true"
}
```

### Generated values

When `generated` is set the field value is computed automatically. The format is:

```
"<source>|<encoding>|<length>"
```

| Part       | Options          | Description |
|------------|------------------|-------------|
| `source`   | `time`, `random` | `time` seeds the hash from the current timestamp; `random` uses a random number. |
| `encoding` | `md5`, `sha256`  | Hash algorithm applied to the source value. |
| `length`   | integer          | Number of characters to take from the hash output. |

#### Examples

| Pattern             | Result |
|---------------------|--------|
| `"time\|md5\|8"`    | 8-character MD5 hash seeded from the current time. |
| `"random\|md5\|12"` | 12-character MD5 hash seeded from a random number. |
| `"random\|sha256\|20"` | 20-character SHA-256 hash seeded from a random number. |
| `""`                | Auto-generated with default settings (pattern not specified). |

---

## Complete field example

```json
{
    "description": "Nextcloud admin password",
    "key": "NEXTCLOUD_ADMIN_PASSWORD",
    "value": "",
    "required": "true",
    "type": "password",
    "info": "Must be at least 8 characters long."
}
```

Auto-generated database password (not shown in the form):
```json
{
    "description": "Postgres password for user",
    "key": "POSTGRES_PASSWORD",
    "value": "",
    "required": "true",
    "generated": "random|md5|12"
}
```

Advanced optional SMTP field:
```json
{
    "description": "Email sending protocol",
    "key": "MAIL_PROTOCOL",
    "value": "",
    "info": "Options are: empty (for no encryption), ssl, tls",
    "advanced": "true"
}
```
