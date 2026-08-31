---
date: 2026-09-15
authors: [alvsanand]
categories:
  - General
  - Learning
  - OSS
---

# The Open Source Trojan Horse

For the last decade, the Silicon Valley infrastructure playbook was very
predictable: launch a project under a permissive open-source license, ride the
community goodwill to reach market dominance, and raise hundreds of millions in
venture capital.

Then reality arrives. Revenue targets get closer, the big cloud providers
repackage the tool as a managed service, and the corporate sponsor panics and
pulls the rug: the permissive open-source license is swapped for a restrictive,
proprietary one.

![The open source trojan horse timeline](/img/oss-trojan-horse.jpg){ width="80%" }

## The bait-and-switch problem

To be clear: building closed-source or source-available software is not bad by
itself. Companies have every right to build a profitable business and protect
their intellectual property.

Projects like **n8n** (Fair-code / Sustainable Use License) or **Redpanda**
(Business Source License for the enterprise features) set clear commercial
guardrails early in their life. When a company declares its business intentions
from day one, developers can make an informed choice about adopting the
technology. The expectations are honest and the trust stays intact.

The toxicity starts with the bait-and-switch. The betrayal is when a company uses
the open-source wind to become ubiquitous, benefiting from thousands of unpaid
community bug reports, third-party integrations and grassroots evangelism, and
then locks the gates once it is an industry standard. It is using a community to
build your empire, and then punishing that same community for your own failure to
build a defensible business model.

## A history of fractured communities

When companies change the rules of the game in the middle of the game, it almost
always starts an ecosystem war.
* **2018, MongoDB's SSPL salvo.** MongoDB drops its open-source license for the
  new Server Side Public License (SSPL), to force cloud providers to open source
  their underlying management infrastructure. AWS answers with DocumentDB, a
  proprietary clone.
* **2020, the death of CentOS Linux.** Red Hat (IBM) abruptly terminates CentOS
  as a stable downstream clone of Red Hat Enterprise Linux (RHEL). The community
  forks it immediately, and Rocky Linux and AlmaLinux are born to fill the void.
* **2021, the cloud wars escalate.** Elastic moves Elasticsearch to the SSPL to
  fight AWS. AWS forks the code into OpenSearch and the ecosystem splits. At the
  same time, Docker puts Docker Desktop behind a paywall for large companies, and
  many developers move to alternatives like Podman.
* **2022, Lightbend locks down Akka.** Lightbend abandons Apache 2.0 for the
  Business Source License (BSL) in the very popular Akka framework. The Apache
  Software Foundation hosts the community fork, Apache Pekko.
* **2023, the infrastructure shock.** HashiCorp moves its entire suite
  (Terraform, Vault, Consul) to BSL. The infrastructure community revolts, and a
  coalition of vendors forks the code into OpenTofu (Terraform) and OpenBao
  (Vault), both handed to the Linux Foundation for neutral stewardship.
* **2024, the cache crisis and the binary paywalls.** Redis abandons its BSD
  license for a dual SSPL/RSALv2 model, and the Linux Foundation's Valkey fork
  appears almost immediately, backed by AWS, Google and Oracle. Elastic goes in
  the other direction and adds an AGPLv3 option to Elasticsearch. Separately,
  Buoyant keeps the Linkerd code in the CNCF but puts the stable binaries behind
  a paywall.
* **2025, the NATS standoff and the great rollback.** Synadia tries to pull NATS
  out of the CNCF using a trademark loophole, and surrenders after a lot of
  pushback. Redis, watching how many enterprises had already moved to Valkey,
  reverses course and adds the OSI-approved AGPLv3 option. IBM closes the
  HashiCorp acquisition for $6.4B and keeps the BSL, while OpenTofu keeps growing.

## The end of the single-vendor monopoly

The era of the single-vendor open-source monopoly is dead. Today, when a company
tries a rug pull on a foundational tool, the ecosystem does not only complain, it
mobilizes. Cloud providers, competitors and burned developers fund
foundation-backed forks and move the engineering talent away from the original
creator.

If a company wants to build proprietary software, it should do it from the start.
But if it decides to ride the open-source wind to the top, it is learning the hard
way that it cannot suddenly close the window without breaking the glass.
