# Corteza export and import functionality

## Base problem

In the corteza backend, the export functionality is implemented so that it exports the whole components with their ids. This can cause conflicts on import.

## Solution

Clear out all ID fields in the exported types when exporting, and import them with a newly assigned ID.

## Progress

The export functionality is implemented but only on the namespace level. The lower level components in the tree still call to the old export function.

- The relevant types have an "exportable" struct without ID fields and marshalling rules. These structs are put in the types' declaration file under "server/compose/types" (e.g.: the Namespace type is in the "server/compose/types/namespace.go" file, and so is its exportable variant.)
- The functions that makes the conversion between these types are under "server/compose/service" (e.g. for Namespace the "MakeExportable" and "CreateFromExportable" functions are under "server/compose/service/namespace.go")
- The root of the export and import function call is under "server/compose/rest/namespace.go" ("Export", "ImportInit")

## TODO

- Find any ID left in the exported struct and delete them
- Finish import conversion functionality
    - "Create...FromExportable" functions in the "server/compose/service" package -- which are not complete
- Find a clean way of ordering the import components and use lookup functions to find the IDs of their dependencies by the handle
    - Everything is nested under namespace so when importing, the Namespace object should get created first and its newly created ID should be passed down to lower levels
    - Other than that nesting, only the PageLayout is nested under Page, so the import should consider this. The Page object should get created first, and its exportable type the exportable PageLayout objects should be extracted and created (with the newly created id of the Page object passed down to them)
    - Other types only refer to their parents or dependencies by ID, which weren't exported, only their handles. This means that on export a lookup function is necessary to get the handle by id, and on import a lookup function is necessary to get the id by handle
- Finish the import function functionality
    - Call the conversion functions properly
- Maybe make the code cleaner by collecting the related functions in a separate file (import.go, export.go)

## Test

Possible by setting up an env with the provided docker-compose file ("docker-compose build" -> "docker-compose up")
This docker-compose file was not tested in the origoss copy of the corteza repo, might need some adjustments