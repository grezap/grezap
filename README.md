<!--
  This file renders as the landing page of https://github.com/grezap
  Update the "Currently building" section and pinned links as projects ship.
-->

<h1 align="center">Greg Zapantis</h1>
<p align="center">
  <b>Senior .NET &amp; Data Engineer</b> &nbsp;·&nbsp; Athens, Greece
</p>
<p align="center">
  I build production-grade .NET platforms — data pipelines, multi-tenant SaaS,
  ML systems, and native Windows apps — with the full HA infrastructure behind them.
</p>

<p align="center">
  <a href="https://github.com/grezap/portfolio-index">📇 Portfolio index</a> &nbsp;·&nbsp;
  <!-- <a href="https://gregzapantis.dev">🌐 Website</a> &nbsp;·&nbsp; -->
  <a href="https://www.linkedin.com/in/grigoris-zapantis-1a0638b/">💼 LinkedIn</a> &nbsp;·&nbsp;
  <a href="mailto:gzapas@gmail.com">✉️ Email</a>
</p>

---

## What I do

I design and ship end-to-end .NET / C# systems on production-grade infrastructure
— SQL Server Always On, Kafka, StarRocks, ClickHouse, Percona MySQL, Kubernetes, Docker
Swarm, HashiCorp Nomad/Consul/Vault — with full observability and documented runbooks.

