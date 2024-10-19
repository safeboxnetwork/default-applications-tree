# default-applications-tree

You can add more application sections into applications-tree.json.
You have to specify the application name and version. For example:

        {
            "name": "nextcloud",
            "version": "latest"
        }

The name must be the same as the directory name of the application.

The directory of an application have to contain a template.json file and can contain more json files (service, domain, secret, etc. files).

The mandatory template.json file's structure is the following. 
 - "name" - the name of the application, must be the same as the directory name
 - "fields" - array of used variables by service

        {
                "name": "vaultwarden",
                "fields": [
                        ...
                ]
        }

An element of fields can contain the following keys.
 - description - label of the field, this text will appear before element 
 - key - name of variable
 - value - default value of variable in the form
 - required - if set "true" then fill in of the field is required in the form
 - type - if not set then default is text, available field types: text, password, textarea, select 
        - if type is "select" then options are separated by ",". Option's value and text is separated by ":", but text is not mandatory. For example:

        "value": "yes,no"
        "value": "1:gmail,2:microsoft outlook/hotmail,3:other",

 - generated - the value of the variable is auto generated, so the field will not appear in the form. Generated examples:

        "time|md5|8" - generated from time, encoded by md5 and character length is 8
        "random|md5|20" - random generated number, encoded by sha256, length is 20

Field element example:

        {
                "description": "Please add Nextcloud password:",
                "key": "NEXTCLOUD_PASSWORD",
                "value": "",
                "required": "true",
                "type": "password"
        },

