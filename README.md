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

The work is organised as the **NexusPlatform portfolio**: 14 application projects, 7 built
infrastructure repositories, and a 4-app native Windows suite, each built to senior-engineer
standards. See the [portfolio index](https://github.com/grezap/portfolio-index) for the
complete project grid with live status.

## Currently building

> **Phase 0 — Infrastructure foundation** *(in flight, ~85% complete)*
> The 140-VM lab is mostly built. Foundation tier (AD DS + HashiCorp Vault HA
> on Raft + PKI + LDAPS + Transit auto-unseal), orchestration tier (Docker Swarm +
> HashiCorp Nomad/Consul + Portainer CE, mTLS end-to-end), the **Kafka ecosystem
> tier** (two KRaft clusters on mutual TLS + Schema Registry + Connect + Debezium +
> ksqlDB + MirrorMaker 2 cross-cluster DR), and the **OLTP data tier** (6-node Redis
> 8.0 Cluster + 3-node MongoDB 8.0 Replica Set + **3-node Percona XtraDB Cluster +
> 2-node ProxySQL with VRRP-floated VIP**, all on mTLS via Vault PKI, all cold-
> rebuild-proven end-to-end via per-engine Packer templates + per-cluster Terraform
> states) are all live. The .NET 10 Native AOT operator CLI (`nexus-cli`) ships
> **all 5 of 5 master-plan verbs** as of `v0.5.0` — Phase 0.F closed; live RTOs
> all under master-plan budgets. **`v0.6.1` (2026-06-05) — the data-tier
> adapter expansion: 2 of 11 adapters live-verified end-to-end. Redis (v0.6.0,
> mTLS-only) + Mongo (v0.6.1, the first password-auth adapter, on `nexus-rs`)
> ship all data-tier verbs green — status/health/topology/failover (Mongo
> `rs.stepDown` RTO≈2.8s)/cert-rotate/acl/backup/scale-out/chaos. Mongo adds the
> Vault-KV operator-credential model (the `nexus-cluster-admin` password lives
> only in Vault KV, fetched at runtime via an optional `INexusVaultClient`); 23.9
> MB under the ≤30 MB gate. 8 adapters follow.** **0.G.3.5 architectural refactor** (per-cluster
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
> sqlserver-fci-ag).** The **analytics tier** (ClickHouse 3×2 + 3-node Keeper,
> StarRocks 3 FE + 3 BE — `nexus-infra-analytics` `v0.1.0`) and the **lakehouse +
> registry tier** are live + cold-rebuild-proven: `nexus-infra-lakehouse` (MinIO
> distributed-EC object store + Iceberg/Nessie REST on a dedicated PG HA pair +
> Apache Spark HA with a ZooKeeper-elected master pair), and `nexus-infra-registry`
> — a **highly-available Harbor** container registry (2 stateless app nodes +
> dedicated PG/Redis HA datastore + VRRP VIP; **image blobs in MinIO S3**; Trivy
> scanning + cosign signing; **Vault OIDC SSO** → AD; ADR-0036). **Phase 0.L.5
> StarRocks shared-data SEALED + analytics `v0.2.0` released (2026-05-26)** — second
> StarRocks cluster live + cold-rebuild-proven in `nexus-infra-analytics`
> (3 FE BDB-JE quorum + 2 stateless Compute Nodes, `run_mode=shared_data`,
> internal cloud-native tables in a MinIO storage volume `s3://starrocks/`
> with a dedicated `nexus-starrocks-app` service account + scoped
> `starrocks-tenant` policy; ADR-0037 amends ADR-0030; `smoke-0.L.5.ps1`
> **69/69** GREEN with CN-loss chaos default-on, proving the shared-data HA
> property — any CN serves any query from shared MinIO storage). Fleet 105 VMs
> + 5 VIPs built/cold-rebuild-proven. **0.L.6 close-out COMPLETE 2026-05-26** —
> 3 tags shipped (`nexus-infra-lakehouse v0.1.0` · `nexus-infra-registry v0.1.0`
> · `nexus-infra-analytics v0.2.0`). **Phase 0.I observability COMPLETE
> 2026-05-28 (ADR-0038) -- `nexus-infra-observability v0.1.0` tagged.** All
> 7 sub-phases SEALED (live-ratified + cold-rebuild-proven). **Grafana LGTM
> stack** fully up: Prom HA + Alertmanager mesh (0.I.1) · Loki SSD on
> MinIO (0.I.2) · Tempo scalable on MinIO (0.I.3) · **Grafana HA + Grafana PG
> HA + 2 VRRP VIPs `grafana.nexus.lab .184` + `grafana-db.nexus.lab .185` (0.I.4)** ·
> **OTel Collector pair behind RR DNS `otel.nexus.lab` (0.I.5)** · **Vector log
> shipper added to shared `nexus_observability` ansible role + live retrofit
> via `scripts/fleet-vector-rollout.ps1`; every fleet Linux node ships
> journald + /var/log/* to Loki HA tagged `fleet=nexusplatform` (0.I.6)** ·
> 3-layer canon sweep + tag (0.I.7). 39 transients permanently fixed in source.
> 14 obs VMs + 2 VIPs (fleet 107 + 5 VIPs cold-rebuild-proven).
> **Phase 0.N MongoDB sharded cluster SEALED 2026-05-30 (ADR-0040) —
> live-ratified + cold-rebuild-proven (`smoke-0.N.ps1` 50/50 GREEN both
> times).** 11 VMs: 3 config-server RS + 2 shard RSes (3 each) + 2 stateless
> `mongos` routers — the **document-store sharding** showcase (chunks span
> both shards; `mongos` routes keyed lookups) alongside the 0.G.2 replica-set
> (replication) showcase. keyFile auth; 10 transients fixed in source. OLTP
> tier now **6/6 cold-rebuild-proven**. (Prior: **Phase 0.M foundation HA
> COMPLETE 2026-05-28, ADR-0039** — 2nd AD DC `dc-nexus-2` closes the last
> single-DC SPOF.)
> **Phase 0.O `nexus-infra-vitess` SEALED 2026-06-03 (ADR-0041) —
> live-ratified + cold-rebuild-proven (`smoke-0.O.ps1` 71/71 GREEN both
> times); tagged `v0.1.0`.** 12 VMs on tier `07-vitess`: 3-node etcd 3.5.16
> topo + a control node (vtctld + VTOrc) + 2 stateless vtgate routers (RR DNS
> `vtgate.nexus.lab`, MySQL `:15306`) + 2 shards × 3 tablets (vttablet +
> Percona Server 8.4 LTS). Keyspace `commerce`, 2 shards (`-80`/`80-`) on a
> **hash vindex** — one vtgate insert splits 100 rows 53/47 across both
> shards; **VTOrc auto-reparents** a shard (~15s) on PRIMARY kill. Vitess
> v24.0.1; full Vault-PKI mTLS on every gRPC channel + the mysqld wire + the
> vtgate MySQL listener. The **relational (MySQL) sharding** showcase,
> distinct from PXC/Galera *replication* (0.G.3).
> **Phase 0.P `nexus-infra-citus` SEALED 2026-06-03 (ADR-0042) —
> live-ratified + cold-rebuild-proven (`smoke-0.P.ps1` 69/69 GREEN both times
> incl worker Patroni failover); tagged `v0.1.0`.** 9 VMs + 3 VRRP VIPs on tier
> `08-citus`: 3-node etcd 3.5 DCS + a coordinator Patroni pair (VIP
> `coord.citus.nexus.lab`) holding the `pg_dist_*` catalog + 2 worker Patroni
> pairs (VIPs `worker{1,2}.citus.nexus.lab`) holding the shards. **PostgreSQL 17
> + Citus 14.x**; a distributed `events` table (32 shards) spreads across both
> worker groups, with reference + colocated tables. Every node-group is a
> 2-node Patroni cluster over the shared etcd DCS, fronted by a **keepalived
> VRRP VIP that follows the Patroni leader** (workers registered in
> `pg_dist_node` by VIP, so a failover needs no metadata rewrite). Full
> Vault-PKI mTLS on the PG wire + etcd + Patroni REST. The **relational
> (PostgreSQL) sharding** showcase with full Patroni HA, distinct from Patroni
> *streaming replication* (0.G.4). Fleet **140 VMs + 8 VIPs**
> cold-rebuild-proven. After 0.P: application phases (`dataflow-studio` first).

## Pinned projects

| Project | Status | What it is |
|---|---|---|
| [`portfolio-index`](https://github.com/grezap/portfolio-index) | 🟢 live | Skills matrix + living project grid — start here |
| [`nexus-infra-vmware`](https://github.com/grezap/nexus-infra-vmware) | 🟢 live | Tier-1 foundation — Vault HA + PKI + AD DS + dnsmasq gateway. Phase 0.D closed + 0.E/0.H Vault scaffolding |
| [`nexus-infra-swarm-nomad`](https://github.com/grezap/nexus-infra-swarm-nomad) | 🟢 `v0.2.0` | Tier-2 orchestration — 3+3 Docker Swarm + Nomad + Consul + Portainer CE, cold-rebuildable |
| [`nexus-infra-kafka`](https://github.com/grezap/nexus-infra-kafka) | 🟢 `v0.1.0` | Tier-3 Kafka ecosystem — 15 VMs, two mTLS KRaft clusters + Schema Registry + Connect/Debezium + ksqlDB + MM2 |
| [`nexus-infra-oltp`](https://github.com/grezap/nexus-infra-oltp) | 🟢 SEALED 6/6 | Tier-4 OLTP data tier — **6 clusters cold-rebuild proven (37 VMs)**: 6-node Redis Cluster + 3-node MongoDB RS + 3-node Percona XtraDB Cluster + 2-node ProxySQL VIP `.50` + 3-node Patroni PG 17 HA + 3-node etcd DCS + 2-node HAProxy HA pair VIP `.60` + **2-node SQL Server FCI (shared iSCSI LUN) + 2 async AG replicas + AG Listener VIP `.17`** (0.G.7; `smoke-0.G.7.ps1` 56/56) + **11-node MongoDB sharded cluster** (3 config RS + 2×3 shard RSes + 2 `mongos` routers; 0.N live-ratified + cold-rebuild-proven 2026-05-30, `smoke-0.N.ps1` 50/50). All mTLS via Vault PKI (sharded-Mongo keyFile auth, mTLS in 0.N.1); first GMSA consumer (`gmsa-sql-engine$`); per-engine templates + per-cluster states. |
| [`nexus-infra-analytics`](https://github.com/grezap/nexus-infra-analytics) | 🟢 `v0.2.0` | Tier-4 analytics data tier — ClickHouse + StarRocks **shared-nothing** + StarRocks **shared-data** (20 VMs, **0.G.5 + 0.G.6 + 0.L.5 ALL SEALED**). **ClickHouse**: 3 shards × 2 replicas + 3-node ClickHouse Keeper RAFT quorum (not ZooKeeper). **StarRocks shared-nothing**: 3 FE (BDB-JE quorum) + 3 BE, tablets sharded × `replication_num=3`. **StarRocks shared-data**: 3 FE (BDB-JE) + 2 stateless Compute Nodes, internal cloud-native tables in a MinIO storage volume (`s3://starrocks/` with a scoped `starrocks-tenant` policy). Round-robin DNS front door, **no VIP** (ADR-0031). All 3 clusters live-ratified + cold-rebuild-proven (129/129 + 73/73 + 69/69); chaos default-on on the sd smoke proves any-CN-serves-any-query from shared MinIO. ADRs 0028–0032 + 0037 |
| [`nexus-infra-lakehouse`](https://github.com/grezap/nexus-infra-lakehouse) | 🟢 `v0.1.0` | Lakehouse tier (`08-spark`, 16 VMs) — **MinIO** distributed erasure-coded object store (round-robin DNS, no VIP) + **Apache Iceberg / Project Nessie** REST catalog ×2 on a dedicated **PostgreSQL HA pair** (keepalived VRRP VIP) + **Apache Spark** standalone **HA** (2 ZooKeeper-elected masters + 3 workers + 3-node ZooKeeper). End-to-end Spark→Iceberg→MinIO write path; all mTLS via Vault PKI; cold-rebuild-proven (ADRs 0033-0035) |
| [`nexus-infra-registry`](https://github.com/grezap/nexus-infra-registry) | 🟢 `v0.1.0` | Registry tier (`09-platform`, 4 VMs + VIP) — **highly-available Harbor**: 2 stateless app nodes (round-robin DNS) + dedicated PostgreSQL/Redis master-replica HA datastore (VRRP VIP); **image blobs in MinIO S3**; Trivy scanning + cosign signing; **Vault OIDC SSO** → AD. Live-ratified + cold-rebuild-proven (41/41; 7 transients fixed in source; ADR-0036) |
| [`nexus-infra-vitess`](https://github.com/grezap/nexus-infra-vitess) | 🟢 `v0.1.0` | Vitess-sharded MySQL tier (`07-vitess`, 12 VMs) — the **relational (MySQL) sharding** showcase (distinct from PXC/Galera *replication*). 3-node etcd topo + vtctld/VTOrc control + 2 vtgate routers (RR DNS, MySQL `:15306`) + 2 shards × 3 Percona Server 8.4 tablets; keyspace `commerce`, hash vindex; Vitess v24.0.1; full Vault-PKI mTLS. Live-ratified + cold-rebuild-proven (`smoke-0.O.ps1` 71/71; VTOrc auto-reparent + 53/47 shard split proven; ADR-0041) |
| [`nexus-infra-citus`](https://github.com/grezap/nexus-infra-citus) | 🟢 `v0.1.0` | Citus-sharded PostgreSQL tier (`08-citus`, 9 VMs + 3 VRRP VIPs) — the **relational (PostgreSQL) sharding** showcase with **full Patroni HA** (distinct from Patroni *streaming replication*). 3-node etcd DCS + coordinator Patroni pair + 2 worker Patroni pairs; PostgreSQL 17 + Citus 14.x; distributed (32-shard) + reference + colocated tables; every node-group fronted by a keepalived VRRP VIP that follows the Patroni leader (workers in `pg_dist_node` by VIP); full Vault-PKI mTLS. Live-ratified + cold-rebuild-proven (`smoke-0.P.ps1` 69/69 incl worker Patroni failover; ADR-0042) |
| [`nexus-cli`](https://github.com/grezap/nexus-cli) | 🟢 `v0.6.1` | .NET 10 Native AOT CLI — Phase 0.F verbs (`cluster-status` · `infrastructure` · `failover-test` · `demo` · `kafka failover`) **plus the Phase 0.G `IClusterAdapter` data-tier expansion**: the **Redis adapter** (v0.6.0, mTLS-only) + the **Mongo adapter** (v0.6.1, the first password-auth adapter — Vault-KV operator-credential model via an optional `INexusVaultClient`) both ship live-verified (all verbs: status/health/topology/failover/cert-rotate/acl/backup/scale-out/chaos). 23.9 MB single binary under the ≤30 MB gate (ADR-0024); 2 of 11 adapters live, 8 to go |
| `portfolio` *(coming soon)* | ⚪ planned | Blazor Server portfolio website — the site that lists everything else |
| `dataflow-studio` *(coming soon)* | ⚪ planned | SQL Server CDC → Kafka → StarRocks + ClickHouse data platform |
| `nexus-platform` *(coming soon)* | ⚪ planned | Microservices reference — gRPC + Kafka + REST, sagas, K8s-ready |

## Top skills

```
●  .NET 10 / C# 13 · ASP.NET Core · Blazor Server · gRPC · Native AOT
●  Kafka (KRaft, Streams, ksqlDB, Debezium, MirrorMaker 2) · Schema Registry
●  SQL Server Always On · Percona MySQL PXC · PostgreSQL Patroni · MongoDB RS · Vitess (MySQL sharding)
●  StarRocks · ClickHouse · Redis Cluster · Kimball modelling · SCD2
●  ML.NET · ONNX · PyTorch→ONNX · Semantic Kernel · Ollama · HuggingFace
●  Docker Swarm · HashiCorp Nomad · Consul · Vault · Kubernetes · Terraform · Packer
●  OpenTelemetry · Prometheus HA · Grafana HA (VRRP VIP) · Loki · Tempo · Alertmanager
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
