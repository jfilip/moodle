
# Support for plugins defining their own custom context levels -- [MDL-40339](https://tracker.moodle.org/browse/MDL-40339)

## Requirements

*   based on the existing work already completed in [MDL-20045](https://tracker.moodle.org/browse/MDL-20045)
*   allow plugins to define their own context levels that are available to the system for custom code to allow assigning roles on / checking for capabilities in
*   custom context levels should be installed as part of the standard plugin installation process
*   custom context levels should be able to be updated via a standard plugin upgrade step
*   context level ID values must be dynamically generated at installation time
*   allow referring to the context level number via a constant value
*   uninstalling a plugin must remove the context level ID and also clean up any role assignments on that context level

## Solution

*   new _/lib/accesslib.php_ function *update_custom_contexts()* to check for the presence of a _/plugindir/db/contexts.php_ file and either install or update the system with the contents of this file (_@see /lib/accesslib.php:update_capabilities()_ for how this should work)
*   need a new core database table to store the custom context level ID values used for each custom context level in the system
*   need to assign a context level an ID value on install and use this whenever we need the specific context ID (i.e. within the context definition class)
*   only support local plugins to avoid issues with backup and restore - *

### Potential problems

*   caching (MUC)?
    *   Usage of MUC would be up to the individual custom context levels
*   backup and restore * - see above

## Design

 *  contexts.php file format (i.e. 'CONTEXTLEVEL_CONSTANT' => 'autoloader_class_string'):

        $contexts = array(
            'CONTEXT_ELIS_PROGRAM' => 'local_elisprogram_context_program',
            'CONTEXT_ELIS_TRACK'   => 'local_elisprogram_context_track',
            'CONTEXT_ELIS_COURSE'  => 'local_elisprogram_context_course',
            'CONTEXT_ELIS_CLASS'   => 'local_elisprogram_context_class',
            'CONTEXT_ELIS_USER'    => local_elisprogram_context_user'
        );

    *   These context definition classes will be loaded and an ID value mapping each context level will be stored within the DB
    *   The requested constant name will be defined so that the custom code can use this to refer to the context level

*   ?
