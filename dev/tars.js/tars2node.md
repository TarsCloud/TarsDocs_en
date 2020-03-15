# Tars2Node

`tars2node` can convert the Tars IDL definition file to the version used by the JavaScript language, combined with the [@tars/stream](https://www.npmjs.com/package/@tars/stream) module to perform codec operations.

If you have Tars RPC requirements, you can call it in conjunction with the [@tars/rpc](https://www.npmjs.com/package/@tars/rpc) module.

## Usage

Just use the `tars2node` precompiler in the build directory (Linux platform).

```bash
tars2node [OPTIONS] tarsfile
```

## options

Options | Functions |
| ------------- | ------------- |
| --stream-path = [DIRECTORY] | Specify the codec module name, the default is @tars/stream. |
| --rpc-path = [DIRECTORY] | Specify the RPC module name. The default is @tars/rpc. |
| --allow-reserved-namespace | Whether to allow `tars` as a namespace (because this namespace is mainly used for tars file definitions for framework services). |
| --dir = [DIRECTORY] | Output directory for generated files. |
| --relative | Limits all `.tars` files to look in the current directory. |
| --tarsBase = [DIRECTORY] | Specify the search directory for `.tars` files. |
| --r | Convert nested `.tars` files. |
| --r-minimal | Reduce dependent files and remove unnecessary members. |
| --r-reserved | Members to keep when thinning dependent files. |
| --client | Generate the calling class code for the client. |
| --server | Generate server-side framework code. |
| --ts | Turning on this option will only generate TypeScript (.ts) code. |
| --dts | Append a TypeScript description file (.d.ts) when generating. |
| --long-type = [number | string | bigint] | Optionally use \<Number \ | String \ | BigInt \> to express the \ <long \> type, with a default value of \<Number \>. |
| --string-binary-encoding | When you encounter character encoding problems or need to access the original data, turn on this option to use \<buffer \> to store \<string \>. |
| --enum-reverse-mappings | Output code \<enum \> supports reverse mapping of enum values ​​to enum names. |
| --optimize = [0 \| s] | Optimize the output code size. The default is 0 (that is, not optimized). |

## Examples

```bash
tars2node Protocol.tars
```

The above command will convert the constants, enumerations, and structures defined in the Protocol.tars file to generate ProtocolTars.js for encoding and decoding.
For usage, please refer to the [@tars/stream](https://www.npmjs.com/package/@tars/stream) module documentation.

```bash
tars2node Protocol.tars --client
```

The above command will convert the data types such as `constant`,` enumeration value`, `structure` defined in the file, and convert the` interface` description section into a Tars RPC client interface file, and finally generate `ProtocolProxy.js` for Used by the caller.
For usage, please refer to [@tars/rpc](https://www.npmjs.com/package/@tars/rpc) module documentation.

```bash
tars2node Protocol.tars --server
```

The above command will convert data types such as `constant`,` enumeration value`, `struct` defined in the file, and convert the` interface` description section into a Tars RPC server interface file, and finally generate `Protocol.js` and `ProtocolImp.js` is used by the service provider.  
Developers do not need to change `Protocol.js`, they only need to continue to improve the specific functions in the` ProtocolImp.js` implementation file to provide services as Tars RPC server.
For usage, please refer to [@tars/rpc](https://www.npmjs.com/package/@tars/rpc) module documentation.

## Compile from source

1. Install build-essential for the corresponding platform
2. Install [CMake](https://cmake.org/)

linux & mac:
```
cd build
cmake ..
make -j4
```

windows:
```
cd build
cmake ..
cmake --build . --config Release
``` 