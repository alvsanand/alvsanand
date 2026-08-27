---
date: 2026-08-27
authors: [alvsanand]
categories:
  - General
  - Data
  - Learning
---

# AWS and DuckDB: a Wild West Story

On August 26, DuckDB announced that **DuckLabs is joining AWS** as a new
subsidiary. It is big news in the data world. The internet is already preparing
the funeral, although "AWS bought DuckDB" is not quite correct.

DuckDB is the open-source database. DuckLabs is the Amsterdam company that employs
many of the main DuckDB contributors. The DuckDB Foundation owns the core
intellectual property. According to the announcement, DuckDB, DuckLake, Quack and
the extensions will remain open source under the MIT license.

So, should we worry? Of course we should. We are engineers. I have three theories.

![opencode Loop](/img/duckdb_and_aws.jpg){ width="80%" }

## 1. The good theory: AWS will pay the bills

DuckDB is used everywhere: on laptops, in notebooks, CI pipelines and data
products. It is small, fast and can read formats like Parquet and Iceberg. But
maintaining a project like this is not free. You need engineers, testing, releases
and support.

With AWS money, the DuckDB team may be able to spend more time building the
database and less time finding funding or doing consulting work. That could be very
good for us. We get a better-funded open-source project, and AWS pays the bill.
Finally, someone else is buying the coffee.

The Foundation and the MIT license also offer some protection. AWS cannot simply
take the existing code and make it private tomorrow. At least not without making
the lawyers and the internet very busy.

## 2. The practical theory: AWS wants the technology

AWS has many data services, but the competition is strong. Databricks, Snowflake,
ClickHouse, Trino and others are moving fast. AWS also has a lot of services, so
adding one more to the menu should be easy, right?

DuckDB is interesting for AWS because it is not another cloud warehouse. It can run
inside an application, on a laptop or close to the data. The DuckLabs team's
experience could help AWS with local analytics, Glue, S3, Iceberg and other data
services.

This could be useful. Better integration with AWS is not automatically bad. The
problem starts if DuckDB becomes much better inside AWS than everywhere else. The
community needs a tool that works well across clouds, not only in one AWS garden.
Otherwise the duck may become free, but only after accepting 37 IAM permissions.

## 3. The bad theory: the corporate machine

The biggest risk is not that AWS closes DuckDB. The MIT license protects the
existing code. The real risk is that the core engineers slowly stop working on the
public project and focus on internal AWS priorities.

The code can remain open source and still become less useful. Releases can slow
down. Important extensions can focus on AWS. The project can become less friendly
to people on other clouds. And some engineers could leave to start a new database
called GooseDB.

This is why the next few months matter more than the announcement. I will watch the
release pace, GitHub activity, support for non-AWS environments and what the DuckDB
Foundation actually does. Also, how many times AWS says "customer-centric" in the
next press release.

## What should we do?

For now, nothing dramatic. Keep using DuckDB if it works for you. It is still a
great option for local development, homelabs, CI/CD and analytics workloads. No
need to migrate everything to Spark just because a slide deck said "synergy".

But keep your architecture portable:

1. Store data in open formats like Parquet or Apache Iceberg.
2. Keep transformation logic separate from the database engine.
3. Pin DuckDB versions in CI and test upgrades.
4. Do not depend too much on new AWS-only features before we know how portable they
  are. "Portable" should mean more than "works in three AWS regions".

If things go wrong, we should be able to change the compute engine without moving
all our data and rewriting everything.

## My thoughts

I do not think this is a reason to panic. It can give DuckDB more money and more
engineers. It can also bring more AWS influence into the project. Both can be true.
The cloud can fund the duck and still try to sell us a duck-shaped subscription
later.

The best plan is simple: keep using DuckDB, avoid unnecessary lock-in and watch the
real work instead of guessing from headlines. Keep a backup plan, because "we can
migrate later" is the most popular bedtime story in data engineering.

For now, the more accurate headline is:

> **DuckLabs is joining AWS. DuckDB is still open source, and the interesting part
> is what happens next.**

## Sources

- [DuckLabs to Join AWS, Projects to Remain Open Source](https://duckdb.org/2026/08/26/ducklabs-to-join-aws.html)
- [DuckDB FAQ](https://duckdb.org/faq)
- [DuckDB Foundation](https://duckdb.foundation/)
