<h1 align="center">Matrix Privacy</h1>

<p align="center">
  Client-proved privacy and programmable execution accounts for EVM assets.
</p>

<p align="center">
  <a href="#english">English</a> · <a href="#中文">中文</a>
</p>

<p align="center">
  <img alt="Status: Sepolia Alpha" src="https://img.shields.io/badge/status-Sepolia%20Alpha-2f855a">
  <img alt="EIP-7702: implementation candidate" src="https://img.shields.io/badge/EIP--7702-implementation%20candidate-d69e2e">
  <img alt="Merkle depth: 16" src="https://img.shields.io/badge/Merkle%20depth-16-4a5568">
  <img alt="Proof system: Groth16" src="https://img.shields.io/badge/proofs-Groth16-805ad5">
  <img alt="Prover: client side" src="https://img.shields.io/badge/prover-client--side-2563eb">
</p>

<a id="english"></a>

## English

Matrix Privacy is an open EVM privacy protocol for shielding public assets, transferring them inside
a private UTXO pool, and unshielding them to an EVM address. Keys, note discovery, UTXO selection,
and Groth16 proving stay in the client.

The deployed Alpha supports **Ethereum Sepolia, ERC20 Shield, private Transfer, and Unshield**.
The new **MatrixAccount7702 implementation candidate** adds execution EOAs that call DeFi contracts
directly, encrypted HTTP submission, and ordinary account exits. Its contracts, SDK flows, and local
integration tests are implemented; audited public deployment and product release remain pending.

> Matrix is Alpha testnet software. It has not completed an independent security audit and is not
> a mainnet release. Do not use it with assets of real value.

### What Matrix provides

- **Client-side privacy:** mnemonics, spending/viewing keys, decrypted notes, UTXO selection, and
  private proof inputs remain on the user's device.
- **EVM-native settlement:** Matrix contracts verify proofs and custody assets on the existing EVM
  chain; the protocol does not introduce another execution chain or trusted bridge.
- **Verifiable sync acceleration:** clients can consume checkpoints, leaves, and Merkle witnesses
  from public services, verify them against `Accumulator.rootHistory`, and recover through JSON-RPC.
- **Direct DeFi execution:** an EIP-7702 execution EOA receives assets from the private pool, calls
  target contracts, and can shield supported outputs again within one atomic action. The account
  signature and proof bind the selected nullifiers and action; DeFi sees the execution EOA as caller.
- **HTTP submission:** clients choose direct submission or a configured HTTPS operator. Signed fee
  offers, encrypted requests, durable journals, and idempotent retries support sponsored transactions.
- **Account management:** signed Account calls close positions, revoke approvals, and sweep tracked assets.
  The user retains the execution EOA key and can manage its delegation.
- **Core SDKs:** shared models, Engine, and Wallet expose account allocation, proof preparation,
  arbitrary calls, submission, and closure without requiring protocol-specific recipes.

### Architecture

```text
Web / Desktop
  -> shared-models       public configuration and wire types
  -> Wallet SDK          accounts, sync, balances, encrypted operation journals
       -> Engine         keys, notes, proofs, authorization and signatures

Signed execution
  -> Direct sponsor / HTTP broadcaster
  -> Execution EOA delegated to MatrixAccount7702
       -> Matrix pool    proof verification and private funding
       -> DeFi targets   signed calls from the execution EOA
       -> Matrix pool    optional shield of supported outputs

Public data acceleration
  -> Merkle service      checkpoints, leaves, witnesses
  -> Subgraph            paginated event indexing
  -> JSON-RPC            authoritative recovery path
```

Public data services provide verifiable public chain data; wallet keys, note decryption, and proving
remain local. Account execution requires only the pinned Account implementation and privacy pool configuration.

