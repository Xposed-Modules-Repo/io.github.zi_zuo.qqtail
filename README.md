# QQ 机型尾巴 · QQTail

自定义 QQ 说说底部那行「📱 机型名」的尾巴，解决两个老问题：

1. **去掉厂商前缀** —— 不再出现 `oneplus iPhone 18 Pro Max`
2. **保住大小写** —— 不再被服务端强制改成 `iphone 18 pro max`

> LSPosed 模块 · Android 8.0+
> 包名：`io.github.zi_zuo.qqtail` · 版本：1.9
> 作用域：仅 `com.tencent.mobileqq` —— **只支持 QQ 本体，独立的「QQ空间」App 不支持**
> 适配版本：**QQ 9.3.70（实测通过）**；其他 QQ 版本未经验证，请自行测试

---

## 为什么需要它

QQ 说说的尾巴完全是**服务端**渲染的：

| 环节 | 实际行为 |
|---|---|
| 客户端 | 只上报 `m=<型号>&f=<厂商>`，大小写原样正确 |
| 服务端 | 机型在库里 → 用库里的规范名；不在库里 → 走「厂商 + 小写型号」兜底 |

所以常见的"改机型"工具只能改前半段：型号改了，厂商还是 `oneplus`，于是拼出
`oneplus iphone 18 pro max`；而机型名服务端一旦不认识，就一律 `toLowerCase()`。

本模块两头都治：清空厂商字段 + 用「没有小写映射的 Unicode 同形字」保住大写。

## 功能

- **尾巴文本**自由填写，支持中文 / 英文 / 数字；界面里 1:1 预览 QQ 详情页那行的样子
- **去掉厂商前缀**：清空 `Build.BRAND` / `Build.MANUFACTURER` / `ro.product.brand`
- **大小写保护**：把 ASCII 大写字母换成数学字母区的同形字（`P` → `𝖯`），
  这些码位在 UnicodeData 里没有 `Lowercase_Mapping`，`toLowerCase()` 对它们无效，
  字形却仍是视觉大写。默认无衬线体，和原版几乎看不出差别
- **版本自检**：设置页底部显示本机 QQ 版本与适配状态
- 调试日志可开关（LSPosed 日志搜 `QQTail`）

## 安装

1. 手机已 Root，装好 LSPosed（Zygisk / Riru 均可）
2. 安装本模块 APK，打开填尾巴文本并保存
3. LSPosed 管理器里启用模块，作用域**只勾 QQ**（`com.tencent.mobileqq`）
4. **强行停止 QQ** 再重开（模块在进程启动时注入）
5. 发一条说说查看效果

## 安装了但没生效？

1. 确认作用域只勾了 QQ，且模块处于启用状态
2. **强行停止 QQ** 再重新打开
3. 发一条**新**说说 —— 手机标识仅对新动态生效
4. 还不行就看 LSPosed 日志里 `QQTail` 开头的行，每层 hook 是否命中都打出来了

## 已知限制

- QQ 9 的 NT kernel 有 native 侧设备采集，纯 Java hook 拦不住 `__system_property_get`
- 开启大小写保护后，尾巴那串字符不再是纯 ASCII（视觉一致，复制出来是另一个码位）
- 仅支持 QQ 本体；独立的「QQ空间」App（`com.qzone`）不在支持范围
- 其他 QQ 版本未经验证；QQ 升级后若尾巴异常，请把日志反馈到 Issues
- 仅用于个人自定义展示，请遵守腾讯的服务条款

## License

MIT
