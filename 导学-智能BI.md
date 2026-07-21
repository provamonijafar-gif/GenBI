# 导学：智能BI

## 1. 前置知识（面试高频标注）
| 知识点 | 为何需要 | 在本项目中的位置 | 高频度 |
|---|---|---|---|
| React 表单状态与异步提交流程 | 上传文件、提交目标、处理中禁用按钮、成功后展示结果是本项目最核心的交互闭环 | `frontend/src/pages/AddChart/index.tsx`、`frontend/src/pages/AddChartAsync/index.tsx` | 高 |
| TypeScript API 类型约束 | 前端通过 OpenAPI 生成的 `API.*` 类型约束请求与页面数据结构，面试会追问“类型到底管住了什么” | `frontend/src/services/yubi/typings.d.ts`、`frontend/src/services/yubi/chartController.ts` | 高 |
| `FormData` 与文件上传 | 这个项目的真实入口就是文件上传到图表分析接口，必须讲清 query 参数、body、文件三者如何组合 | `frontend/src/services/yubi/chartController.ts`、`frontend/src/pages/AddChart/index.tsx` | 高 |
| ECharts 渲染安全兜底 | AI 返回的是字符串，不是可信对象，项目里专门做了 JSON 解析、schema 校验、危险字段清洗、错误边界兜底 | `frontend/src/utils/chartSchema.ts`、`frontend/src/components/SafeChart/index.tsx`、`frontend/src/components/ChartErrorBoundary/index.tsx` | 高 |
| React 性能优化与虚拟列表 | `react-window` 确实用了，但只用于原始 CSV 数据弹窗，不要夸大成“整站级性能治理” | `frontend/src/components/VirtualDataTable/index.tsx`、`frontend/src/pages/myChart/index.tsx` | 高 |
| 轮询、指数退避与 Page Visibility API | 异步生成图表后需要刷状态；本项目的亮点是“有任务才轮询 + 无变化退避 + 页面隐藏暂停” | `frontend/src/hooks/usePolling.ts`、`frontend/src/pages/myChart/index.tsx` | 高 |
| Ant Design Pro / Umi Max 页面组织 | 前端工程化的真实证据主要在路由、页面分层、请求封装、ProTable 管理页模式 | `frontend/config/routes.ts`、`frontend/src/pages/TableList/index.tsx` | 中 |
| 本地持久化与布局系统 | 数据看板用了 `react-grid-layout` + `localStorage` 保存布局，适合回答“前端如何做轻量状态持久化” | `frontend/src/pages/Dashboard/index.tsx` | 中 |
| 分页与搜索联动 | 历史图表页里有搜索后重置到第一页、分页驱动查询参数更新，属于很常见的中频考点 | `frontend/src/pages/myChart/index.tsx` | 中 |
| 前后端契约一致性 | 项目描述里写“Excel/CSV”，但仓库真实后端目前只接收 `xls/xlsx`，这是非常好的真实性问题 | `frontend/src/pages/AddChart/index.tsx`、`frontend/src/pages/AddChartAsync/index.tsx`、`backend/src/main/java/com/yupi/springbootinit/controller/ChartController.java` | 高 |

## 2. 重点亮点与学习顺序（先看这个）
- 亮点 1：AI 图表生成前端闭环
  为什么重要：这是最能证明你参与真实业务链路的部分，能从表单、上传、接口、结果展示一口气讲到位。
  通用技术关键词：React 表单、异步请求、提交幂等、文件上传、结果渲染。
  先看文件：`frontend/src/pages/AddChart/index.tsx`、`frontend/src/services/yubi/chartController.ts`。
  建议学习顺序：先看页面如何收集参数，再看服务层如何组装 `FormData`，最后看成功后如何展示图表与分析结论。

- 亮点 2：同步/异步双模式设计
  为什么重要：面试官很喜欢问“为什么不是一个接口做到底”，这题能自然引到用户体验、耗时任务、状态展示与轮询。
  通用技术关键词：同步交互、异步任务、任务状态、用户反馈、页面跳转。
  先看文件：`frontend/src/pages/AddChartAsync/index.tsx`、`frontend/src/pages/myChart/index.tsx`、`backend/src/main/java/com/yupi/springbootinit/controller/ChartController.java`。
  建议学习顺序：先理解异步提交为什么不直接返回图表，再看任务状态在“我的图表”页如何可视化。

