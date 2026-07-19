<h1 align="center">Matrix Privacy</h1>

<p align="center">
  Private assets and transfers for EVM, built on client-side Groth16 proofs and a Poseidon UTXO tree.
</p>

<p align="center">
  <a href="#english">English</a> · <a href="#中文">中文</a>
</p>

<p align="center">
  <img alt="Status: Sepolia Alpha" src="https://img.shields.io/badge/status-Sepolia%20Alpha-2f855a">
  <img alt="Merkle depth: 16" src="https://img.shields.io/badge/Merkle%20depth-16-4a5568">
  <img alt="Proof system: Groth16" src="https://img.shields.io/badge/proofs-Groth16-805ad5">
</p>

<a id="english"></a>

## English

Matrix Privacy is an open EVM privacy stack for shielding public assets, transferring them inside a
private UTXO pool, and unshielding them to an EVM address. Keys, note decryption, UTXO selection, and
Groth16 proving stay in the client.

The current product boundary is intentionally narrow: **Ethereum Sepolia, ERC20 Shield, private
Transfer, and Unshield, submitted directly by the user's public wallet**. It is testnet software,
not an audited or mainnet-ready release.

### How it fits together

```text
App
  -> Wallet SDK          providers, sync, balances, transactions
  -> Engine              keys, notes, Merkle tree, Groth16 prover
  -> Matrix contracts    proof verification, custody, state accumulation

Optional public data
  -> Subgraph            QuickSync acceleration
  -> JSON-RPC            authoritative event-scan fallback
```

