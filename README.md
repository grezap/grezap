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
> all under master-plan budgets. **`v0.8.3` (2026-06-22) — Phase 0.I: the **Observability** adapter (ClusterId `observability`)** — the **third non-data-tier** adapter, managing the **Grafana LGTM stack** (Prometheus + Loki + Grafana + Tempo + Alertmanager + OTel Collector) across **14 VMs + 2 VRRP VIPs** (grafana VIP `.184`, grafana-db VIP `.185`; MinIO is the Loki/Tempo S3 backend). Deliberate access divergence caught by diagnosing the live contract first: the obs node TLS leaves are on the tier's OLD CA generation (the tier was offline during the v0.8.1 Vault greenfield) while the build host now trusts the NEW root — so the adapter probes service endpoints over SSH using each node's own `ca.crt` + reads runtime creds from Vault KV via `INexusVaultClient`; no managed Prometheus/Grafana/Loki driver (NetArchTest). 8 verbs live-verified GREEN, zero adapter code bugs: status (14 nodes + VIP holders) · health (Prom ready + scrape-targets-up, Alertmanager mesh peers, Loki/Tempo memberlist rings, Grafana database=ok, OTel loopback, Grafana-PG streaming replication, MinIO S3 reachable, both VIPs bound) · topology (14 nodes + 2 VIP pseudo-nodes) · failover = Grafana/Grafana-PG VRRP cutover (`--direction grafana` proven, RTO≈1.2s) · scale-out = Loki/Tempo memberlist ring add/remove (fixed-HA roles → graceful N/A) · cert-rotate = build-host `pki_int/observability-server` issue + SSH-push + per-service reload · acl = Grafana users via `/api/admin/users` · backup = graceful actionable N/A (MinIO erasure-coded + PG repl RPO≈0 + dashboards-as-code + ephemeral Prom TSDB). The verify surfaced three infra divergences (all v0.8.1-Vault-greenfield-while-obs-offline casualties): a tier-wide vault-agent broken-trust state (this drove the SSH-local-curl posture), the Grafana admin password drifted from KV (`acl` honestly returns HTTP 401 + the reconcile command), and the grafana-pg streaming replication is split (`health` correctly red); cert-rotate / failover grafana-db / acl grant are implemented but not live-run on the degraded tier (they need a Greg-authorized tier trust re-apply first). 27.59 MB AOT (unchanged from v0.8.2); 194/194 tests; ADR-0024. **3 of the 5 non-data tiers done; the CLI now manages the data tier + 3 non-data tiers (Foundation + Orchestration + Observability).** Preceded by **`v0.8.2` (2026-06-19) — Phase 0.E: the **Swarm** adapter (ClusterId `swarm`)** — the second **non-data-tier** adapter and the **most reusable**: it wires the already-built Consul/Nomad/Portainer clients + `ClusterStatusService` + `FailoverTestService` (built v0.1–v0.5) into the full `IClusterAdapter` surface over the orchestration tier (3 Consul-server/Nomad-server/Swarm-manager nodes + 3 Consul-client/Nomad-client/Swarm-worker/Portainer-agent nodes + a manager-pinned Portainer service); same build-host control-plane posture (Consul/Nomad mgmt tokens from Vault KV, over HTTPS; no managed Docker/Consul/Nomad driver). status/health/topology = the 3-way rollup enriched with `docker node ls`; failover `--direction` → consul-leader / nomad-leader (RTO≈2–3s) / **swarm-manager** (vmrun host-level suspend of the raft-leader VM, RTO≈21s); reversible `docker node drain`/`demote` scale-out; `consul`+`nomad` snapshot backup round-trip (restore refused on the live cluster); cert-rotate force-reissues the `pki_int` leaves (the vault-agent `pkiCert` persists+reuses the cert) then consul ROLLING / nomad PARALLEL big-bang; acl = Consul + Nomad tokens (bootstrap/agent protected); chaos = `nexus-chaos.sh` on a worker + docker-restart-after-nft. 3 live-caught bugs fixed; cold-rebuild-proven; 27.59 MB AOT; 173/173 tests; ADR-0023. **2 of the 5 non-data tiers done.** Preceded by **`v0.8.1` (2026-06-18) — the first two **non-data-tier** adapters: `VaultAdapter` (ClusterId `vault`) + `FoundationAdAdapter` (ClusterId `foundation-ad`)** — the CLI now manages the **Foundation** tier. **VaultAdapter** drives the 3-node raft Vault HA trust root (`vault-1/2/3`) + `vault-transit` (Shamir-seal auto-unseal custodian): control plane = HTTP from the build host with the operator `VAULT_TOKEN` (root token never ships to a node) + node-local SSH; no managed Vault driver / no shelled `vault` binary (NetArchTest). All verbs first-try-green — failover = `vault operator step-down` on the active (standby promotes, RTO≈2.0s); scale-out = stop/start a STANDBY peer; backup = raft snapshot save + non-destructive `meta.json` inspect (restore refused on the live trust root); cert-rotate = re-issue listener certs from `pki_int/vault-server` + SIGHUP leader-last; acl = Vault policies+approles; chaos = process-kill a STANDBY — plus a new bespoke **`recover-ha`** verb (a new `IRecoverableCluster` capability = the declarative boot-race recovery: unseal `vault-transit` → restart `vault-1/2/3` → poll unsealed; the only exposed unseal path). Mutating verbs target STANDBYS so the active stays up. **FoundationAdAdapter** manages the 2-DC `nexus.lab` AD forest (Windows Server 2025: `dc-nexus` + `dc-nexus-2`) over Windows-SSH + the `nexus-gateway` egress over Linux-SSH; status/health/topology/acl are real, and AD being multi-master, failover/FSMO, DC add/remove, system-state backup, NTDS cert-rotation/chaos return graceful actionable "not applicable" (terraform/out-of-band); `health` proves both DCs reachable + AD replication result=0 + AD-integrated DNS zones + KDS root key + all 5 FSMO roles + the gateway dnsmasq/nftables/NAT. 1 live-caught bug fixed (the AD-replication probe's explicit `-Server` arg degraded `Get-ADReplicationPartnerMetadata` to empty fields — dropped). No new VMs; 27.36 MB AOT; 159/159 tests; ADR-0022. **Begins the 5 non-data-tier adapters block** (next v0.8.2 Swarm → v0.8.3 Observability → v0.8.4 Lakehouse → v0.8.5 Harbor). Preceded by **`v0.8.0` (2026-06-18) — the full-fleet roll-up** (all 12 data+sharded adapter families sealed + AOT re-validated ≤30 MB, no new adapter). Earlier: **`v0.7.3` (2026-06-18) — the **Citus** adapter** (ClusterId `citus`, Phase 0.P): the Citus-sharded PostgreSQL cluster with **full Patroni HA** — 3 etcd DCS + a coordinator Patroni pair + 2 worker Patroni pairs; PG 17 + Citus 14.1; `events` hash-distributed on `tenant_id` (32 shards = 16+16 across the worker groups). **Citus = Patroni HA per group + Citus distribution.** Operator `nexus-cluster-admin` (Vault-KV) auto-propagated to the workers + `.pgpass` so distributed queries run as the operator via the coordinator VIP; `topology` populates Shards; `patronictl switchover` failover (RTO≈1.6s, VIP follows the leader); operator `COPY` round-trip backup of the distributed dataset (800 rows); cert-rotate PG-reload/etcd-restart; acl = PG roles propagated to workers. Adapter first-try-green on every verb; 26.71 MB AOT; 137/137 tests. **12 of 13 adapter families live.** Earlier: **`v0.7.2` (2026-06-17) — the **Vitess** adapter** (ClusterId `vitess`, Phase 0.O): the Vitess-sharded MySQL cluster — 2 shards ×3 tablets, keyspace `commerce` hash-vindex on `customer_id`; hybrid mTLS-control-plane (`nexus-vtctldclient`) + vtgate `:15306` SQL plane; graceful `PlannedReparentShard` failover (RTO≈0.17s); logical `mysqldump`-per-shard backup; chaos primary-freeze → VTOrc auto-reparent. 26.52 MB AOT; 114/114 tests. Earlier: **`v0.7.1` (2026-06-16) — the **MongoSharded**
> adapter** (ClusterId `mongo-sharded`, Phase 0.N): the first adapter on the 0.7.x
> line and the genuinely-**sharded** MongoDB cluster (3 config-server RS + 2 shard
> RSes ×3 + 2 `mongos` routers), distinct from the 0.G.2 `mongo` replica set —
> two-headed keyFile auth (`__system`@local for direct mongod RS ops + `nexus-sharded-admin`@admin
> through a mongos), `topology` populates Shards, shard-primary failover RTO≈2.8s,
> `mongodump`-through-mongos backup round-trip, `cert-rotate` graceful N/A (no TLS in
> v1). Preceded by **`v0.7.0` (2026-06-16) — the Phase 0.G base roll-up** (the 0.G
> exit-gate milestone: the 9 data-tier adapter families sealed + AOT validated ≤30 MB).
> **10 of 13 adapter families live.** Earlier: **`v0.6.7` (2026-06-15) — the data-tier
> adapter expansion. Redis (v0.6.0,
> mTLS-only) + Mongo (v0.6.1, password-auth) + Percona XtraDB Cluster + ProxySQL
> (v0.6.2, Galera synchronous multi-primary) + PostgreSQL Patroni HA (v0.6.3,
> streaming replication + etcd DCS + HAProxy VIP) + ClickHouse (v0.6.4, the first
> **sharded** + **analytics-tier** engine — 3 shards × 2 replicas over a ClickHouse
> Keeper RAFT quorum) + StarRocks (v0.6.5, an MPP MySQL-protocol warehouse — 3 FE
> BDB-JE metadata quorum + 3 BE tablet nodes) + **SQL Server FCI + Always On AG
> (v0.6.6, the first WINDOWS cluster — two adapters over one vms.yaml cluster:
> `sqlserver` WSFC/FCI + `sqlserver-ag` AG/Listener; Windows-SSH + `sqlcmd`, no
> managed `Microsoft.Data.SqlClient`; FCI `Move-ClusterGroup` failover RTO≈4.5s + AG
> `ALTER AVAILABILITY GROUP FAILOVER` RTO≈8.2s; Listener strict-TLS; one shared FCI
> cert + single cluster checkpoint; manual-seed AG scale-out)** + **Kafka (v0.6.7,
> the last data tier — `kafka-east` + `kafka-west` KRaft clusters + the
> `kafka-ecosystem` observe adapter; mTLS-only like Redis, no managed driver;
> controller-leader failover RTO≈4.5s; enabled the KRaft `StandardAuthorizer` so
> `acl` enforces; zero live bugs)** ship all
> data-tier verbs green — status/health/topology/failover (StarRocks **FE leader
> re-election** RTO≈1.5s; ClickHouse Keeper re-election RTO≈1.1s; SQL Server FCI
> Move-ClusterGroup RTO≈4.5s + AG failover RTO≈8.2s)/cert-rotate/acl/backup
> (StarRocks genuine async `BACKUP/RESTORE SNAPSHOT` to the file:// NFS repo; SQL
> Server COPY_ONLY RESTORE-WITH-MOVE round-trip)/scale-out/chaos. The Vault-KV
> operator-credential model (the `nexus-cluster-admin` password lives only in Vault
> KV, fetched at runtime via an optional `INexusVaultClient`) carries across every
> password-auth adapter; 25.95 MB under the ≤30 MB gate. 5 adapters follow.** **0.G.3.5 architectural refactor** (per-cluster
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
| [`nexus-cli`](https://github.com/grezap/nexus-cli) | 🟢 `v0.8.3` | .NET 10 Native AOT CLI — Phase 0.F verbs (`cluster-status` · `infrastructure` · `failover-test` · `demo` · `kafka failover`) **plus the `IClusterAdapter` data-tier + non-data-tier expansion**. **`v0.8.3` (Phase 0.I) — the Observability adapter** (ClusterId `observability`; the Grafana LGTM stack — Prometheus + Loki + Grafana + Tempo + Alertmanager + OTel Collector across 14 VMs + 2 VRRP VIPs; MinIO as the Loki/Tempo S3 backend; the third non-data-tier adapter — probes service endpoints over SSH using each node's own `ca.crt` [a deliberate divergence: obs leaves on the OLD CA, build host on the NEW root] + reads creds from Vault KV; no managed Prometheus/Grafana/Loki driver; 8 verbs live-verified GREEN, zero adapter bugs — failover = Grafana VRRP cutover RTO≈1.2s; scale-out = Loki/Tempo memberlist ring; cert-rotate = `pki_int/observability-server`; acl = Grafana users; backup = graceful N/A; `health` honestly reports the grafana-pg split + `acl` the admin-password drift), the third of the **5 non-data-tier adapters** after **`v0.8.2`** (Phase 0.E — the Swarm adapter, ClusterId `swarm`; the orchestration tier — Docker Swarm + Nomad + Consul + Portainer; the most reusable adapter — wires the v0.1–v0.5 Consul/Nomad/Portainer clients + ClusterStatusService + FailoverTestService into the full SPI; `--direction` failover to consul-leader/nomad-leader/swarm-manager [vmrun suspend] RTO≈2/3/21s; reversible drain scale-out; consul+nomad snapshot backup; force-reissue cert-rotate consul-rolling/nomad-parallel; Consul+Nomad acl; chaos-on-a-worker) and **`v0.8.1`** (Phase 0.A-0.D/0.M — the Vault HA trust root + the 2-DC AD forest, the first non-data-tier adapters; + the bespoke `recover-ha` verb) and the **`v0.8.0`** full-fleet roll-up (all 12 data+sharded families sealed). **15 adapter families live (12 data + Vault + Swarm + Observability) across the tiers; 3 of 5 non-data tiers done.** Earlier: **`v0.7.2` (Phase 0.O) — the Vitess adapter** (ClusterId `vitess`: 3 etcd + vtctld/VTOrc + 2 vtgate + 2 shards ×3 tablets; keyspace `commerce` hash-vindex; hybrid mTLS control plane + vtgate `:15306` SQL plane; `topology` populates Shards; `PlannedReparentShard` RTO≈0.17s; logical mysqldump backup; chaos→VTOrc auto-reparent), after the **`v0.7.1`** Phase 0.N MongoSharded adapter, (ClusterId `mongo-sharded`: 3 config-server RS + 2 shard RSes ×3 + 2 `mongos`; two-headed keyFile auth — `__system`@local for mongods + `nexus-sharded-admin`@admin through mongos; `topology` populates Shards; shard-primary failover RTO≈2.8s; cert-rotate graceful N/A — no TLS in v1), after the **`v0.7.0`** Phase 0.G base roll-up (9 families sealed, AOT ≤30 MB). **11 of 13 families live.** The 0.G data-tier set: **Redis** (v0.6.0, mTLS-only) + **Mongo** (v0.6.1) + **Percona XtraDB Cluster + ProxySQL** (v0.6.2, Galera) + **PostgreSQL Patroni HA** (v0.6.3, etcd DCS + HAProxy VIP) + **ClickHouse** (v0.6.4, sharded + Keeper RAFT) + **StarRocks** (v0.6.5, MPP warehouse) + **SQL Server FCI + Always On AG** (v0.6.6, the first Windows cluster) + **Kafka** (v0.6.7, `kafka-east`/`kafka-west` KRaft + the `kafka-ecosystem` observe adapter; mTLS-only, no managed `Confluent.Kafka`; controller-leader failover RTO≈4.5s; enabled the KRaft `StandardAuthorizer` so `acl` enforces) all ship live-verified (status/health/topology/failover/cert-rotate/acl/backup/scale-out/chaos). The Vault-KV operator-credential model carries across every password-auth adapter; Redis + Kafka are mTLS-only (no operator password). 26.18 MB single binary under the ≤30 MB gate (ADR-0024); **11 of 13 adapter families live**, remaining per canon order (mongo-sharded → Vitess → Citus → the 5 non-data tiers) |
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