The work is organised as the **NexusPlatform portfolio**: 13 application projects, 2
infrastructure repositories, and a 4-app native Windows suite, each built to senior-engineer
standards. See the [portfolio index](https://github.com/grezap/portfolio-index) for the
complete project grid with live status.

## Currently building

> **Phase 0 — Infrastructure foundation** *(in flight, ~75% complete)*
> The 66-VM lab is three-quarters built. Foundation tier (AD DS + HashiCorp Vault HA
> on Raft + PKI + LDAPS + Transit auto-unseal), orchestration tier (Docker Swarm +
> HashiCorp Nomad/Consul + Portainer CE, mTLS end-to-end), the **Kafka ecosystem
> tier** (two KRaft clusters on mutual TLS + Schema Registry + Connect + Debezium +
> ksqlDB + MirrorMaker 2 cross-cluster DR), and the **OLTP data tier** (6-node Redis
> 8.0 Cluster + 3-node MongoDB 8.0 Replica Set + **3-node Percona XtraDB Cluster +
> 2-node ProxySQL with VRRP-floated VIP**, all on mTLS via Vault PKI, all cold-
> rebuild-proven end-to-end via per-engine Packer templates + per-cluster Terraform
> states) are all live. The .NET 10 Native AOT operator CLI (`nexus-cli`) ships
> **all 5 of 5 master-plan verbs** as of `v0.5.0` — Phase 0.F closed; live RTOs
> all under master-plan budgets. **0.G.3.5 architectural refactor** (per-cluster
> Terraform state + per-engine Packer template — the rule born from 0.G.3's
> 16-transient stall) canonized; shrinks per-cluster iteration from ~30 min
> monolithic → ~5-10 min. **0.G.4 (Patroni PG 17 HA + etcd 3.5 DCS + HAProxy 3
> HA pair + VRRP VIP `.60`, 8 VMs) ✅ CLOSED 2026-05-19 end-to-end** — smoke
> gate 152/152 ALL GREEN; 18 transients surfaced + all root-caused +
> permanently fixed in source. **0.G.7 (SQL Server FCI + Always On AG on
> Windows Server 2025, 4 nodes) ✅ LIVE-RATIFIED 2026-05-22** — smoke
> `smoke-0.G.7.ps1` **ALL GREEN 56/56**. Full canon hybrid FCI+AG: a
> 2-node WSFC Failover Cluster Instance (`sqlfci` @ `.70.16`) sharing an
> iSCSI LUN from nexus-gateway (per ADR-0026) + 2 async AG replicas; AG
> Listener (`sql-ag-listener` @ `.70.17`) is the LB-tier HA primitive (per
> ADR-0025) — a remote domain client `sqlcmd -E -N` (Encrypt + strict
> cert-chain validate) reaches the primary across the listener; SQL service
> identity = `gmsa-sql-engine$` on the FCI (first real GMSA consumer; 0.D.5
> scaffolded the infrastructure); AG endpoint authentication cert-based per
> ADR-0027. First Windows-fleet data cluster + first iSCSI shim; 40+
> ratification transients all fixed in source. **OLTP tier SEALED — 5/5
> clusters cold-rebuild proven (redis + mongo + percona + patroni +
> sqlserver-fci-ag).** Next: complete the application phases (Vol01-Vol13
> — `dataflow-studio` first).

## Pinned projects

| Project | Status | What it is |
|---|---|---|
| [`portfolio-index`](https://github.com/grezap/portfolio-index) | 🟢 live | Skills matrix + living project grid — start here |
| [`nexus-infra-vmware`](https://github.com/grezap/nexus-infra-vmware) | 🟢 live | Tier-1 foundation — Vault HA + PKI + AD DS + dnsmasq gateway. Phase 0.D closed + 0.E/0.H Vault scaffolding |
| [`nexus-infra-swarm-nomad`](https://github.com/grezap/nexus-infra-swarm-nomad) | 🟢 `v0.2.0` | Tier-2 orchestration — 3+3 Docker Swarm + Nomad + Consul + Portainer CE, cold-rebuildable |
| [`nexus-infra-kafka`](https://github.com/grezap/nexus-infra-kafka) | 🟢 `v0.1.0` | Tier-3 Kafka ecosystem — 15 VMs, two mTLS KRaft clusters + Schema Registry + Connect/Debezium + ksqlDB + MM2 |
| [`nexus-infra-oltp`](https://github.com/grezap/nexus-infra-oltp) | 🟢 0.G.1-0.G.4 CLOSED + 0.G.7 SCAFFOLDED | Tier-4 OLTP data tier — **SEALED 2026-05-20 (5/5 clusters)**. Cold-rebuild proven on 4 of 5: 6-node Redis Cluster + 3-node MongoDB RS + 3-node Percona XtraDB Cluster + 2-node ProxySQL VIP `.50` + 3-node Patroni PG 17 HA + 3-node etcd DCS + 2-node HAProxy HA pair VIP `.60`. Scaffolded ready-to-ratify: 2-node SQL Server FCI sharing iSCSI LUN + 2 async AG replicas + AG Listener VIP `.70.17`. All mTLS via Vault PKI; first GMSA consumer (gmsa-sql-engine$). 26 of 30 VMs proven |
| [`nexus-infra-analytics`](https://github.com/grezap/nexus-infra-analytics) | 🟡 0.G.5 + 0.G.6 SCAFFOLDED | Tier-4 analytics data tier — ClickHouse + StarRocks (15 VMs). **ClickHouse**: 3 shards × 2 replicas + 3-node ClickHouse Keeper RAFT quorum (not ZooKeeper), `Distributed`/`ReplicatedMergeTree`, mTLS. **StarRocks**: 3 FE (BDB-JE quorum) + 3 BE, tablets `DISTRIBUTED BY HASH BUCKETS` × `replication_num=3`, MySQL `:9030`. Round-robin DNS front door, **no VIP** (ADR-0031). Per-engine templates + per-cluster TF states + 22 demos + ADRs 0028–0032. Live-ratify + `v0.1.0` pending |
| [`nexus-cli`](https://github.com/grezap/nexus-cli) | 🟢 `v0.5.0` | .NET 10 Native AOT CLI — **all 5 of 5 master-plan verbs live** (`cluster-status` · `infrastructure` · `failover-test` · `demo` · `kafka failover`). 22.75 MB single binary under the 25 MB gate |
| `portfolio` *(coming soon)* | ⚪ planned | Blazor Server portfolio website — the site that lists everything else |
| `dataflow-studio` *(coming soon)* | ⚪ planned | SQL Server CDC → Kafka → StarRocks + ClickHouse data platform |
| `nexus-platform` *(coming soon)* | ⚪ planned | Microservices reference — gRPC + Kafka + REST, sagas, K8s-ready |

## Top skills

```
●  .NET 10 / C# 13 · ASP.NET Core · Blazor Server · gRPC · Native AOT
●  Kafka (KRaft, Streams, ksqlDB, Debezium, MirrorMaker 2) · Schema Registry
●  SQL Server Always On · Percona MySQL PXC · PostgreSQL Patroni · MongoDB RS
●  StarRocks · ClickHouse · Redis Cluster · Kimball modelling · SCD2
●  ML.NET · ONNX · PyTorch→ONNX · Semantic Kernel · Ollama · HuggingFace
●  Docker Swarm · HashiCorp Nomad · Consul · Vault · Kubernetes · Terraform · Packer
●  OpenTelemetry · Prometheus · Grafana · Jaeger · Seq
●  Windows Forms · WPF · WinUI 3 · MAUI · MVVM · ReactiveUI
```

Full matrix with project mapping: [portfolio-index/PORTFOLIO.md](https://github.com/grezap/portfolio-index/blob/main/PORTFOLIO.md).

## Contact

- **Email:** `gzapas@gmail.com`
- **LinkedIn:** `https://www.linkedin.com/in/grigoris-zapantis-1a0638b/`
- **Upwork:** `https://www.upwork.com/freelancers/~01de3f0552684544ee`
- **CV:** `<link once hosted>`

<!--
  Stats badges (optional — uncomment once repos are public and active)

  ![GitHub Streak](https://streak-stats.demolab.com?user=grezap)
  ![Top Languages](https://github-readme-stats.vercel.app/api/top-langs/?username=grezap&layout=compact)
-->