- 亮点 3：不可信 AI 输出的前端安全处理
  为什么重要：这是项目最像“大厂面试题”的部分，能把 AI 应用从“调接口”升级到“可信渲染”。
  通用技术关键词：JSON 容错解析、schema 校验、运行时校验、危险字段清洗、错误边界。
  先看文件：`frontend/src/utils/chartSchema.ts`、`frontend/src/components/SafeChart/index.tsx`、`frontend/src/components/ChartErrorBoundary/index.tsx`。
  建议学习顺序：先看 `parseChartOption` 做了哪些校验，再看 `SafeChart` 如何根据校验结果决定兜底渲染。

- 亮点 4：任务轮询性能优化
  为什么重要：这是“前端不只是写页面”的直接证据，能讲出请求治理和资源浪费控制。
  通用技术关键词：Polling、指数退避（Exponential Backoff）、Page Visibility API、状态快照、无效请求削减。
  先看文件：`frontend/src/hooks/usePolling.ts`、`frontend/src/pages/myChart/index.tsx`。
  建议学习顺序：先看 Hook 的计时器管理，再看业务页面如何用状态快照告诉 Hook“数据有没有变”。

- 亮点 5：大数据量展示的轻量性能优化
  为什么重要：你简历里写了 `react-window`，但要讲得真实，最好明确场景是“查看原始 CSV 数据弹窗”而不是全站。
  通用技术关键词：虚拟列表（Virtual List）、按需渲染、CSV 解析、弹窗性能。
  先看文件：`frontend/src/components/VirtualDataTable/index.tsx`、`frontend/src/pages/myChart/index.tsx`。
  建议学习顺序：先看弹窗的输入输出，再看 `parseCsv` 和 `FixedSizeList` 如何减少一次性 DOM 渲染量。

- 亮点 6：后台列表与可配置看板
  为什么重要：这块体现的是企业后台常见能力：列表、详情、删除、布局保存。
  通用技术关键词：ProTable、分页查询、Drawer 详情、localStorage 持久化、拖拽布局。
  先看文件：`frontend/src/pages/TableList/index.tsx`、`frontend/src/pages/Dashboard/index.tsx`、`frontend/config/routes.ts`。
  建议学习顺序：先看列表管理模式，再看看板页面如何做本地状态持久化与布局生成。

## 3. 必备知识点
- 搞懂 `AddChart` 与 `AddChartAsync` 的交互差异，以及为什么异步模式提交成功后跳到“我的图表”页。
- 搞懂 `chartController.ts` 中 `FormData` 是怎么拼文件和参数的。
- 搞懂 OpenAPI 生成的 `API.Chart`、`API.BiResponse`、`API.ChartQueryRequest` 如何约束页面代码。
- 搞懂 `parseChartOption()` 的四层逻辑：JSON 解析、Zod 校验、危险字段清洗、标题裁剪。
- 搞懂为什么 `SafeChart` 既做 schema 校验，又包一层 `ChartErrorBoundary`。
- 搞懂历史图表页里搜索、分页、任务状态、数据弹窗四个模块怎么协同。
- 搞懂 `usePolling` 为什么不用 `setInterval`，而是用 `setTimeout` 递归调度。
- 搞懂“有变化重置轮询间隔、没变化指数退避、页面隐藏暂停”的完整策略。
- 搞懂 `react-window` 的使用边界：它优化的是大量行渲染，不解决 CSV 解析复杂度本身。
- 搞懂前后端契约不一致的真实风险：前端文案/交互允许 CSV，但后端目前只校验 Excel 后缀。

