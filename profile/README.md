<h1 align="center">Matrix Privacy</h1>

<p align="center">
  A client-proved privacy protocol and SDK stack for EVM assets.
</p>

<p align="center">
  <a href="#english">English</a> · <a href="#中文">中文</a>
</p>

<p align="center">
  <img alt="Status: Sepolia Alpha" src="https://img.shields.io/badge/status-Sepolia%20Alpha-2f855a">
  <img alt="Merkle depth: 16" src="https://img.shields.io/badge/Merkle%20depth-16-4a5568">
  <img alt="Proof system: Groth16" src="https://img.shields.io/badge/proofs-Groth16-805ad5">
  <img alt="Prover: client side" src="https://img.shields.io/badge/prover-client--side-2563eb">
</p>

<a id="english"></a>

## English

Matrix Privacy is an open EVM privacy protocol for shielding public assets, transferring them inside
a private UTXO pool, and unshielding them to an EVM address. Keys, note discovery, UTXO selection,
and Groth16 proving stay in the client.

The project is focused on a narrow, verifiable product boundary: **Ethereum Sepolia, ERC20 Shield,
private Transfer, and Unshield**. The current phase prioritizes reproducibility, resilient data
recovery, browser-ready integration boundaries, and evidence-backed security claims over adding a
large feature surface.

> Matrix is Alpha testnet software. It has not completed an independent security audit and is not
> a mainnet release. Do not use it with assets of real value.

### What Matrix provides

- **Client-side privacy:** mnemonics, spending/viewing keys, decrypted notes, selected UTXOs, and
  private proof inputs remain on the user's device.
- **EVM-native settlement:** Matrix contracts verify proofs and custody assets on the existing EVM
  chain; the protocol does not introduce another execution chain or trusted bridge.
- **Verifiable sync acceleration:** clients can consume checkpoints, leaves, and Merkle witnesses
  from public services, verify them against `Accumulator.rootHistory`, and recover through JSON-RPC.
- **Bound execution:** RelayAdapt binds complete action calldata, values, gas policy, and private
  transaction nullifiers to the proved transaction intent.
- **Composable SDKs:** shared models, Engine, Wallet, and Cookbook packages expose the protocol to
  wallet and application developers.

### Architecture

```text
Application / Desktop
  -> Wallet SDK          providers, sync, balances, durable operations
  -> Engine              keys, notes, Merkle verification, Groth16 prover
  -> Matrix contracts    proof verification, custody, state accumulation

Public data acceleration
  -> Merkle service      checkpoints, leaves, witnesses
  -> Subgraph            paginated event indexing
  -> JSON-RPC            authoritative recovery path
```

Public data services are accelerators, not trusted wallet backends. They do not receive a mnemonic,
viewing key, decrypted note, selected input, or circuit-private witness.

### Projects

