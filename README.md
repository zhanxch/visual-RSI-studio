# visual RSI studio

**非官方社区插件，由社区成员独立开发和维护，未经过 DeepSeek 官方审核或背书。**

**用于具身 Agent 的可视化工作台，重点做好四件事：可视化、数据管理、环境注册、实际效果。**

1. **可视化**：在会话中看真实控制过程和摄像头，在 Agent RSI Board 看多轮迭代的改动、指标和分支。
2. **数据管理**：整理每次运行的输入、配置、代码版本、轨迹、画面和结果，能够查找与比较。
3. **环境注册**：接入并选择仿真或真实环境，说明依赖、资源、连接和启动条件；同一环境供不同方案复用。
4. **实际效果**：看任务是否完成、评分与耗时，以及修改前后是否改善，用实际结果检验工作台是否好用。

日常使用：选工作区与环境 → 会话中启动 → 看过程 → 保存和比较结果。仍沿用 DSH 原生会话操作，持续任务可选用 `/goal 复现|优化|开发`。

**第一次试用**　打开 `examples/studio-project`，在 **运行配置** 启动 `rsi-workflow-demo`，再看 **Agent RSI Board**。演化流程实时显示候选的审查、可插拔验证、评估、采用和下一轮加载凭据。点 **批量评测** 查看 RoboDojo 8 个任务、400 个场景的历史矩阵。[演化流程与探针接入](docs/rsi-flow.md)

要接入仿真器，点 **环境注册**，选择本机 checkout 与 Python。内置支持 LIBERO、LIBERO-Pro、RoboCasa、RoboTwin 2；其他环境可用同一份清单协议接入。LIBERO 与 LIBERO-Pro 已验证重置与取帧，另两个已核对入口，尚待完整仿真验证。[环境与 Board 使用指南](docs/rsi-board.md)

