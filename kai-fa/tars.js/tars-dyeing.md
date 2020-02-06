# @ tars / dyeing

The TARS dyeing basic module provides methods for obtaining and judging `staining objects`.

__Users: You should not use this module directly, but use other modules that meet the `staining standards` to obtain` staining objects`__

__Module developer: Get `TARS stained objects` You should use this module's `gen()` method to get` colored objects`__

## Dyeing (Introduction)

Coloring is a state passed on the interface's invocation chain (server <==> client) to identify a specific request processing process.

This state consists of a flag and additional information `KEY` _ (optional) _.

Services on the call chain can set (originate) and read (receive) this state, and perform corresponding processing (such as outputting specific logs and characteristics).

In the system, dyeing consists of this module and its corresponding convention.

## Standard (Convention)

If the module uses a dyeing system, it is necessary to comply with the following conventions (At the same time, we will refer to the modules that meet the conventions as the modules that meet the `staining standards`):

1. Provide the `getDyeingObj()` method and return `Dyeing Object`.
2. Use the @ tars/dyeing.gen method to generate a dyeing object.
3. Use the @ tars/dyeing.is() method to determine whether the dyeing object is valid.

## Module method

### TarsDyeing.gen (dyeing [, key, args])

By calling this method, you can get `colored objects`:

__dyeing__: Do you need dyeing
__key__: Coloring additional information `KEY` (optional)
__args__: additional parameters of the program (optional)

`key` is passed to the next service through the call chain, while `args` is only valid in the current dyed object (it is not passed).

__Please note: The cause of the staining is different from the cause of the staining (thing). This module only cares about the stained objects, not the cause of the staining.__

### TarsDyeing.is(obj)

Call this method to determine whether an object passed in is a `colored object`:

If an empty object is passed in, the method will also return `false`

## Dyeing Object Properties

### dyeingObj.dyeing

Whether coloring is required, this is `Boolean`

### dyeingObj.key

Additional information passed by the dye, this is `String` and is optional and may not exist

### dyeingObj.args

Additional information about the dyed object itself, which should not be passed between services by business code (only used as a local parameter).

This object is not limited in type and is also optional