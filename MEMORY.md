# TALIH PD1 5.15 内核迁移 — 项目记忆 (MT6771)

> 本文档覆盖 **5.15 迁移线**；4.14 生产线文档 = ../../MEMORY.md（kernel/ 根）
> 更新: 2026-08-16 — 项目刚开工，处于阶段 0（基座编译验证）
> 仓库: github.com/fumorn/TALIH-PD1-5.15-Kernel | 上游: github.com/techyminati/alps-5.15
> 本地: /home/fumor/work/flash_boom/pad1/boot/kernel/5.15/

## 目标
将 Chuwi Hi10 Max（MT6771，当前 4.14.186）内核迁移到 5.15（MTK alps-release
t0.mp1.tc8sp2-V1.14，5.15.41），保持全部硬件功能 + KSU/SUSFS。

## 关键事实
- 设备：MT6771 Helio P70 平板（tb8788p1_64_wifi 工程），combo 芯片 MT6631（gen3 wifi）
- 分区：boot 32MB / vendor_boot 64MB（boot v3+ 方案可行）
- 4.14 生产内核：github.com/fumorn/TALIH-PD1-Kernel（wmt_drv 已内建，KPM 关闭）
- 骨架平台：mt6835（alps515 的 dts: k6835v1_64.dts + mt6835_6377.dtsi + cust_*.dtsi）
- defconfig 起点：k6853v1_64_gki_defconfig（alps515 唯一 MTK gki defconfig）
- 构建：build.config.mtk.aarch64（LLVM=1 + aarch64-linux-gnu-）→ 本地无 clang，
  编译验证走 Actions
- 调试手段（无 UART）：mtk client 抓 expdb Preloader/LK 日志 + pstore/last_kmsg
  —— mtk client 由用户独立操作，我只需给出要执行的命令

## 逆向/参考素材库
| 仓库 | 用途 |
|---|---|
| techyminati/alps-5.15（本仓上游，分支 alps-u0） | 5.15 基座（5.15.41 MTK 全框架） |
| techyminati/android_kernel_gigaset_mt6768 | 4.14.191 connectivity 参照（已用于 wmt 还原） |
| techyminati/realme_kernel_modules + realme C12/C15（realme-kernel-opensource） | **MT6771 4.14 connectivity 全套源码**（wmt_drv 还原基座，已验证） |
| AgentFabulous/begonia | MT6785 4.14（wmt_step/cpupcr 机制来源） |
| NothingOSS/android_kernel_modules_nothing_mt6878（branch mt6878/Tetris/u） | **6.1 配套 MT6631 全套 connectivity（wlan/core/gen3 等）**——5.15 connsys 适配主参考 |
| techyminati/android_kernel_realme_mt6771V | MT6771 4.14 完整内核源（平台驱动对照） |
| techyminati/alps-4.19 | 4.19 中间版本参照（API 过渡） |

## TODO（阶段 0 开工）
- [ ] GitHub 创建 TALIH-PD1-5.15-Kernel 仓库（用户建仓，token 无权限）→ push
- [ ] Actions workflow：build.config.mtk.aarch64 编译（clang 环境）跑通
- [ ] 记录 5.15 构建产物格式（Image 大小、boot v3 + vendor_boot 打包组件清单）
- [ ] 骨架平台定案：mt6835 dts 基线整理

## TODO（阶段 1 最小 boot，2-4 周）
- [ ] mt6771 dts 转换（4.14 → 5.15 格式，对照 mt6835 骨架）
- [ ] 最小驱动集：gic/sysirq、arch timer、clk-mtk、pinctrl、8250-mtk uart
- [ ] ATF/TEE 握手（4.14 mtk_sip_call/emi 保护/teei 推断 → 5.15 对接）
- [ ] boot v3 + vendor_boot 打包链路
- [ ] 最小 boot 验证：mtk client 抓 expdb/pstore 日志出 console 字符
- [ ] 风险确认：MT6771 GIC 版本（v2/v3）、iommu 形态（4.14 m4u vs 5.15 mtk-iommu）

## TODO（阶段 2 核心平台，4-8 周）
- [ ] mt6358 PMIC + regulator 移植
- [ ] iommu
- [ ] mtk-drm 5.15 架构 + LCM（panel-himax-hx83102，4.14 源码已有）
- [ ] 触摸（4.14 驱动已有）

## TODO（阶段 3 connsys，启动到系统后，8-16 周）
- [ ] wmt 框架：alps515 内建 conninfra + Nothing 5.x 模块源码适配
- [ ] wlan gen3 / bt / gps / fm：Nothing 5.x 源码 + mt6771 平台文件（mt6771.c 从 4.14 适配）
- [ ] 4.14 剩余 vendor 模块还原（wlan_drv_gen3 等）——视需要穿插

## TODO（阶段 4 收尾，4-8 周）
- [ ] 相机/音频/传感器
- [ ] 全硬件回归 + KSU/SUSFS 移植

## 历史记录
- 2026-08-16 开工：本地树从 /tmp/opencode/alps515 克隆；origin 指向新仓库，
  upstream 指向 techyminati；阶段 0 编译验证待仓库建成后走 Actions