Studio 现名 **visual RSI studio**，仓库已更名为 `visual-RSI-studio`（旧地址自动跳转）。LinkMind 指独立的研究仓库 [embodied-infra-half-life](https://github.com/zhanxch/embodied-infra-half-life)。插件标识与存储路径暂沿用旧值，兼容已有安装和会话。为什么要做成一个完整的 Studio，而不只是插件：见 [essay](docs/essay.md)（English）。

这是开发预览 `0.12.0`，当前验证环境为 **Linux x64 + DSH 0.2.2-rc.2**。它不是新的控制算法，也不自动完成任意仓库的复现。

## 界面

![IDE 工作台：项目会话树、真实控制会话与历史摄像头快照](docs/images/studio-0.8.png)

工作台沿用 DSH 的模型、登录、流式会话与原生文件查看器；外框采用活动栏、项目树、标签页、下方对话和状态栏。下图保留 0.8 版界面截图；0.8.4 已显示新名称。截图来自隔离验证项目中的历史 HexaShell 记录，未重跑机器人。

## 能做什么

- **从一句话开始**：`/goal 复现 当前仓库的 Push-T，保留原模型、场景和预算`。三种任务明确区分，无需新建项目卡或填写验收表。
- **会话协作**：使用原生流式回复和插话；通过 `/goal` 查看目标，`/goal edit 新目标` 调整，`/goal pause` 暂停，`/goal resume` 恢复。普通消息也可以直接启动一次测试。
- **任务可视化与接续**：面板展示原始指令、任务说明、验证标准、步骤、决定、证据与关联运行。在同一工作区的新会话点击 **在此会话接续**，读取同一任务，不复制一份进度。
- **打开原有代码库**：使用 DSH 工作区选择器和文件树；配置保存在普通项目文件中，由助手准备，也可以自己编辑。
- **项目会话目录**：每个原生工作区是一个项目根节点，里面按功能、修复或实验自由建文件夹。右键项目或文件夹新建会话，自动绑定右键所在项目；支持重命名、同项目内移动、归档与恢复。文件夹只做分类，不改变执行目录，也不建立父子 Agent 关系。
- **项目概览**：读取最近运行的原始分数、耗时、相机快照与任务进展。没有报告就不补成绩，单次分数不当成成功率。
- **任务图**：选择项目任务，查看工作项与 `depends_on` 依赖。任务仍由 `/goal` 启动，无需绘图或填表。
- **快速打开**：顶部搜索会话和视图，左侧活动栏访问项目文件、任务图、运行配置和控制会话。
- **上下工作台**：下方正常对话；上方用原生标签页查看文件、其他会话、控制会话和摄像头。左侧会话的 ↗ 按钮可在上方查看，下方保持当前会话。拖动分界线调整对话高度和项目树宽度；小窗口可展开项目抽屉。
- **真实控制会话**：直接读取 HexaShell 等方案的 Codex 原始事件，显示 Agent 消息、正在执行的工具和返回结果；普通 stdout 收在诊断日志。API 方案可接入结构化事件协议。

对话中的模型负责协助开发；外部方案实际使用哪个模型、场景和预算，由它自己的启动命令与配置决定。选择 GPT 作为会话模型不会自动改写项目模型。

## 安装

需要 Linux、Python 3.11+（含 `venv`）、Git，以及 DSH **0.2.2-rc.2**。已有的 DSH 登录和默认模型继续使用。Linux 桌面发行版可使用 [dsh-linux](https://github.com/zhanxch/dsh-linux)。

先准备独立的 Python MCP 环境：

```bash
git clone https://github.com/zhanxch/visual-RSI-studio.git
cd visual-RSI-studio
python3 -m venv "$HOME/.local/share/linkmind-studio/python"
"$HOME/.local/share/linkmind-studio/python/bin/python" -m pip install -r requirements.txt
```

然后通过 DSH 自带的插件管理器安装此仓库目录：

> 安装 visual RSI studio 插件：用 plugin_manager 的 install_bundle，target 传刚下载的 visual-RSI-studio 目录的绝对路径。安装后彻底退出并重新打开 DSH。

也可以在完全退出 DSH 后使用它的 CLI，**profile 和 DSH_HOME 必须对应平时使用的应用**：

```bash
# 普通 dsh web 用户的 web profile
npm pack --ignore-scripts
dsh plugin --profile web add ./linkmind-dsh-studio-0.12.0.tgz --ignore-scripts
```

`zhanxch/dsh-linux` 桌面版使用 `desktop` profile，默认数据目录为 `~/.dsh`。可运行本仓库的安装器（需要 Node.js 24.9+ 和 npm）：

```bash
node install-desktop.mjs
# 使用自定义数据目录时，例如本机便携启动器：
# node install-desktop.mjs --home "$HOME/.local/share/dsh-linux/home"
```

重新打开 **DeepSeek Harness**，新建会话，模式菜单选 **具身模式 · visual RSI studio**。默认无需配置 LinkMind 源码路径，也无需仿真器。插件设置中的 `python` 可指定另一 MCP Python 环境；默认使用上面创建的解释器。

## 第一次使用

1. 用 DSH 原生 **工作区** 选择器打开本地仓库，模式选 **具身模式 · visual RSI studio**，照常选择会话模型。
2. 在输入框键入 `/`，选择 **goal**，再写想完成的事情。例如：

   ```text
   /goal 复现 当前仓库的 Push-T。保持原方案的模型、场景和预算，检查环境、运行并核对原始结果。
   ```

3. 助手先从指令完善任务说明、约束、验证标准与必要步骤，原始要求一直保留。点击 **任务** 查看；正常对话即可补充或纠正，`/goal edit 新要求` 可直接更新目标。
4. 要优化已有方案，用 `/goal 优化 ...`，保留基线并比较改动；要实现新功能，用 `/goal 开发 ...`，围绕使用场景实现并验证。
5. 运行后点击 **控制会话与摄像头** 查看实际控制会话与画面。需要检查配置时，在文件侧栏点击 **运行配置**，或通过右侧栏添加该页签；它是可选工具。

输入 `/goal` 查看当前目标；`/goal pause` 暂停续行；`/goal resume` 恢复；`/goal clear` 清除当前目标。**暂停目标不会停止已经启动的外部程序**，停止程序请使用运行侧栏的停止按钮。目标在原会话中持久保存，重启后需恢复续行。

只跑一次无需 Goal。例如：

> 使用当前项目已有的 hexashell 配置启动一次 Push-T，保持原模型、场景和预算。告诉我运行状态和如何查看摄像头。

没有 GPU 或仿真器也能测试：将本仓库 `examples/studio-project` 作为工作区打开，说“运行 smoke 配置”。它只用 Python 标准库，约 8 秒，输出移动蓝点的合成 PNG、日志和 JSON；不调用模型或机器人。

## 整理一个项目里的多次开发

左侧 **项目与会话** 显示 DSH 已打开的工作区。一个项目对应一个真实代码目录；下面的文件夹只是会话分类，不是在磁盘上创建子目录。

1. 点文件夹图标打开本机项目，或在目录选项中输入项目绝对路径。
2. **右键项目 → 新建文件夹**，例如“功能开发”“问题修复”“复现实验”。名称在树中直接输入；右键文件夹可以继续建子文件夹。
3. **右键文件夹 → 新建会话**。会话使用该项目的原生工作区，无论下方之前正在聊哪个项目。也可以点击该行出现的新建会话按钮。
4. 右键会话可以 **在同一目录新建会话**，适合一次修改开一个独立对话。新会话不自动继承上一段聊天；延续共同任务可用 `/goal continue`。
5. `F2` 或右键重命名；拖动或“移动到”只在同一项目内整理。会话与分类不改变实际代码路径。需要操作另一份代码时，在另一个项目中新建会话。
6. 完成的小任务可以 **归档会话**；目录选项中的 **查看归档会话** 可恢复原会话和分类。正在执行的会话不会通过归档自动停止。

左侧的“新建于”会显示完整分类位置。右键项目菜单也显示实际工作区路径。已有会话仍可在上方单独打开，同时保留下方当前对话。

## 可选的启动配置

配置是给程序保存启动参数的普通文件，不是开展对话的前置条件。已有配置可复用；没有配置时让助手读 README 和运行入口后准备，也可以在文件侧栏的 **运行配置** 中新建、复制和修改。

配置示例：

```json
{
  "version": 1,
  "recipes": {
    "original": {
      "argv": ["{project}/.venv/bin/python", "run.py", "--output", "{output}"],
      "cwd": ".",
      "timeout_seconds": 5400,
      "result": "result.json",
      "cameras": {"head": "live/head.png"},
      "metadata": {"model": "实际使用的模型", "task": "任务与场景"}
    }
  }
}
```

示例字段必须对应项目真实入口。`argv` 是参数数组，`cameras` 和 `result` 是本次输出目录下的相对路径；未提供的输出可以省略。每次运行都有新的 `{output}`，也通过环境变量 `LINKMIND_RUN_OUTPUT` 传入。不要在配置中写密码或 API Key。更多协议与比较方法见 [项目指南](docs/projects.md)。

## 接入控制会话

旧 HexaShell 配置无需改动：自动识别输出目录中的 `agent-events.jsonl` 或 `hexashell/agent-events.jsonl`。保留实际 thread ID、消息、工具参数及结果，started/updated/completed 更新同一条工具卡片。当前 Codex CLI 来源主要按消息和工具事件更新，并非逐字输出。

API 方案可在 recipe 中设置 `"conversation": {"format": "linkmind-events", "path": "control.jsonl"}`，将 API 的实际消息和工具事件写成 JSONL；具体格式见 [事件协议](docs/control-conversation.md)。不把 stdout 推测成 Agent 对话。

查看外部控制会话不会接管它。是否支持中途引导取决于该方案的输入接口；当前 HexaShell 接入为实时查看。下方 DSH 原生会话照常支持流式交互和插话。

## 跨对话接续

任务记录属于当前工作区，原生 Goal 属于执行它的会话。在新会话选择同一工作区和具身模式，点击 **任务**，选择已有任务并点击 **在此会话接续**。也可输入 `/goal tasks` 查看列表，再用 `/goal continue <任务编号>` 接续。助手会得到之前完善的说明、进展、证据和下一步。

同一任务一次由一个会话执行。原会话还在运行时，接续会提示先暂停并等本轮结束；转移成功后旧 Goal 暂停，旧会话不能继续改写该任务。新会话已有未完成目标时，先 `/goal clear` 收起当前目标；其共享任务记录仍保留。

`/goal pause` 只暂停续行，`/goal clear` 只解绑当前 Goal；它们都不会删除共享任务或停止外部程序。结束程序请使用运行侧栏。完成状态来自助手的原生 Goal 报告，面板同时保留结论和原始证据，不把进程退出视为成功。

`0.5.0` 的手工项目表单已移除，旧目标与修订仍可通过 `project_context` 查询，没有删除或自动转换。新版任务同样保存在插件 state 的 `project-book.sqlite3`。退出 DSH 后备份整个 state 即可保留任务记录；只复制 Git 仓库不会复制本机任务库。

## 当前边界

- 原生 Goal 使用 DSH 的同会话续行与轮次限制；它不是常驻后台服务、预算强制执行器或定时任务。关闭 DSH 会停止执行，重启后需要 `/goal resume`。
- 任务接续覆盖同一个本地工作区；目前没有跨设备同步或多会话并行执行同一任务。对话关联保留，换目录不会自动合并任务。
- 任务说明、步骤和证据由会话模型维护，质量取决于模型和实际验证。没有固定四步流程，也不会替你编造未知的实验协议。
- 已在 Linux 上验证；进程管理使用 `/proc` 和 Linux 子进程监督机制，未支持 Windows/macOS。
- 运行配置有相应用户权限，可以执行本地程序。导入仓库后仍需查看命令并准备它的环境、权重和资产；文件检查通过不等于复现成功。
- 外部方案独立执行。会话中途发送的消息会到达 DSH 助手，**不会自动注入外部控制策略**。中途策略引导需要该方案提供适配接口。
- 摄像头来自程序发布的 PNG/JPEG 快照，更新频率取决于原程序；它不是 USB 摄像头采集器或固定帧率视频流。界面显示最新画面的时间。
- 同时只运行一个项目或仿真；停止按钮结束进程，并非实体机器人的急停。
- 源码追溯记录 Git HEAD、tracked diff 和未跟踪文件的哈希，不打包权重、被忽略资产或完整环境。退出码 0 也不等于任务成功。
- 配置编辑器保护未保存草稿和并发修改；它是启动 JSON 编辑器，源码查看仍使用 DSH 原生文件功能。

若需要 DSH 会话直接控制 LinkMind 内置仿真，可在设置中指定另行配置好的完整 [Studio 集成开发分支](https://github.com/zhanxch/embodied-infra-half-life/tree/feat/dsh-embodied-studio) checkout（`root`）及 Python。目前该集成仍在开发分支，不能直接使用 LinkMind 的 `main` 代替。该可选路线需要仿真资产和依赖，详见[上游 Studio 文档](https://github.com/zhanxch/embodied-infra-half-life/blob/feat/dsh-embodied-studio/docs/studio-projects.md)。`codex` 设置也是可选的实验适配器，默认关闭，不改变 DSH 默认模型。该适配器在已有会话中切换模型可能受上下文压缩限制；可以新建会话、选择模型，再接续同一任务。

## 开发与验证

```bash
PYTHONPATH=backend/src python3 -m unittest discover -s tests -q
node --check client.js && node --check goal.mjs
npm pack --dry-run --ignore-scripts
```

独立包的来源和文件哈希记录在 [SOURCE.json](SOURCE.json)。界面验证使用真实 DSH 的隔离预览 profile；桌面宿主协议另行验证。截图中的蓝点是合成自检，任务面板使用实际会话的功能验证记录；原生 Push-T 的开发运行不代表这个插件的算法优势或跨平台验证。控制会话截图使用此前 HexaShell 原始事件的定时重放，未执行新的模型或机器人评测。具体检查与边界见 [0.8.0 验证记录](docs/validation-0.8.0.md)。

MIT license. 使用 DSH 的 `agentPresets`、会话工具钩子、MCP、`conversation.hero.options`、原生工作区及右侧栏插件扩展点；没有修改 DSH 聊天实现。HALOFLUX 的源码、美术和动效未包含在此仓库。

### Codex 连续会话（0.8.1）

本机 Codex 模型现在按 DSH 会话复用持久线程，工具结果回到原调用继续执行；DSH 仍负责工具与权限。自然语言创建 Goal 也会自动绑定工作区任务，已有未绑定 Goal 会在继续会话时补齐。

同一 GPT-6-Astra xhigh 四次工具调用的小样本对照：旧适配器 61.1 秒，新适配器 19.7 秒。此数据不是复杂项目的提速承诺，也不是与 Codex 桌面版的对照。[验证、恢复行为和限制](docs/codex-continuity.md)。

### 上下文与运行统计

选择 Codex 模型后，输入框下方显示输出吞吐、缓存命中率和 token，点击可看详情。上下文容量已接入 DSH，长对话沿用自动压缩，也可输入 `/compact`。项目约束与验收标准完整保留，历史证据按需读取。统计口径、旧记录限制和验证结果见 [上下文与统计说明](docs/codex-continuity.md)。