## 4. 推荐阅读（结合仓库）
| 主题 | 通用技术点 | 建议阅读位置 | 预计时间 | 读完能回答什么 |
|---|---|---|---|---|
| AI 图表同步生成 | React 表单、异步提交、防重复点击 | `frontend/src/pages/AddChart/index.tsx` | 20 分钟 | 我怎么实现文件上传、提交锁定和结果展示 |
| AI 图表异步生成 | 任务提交、成功反馈、页面跳转 | `frontend/src/pages/AddChartAsync/index.tsx` | 15 分钟 | 为什么要拆同步/异步两条链路 |
| 上传服务封装 | `FormData`、请求封装、OpenAPI 生成代码 | `frontend/src/services/yubi/chartController.ts` | 20 分钟 | 我如何把文件和业务参数一起传给后端 |
| 运行时图表校验 | Zod、运行时类型校验、容错解析 | `frontend/src/utils/chartSchema.ts` | 30 分钟 | 为什么 TypeScript 不够，还要加 zod |
| 图表安全渲染 | 安全兜底、错误边界、降级展示 | `frontend/src/components/SafeChart/index.tsx`、`frontend/src/components/ChartErrorBoundary/index.tsx` | 20 分钟 | AI 返回异常内容时页面为什么不会直接炸掉 |
| 历史图表页 | 搜索分页、状态展示、弹窗联动 | `frontend/src/pages/myChart/index.tsx` | 30 分钟 | 历史页如何兼顾查询体验与异步任务刷新 |
| 智能轮询 Hook | 轮询调度、指数退避、可见性感知 | `frontend/src/hooks/usePolling.ts` | 25 分钟 | 我如何减少无意义轮询请求 |
| 原始数据虚拟列表 | 虚拟滚动、表格弹窗、大数据量展示 | `frontend/src/components/VirtualDataTable/index.tsx` | 20 分钟 | 为什么 `react-window` 适合这里、收益边界在哪里 |
| 数据看板 | 拖拽布局、本地持久化、批量拉取 | `frontend/src/pages/Dashboard/index.tsx` | 25 分钟 | 如何做用户自定义布局与页面恢复 |
| 后台表格页 | ProTable、详情抽屉、批量删除 | `frontend/src/pages/TableList/index.tsx` | 20 分钟 | 我如何组织企业后台常见管理页面 |
| 路由与工程组织 | Umi Max、页面分层、访问控制 | `frontend/config/routes.ts` | 10 分钟 | 这个前端工程不是单页 Demo，而是有路由分层的后台应用 |
| 接口业务旁证 | 同步/异步接口、状态流转、文件校验 | `backend/src/main/java/com/yupi/springbootinit/controller/ChartController.java` | 25 分钟 | 前端页面为什么这样设计，后端任务状态又是怎么流转的 |

## 5. 自学提醒
- 若某文件或原理看不懂，请继续追问 AI；本 skill 负责给学习路径与题目，不提供逐行讲解。

## 6. 项目技术定位
- 倾向：`前端`
- 依据：项目亮点主要落在前端的上传交互、图表安全渲染、历史页管理、性能优化与轮询治理；`backend` 这里更多是帮助理解接口契约和任务状态流转。

## 7. 核心原理解析
### 7.1 文件上传到 AI 图表生成
- 问题：用户既要输入自然语言分析目标，又要上传原始数据文件，前端不能只传一个 JSON。
- 机制：页面表单收集 `goal/name/chartType/file`，服务层用 `FormData` 把文件二进制和其他字段一起提交给接口。
- 在本项目中的落点：`frontend/src/pages/AddChart/index.tsx` 负责取出 `values.file.file.originFileObj`，`frontend/src/services/yubi/chartController.ts` 负责构造 `FormData`。

### 7.2 同步与异步两种任务模式
- 问题：有些数据集很小，用户希望直接得到结果；有些任务耗时长，如果一直阻塞页面体验很差。
- 机制：同步模式直接等待接口返回图表与分析结论；异步模式先落库并返回任务 id，再由历史页查询任务状态。
- 在本项目中的落点：`frontend/src/pages/AddChartAsync/index.tsx` 提交后跳转 `my_chart`，`backend/src/main/java/com/yupi/springbootinit/controller/ChartController.java` 中异步接口先保存 `wait` 状态，再异步改成 `running/succeed/failed`。

### 7.3 AI 输出不可信时如何渲染
- 问题：AI 返回的字符串可能不是合法 JSON，也可能包含不适合直接渲染的字段。
- 机制：先尝试 `JSON.parse`，失败时截取最外层 JSON；再用 Zod 校验基础结构；然后清理可能包含函数字符串或高风险键；最后再交给 ECharts。
- 在本项目中的落点：`frontend/src/utils/chartSchema.ts` 的 `parseChartOption()`。

### 7.4 双层兜底避免图表页面崩溃
- 问题：即使 JSON 结构合法，也可能在 ECharts 运行时抛错。
- 机制：第一层是 schema 校验失败时给出 fallback option；第二层是 `ChartErrorBoundary` 捕获渲染期异常并给用户一个可重试的结果页。
- 在本项目中的落点：`frontend/src/components/SafeChart/index.tsx`、`frontend/src/components/ChartErrorBoundary/index.tsx`。

