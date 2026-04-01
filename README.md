# Qt RPC MOC Tool

## 项目简介

Qt RPC MOC Tool 是一个定制化的 Qt 元对象编译工具（Meta-Object Compiler, MOC），用于为 **[qtrpc 项目](https://github.com/Starry-Oceaning/qtrpc)** 提供自动化的类型注册能力。

该工具在 Qt 原生 MOC 的基础上进行扩展，重点解决以下问题：

- 自动注册接口参数的类型
- 自动注册序列化/反序化操作符
- 减少手动调用 `qRpcRegisterMetaType` 的工作量
- 提升 RPC 调用中参数传递的可靠性和一致性

## 发布方式
本项目**不提供完整的 MOC 工具副本及源代码**，而是以 **补丁文件** 的形式发布，供开发者基于 Qt 官方源码进行定制构建。

补丁在 Qt 原生的 MOC 工具基础上，增加了解析用户自定义 `Q_RPC_MODULE` 标记、自动生成类型注册代码逻辑，并保持与 Qt 原生 MOC 输出结构的兼容性。

使用方法如下：首先从 Qt 官方仓库或源码包获取与目标 Qt 版本对应的完整源码，进入 Qt 源码根目录后执行 `patch -p1 < /patches/xxx-xxx.xxx.patch` 应用补丁，然后按照 Qt 官方构建流程编译 `moc` 工具（通常位于 `qtbase/src/tools/moc`），最后将编译生成的新 `moc` 工具放置在 Qt 工具链路径下供项目构建使用。

以补丁形式发布主要是出于以下考虑：补丁体积小，易于适配不同 Qt 版本，开发者可根据自身 Qt 版本灵活选择补丁，并可清晰审查改动内容。

## 许可证

本项目与Qt采用完全相同的许可证：

- **GNU General Public License v2.0** - 查看 [LICENSE.GPLv2](LICENSE.GPLv2)
- **GNU Lesser General Public License v2.1** - 查看 [LICENSE.LGPLv21](LICENSE.LGPLv21)
- **GNU Lesser General Public License v3.0** - 查看 [LICENSE.LGPLv3](LICENSE.LGPLv3)
- **GNU General Public License v3.0** - 查看 [LICENSE.GPLv3](LICENSE.GPLv3)
- **GNU General Public License v3.0 with exceptions** - 查看 [LICENSE.GPL3-EXCEPT](LICENSE.GPL3-EXCEPT)
- **Qt Commercial License** - 查看 [LICENSE.QT-LICENSE-AGREEMENT](LICENSE.QT-LICENSE-AGREEMENT)

原始Qt代码版权归 **The Qt Company Ltd** 所有。