| Project | Purpose | Alpha state |
| --- | --- | --- |
| [shared-models](https://github.com/matrix-privacy/shared-models) | Network configuration, shared types, RPC and gas utilities | Published |
| [engine](https://github.com/matrix-privacy/engine) | Keys, notes, encrypted storage, Merkle trees, proofs and contract wrappers | Published |
| [wallet](https://github.com/matrix-privacy/wallet) | App-facing wallet, balance, history, sync and transaction services | Published |
| [contracts](https://github.com/matrix-privacy/contracts) | Matrix, Accumulator, Verifier, TokenVault and RelayAdapt | Sepolia deployed |
| [circuits](https://github.com/matrix-privacy/circuits) | Groth16 circuits, vectors and artifact build inputs | Research artifacts |
| [subgraph](https://github.com/matrix-privacy/subgraph) | Optional `AccumulatorStateUpdate` indexing | Sepolia indexed |
| [frontend](https://github.com/matrix-privacy/frontend) | Desktop Alpha and browser integration surface | Developer build |
| [cookbook](https://github.com/matrix-privacy/cookbook) | Experimental action composition and RelayAdapt helpers | Published, disabled in UI |
| [waku-broadcaster](https://github.com/matrix-privacy/waku-broadcaster) | Experimental private transaction broadcaster | Deferred |
| [waku-broadcaster-client](https://github.com/matrix-privacy/waku-broadcaster-client) | Broadcaster discovery and encrypted submission clients | Deferred |
| [design](https://github.com/matrix-privacy/design) | Architecture, threat boundaries, operations, evidence and roadmap | Living record |

### Published SDK snapshot

```bash
npm install --save-exact \
  @matrix-privacy/shared-models@0.0.11 \
  @matrix-privacy/engine@0.0.6 \
  @matrix-privacy/wallet@0.0.17
```

`@matrix-privacy/cookbook@0.0.5` is also published. Cookbook, RelayAdapt-driven DeFi actions,
native-token flows, and Waku broadcaster submission are not enabled in the Alpha product surface.

### Sepolia Alpha snapshot

As of July 20, 2026:

- Direct ERC20 Shield, private Transfer, Unshield, mnemonic recovery, and forced JSON-RPC recovery
  have passed controlled Sepolia flows.
- Core SDK packages were published in dependency order and consumed by the Desktop build.
- The Sepolia subgraph indexed the controlled event range without indexing errors; JSON-RPC remains
  the authoritative fallback.
- A separate Rust Merkle service implementation passed tree-capacity, witness, replay, and PostgreSQL
  reorg tests; clients independently verify its roots and leaves.
- The repaired RelayAdapt is deployed on Sepolia, with SDK address alignment and adversarial
  action-substitution/replay coverage.
- Contract tests, SDK tests, circuit provenance work, and reproducible runbooks are recorded in the
  design repository.

### Current focus

- Reproduce circuit artifacts and verification keys from a clean build.
- Preserve an untrusted public-data boundary with deterministic RPC recovery.
- Complete packaged Desktop and real browser/CSP acceptance flows.
- Reduce existing lint, packaging, and release metadata debt.
- Require independent review before any mainnet or production claim.

Matrix does not claim a production anonymity set, completed decentralization, regulatory approval,
or stronger privacy than established deployed systems. Feature ideas remain research until code,
tests, deployment evidence, and an explicit supported product flow exist.

See the [Sepolia Alpha acceptance criteria](https://github.com/matrix-privacy/design/blob/main/10_alpha_acceptance.md),
[E2E runbook](https://github.com/matrix-privacy/design/blob/main/11_sepolia_e2e_runbook.md), and
[delivery status](https://github.com/matrix-privacy/design/blob/main/16_todo.md).

---

<a id="中文"></a>

## 中文

Matrix Privacy 是面向 EVM 资产的开源隐私协议与 SDK 技术栈。用户可以将公开资产 Shield
进入私密 UTXO 池，在池内进行私密 Transfer，再 Unshield 到 EVM 地址。密钥、note 发现、
UTXO 选择和 Groth16 proving 始终留在客户端。

项目当前聚焦于一个边界明确、可独立验证的产品范围：**Ethereum Sepolia、ERC20 Shield、
私密 Transfer 和 Unshield**。现阶段优先巩固可复现构建、可靠的数据恢复、浏览器集成边界和
有证据支撑的安全表述，而不是扩张大量未经验证的功能。

> Matrix 目前是 Alpha 测试网软件，尚未完成独立安全审计，也不是主网版本。请勿投入任何
> 具有实际价值的资产。

### Matrix 提供什么

- **客户端隐私边界：** mnemonic、spending/viewing key、已解密 note、选中的 UTXO 和私有
  proof inputs 保留在用户设备内。
- **EVM 原生结算：** Matrix 合约直接在现有 EVM 链上验证 proof 和托管资产，不引入新的执行链
  或受信桥。
- **可验证同步加速：** 客户端可以使用公共 checkpoint、leaf 和 Merkle witness，通过
  `Accumulator.rootHistory` 独立校验，并在服务异常时回退 JSON-RPC。
- **受约束的外部执行：** RelayAdapt 将完整 action calldata、value、gas policy 和私密交易
  nullifier 绑定到已证明的交易意图。
- **可组合 SDK：** shared models、Engine、Wallet 和 Cookbook 为钱包与应用开发者暴露协议能力。

### 系统架构

```text
应用 / Desktop
  -> Wallet SDK          provider、同步、余额、持久化操作状态
  -> Engine              密钥、note、Merkle 校验、Groth16 prover
  -> Matrix contracts    proof 验证、资产托管、状态累积

公共数据加速
  -> Merkle service      checkpoint、leaf、witness
  -> Subgraph            分页事件索引
  -> JSON-RPC            权威恢复路径
```

公共数据服务只是加速器，不是受信钱包后端。服务不得接收 mnemonic、viewing key、已解密 note、
选中的输入或 circuit-private witness。

### 项目组成

| 项目 | 职责 | Alpha 状态 |
| --- | --- | --- |
| [shared-models](https://github.com/matrix-privacy/shared-models) | 网络配置、公共类型、RPC 与 gas 工具 | 已发布 |
| [engine](https://github.com/matrix-privacy/engine) | 密钥、note、加密存储、Merkle tree、proof 与合约封装 | 已发布 |
| [wallet](https://github.com/matrix-privacy/wallet) | 面向应用的钱包、余额、历史、同步和交易服务 | 已发布 |
| [contracts](https://github.com/matrix-privacy/contracts) | Matrix、Accumulator、Verifier、TokenVault 与 RelayAdapt | 已部署 Sepolia |
| [circuits](https://github.com/matrix-privacy/circuits) | Groth16 circuits、测试向量和 artifact 构建输入 | 研究工件 |
| [subgraph](https://github.com/matrix-privacy/subgraph) | `AccumulatorStateUpdate` 可选索引 | 已索引 Sepolia |
| [frontend](https://github.com/matrix-privacy/frontend) | Desktop Alpha 与浏览器集成入口 | 开发构建可用 |
| [cookbook](https://github.com/matrix-privacy/cookbook) | 实验性 action 编排与 RelayAdapt helper | 已发布，UI 未启用 |
| [waku-broadcaster](https://github.com/matrix-privacy/waku-broadcaster) | 实验性私密交易 broadcaster | 延后 |
| [waku-broadcaster-client](https://github.com/matrix-privacy/waku-broadcaster-client) | Broadcaster 发现与加密提交客户端 | 延后 |
| [design](https://github.com/matrix-privacy/design) | 架构、威胁边界、运维、证据和路线记录 | 持续维护 |

### 已发布 SDK 快照

```bash
npm install --save-exact \
  @matrix-privacy/shared-models@0.0.11 \
  @matrix-privacy/engine@0.0.6 \
  @matrix-privacy/wallet@0.0.17
```

`@matrix-privacy/cookbook@0.0.5` 也已发布。Cookbook、RelayAdapt 驱动的 DeFi action、原生币
流程和 Waku broadcaster 提交尚未进入 Alpha 产品范围。

### Sepolia Alpha 快照

截至 2026 年 7 月 20 日：

- ERC20 直接 Shield、私密 Transfer、Unshield、mnemonic 恢复和强制 JSON-RPC 恢复已完成
  受控 Sepolia 流程。
- 核心 SDK 包已按依赖顺序发布，并由 Desktop 构建消费。
- Sepolia subgraph 已无错误地索引受控事件范围；JSON-RPC 继续作为权威回退路径。
- 独立 Rust Merkle service 实现已通过满容量 tree、witness、replay 和 PostgreSQL reorg 测试；
  客户端独立校验其 root 与 leaf。
- 修复后的 RelayAdapt 已部署到 Sepolia，SDK 地址已经对齐，并覆盖 action 替换和重放测试。
- 合约测试、SDK 测试、circuit provenance 和可复现 runbook 已记录在 design 仓库。

### 当前重点

- 从 clean build 重现 circuit artifacts 与 verification keys。
- 保持不受信的公共数据边界和确定性的 JSON-RPC 恢复。
- 完成打包 Desktop 与真实 browser/CSP 验收流程。
- 清理现有 lint、package 和 release metadata 技术债。
- 在任何主网或生产声明前完成独立审查。

Matrix 不宣称已经形成生产匿名集、完成去中心化、获得监管认可，或比已经部署的协议具有更强
隐私。任何功能构想在具备代码、测试、部署证据和明确支持的产品流程前都只属于研究范围。

详细状态见 [Sepolia Alpha 验收标准](https://github.com/matrix-privacy/design/blob/main/10_alpha_acceptance.md)、
[E2E Runbook](https://github.com/matrix-privacy/design/blob/main/11_sepolia_e2e_runbook.md) 和
[交付状态](https://github.com/matrix-privacy/design/blob/main/16_todo.md)。