| Project | Purpose |
| --- | --- |
| [shared-models](https://github.com/matrix-privacy/shared-models) | Network configuration, shared types, RPC and gas utilities |
| [engine](https://github.com/matrix-privacy/engine) | Keys, notes, encrypted storage, Merkle trees, proofs and contract wrappers |
| [wallet](https://github.com/matrix-privacy/wallet) | App-facing provider, wallet, balance, history and transaction services |
| [matrix-contracts](https://github.com/defi256-labs/matrix-contracts) | Matrix, Accumulator, Verifier, TokenVault and RelayAdapt contracts |
| [matrix-circuits](https://github.com/defi256-labs/matrix-circuits) | Groth16 circuits and artifact build inputs |
| [subgraph](https://github.com/matrix-privacy/subgraph) | Optional indexing for `AccumulatorStateUpdate` events |
| [cookbook](https://github.com/matrix-privacy/cookbook) | Future DeFi recipe and action composition |
| [frontend](https://github.com/matrix-privacy/frontend) | Desktop Alpha and future browser client work |
| [waku-broadcaster-client](https://github.com/matrix-privacy/waku-broadcaster-client) | Deferred Waku broadcaster discovery and submission client |
| [matrix-design](https://github.com/defi256-labs/matrix-design) | Architecture, operations, acceptance criteria and delivery status |

### SDK releases

```bash
npm install --save-exact \
  @matrix-privacy/shared-models@0.0.6 \
  @matrix-privacy/engine@0.0.3 \
  @matrix-privacy/wallet@0.0.7
```

`@matrix-privacy/cookbook@0.0.2` is also published, but Cookbook and RelayAdapt-driven DeFi actions
are not enabled in the Alpha.

### Privacy boundary

- Mnemonics, spending/viewing keys, decrypted notes, private circuit inputs, and transaction
  signatures must not leave the client.
- Groth16 proofs are generated in the desktop/browser client, not by a hosted prover.
- Public services may cache finalized events, checkpoints, leaves, roots, and Merkle witnesses;
  clients must verify witnesses and on-chain roots independently.
- Direct JSON-RPC recovery remains available when accelerated indexing is unavailable.

### Current status

- The direct Sepolia ERC20 Shield/Transfer/Unshield sequence and mnemonic recovery have been tested.
- Core packages are published in dependency order and their registry metadata has been verified.
- The Graph's Sepolia service is currently not syncing the deployed subgraph, so clients fall back
  to RPC scanning.
- RelayAdapt action binding is fixed and adversarially tested in source, but the repaired contract is
  not yet deployed. Relay, broadcaster, native-token, swap, liquidity, and farming paths stay disabled.
- Contract source verification, signed desktop distribution, browser release, independent audit, and
  mainnet deployment remain open work.

For the exact release gate and open tasks, see
[Sepolia Alpha acceptance](https://github.com/defi256-labs/matrix-design/blob/main/10_alpha_acceptance.md)
and the [delivery TODO](https://github.com/defi256-labs/matrix-design/blob/main/16_todo.md).

---

<a id="中文"></a>

## 中文

Matrix Privacy 是面向 EVM 的开源隐私技术栈：用户可以把公开资产 Shield 进入隐私
UTXO 池，在池内进行私密 Transfer，再 Unshield 到 EVM 地址。密钥、note 解密、UTXO
选择和 Groth16 证明始终留在客户端。

当前产品边界刻意保持精简：**Ethereum Sepolia、ERC20 Shield、私密 Transfer 和
Unshield，并由用户的公开钱包直接提交交易**。这是测试网软件，不代表已经审计或可用于主网。

### 系统组成

```text
应用
  -> Wallet SDK          provider、同步、余额、交易
  -> Engine              密钥、note、Merkle tree、Groth16 prover
  -> Matrix contracts    证明验证、资产托管、状态累积

可选公共数据
  -> Subgraph            QuickSync 加速
  -> JSON-RPC            权威事件扫描回退
```

| 项目 | 职责 |
| --- | --- |
| [shared-models](https://github.com/matrix-privacy/shared-models) | 网络配置、公共类型、RPC 与 gas 工具 |
| [engine](https://github.com/matrix-privacy/engine) | 密钥、note、加密存储、Merkle tree、证明和合约封装 |
| [wallet](https://github.com/matrix-privacy/wallet) | 面向应用的 provider、钱包、余额、历史和交易服务 |
| [matrix-contracts](https://github.com/defi256-labs/matrix-contracts) | Matrix、Accumulator、Verifier、TokenVault 与 RelayAdapt |
| [matrix-circuits](https://github.com/defi256-labs/matrix-circuits) | Groth16 电路和 artifact 构建输入 |
| [subgraph](https://github.com/matrix-privacy/subgraph) | `AccumulatorStateUpdate` 的可选索引 |
| [cookbook](https://github.com/matrix-privacy/cookbook) | 后续 DeFi recipe 与 action 编排 |
| [frontend](https://github.com/matrix-privacy/frontend) | Desktop Alpha 与浏览器客户端工作 |
| [waku-broadcaster-client](https://github.com/matrix-privacy/waku-broadcaster-client) | 延后的 Waku broadcaster 发现与提交客户端 |
| [matrix-design](https://github.com/defi256-labs/matrix-design) | 架构、运维、验收标准与推进状态 |

### SDK 版本

```bash
npm install --save-exact \
  @matrix-privacy/shared-models@0.0.6 \
  @matrix-privacy/engine@0.0.3 \
  @matrix-privacy/wallet@0.0.7
```

`@matrix-privacy/cookbook@0.0.2` 也已发布，但 Alpha 尚未启用 Cookbook 和依赖
RelayAdapt 的 DeFi action。

### 隐私边界

- mnemonic、spending/viewing key、已解密 note、私有电路输入和交易签名不得离开客户端。
- Groth16 proof 在桌面或浏览器客户端生成，不交给托管 prover。
- 公共服务可以缓存已确认事件、checkpoint、leaf、root 和 Merkle witness；客户端必须
  独立校验 witness 与链上 root。
- 加速索引不可用时，客户端保留 JSON-RPC 直接恢复路径。

### 当前状态

- Sepolia ERC20 的直接 Shield/Transfer/Unshield 与 mnemonic 恢复已经跑通。
- 核心 npm 包已按依赖顺序发布，并回读验证 registry 元数据。
- The Graph 的 Sepolia 服务当前没有同步已部署 subgraph，客户端因此回退到 RPC 扫描。
- RelayAdapt action 绑定已在源码修复并完成对抗测试，但新合约尚未部署；Relay、
  Broadcaster、原生币、Swap、流动性和 Farming 功能继续关闭。
- 合约公开验证、签名桌面发行、浏览器发行、独立审计和主网部署仍未完成。

准确的发布门槛和剩余任务见
[Sepolia Alpha 验收](https://github.com/defi256-labs/matrix-design/blob/main/10_alpha_acceptance.md)
与 [推进清单](https://github.com/defi256-labs/matrix-design/blob/main/16_todo.md)。
