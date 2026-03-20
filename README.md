# Qt RPC MOC Tool

## 项目简介

Qt RPC MOC Tool 是一个定制化的 Qt 元对象编译工具（Meta-Object Compiler, MOC），用于为 **[qt-rpc 项目](https://github.com/Starry-Oceaning/qt-rpc)** 提供自动化的类型注册能力。

该工具在 Qt 原生 MOC 的基础上进行扩展，重点解决以下问题：

- 自动注册接口参数的类型
- 自动注册序列化/反序化操作符
- 减少手动调用 `qRpcRegisterMetaType` 的工作量
- 提升 RPC 调用中参数传递的可靠性和一致性

## 许可证

本项目与Qt采用完全相同的许可证：

- **GNU Lesser General Public License v3.0** - 查看 [LICENSE.LGPLv3](LICENSE.LGPLv3)
- **GNU General Public License v3.0** - 查看 [LICENSE.GPLv3](LICENSE.GPLv3)
- **GNU General Public License v3.0 with exceptions** - 查看 [LICENSE.GPL3-EXCEPT](LICENSE.GPL3-EXCEPT)

原始Qt代码版权归 **The Qt Company Ltd** 所有。