EIP-7702 requires chain and RPC support. Arbitrary calls do not guarantee compatibility with every
protocol: caller-code checks, signatures, and exit paths require validation. dApp signing uses ordinary
ECDSA; flows that require an ERC-1271 callback are unsupported. DeFi positions and
execution-account activity are public; re-shielding does not erase that history. Strategy-account reuse
links its activity. Transport encryption protects submission contents in transit, not against the
chosen operator or on-chain observers.

Account batches revert when a call fails. Wallet checks tracked assets and token approvals after confirmation;
remaining assets prevent closure and archive. The Account adds no generic state assertions. Protocol calldata
controls trade limits, and quoted private outputs use the pool fees available at quote time.

### Projects

| Project | Purpose | State |
| --- | --- | --- |
| [shared-models](https://github.com/matrix-privacy/shared-models) | Network configuration, account types and encrypted HTTP wire protocol | Alpha package published; 7702 source candidate |
| [engine](https://github.com/matrix-privacy/engine) | Keys, notes, Merkle verification, proofs, 7702 signing and estimation | Alpha package published; 7702 source candidate |
| [wallet](https://github.com/matrix-privacy/wallet) | Account lifecycle, transaction journals, HTTP submission and closure | Alpha package published; 7702 source candidate |
| [contracts](https://github.com/matrix-privacy/contracts) | Privacy pool and the Account implementation | Pool deployed on Sepolia; 7702 local candidate |
| [circuits](https://github.com/matrix-privacy/circuits) | Groth16 circuits, vectors and artifact build inputs | Research artifacts |
| [tree-service](https://github.com/matrix-privacy/tree-service) | Rust service for public checkpoints, events, leaves and witnesses | Makefile and Docker build verified locally |
| [subgraph](https://github.com/matrix-privacy/subgraph) | Optional `AccumulatorStateUpdate` indexing | Sepolia indexed |
| [frontend](https://github.com/matrix-privacy/frontend) | Web/Desktop account flows and Desktop injected dApp connector | Local integration and Web Docker build verified |
| [http-broadcaster](https://github.com/matrix-privacy/http-broadcaster) | HTTPS fee offers, encrypted submission and PostgreSQL custody records | Source published; local tests and Docker build verified |
| [design](https://github.com/matrix-privacy/design) | Architecture, threat boundaries, operations, evidence and roadmap | Living record |

### Published ERC20 Alpha SDK snapshot

Current source builds use Node.js 24 and pnpm 12.3.4. Contracts use Solidity 0.8.36 and OpenZeppelin 5.7.0
for fresh deployments;
see the [toolchain upgrade](https://github.com/matrix-privacy/design/blob/main/26_node24_pnpm_solidity_upgrade.md).

```bash
pnpm add --save-exact \
  @matrix-privacy/shared-models@0.0.11 \
  @matrix-privacy/engine@0.0.6 \
  @matrix-privacy/wallet@0.0.17
```

These versions describe the existing ERC20 Alpha packages. They do not contain the newly committed
7702 implementation. That implementation uses coordinated workspace sources and locally verified
candidate packages; npm publication and audited public-chain release pins remain separate steps.

### Deployment and implementation evidence

Deployed ERC20 Alpha baseline, July 20, 2026:

- Direct ERC20 Shield, private Transfer, Unshield, mnemonic recovery, and forced JSON-RPC recovery
  have passed controlled Sepolia flows.
- Core SDK packages were published in dependency order and consumed by the Desktop build.
- The Sepolia subgraph indexed the controlled event range without indexing errors; JSON-RPC remains
  the authoritative fallback.
- A separate Rust Merkle service implementation passed tree-capacity, witness, replay, and PostgreSQL
  reorg tests; clients independently verify its roots and leaves.
- Contract tests, SDK tests, circuit provenance work, and reproducible runbooks are recorded in the
  design repository.

7702 implementation checkpoint, September 9, 2026:

- The consolidated local verification passed 18/18 steps, covering contracts, SDKs, real Groth16
  direct/HTTP execution, explicit Account exits, browser/Worker flows, and clean candidate installation.
- The archive-RPC fork suite covers Uniswap/Aave interaction and ordinary Account exits.
  Arbitrary calldata support does not certify every protocol or enumerate every position.
- Rust, HTTP broadcaster, and Web frontend images built and passed container checks on Linux/arm64.
  Makefiles and Railway port/health configuration are included; no cloud deployment is claimed.
- Default 7702 builds contain no enabled public deployment pins. The Web container does not include
  the Electron-only injected dApp browser.

### Current focus

- Reproduce circuit artifacts and verification keys from a clean build.
- Preserve an untrusted public-data boundary with deterministic RPC recovery.
- Complete packaged Desktop, browser/Worker bootstrap, and public product acceptance flows.
- Finish account, cryptography, and application security audits before enabling public 7702 deployments.
- Validate operator retention, key rotation, and longer pending/replacement/reorg runs.
- Publish coordinated SDK versions, reviewed deployment pins, and operator configuration.
- Reduce existing lint, packaging, and release metadata debt.
- Require independent review before any mainnet or production claim.

Matrix does not claim a production anonymity set, completed decentralization, regulatory approval,
or stronger privacy than established deployed systems. Feature ideas remain research until code,
tests, deployment evidence, and an explicit supported product flow exist.

See the [7702 implementation plan](https://github.com/matrix-privacy/design/blob/main/22_matrix_account_7702_implementation_plan.md),
[implementation record](https://github.com/matrix-privacy/design/blob/main/23_7702_implementation_progress.md),
[release gates](https://github.com/matrix-privacy/design/blob/main/24_7702_release_candidate_and_gates.md), and
[Docker/Railway guide](https://github.com/matrix-privacy/design/blob/main/25_build_docker_railway.md).
The deployed Alpha has separate [acceptance criteria](https://github.com/matrix-privacy/design/blob/main/10_alpha_acceptance.md)
and an [E2E runbook](https://github.com/matrix-privacy/design/blob/main/11_sepolia_e2e_runbook.md).

---

<a id="中文"></a>

## 中文

Matrix Privacy 是面向 EVM 资产的开源隐私协议与 SDK 技术栈。用户可以将公开资产 Shield
进入私密 UTXO 池，在池内进行私密 Transfer，再 Unshield 到 EVM 地址。密钥、note 发现、
UTXO 选择和 Groth16 proving 始终留在客户端。

已部署 Alpha 支持 **Ethereum Sepolia、ERC20 Shield、私密 Transfer 和 Unshield**。
新的 **MatrixAccount7702 候选实现**增加了直接调用 DeFi 的执行 EOA、加密 HTTP 提交与普通退出能力。合约、SDK 流程与本地集成测试已实现，经过审计的公共部署和产品发布仍待完成。

> Matrix 目前是 Alpha 测试网软件，尚未完成独立安全审计，也不是主网版本。请勿投入任何
> 具有实际价值的资产。

### Matrix 提供什么

- **客户端隐私边界：** mnemonic、spending/viewing key、已解密 note、UTXO 选择和私有
  proof inputs 保留在用户设备内。
- **EVM 原生结算：** Matrix 合约直接在现有 EVM 链上验证 proof 和托管资产，不引入新的执行链
  或受信桥。
- **可验证同步加速：** 客户端可以使用公共 checkpoint、leaf 和 Merkle witness，通过
  `Accumulator.rootHistory` 独立校验，并在服务异常时回退 JSON-RPC。
- **直接 DeFi 执行：** EIP-7702 执行 EOA 从隐私池接收资产，调用目标合约，并可在同一原子 action
  中把支持的输出重新 Shield。账户签名和 proof 绑定选中的 nullifier 与 action；DeFi 看到的 caller
  是执行 EOA。
- **HTTP 提交：** 客户端选择直接提交或已配置的 HTTPS operator，通过签名报价、加密请求、持久化
  journal 和幂等重试支持代付 gas。
- **账户管理：** 已签名 Account 调用退出头寸、撤销批准和清空已跟踪资产。用户保留执行 EOA
  私钥，并可管理其 delegation。
- **核心 SDK：** shared models、Engine、Wallet 提供账户分配、proof 准备、任意合约调用、提交和
  关闭能力，无需为每个协议新增 recipe。

### 系统架构

```text
Web / Desktop
  -> shared-models       公开配置与传输类型
  -> Wallet SDK          账户、同步、余额、加密操作 journal
       -> Engine         密钥、note、proof、authorization 与签名

已签名执行
  -> 直接 sponsor / HTTP broadcaster
  -> 委托给 MatrixAccount7702 的执行 EOA
       -> Matrix pool    proof 校验与隐私资金转出
       -> DeFi targets   由执行 EOA 发起已签名调用
       -> Matrix pool    可选：将支持的输出重新 Shield

公共数据加速
  -> Merkle service      checkpoint、leaf、witness
  -> Subgraph            分页事件索引
  -> JSON-RPC            权威恢复路径
```

公共数据服务提供可验证的公开链上数据；钱包密钥、note 解密和 proving 留在本地。执行路径只需要
已固定的 Account 实现与隐私池配置。

EIP-7702 需要底层链与 RPC 支持。任意调用能力不保证每个协议都兼容：caller code 检查、签名和
退出路径都需要验证。dApp 使用普通 ECDSA 签名，暂不支持强制 ERC-1271 回调的流程。
DeFi 头寸与执行账户活动是公开的，重新 Shield 不会抹除这些历史，复用
strategy account 也会关联其活动。传输加密保护传输中的提交内容，不对选定 operator 或链上
观察者隐藏执行内容。

Account 批量调用在任一调用失败时回滚。Wallet 在确认后检查已跟踪资产与 token 授权，残留资产会
阻止关闭和归档；Account 不附加通用状态断言。成交限制由协议 calldata 控制，私有输出报价使用
报价时的池费率。

### 项目组成

| 项目 | 职责 | 状态 |
| --- | --- | --- |
| [shared-models](https://github.com/matrix-privacy/shared-models) | 网络配置、账户类型、加密 HTTP 协议 | Alpha 包已发布；7702 源码候选版 |
| [engine](https://github.com/matrix-privacy/engine) | 密钥、note、Merkle 校验、proof、7702 签名与估算 | Alpha 包已发布；7702 源码候选版 |
| [wallet](https://github.com/matrix-privacy/wallet) | 账户生命周期、交易 journal、HTTP 提交与关闭 | Alpha 包已发布；7702 源码候选版 |
| [contracts](https://github.com/matrix-privacy/contracts) | 隐私池与 Account 实现 | 隐私池已部署 Sepolia；7702 本地候选版 |
| [circuits](https://github.com/matrix-privacy/circuits) | Groth16 circuits、测试向量和 artifact 构建输入 | 研究工件 |
| [tree-service](https://github.com/matrix-privacy/tree-service) | Rust 公共 checkpoint、event、leaf、witness 服务 | Makefile 与 Docker 构建已本地验证 |
| [subgraph](https://github.com/matrix-privacy/subgraph) | `AccumulatorStateUpdate` 可选索引 | 已索引 Sepolia |
| [frontend](https://github.com/matrix-privacy/frontend) | Web/Desktop 账户流程与 Desktop 注入式 dApp 连接器 | 本地集成与 Web Docker 构建已验证 |
| [http-broadcaster](https://github.com/matrix-privacy/http-broadcaster) | HTTPS 报价、加密提交与 PostgreSQL 请求托管 | 源码已提交 GitHub；本地测试与 Docker 构建已验证 |
| [design](https://github.com/matrix-privacy/design) | 架构、威胁边界、运维、证据和路线记录 | 持续维护 |

### 已发布 ERC20 Alpha SDK 快照

当前源码使用 Node.js 24 与 pnpm 12.3.4 构建，合约采用 Solidity 0.8.36 与 OpenZeppelin 5.7.0
进行全新部署，详见
[工具链升级记录](https://github.com/matrix-privacy/design/blob/main/26_node24_pnpm_solidity_upgrade.md)。

```bash
pnpm add --save-exact \
  @matrix-privacy/shared-models@0.0.11 \
  @matrix-privacy/engine@0.0.6 \
  @matrix-privacy/wallet@0.0.17
```

这些版本是既有 ERC20 Alpha 包的快照，不包含刚提交的 7702 实现。7702 使用协调后的工作区
源码和已在本地验证的候选包；npm 发布与经过审计的公共链 release pins 仍是后续独立步骤。

### 部署与实现证据

已部署 ERC20 Alpha 基线，2026 年 7 月 20 日：

- ERC20 直接 Shield、私密 Transfer、Unshield、mnemonic 恢复和强制 JSON-RPC 恢复已完成
  受控 Sepolia 流程。
- 核心 SDK 包已按依赖顺序发布，并由 Desktop 构建消费。
- Sepolia subgraph 已无错误地索引受控事件范围；JSON-RPC 继续作为权威回退路径。
- 独立 Rust Merkle service 实现已通过满容量 tree、witness、replay 和 PostgreSQL reorg 测试；
  客户端独立校验其 root 与 leaf。
- 合约测试、SDK 测试、circuit provenance 和可复现 runbook 已记录在 design 仓库。

7702 实现检查点，2026 年 9 月 9 日：

- 综合本地验证 18/18 步骤通过，覆盖合约、SDK、真实 Groth16 直接/HTTP 执行、显式 Account 退出、
  browser/Worker 流程和候选包干净安装。
- archive RPC fork 测试覆盖 Uniswap/Aave 交互和普通 Account 退出；任意 calldata 能力不等于
  自动认证每个协议或发现所有头寸。
- Rust、HTTP broadcaster 与 Web 前端镜像已在 Linux/arm64 构建并通过容器检查，包含 Makefile
  和 Railway 端口/健康检查配置；尚未实际云端部署。
- 默认 7702 构建不包含已启用的公共 deployment pins。Web 容器不包含 Electron 专用的内嵌
  dApp 浏览器。

### 当前重点

- 从 clean build 重现 circuit artifacts 与 verification keys。
- 保持不受信的公共数据边界和确定性的 JSON-RPC 恢复。
- 完成打包 Desktop、browser/Worker 启动和公共产品验收流程。
- 启用公共 7702 部署前完成账户、密码学与应用完整安全审计。
- 验证 operator 数据保留、密钥轮换和更长时间的 pending/replacement/reorg 场景。
- 发布协调后的 SDK 版本、已审核 deployment pins 和 operator 配置。
- 清理现有 lint、package 和 release metadata 技术债。
- 在任何主网或生产声明前完成独立审查。

Matrix 不宣称已经形成生产匿名集、完成去中心化、获得监管认可，或比已经部署的协议具有更强
隐私。任何功能构想在具备代码、测试、部署证据和明确支持的产品流程前都只属于研究范围。

详细状态见 [7702 实现计划](https://github.com/matrix-privacy/design/blob/main/22_matrix_account_7702_implementation_plan.md)、
[实现记录](https://github.com/matrix-privacy/design/blob/main/23_7702_implementation_progress.md)、
[发布门槛](https://github.com/matrix-privacy/design/blob/main/24_7702_release_candidate_and_gates.md) 和
[Docker/Railway 部署说明](https://github.com/matrix-privacy/design/blob/main/25_build_docker_railway.md)。
已部署 Alpha 的验证见 [验收标准](https://github.com/matrix-privacy/design/blob/main/10_alpha_acceptance.md)
和 [E2E Runbook](https://github.com/matrix-privacy/design/blob/main/11_sepolia_e2e_runbook.md)。
