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



