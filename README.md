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

> **Phase 0 — Infrastructure foundation** *(in flight, ~60% complete)*
> The 66-VM lab is half-built. Foundation tier (AD DS + HashiCorp Vault HA on Raft +
> PKI + LDAPS + Transit auto-unseal), orchestration tier (Docker Swarm + HashiCorp
> Nomad/Consul + Portainer CE, mTLS end-to-end), and the **Kafka ecosystem tier**
> (two KRaft clusters on mutual TLS + Schema Registry + Connect + Debezium + ksqlDB +
> MirrorMaker 2 cross-cluster DR) are all live and cold-rebuildable. The .NET 10
> Native AOT operator CLI (`nexus-cli`) ships **4 of 5 master-plan verbs**.
> Next: the remaining v0.5 CLI verb (`kafka failover`), then the application phases
> (Vol01-Vol13 — `dataflow-studio` lights up first).

## Pinned projects

| Project | Status | What it is |
|---|---|---|
| [`portfolio-index`](https://github.com/grezap/portfolio-index) | 🟢 live | Skills matrix + living project grid — start here |
| [`nexus-infra-vmware`](https://github.com/grezap/nexus-infra-vmware) | 🟢 live | Tier-1 foundation — Vault HA + PKI + AD DS + dnsmasq gateway. Phase 0.D closed + 0.E/0.H Vault scaffolding |
| [`nexus-infra-swarm-nomad`](https://github.com/grezap/nexus-infra-swarm-nomad) | 🟢 `v0.2.0` | Tier-2 orchestration — 3+3 Docker Swarm + Nomad + Consul + Portainer CE, cold-rebuildable |
| [`nexus-infra-kafka`](https://github.com/grezap/nexus-infra-kafka) | 🟢 `v0.1.0` | Tier-3 Kafka ecosystem — 15 VMs, two mTLS KRaft clusters + Schema Registry + Connect/Debezium + ksqlDB + MM2 |
| [`nexus-cli`](https://github.com/grezap/nexus-cli) | 🟢 `v0.4.0` | .NET 10 Native AOT CLI — 4 of 5 master-plan verbs (`cluster-status` · `infrastructure` · `failover-test` · `demo`); `kafka failover` queued for v0.5 |
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