### 7.5 轮询如何避免无效请求
- 问题：异步任务页如果固定 3 秒轮询一次，即使数据没变化、页面还在后台标签页，也会一直打接口。
- 机制：有待处理任务时才启动轮询；若前后两次状态快照一致，则把间隔按倍数放大；页面隐藏时暂停，恢复可见时立即拉一次。
- 在本项目中的落点：`frontend/src/hooks/usePolling.ts` 与 `frontend/src/pages/myChart/index.tsx`。

### 7.6 大量原始数据如何展示
- 问题：用户查看图表原始 CSV 时，如果一次性渲染所有行，弹窗很容易卡顿。
- 机制：先解析 CSV 为表头和行数据，再用 `react-window` 的固定高度列表只渲染可见区域。
- 在本项目中的落点：`frontend/src/components/VirtualDataTable/index.tsx`。

## 8. 关键设计决策
| 决策点 | 备选 | 取舍 | 风险 | 验证 |
|---|---|---|---|---|
| 图表生成做同步还是异步 | 只做同步 / 只做异步 / 双模式 | 本项目选择双模式，兼顾小任务即时反馈和长任务可用性 | 页面与状态流转更复杂 | 阅读 `AddChart`、`AddChartAsync`、`myChart` 三页联动是否闭环 |
| AI 图表结果是否直接渲染 | 直接 `JSON.parse` 后渲染 / 增加运行时校验与兜底 | 选择增加 Zod + sanitize + error boundary | 增加一些前端实现复杂度 | 阅读 `chartSchema.ts` 与 `SafeChart` 的失败分支 |
| 轮询是否固定间隔 | 固定间隔 / 智能退避 | 选择智能退避，减少无变化时的请求浪费 | 逻辑更绕，调试成本更高 | 阅读 `usePolling.ts` 中 `reportDataSnapshot()` |
| 原始数据展示是否直接普通表格 | 全量表格 / 虚拟列表 | 选择 `react-window`，把优化聚焦在大行数弹窗 | CSV 解析仍是一次性完成，不是全链路优化 | 阅读 `VirtualDataTable` 的 `parseCsv` 和 `VirtualList` |
| 看板布局是否存后端 | 后端持久化 / 本地持久化 | 当前选择 `localStorage`，实现轻、成本低 | 换设备或清缓存会丢失布局 | 阅读 `Dashboard` 中 `loadDashboard()` 和 `saveDashboard()` |
| 是否把“支持 CSV”写进口播 | 直接说支持 Excel/CSV / 诚实说明当前仓库实现 | 建议诚实说明“前端文案写 CSV，但后端当前只验 Excel 后缀” | 面试官继续追问契约不一致 | 准备好回答“如果我来继续迭代，会统一前后端校验与文案” |

## 9. 量化与验证（含待测，建议）
- `react-window` 优化效果（待测）：建议准备 1k / 5k / 10k 行 CSV 样本，对比普通全量渲染和虚拟列表在弹窗打开时间、滚动流畅度、浏览器内存占用上的差异。工具可用 Chrome Performance 面板、React Profiler。
- 轮询优化效果（待测）：建议在有长时间 `wait/running` 任务时，对比“固定 3 秒轮询”与“指数退避 + 页面隐藏暂停”的请求次数。可用浏览器 Network 面板录制 5 分钟窗口统计请求数。
- 图表安全兜底效果（待测）：可构造 3 类异常数据进行验证，分别是非法 JSON、缺失 `series`、包含函数字符串字段；观察页面是否都能给出告警/兜底而不是空白页。
- 前后端契约一致性验证：准备一份 `.csv` 和一份 `.xlsx`，验证前端文案、上传控件和后端校验是否一致。目前从代码看，后端只允许 `xls/xlsx`，面试时应诚实说明这是待修复点。
- 看板布局恢复验证：在 `Dashboard` 添加多个图表并手动拖拽，再刷新页面检查 `localStorage` 恢复效果；同时说明该方案是单端持久化，不支持跨设备同步。

## 不建议回答
- “我做了完整的 CSV/Excel 双格式支持”：不建议这样回答。仓库真实代码里前端文案写 CSV，但后端 `ChartController` 只校验 `xls/xlsx`。
- “我做了全站级性能优化”：不建议这样回答。当前最明确的性能优化证据是原始数据弹窗用了 `react-window`，以及异步任务页的轮询治理。
- “我做了完整的前端测试体系/监控告警/灰度发布”：仓库没有直接证据，不建议主动展开。
- “我用 TypeScript 完全保证了 AI 返回数据安全”：不建议这样回答。真正兜住运行时风险的是 `zod` 和渲染兜底，不是 TS 编译期类型本身。
