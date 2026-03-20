# Qt RPC MOC Tool

## Project Overview

Qt RPC MOC Tool is a customized Qt Meta-Object Compiler (MOC) designed to provide **[qt-rpc project](https://gitee.com/yi_luckys/qtrpc)** with automated type registration capabilities.

Built on top of the native Qt MOC, this tool focuses on solving the following problems:

- Automatically register types of RPC interface parameters
- Automatically register serialization/deserialization operators
- Reduce the need to manually call `qRpcRegisterMetaType`
- Improve the reliability and consistency of parameter transmission in RPC calls